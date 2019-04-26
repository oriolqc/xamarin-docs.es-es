---
title: Devoluciones de llamada en Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: c6eaf4dd90b172053b4b87e3427cfe35213c6727
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61215883"
---
# <a name="callbacks-on-android"></a>Devoluciones de llamada en Android

Llamar a Java desde C# es ligeramente un *riesgosa*. Es decir hay un *patrón* para devoluciones de llamada de C# a Java; sin embargo, resulta más complicado que nos gustaría.

Hablaremos sobre las tres opciones para realizar devoluciones de llamada que sean más convenientes para Java:

- Clases abstractas
- Interfaces
- Métodos virtuales

## <a name="abstract-classes"></a>Clases abstractas

Esta es la ruta más sencilla para las devoluciones de llamada, por lo que también le recomendaría utilizar `abstract` si simplemente desea obtener una devolución de llamada que trabaja en la forma más sencilla.

Comencemos con un C# nos gustaría Java para implementar de clase:

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

Estos son los detalles para solucionar este problema:

- `[Register]` genera un nombre de paquete bueno en Java, obtendrá un nombre generado automáticamente el paquete sin él.
- Creación de subclases de `Java.Lang.Object` señales para la inserción de .NET para ejecutar la clase a través del generador de Java de Xamarin.Android.
- Constructor vacío: es lo que desea utilizar desde el código de Java.
- `(IntPtr, JniHandleOwnership)` constructor: es lo que Xamarin.Android usará para crear el C#-equivalentes de los objetos de Java.
- `[Export]` señales de Xamarin.Android para exponer el método a Java. También podemos cambiar el nombre del método, ya que el mundo de Java le gusta usar métodos en minúsculas.

Siguiente vamos a hacer un C# método para probar el escenario:

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
`JavaCallbacks` puede ser cualquier clase para probar esto, siempre sea un `Java.Lang.Object`.

Ahora, ejecute la inserción de .NET en el ensamblado de .NET para generar un AAR. Consulte la [Guía de introducción](~/tools/dotnet-embedding/get-started/java/android.md) para obtener más información.

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
Por lo que nos:

- Implementa el `AbstractClass` en Java con un tipo anónimo
- Asegurado de que nuestra instancia devuelve `"Java"` desde Java
- Asegurado de que nuestra instancia devuelve `"Java"` desdeC#
- Agregar `throws Throwable`, puesto que C# constructores actualmente se marcan con `throws`

Si ejecutamos esta prueba unitaria como-es, produciría un error con un error como:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

¿Qué falta aquí es un `Invoker` tipo. Esto es una subclase de `AbstractClass` que reenvía C# las llamadas a Java. Si escribe un objeto de Java el C# world y el equivalente C# tipo es abstracto y, después, Xamarin.Android busca automáticamente un C# tipo con el sufijo `Invoker` para su uso en C# código.

Xamarin.Android usa esto `Invoker` patrón de enlace para proyectos de Java entre otras cosas.

Aquí está nuestra implementación de `AbstractClassInvoker`:
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

Hay bastantes sucede aquí, hemos:

- Agrega una clase con el sufijo `Invoker` que cree subclases `AbstractClass`
- Agregar `class_ref` para mantener la referencia JNI a la clase de Java que cree subclases nuestro C# clase
- Agregar `id_gettext` para mantener la referencia JNI a Java `getText` (método)
- Incluye un `(IntPtr, JniHandleOwnership)` constructor
- Implementa `ThresholdType` y `ThresholdClass` como un requisito para Xamarin.Android para conocer los detalles sobre la `Invoker`
- `GetText` es necesario para buscar el Java `getText` método con la firma apropiada de JNI y llámelo
- `Dispose` solo se necesita para borrar la referencia a `class_ref`

Después de agregar esta clase y generar un nuevo AAR, nuestra prueba unitaria se aprueba. Como puede ver este patrón para las devoluciones de llamada no es *ideal*, pero factible.

Para obtener más información sobre la interoperabilidad de Java, vea el tipo de increíbles [Xamarin.Android documentación](~/android/platform/java-integration/working-with-jni.md) sobre este tema.

## <a name="interfaces"></a>Interfaces

Las interfaces son igual que las clases abstractas, excepto un detalle: Xamarin.Android no genera Java para ellos. Esto es porque antes de incrustación de. NET, no hay muchos escenarios donde Java implementaría una C# interfaz.

Supongamos que tenemos la siguiente C# interfaz:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` señala a la inserción de .NET que se trata de una interfaz de Xamarin.Android, pero en caso contrario, esto es exactamente igual que un `abstract` clase.

Puesto que Xamarin.Android actualmente no se generará el código de Java para esta interfaz, agregue el siguiente de Java para su C# proyecto:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

Puede colocar el archivo en cualquier parte, pero asegúrese de establecer su acción de compilación en `AndroidJavaSource`. Esto señalará a la inserción de .NET para copiarlo en el directorio adecuado para que se compilan en el archivo AAR.

A continuación, el `Invoker` implementación será exactamente lo mismo:

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

Supongamos que tiene el siguiente C# clase:

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

Si ha seguido el `abstract` ejemplo de clase anterior, podría funcionar, excepto un detalle: _Xamarin.Android no buscar las `Invoker`_ .

Para solucionar este problema, modifique el C# clase sea `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Esto no es ideal, pero obtiene este trabajo de escenario. Xamarin.Android recogerá el `VirtualClassInvoker` y puede usar Java `@Override` en el método.

## <a name="callbacks-in-the-future"></a>Devoluciones de llamada en el futuro

Hay un par de cosas que podríamos para mejorar estos escenarios:

1. `throws Throwable` en C# constructores se ha corregido en esta [PR](https://github.com/xamarin/java.interop/pull/170).
1. Hacer que el generador de Java en Xamarin.Android admite las interfaces.
    - Esto elimina la necesidad de agregar el archivo de código fuente de Java con una acción de compilación `AndroidJavaSource`.
1. Asegúrese de una manera de Xamarin.Android cargar un `Invoker` de clases virtuales.
    - Esto elimina la necesidad para marcar la clase en nuestra `virtual` ejemplo `abstract`.
1. Generar `Invoker` clases automáticamente para la inserción de .NET
    - Esto va a ser complicado, pero es factible. Xamarin.Android ya está haciendo algo parecido a este enlace para proyectos de Java.

Hay una gran cantidad de trabajo que realizar aquí, pero estas mejoras a la inserción de .NET son posibles.

## <a name="further-reading"></a>Información adicional

* [Introducción a Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Investigación preliminar de Android](~/tools/dotnet-embedding/android/index.md)
* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Las descripciones y códigos de error](~/tools/dotnet-embedding/errors.md)
