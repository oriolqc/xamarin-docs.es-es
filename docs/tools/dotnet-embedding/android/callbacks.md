---
title: Devoluciones de llamada en Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 3f18516643c00dc67fe533ecab00e1f415eb5c46
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922827"
---
# <a name="callbacks-on-android"></a>Devoluciones de llamada en Android

Llamar a Java desde C# es en cierto modo una *arriesgado*. Es decir hay un *patrón* para las devoluciones de llamada de C# para Java; sin embargo, resulta más complicado que nos gustaría.

Trataremos las tres opciones para realizar las devoluciones de llamada que hacen que mejor se adapte Java:

- Clases abstractas
- Interfaces
- Métodos virtuales

## <a name="abstract-classes"></a>Clases abstractas

Se trata de la ruta más sencilla para las devoluciones de llamada, por lo que le recomendará usar `abstract` si solo desea obtener una devolución de llamada que se trabaja en la forma más sencilla.

Puede empezar con una clase de C#, que nos gustaría Java para implementar:

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

Aquí encontrará los detalles para solucionar este problema:

- `[Register]` genera un nombre de paquete "nice" en Java, obtendrá un nombre de paquete generado automáticamente sin él.
- Creación de subclases `Java.Lang.Object` señales a la inserción de .NET para atravesar la clase de generador de Java de Xamarin.Android.
- Constructor vacío: es lo que desea utilizar desde el código de Java.
- `(IntPtr, JniHandleOwnership)` constructor: es lo que Xamarin.Android va a utilizar para la creación de C#-equivalente de objetos de Java.
- `[Export]` envía una señal Xamarin.Android para exponer el método para Java. También podemos cambiar el nombre del método, ya que todo el mundo de Java le gusta usar métodos de minúsculas.

Siguiente vamos a hacer un método de C# para probar el escenario:

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```
`JavaCallbacks` puede ser cualquier clase para probar esto, siempre y cuando sea un `Java.Lang.Object`.

Ahora, ejecute la incrustación de .NET en el ensamblado de .NET para generar un AAR. Consulte la [Guía de introducción](~/tools/dotnet-embedding/get-started/java/android.md) para obtener más información.

Después de importar el archivo AAR en Android Studio, vamos a escribir una prueba unitaria:

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```
Por lo que es:

- Implementa el `AbstractClass` en Java con un tipo anónimo
- Asegurado de que nuestra instancia devuelve `"Java"` desde Java
- Asegurado de que nuestra instancia devuelve `"Java"` desde C#
- Agregar `throws Throwable`, ya que los constructores de C# están marcados actualmente con `throws`

Si ejecutamos esta prueba unitaria como-es, produciría un error con un error como:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

¿Qué es falta mostramos una `Invoker` tipo. Esto es una subclase de `AbstractClass` que reenvía las llamadas de C# para Java. Si un objeto de Java entra en el mundo de C# y el tipo equivalente de C# es abstracto, Xamarin.Android busca automáticamente un tipo de C# con el sufijo `Invoker` para su uso en código de C#.

Xamarin.Android utiliza este `Invoker` patrón para los proyectos de enlace de Java entre otras cosas.

Esta es la implementación de `AbstractClassInvoker`:
```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Hay un poco sucediendo aquí, hemos:

- Agrega una clase con el sufijo `Invoker` que cree subclases `AbstractClass`
- Agregar `class_ref` para contener el JNI hacen referencia a la clase de Java que subclasifique nuestra clase de C#
- Agregar `id_gettext` para mantener la referencia JNI a Java `getText` (método)
- Incluye un `(IntPtr, JniHandleOwnership)` constructor
- Implementa `ThresholdType` y `ThresholdClass` como un requisito para Xamarin.Android conocer los detalles sobre el `Invoker`
- `GetText` necesarios para buscar el Java `getText` método con la firma adecuada de JNI y llámelo
- `Dispose` solo es necesario para borrar la referencia a `class_ref`

Después de agregar esta clase y generar un nuevo AAR, nuestra unidad probar pasadas. Como puede ver este patrón para las devoluciones de llamada no es *ideal*, pero factible.

Para obtener detalles sobre la interoperabilidad de Java, consulte las sorprendentes [Xamarin.Android documentación](~/android/platform/java-integration/working-with-jni.md) sobre este asunto.

## <a name="interfaces"></a>Interfaces

Las interfaces son igual que las clases abstractas, excepto un detalle: Xamarin.Android no genera Java para ellos. Esto es porque antes de la incrustación de. NET, no hay muchos escenarios donde Java podría implementar una interfaz de C#.

Supongamos que tenemos la interfaz de C# siguiente:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` señala a la inserción de .NET que se trata de una interfaz Xamarin.Android, pero en caso contrario, esto es exactamente el mismo que un `abstract` clase.

Puesto que Xamarin.Android actualmente no generará el código de Java para esta interfaz, agregue el siguiente Java al proyecto de C#:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

Puede colocar el archivo en cualquier lugar, pero asegúrese de que establece la acción de compilación en `AndroidJavaSource`. Esto indicará la incrustación de .NET para copiarlo en el directorio adecuado que se compilan en el archivo AAR.

Después, el `Invoker` implementación será exactamente lo mismo:

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Después de generar un archivo AAR, en Android Studio podríamos escribir la siguiente unidad de paso de prueba:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>Métodos virtuales

Reemplazar un `virtual` en Java, es posible, pero no una gran experiencia.

Supongamos que tiene la siguiente clase de C#:

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

Si ha seguido el `abstract` ejemplo de clase anterior, funcionará excepto un detalle: _Xamarin.Android no buscar el `Invoker`_ .

Para solucionar este problema, modifique la clase de C# para que sea `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Esto no es lo ideal, pero obtiene este trabajo de escenario. Xamarin.Android recogerá el `VirtualClassInvoker` y Java puede usar `@Override` en el método.

## <a name="callbacks-in-the-future"></a>Devoluciones de llamada en el futuro

Hay un par de cosas pudimos para mejorar estos escenarios:

1. `throws Throwable` en C#, constructores se fija en el objeto [PR](https://github.com/xamarin/java.interop/pull/170).
1. Hacer que el generador de Java en Xamarin.Android admite interfaces.
    - Esto elimina la necesidad de agregar el archivo de código fuente de Java con una acción de compilación `AndroidJavaSource`.
1. Asegúrese de una manera de Xamarin.Android cargar una `Invoker` de clases virtuales.
    - Esto elimina la necesidad de marcar la clase en nuestro `virtual` ejemplo `abstract`.
1. Generar `Invoker` clases para .NET incrustar automáticamente
    - Esto va a ser complicado, pero es factible. Xamarin.Android ya está haciendo algo parecido a esto para los proyectos de enlace de Java.

Hay mucho trabajo hacerse aquí, pero estas mejoras a la inserción de .NET son posibles.

## <a name="further-reading"></a>Información adicional

* [Introducción de Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Investigación de Android preliminar](~/tools/dotnet-embedding/android/index.md)
* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
