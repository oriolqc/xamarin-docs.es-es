---
title: Trabajo con JNI
description: Xamarin.Android permite escribir aplicaciones para Android en C# en lugar de Java. Varios ensamblados se proporcionan con Xamarin.Android que proporcionan los enlaces para las bibliotecas de Java, incluidos Mono.Android.dll y Mono.Android.GoogleMaps.dll. Sin embargo, los enlaces no se proporcionan para cada posible biblioteca Java y no pueden enlazar los enlaces que se proporcionan todos los tipos de Java y miembro. Para usar sin enlazar tipos de Java y los miembros, se puede usar Java Native Interface (JNI). Este artículo muestra cómo usar JNI para interactuar con tipos de Java y los miembros de las aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 8ad2dde701814c0977e25e6e58272c0aa01ca4ca
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672851"
---
# <a name="working-with-jni"></a>Trabajo con JNI

_Xamarin.Android permite escribir aplicaciones para Android en C# en lugar de Java. Varios ensamblados se proporcionan con Xamarin.Android que proporcionan los enlaces para las bibliotecas de Java, incluidos Mono.Android.dll y Mono.Android.GoogleMaps.dll. Sin embargo, los enlaces no se proporcionan para cada posible biblioteca Java y no pueden enlazar los enlaces que se proporcionan todos los tipos de Java y miembro. Para usar sin enlazar tipos de Java y los miembros, se puede usar Java Native Interface (JNI). Este artículo muestra cómo usar JNI para interactuar con tipos de Java y los miembros de las aplicaciones de Xamarin.Android._


## <a name="overview"></a>Información general

No es siempre necesario ni posible crear un administrada que se puede llamar contenedor (MCW) para invocar código de Java. En muchos casos, "alineado" JNI es perfectamente aceptable y útil para su uso único uso de miembros de Java independientes. A menudo resulta más sencillo utilizar JNI para invocar un método único en una clase de Java que al generar un enlace .jar todo.

Xamarin.Android proporciona el `Mono.Android.dll` ensamblado, que proporciona un enlace de Android `android.jar` biblioteca. Tipos y miembros no presentan en `Mono.Android.dll` y tipos no están presentes en `android.jar` puede usarse por enlazarlos manualmente. Para enlazar tipos de Java y miembros, usa el **Java Native Interface** (**JNI**) para buscar tipos, leer y escribir los campos e invocar métodos.

La API de JNI en Xamarin.Android es conceptualmente muy similar a la `System.Reflection` API en .NET: hace posible para que pueda buscar los tipos y miembros por nombre, leer y escribir valores de campo, invocar métodos y mucho más. Puede usar JNI y `Android.Runtime.RegisterAttribute` declarar métodos virtuales que se pueden enlazar para admitir la invalidación del atributo personalizado. Puede enlazar las interfaces para que se pueden implementar en C#.

Este documento se explica:

-  Cómo JNI hace referencia a tipos.
-  Cómo buscar, leer y escribir los campos.
-  Cómo buscar e invocar métodos.
-  Cómo exponer los métodos virtuales para permitir el reemplazo de código administrado.
-  Cómo exponer interfaces.



## <a name="requirements"></a>Requisitos

JNI, tal como se expone a través de la [espacio de nombres Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/), está disponible en todas las versiones de Xamarin.Android.
Para enlazar las interfaces y tipos de Java, debe usar Xamarin.Android 4.0 o posterior.


## <a name="managed-callable-wrappers"></a>Administra los contenedores RCW

Un **administrados Callable Wrapper** (**MCW**) es un *enlace* para una clase de Java o interfaz que resume todas las máquinas JNI por lo que ese cliente C# código no necesita preocuparse por la complejidad subyacente de JNI. La mayoría de `Mono.Android.dll` consta de los contenedores RCW administrados.

Contenedores invocables administrados tienen dos finalidades:

1.  Encapsular el uso JNI para que el código de cliente no necesita saber acerca de la complejidad subyacente.
1.  Permiten que subclasifique tipos de Java e implementar interfaces de Java.

El primer propósito es exclusivamente para su comodidad y la encapsulación de complejidad para que los consumidores tienen un conjunto de clases que se usan simple y administrado. Esto requiere el uso de los distintos [JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) miembros tal como se describe más adelante en este artículo. Tenga en cuenta que los contenedores RCW administrados no son estrictamente necesarias &ndash; "alineado" usar JNI es perfectamente aceptable y es útil para su uso único uso de miembros de Java independientes. Subclasificando e implementación de interfaces requiere el uso de contenedores RCW administrados.



## <a name="android-callable-wrappers"></a>Contenedores que se pueden llamar de Android

Android contenedores RCW (ACW) se requieren siempre que el tiempo de ejecución de Android (ART) necesita invocar código administrado. estos contenedores son necesarios porque no hay ninguna manera de registrar las clases con el material gráfico en tiempo de ejecución.
(En concreto, el [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) función JNI no es compatible con el tiempo de ejecución de Android. Contenedores invocables Android, por tanto, componen la falta de compatibilidad de registro de tipo en tiempo de ejecución.)

Siempre que sea necesario ejecutar una virtual o método que se reemplaza o implementa en código administrado de la interfaz código Android, Xamarin.Android debe proporcionar a un proxy de Java para que este método se distribuye al tipo administrado adecuado. Estos tipos de proxy Java son código de Java que tienen el "mismo" clase base y la lista de interfaces de Java como el tipo administrado, la implementación de los constructores mismos y declarar cualquier clase base invalidado y los métodos de interfaz.

Android contenedores RCW generados por el **monodroid.exe** de programa durante la [proceso de compilación](~/android/deploy-test/building-apps/build-process.md)y se generan para todos los tipos que heredan (directa o indirectamente) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).



### <a name="implementing-interfaces"></a>Implementar interfaces

Hay veces cuando es posible que necesite para implementar una interfaz de Android, (como [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)).

Todas las clases de Android e interfaces extienden la [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interfaz; por lo tanto, deben implementar todos los tipos de Android `IJavaObject`.
Xamarin.Android aprovecha este hecho &ndash; usa `IJavaObject` para proporcionar Android con un proxy de Java (un contenedor CCW Android) para el tipo administrado especificado. Dado que **monodroid.exe** solo busca `Java.Lang.Object` subclases (que debe implementar `IJavaObject`), el uso de subclases `Java.Lang.Object` nos ofrece una manera de implementar interfaces en código administrado. Por ejemplo:

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {
    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig) {
        // implementation goes here...
    }
    public void OnLowMemory () {
        // implementation goes here...
    }
}
```


### <a name="implementation-details"></a>Detalles de implementación

*El resto de este artículo proporciona detalles de implementación sujeta a cambios sin previo aviso* (y se presenta aquí solo porque los desarrolladores pueden ser curiosidad sobre lo que está sucediendo en segundo plano).

Por ejemplo, dada la siguiente C# origen:

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

El **mandroid.exe** programa generará el siguiente contenedor invocable Android:

```java
package mono.samples.helloWorld;

public class HelloAndroid extends android.app.Activity {
    static final String __md_methods;
    static {
        __md_methods =
            "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" +
            "";
        mono.android.Runtime.register (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                HelloAndroid.class,
                __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                "", this, new java.lang.Object[] { });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Tenga en cuenta que la clase base se conserva y se proporcionan declaraciones de método nativo para cada método que se invalide en código administrado.



### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute y ExportFieldAttribute

Por lo general, Xamarin.Android genera automáticamente el código de Java que comprende el ACW; Esta generación se basa en los nombres de clase y método cuando una clase se deriva de una clase de Java e invalida los métodos de Java existentes. Sin embargo, en algunos escenarios, la generación de código no es adecuada, tal como se describe a continuación:

-   Android admite los nombres de acción en los atributos XML de diseño, por ejemplo el [android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) atributo XML. Cuando se especifica, la instancia de la vista aumentada intenta buscar el método de Java.

-   El [java.io.Serializable](https://developer.android.com/reference/java/io/Serializable.html) interfaz requiere `readObject` y `writeObject` métodos. Puesto que no son miembros de esta interfaz, nuestra implementación administrada correspondiente no expone estos métodos al código de Java.

-   El [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/) interfaz espera que una clase de implementación debe tener un campo estático `CREATOR` de tipo `Parcelable.Creator`. El código generado de Java requiere algún campo explícito. Con este escenario estándar, no hay ninguna manera al campo de salida en el código de Java desde el código administrado.


Dado que la generación de código no proporciona una solución para generar métodos de Java arbitrarios con nombres arbitrarios, a partir de Xamarin.Android 4.2, el [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/) y [ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/) eran se introdujo para ofrecer una solución para los escenarios anteriores. Ambos atributos residen en el `Java.Interop` espacio de nombres:

-   `ExportAttribute` &ndash; Especifica un nombre de método y sus tipos de excepción esperado (para dar explícito "lanza" en Java). Cuando se utiliza en un método, el método "exportará" un método de Java que genera un código de envío a la invocación de JNI correspondiente al método administrado. Esto se puede usar con `android:onClick` y `java.io.Serializable`.

-   `ExportFieldAttribute` &ndash; Especifica un nombre de campo. Reside en un método que funciona como un inicializador de campo. Esto se puede usar con `android.os.Parcelable`.

El [ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/) proyecto de ejemplo muestra cómo utilizar estos atributos.


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Solución de problemas ExportAttribute y ExportFieldAttribute

-   Empaquetado se produce un error debido a que faltan **Mono.Android.Export.dll** &ndash; si usó `ExportAttribute` o `ExportFieldAttribute` en algunos métodos en el código o bibliotecas dependientes, tiene que agregar  **Mono.Android.Export.dll**. Este ensamblado está aislado en el código de devolución de llamada de Java de soporte técnico. Es independiente de **Mono.Android.dll** mientras agrega tamaño adicional a la aplicación.

-   En la versión de lanzamiento, `MissingMethodException` se produce para los métodos de exportación &ndash; en la versión de lanzamiento, `MissingMethodException` se produce para los métodos de exportación. (Este problema está corregido en la versión más reciente de Xamarin.Android).



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` y `ExportFieldAttribute` proporcionan una funcionalidad que puede usar el código de tiempo de ejecución de Java. Este código de tiempo de ejecución tiene acceso a código administrado a través de los métodos generados de JNI controlados por esos atributos. Como resultado, no hay ningún método de Java existente que se enlaza el método administrado; por lo tanto, el método de Java se genera a partir de una firma de método administrado.

Sin embargo, en este caso no es completamente determinante. En concreto, esto es cierto en algunas asignaciones avanzadas entre los tipos administrados y tipos de Java, como:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

Cuando los tipos como los siguientes son necesarios para los métodos exportados, los `ExportParameterAttribute` debe usarse para proporcionar el parámetro correspondiente de forma explícita o un tipo de valor devuelto.



### <a name="annotation-attribute"></a>Atributo de anotación

En Xamarin.Android 4.2, convertimos `IAnnotation` tipos de implementación en atributos (System.Attribute) y se ha agregado compatibilidad para la generación de anotación en contenedores de Java.

Esto significa que los siguientes cambios direccionales:

-   El generador de enlace genera `Java.Lang.DeprecatedAttribute` desde `java.Lang.Deprecated` (aunque debería ser `[Obsolete]` en código administrado).

-   Esto no significa existentes `Java.Lang.Deprecated` clase desaparecerá. Estos objetos basados en Java podrían utilizarse todavía como objetos usuales de Java (si existe dicho uso). Habrá `Deprecated` y `DeprecatedAttribute` clases.

-   El `Java.Lang.DeprecatedAttribute` clase está marcada como `[Annotation]` . Cuando hay un atributo personalizado que se hereda de esto `[Annotation]` atributo, tarea msbuild generará una anotación de Java para ese atributo personalizado (@Deprecated) en el contenedor RCW Android (ACW).

-   Las anotaciones se puede generar en clases, métodos y exportan los campos (que es un método en código administrado).

Si la clase contenedora (la propia clase anotada, o la clase que contiene a los miembros anotados) no está registrada, todo el origen de clase Java no se genera en absoluto, incluidas las anotaciones. Para los métodos, puede especificar el `ExportAttribute` para obtener el método generado y anotar explícitamente. Además, no es una característica para una definición de clase de anotación de Java "generar". En otras palabras, si define un atributo personalizado administrado para una anotación de determinados, tendrá que agregar otra biblioteca .jar que contiene la clase de anotación correspondiente de Java. Agregar un archivo de código fuente Java que define el tipo de anotación no es suficiente. El compilador de Java no funciona en la misma manera que **apt**.

Además se aplican las siguientes limitaciones:

-   No tiene en cuenta este proceso de conversión `@Target` anotación hasta el momento en el tipo de anotación.

-   Los atributos en una propiedad no funciona. Usar atributos para el captador o establecedor en su lugar.



## <a name="class-binding"></a>Enlace de la clase

Una clase de enlace significa escribir un contenedor administrado que se puede llamar para simplificar la invocación del tipo subyacente de Java.

Enlazar métodos abstractos y virtuales para permitir el reemplazo de C# requiere Xamarin.Android 4.0. Sin embargo, cualquier versión de Xamarin.Android puede enlazar métodos no virtuales, los métodos estáticos o métodos virtuales sin permitir invalidaciones.

Normalmente, un enlace contiene los siguientes elementos:

-  Un [JNI controlar para el tipo de Java que se está enlazando](#_Looking_up_Java_Types).

-  [Campo de JNI identificadores y las propiedades de cada campo enlazado](#_Instance_Fields).

-  [Identificadores del método JNI y métodos para cada enlazan método](#_Instance_Methods).

-  Si es necesario subclasificando, el tipo debe tener un [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado en la declaración de tipos con [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) establecido en `true`.



### <a name="declaring-type-handle"></a>Identificador del tipo declarativo

Los métodos de búsqueda de campo y método requieren una referencia de objeto que hace referencia a su tipo declarativo. Por convención, se mantiene en un `class_ref` campo:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Consulte la [las referencias de tipos de JNI](#_JNI_Type_References) sección para obtener más información sobre la `CLASS` token.


### <a name="binding-fields"></a>Campos de enlace

Campos de Java se exponen como C# propiedades, por ejemplo el campo Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) se enlaza como el C# propiedad [Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/).
Además, puesto que JNI distingue entre los campos estáticos y los campos de instancia, distintos métodos usarse al implementar las propiedades.

El enlace de campo implica tres conjuntos de métodos:

1.  El *obtener Id. de campo* método. El *obtener Id. de campo* método es responsable de devolver un campo que controlen el *obtener valor de campo* y *establecer valor de campo* métodos van a usar. Obtener el identificador de campo, es necesario saber la declaración de tipos, el nombre del campo y el [signatura de tipo JNI](#JNI_Type_Signatures) del campo.

1.  El *obtener valor de campo* métodos. Estos métodos requieren el identificador de campo y son responsables de leer el valor del campo de Java.
    El método debe usar depende del tipo del campo.

1.  El *establecer valor de campo* métodos. Estos métodos requieren el identificador de campo y son responsables de escribir el valor del campo dentro de Java. El método debe usar depende del tipo del campo.


 [Los campos estáticos](#_Static_Fields) utilizar el [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), `JNIEnv.GetStatic*Field`, y [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/) métodos.

 [Campos de instancia](#_Instance_Fields) utilizar el [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), `JNIEnv.Get*Field`, y [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/) métodos.

Por ejemplo, la propiedad estática `JavaSystem.In` puede implementarse como:

```csharp
static IntPtr in_jfieldID;
public static System.IO.Stream In
{
    get {
        if (in_jfieldId == IntPtr.Zero)
            in_jfieldId = JNIEnv.GetStaticFieldID (class_ref, "in", "Ljava/io/InputStream;");
        IntPtr __ret = JNIEnv.GetStaticObjectField (class_ref, in_jfieldId);
        return InputStreamInvoker.FromJniHandle (__ret, JniHandleOwnership.TransferLocalRef);
    }
}
```

Nota: Estamos usando [InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para convertir la referencia JNI en un `System.IO.Stream` instancia, por lo que estamos usando `JniHandleOwnership.TransferLocalRef` porque [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) devuelve un referencia local.

Muchos de los [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) tipos tienen `FromJniHandle` hacen referencia a los métodos que se convertirán un JNI en el tipo deseado.



### <a name="method-binding"></a>Enlace de método

Métodos de Java se exponen como C# métodos y como C# propiedades. Por ejemplo, el método Java [java.lang.Runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) método se enlaza como el [Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/) método y el [java.lang.Object.getClass ](https://developer.android.com/reference/java/lang/Object.html#getClass) método se enlaza como el [Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/) propiedad.

Invocación del método es un proceso de dos pasos:

1.  El *obtener el identificador de método* para el método que se invoca. El *obtener el identificador de método* método es responsable de devolver un identificador de método que va a usar los métodos de invocación de método. Obtener el identificador de método, es necesario saber la declaración de tipos, el nombre del método y el [signatura de tipo JNI](#JNI_Type_Signatures) del método.

1.  Invoque al método.

Al igual que con los campos, los métodos para obtener el identificador de método e invocar el método difieren entre los métodos estáticos y métodos de instancia.

[Los métodos estáticos](#_Static_Methods_1) usar [JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/) para buscar el identificador de método y usar el `JNIEnv.CallStatic*Method` familia de métodos de invocación.

[Métodos de instancia](#_Instance_Methods) usar [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) para buscar el identificador de método y usar el `JNIEnv.Call*Method` y `JNIEnv.CallNonvirtual*Method` familias de métodos de invocación.

Enlace de método es potencialmente algo más que la invocación de método. Enlace de método también incluye lo que permite un método que se invalide (para los métodos abstractos y no final) o implementada (para los métodos de interfaz). El [que admiten la herencia, Interfaces](#_Supporting_Inheritance,_Interfaces_1) sección tratan las complejidades de admitir los métodos virtuales y los métodos de interfaz.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Métodos estáticos

Un método estático de enlace implica el uso de `JNIEnv.GetStaticMethodID` para obtener un identificador de método, a continuación, mediante el correspondiente `JNIEnv.CallStatic*Method` método, según el tipo de valor devuelto del método. El siguiente es un ejemplo de un enlace para el [Runtime.getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) método:

```csharp
static IntPtr id_getRuntime;

[Register ("getRuntime", "()Ljava/lang/Runtime;", "")]
public static Java.Lang.Runtime GetRuntime ()
{
    if (id_getRuntime == IntPtr.Zero)
        id_getRuntime = JNIEnv.GetStaticMethodID (class_ref,
                "getRuntime", "()Ljava/lang/Runtime;");

    return Java.Lang.Object.GetObject<Java.Lang.Runtime> (
            JNIEnv.CallStaticObjectMethod  (class_ref, id_getRuntime),
            JniHandleOwnership.TransferLocalRef);
}
```

Tenga en cuenta que se almacena el identificador de método en un campo estático, `id_getRuntime`. Se trata de una optimización del rendimiento, por lo que no necesita el identificador de método va a buscar en cada invocación. No es necesario para almacenar en caché el identificador de método de esta manera. Una vez que se obtiene el identificador de método, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) se utiliza para invocar el método. `JNIEnv.CallStaticObjectMethod` Devuelve un `IntPtr` que contiene el identificador de la instancia devuelta de Java.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) se utiliza para convertir el identificador de Java en una instancia de objeto fuertemente tipado.



#### <a name="non-virtual-instance-method-binding"></a>Enlace de método de instancia no virtual

Enlace un `final` método de instancia o un método de instancia que no requiere invalidar, implica el uso de `JNIEnv.GetMethodID` para obtener un identificador de método, a continuación, mediante el correspondiente `JNIEnv.Call*Method` método, según el tipo de valor devuelto del método. El siguiente es un ejemplo de un enlace para el `Object.Class` propiedad:

```csharp
static IntPtr id_getClass;
public Java.Lang.Class Class {
    get {
        if (id_getClass == IntPtr.Zero)
            id_getClass = JNIEnv.GetMethodID (class_ref, "getClass", "()Ljava/lang/Class;");
        return Java.Lang.Object.GetObject<Java.Lang.Class> (
                JNIEnv.CallObjectMethod (Handle, id_getClass),
                JniHandleOwnership.TransferLocalRef);
    }
}
```

Tenga en cuenta que se almacena el identificador de método en un campo estático, `id_getClass`.
Se trata de una optimización del rendimiento, por lo que no necesita el identificador de método va a buscar en cada invocación. No es necesario para almacenar en caché el identificador de método de esta manera. Una vez que se obtiene el identificador de método, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) se utiliza para invocar el método. `JNIEnv.CallStaticObjectMethod` Devuelve un `IntPtr` que contiene el identificador de la instancia devuelta de Java.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) se utiliza para convertir el identificador de Java en una instancia de objeto fuertemente tipado.


### <a name="binding-constructors"></a>Constructores de enlace

Los constructores son métodos de Java con el nombre `"<init>"`. Al igual que con los métodos de instancia de Java, `JNIEnv.GetMethodID` se utiliza para buscar el identificador del constructor. A diferencia de los métodos de Java, el [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/) métodos se usan para invocar el identificador de método de constructor. El valor devuelto de `JNIEnv.NewObject` es una referencia local de JNI:


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalmente un enlace de la clase creará una subclase [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).
Cuando se crean subclases `Java.Lang.Object`, un adicionales semántica entra en juego: un `Java.Lang.Object` instancia mantiene una referencia global a una instancia de Java a través de la `Java.Lang.Object.Handle` propiedad.

1.  El `Java.Lang.Object` constructor predeterminado asigna una instancia de Java.

1.  Si el tipo tiene un `RegisterAttribute` , y `RegisterAttribute.DoNotGenerateAcw` es `true` , a continuación, una instancia de la `RegisterAttribute.Name` tipo se crea a través de su constructor predeterminado.

1.  En caso contrario, el [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) correspondiente a `this.GetType` se crea una instancia a través de su constructor predeterminado. Contenedores invocables Android se generan durante la creación del paquete para cada `Java.Lang.Object` subclase para el que `RegisterAttribute.DoNotGenerateAcw` no está establecido en `true`.

Para tipos que son de clase no enlaces, esto es el esperado semántico: crear instancias de un `Mono.Samples.HelloWorld.HelloAndroid` C# instancia debe construir un Java `mono.samples.helloworld.HelloAndroid` instancia que es un contenedor CCW Android generado.

Para los enlaces de clase, esto puede ser el comportamiento correcto si el tipo de Java contiene un constructor predeterminado y ningún otro constructor debe invocarse. En caso contrario, se debe proporcionar un constructor que realiza las acciones siguientes:

1.  Invocar el [Java.Lang.Object (IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) en lugar del predeterminado `Java.Lang.Object` constructor. Esto es necesario para evitar la creación de una nueva instancia de Java.

1.  Compruebe el valor de [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) antes de crear las instancias de Java. El `Object.Handle` propiedad tendrá un valor distinto de `IntPtr.Zero` si se construyó un contenedor CCW Android en el código de Java y se construye el enlace de la clase para que contenga la instancia de contenedor CCW Android creada. Por ejemplo, cuando Android crea un `mono.samples.helloworld.HelloAndroid` instancia, el contenedor RCW Android se crearán primero y Java `HelloAndroid` constructor creará una instancia de la correspondiente `Mono.Samples.HelloWorld.HelloAndroid` tipo, con el `Object.Handle` propiedad se establece en la instancia de Java antes de la ejecución del constructor.

1.  Si el tipo de tiempo de ejecución actual no es igual que la declaración de tipo y, a continuación, una instancia del contenedor invocable Android correspondiente se debe crear y usar [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para almacenar el identificador devuelto por [ JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/).

1.  Si el tipo de tiempo de ejecución actual es igual que el tipo declarativo, a continuación, invocar el constructor de Java y use [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para almacenar el identificador devuelto por `JNIEnv.NewInstance` .


Por ejemplo, considere la [java.lang.Integer(int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) constructor. Esto se enlaza como:

```csharp
// Cache the constructor's method handle for later use
static IntPtr id_ctor_I;

// Need [Register] for subclassing
// RegisterAttribute.Name is always ".ctor"
// RegisterAttribute.Signature is tye JNI type signature of constructor
// RegisterAttribute.Connector is ignored; use ""
[Register (".ctor", "(I)V", "")]
public Integer (int value)
    // 1. Prevent Object default constructor execution
    : base (IntPtr.Zero, JniHandleOwnership.DoNotTransfer)
{
    // 2. Don't allocate Java instance if already allocated
    if (Handle != IntPtr.Zero)
        return;

    // 3. Derived type? Create Android Callable Wrapper
    if (GetType () != typeof (Integer)) {
        SetHandle (
                Android.Runtime.JNIEnv.CreateInstance (GetType (), "(I)V", new JValue (value)),
                JniHandleOwnership.TransferLocalRef);
        return;
    }

    // 4. Declaring type: lookup &amp; cache method id...
    if (id_ctor_I == IntPtr.Zero)
        id_ctor_I = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
    // ...then create the Java instance and store
    SetHandle (
            JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value)),
            JniHandleOwnership.TransferLocalRef);
}
```

El [JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/) métodos son aplicaciones auxiliares para realizar un `JNIEnv.FindClass`, `JNIEnv.GetMethodID`, `JNIEnv.NewObject`, y `JNIEnv.DeleteGlobalReference` en el valor devuelto de `JNIEnv.FindClass`. Para obtener más información, vea la siguiente sección.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Compatibilidad con herencia, Interfaces

Creación de subclases de un tipo de Java o implementar una interfaz de Java requiere la generación de [Android contenedores RCW](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) que se generan para cada `Java.Lang.Object` subclase durante el proceso de empaquetado. Generación de ACW se controla a través de la [Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado.

Para C# tipos, el `[Register]` constructor de atributos personalizados requiere un argumento: el [JNI simplificado de referencia de tipo](#_Simplified_Type_References_1) para Java correspondiente escriba. Esto permite proporcionar nombres diferentes entre Java y C#.

Anteriores a 4.0 de Xamarin.Android, la `[Register]` atributo personalizado no estaba disponible para tipos de Java existentes "alias". Esto es porque el proceso de generación ACW generaría ACWs para cada `Java.Lang.Object` subclase encontrado.

Xamarin.Android 4.0 introducidas el [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) propiedad. Esta propiedad indica el proceso de generación ACW *omitir* el tipo anotado, lo que permite la declaración de nuevos contenedores RCW administrados que no causarán ACWs generados en el momento de creación del paquete. Esto permite que los tipos de Java de enlace existentes. Por ejemplo, considere la siguiente clase Java simple, `Adder`, que contiene un método `add`, que agrega a enteros y devuelve el resultado:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

El `Adder` se pudo enlazar el tipo como:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public partial class Adder : Java.Lang.Object {
    static IntPtr class_ref = JNIEnv.FindClass ( "mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }
}
partial class ManagedAdder : Adder {
}
```

En este caso, el `Adder` C# tipo *alias* el `Adder` tipo Java. El `[Register]` atributo se utiliza para especificar el nombre JNI de la `mono.android.test.Adder` tipo Java y el `DoNotGenerateAcw` propiedad se utiliza para impedir la generación de ACW. Esto dará como resultado la generación de un ACW para el `ManagedAdder` escribe, lo que correctamente las subclases el `mono.android.test.Adder` tipo. Si el `RegisterAttribute.DoNotGenerateAcw` no había se ha usado la propiedad y, después, el proceso de compilación de Xamarin.Android habría generado un nuevo `mono.android.test.Adder` tipo Java. Esto daría lugar a errores de compilación, como la `mono.android.test.Adder` tipo sería presente dos veces, en dos archivos independientes.



### <a name="binding-virtual-methods"></a>Enlazar métodos virtuales

`ManagedAdder` las subclases de Java `Adder` tipo, pero no es especialmente interesante: la C# `Adder` tipo no define ningún método virtual, por lo que `ManagedAdder` no se puede invalidar cualquier cosa.

Enlace `virtual` métodos para permitir el reemplazo por las subclases requiere varias cosas que deben realizarse en la que se dividen en las dos categorías siguientes:

1.  **Enlace de método**

1.  **Registro del método**


#### <a name="method-binding"></a>Enlace de método

El enlace de un método requiere la adición de dos miembros de soporte técnico para el C# `Adder` definición: `ThresholdType`, y `ThresholdClass`.

##### <a name="thresholdtype"></a>ThresholdType

El `ThresholdType` propiedad devuelve el tipo del enlace actual:

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` se utiliza en el método de enlace para determinar cuándo se debe realizar virtual frente a la distribución de métodos no virtuales. Siempre debe devolver un `System.Type` instancia que se corresponde con el tipo declarativo C# tipo.

##### <a name="thresholdclass"></a>ThresholdClass

El `ThresholdClass` propiedad devuelve la referencia de clase JNI para el tipo enlazado:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` se usa en el método de enlace al invocar los métodos no virtuales.

#### <a name="binding-implementation"></a>Implementación del enlace

La implementación del enlace de método es responsable de invocación en tiempo de ejecución del método de Java. También contiene un `[Register]` declaración de atributo personalizado que es parte del registro del método y se describe en la sección de registro del método:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }
}
```

El `id_add` campo contiene el identificador de método para el método de Java debe invocar. El `id_add` se obtiene el valor de `JNIEnv.GetMethodID`, lo que requiere la clase de declaración (`class_ref`), el nombre del método de Java (`"add"`) y la firma JNI del método (`"(II)I"`).

Una vez que se obtiene el identificador de método, `GetType` se compara con `ThresholdType` para determinar si es necesario que no sean de máquina virtual o de envío. Distribución virtual es obligatorio cuando `GetType` coincide con `ThresholdType`, como `Handle` pueden hacer referencia a una subclase asignado de Java que invalida el método.

Cuando `GetType` no coincide con `ThresholdType`, `Adder` derivado (por ejemplo, `ManagedAdder`) y el `Adder.Add` implementación solo se invocará si invoca la subclase `base.Add`. Este es el caso de distribución que no es virtual, que es donde `ThresholdClass` entra en juego. `ThresholdClass` Especifica qué clase de Java proporcionará la implementación del método que se va a invocar.



#### <a name="method-registration"></a>Registro del método

Supongamos que tenemos un controlador actualizado `ManagedAdder` definición que reemplaza el `Adder.Add` método:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Recuerde que `Adder.Add` tenía un `[Register]` atributo personalizado:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

El `[Register]` constructor de atributo personalizado acepta tres valores:

1.  El nombre del método de Java, `"add"` en este caso.

1.  La firma del tipo de JNI del método, `"(II)I"` en este caso.

1.  El *método conector* , `GetAddHandler` en este caso.
    Métodos de conector se tratará más adelante.


Los dos primeros parámetros permiten que el proceso de generación ACW generar una declaración de método para invalidar el método. El ACW resultante contendría parte del código siguiente:

```csharp
public class ManagedAdder extends mono.android.test.Adder {
    static final String __md_methods;
    static {
        __md_methods = "n_add:(II)I:GetAddHandler\n" +
            "";
        mono.android.Runtime.register (...);
    }
    @Override
    public int add (int p0, int p1) {
        return n_add (p0, p1);
    }
    private native int n_add (int p0, int p1);
    // ...
}
```

Tenga en cuenta que un `@Override` se declara el método, que delega en un `n_`-como prefijo el método del mismo nombre. Garantizar que, cuando se invoca el código Java `ManagedAdder.add`, `ManagedAdder.n_add` se invocará, lo que permitirá la invalidación C# `ManagedAdder.Add` método que se ejecutará.

Por lo tanto, la cuestión más importante: ¿cómo es `ManagedAdder.n_add` enlazada a `ManagedAdder.Add`?

Java `native` métodos están registrados con el tiempo de ejecución de Java (el tiempo de ejecución de Android) a través de la [RegisterNatives JNI función](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` toma una matriz de estructuras que contienen el nombre del método de Java, la firma del tipo de JNI y un puntero de función para invocar que sigue [JNI convención de llamada](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
El puntero de función debe ser una función que toma dos argumentos de puntero, seguidos de los parámetros del método. Java `ManagedAdder.n_add` método debe implementarse a través de una función que tiene el siguiente prototipo C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android no expone un `RegisterNatives` método. En su lugar, el ACW y el MCW proporcionan la información necesaria para invocar `RegisterNatives`: el ACW contiene el nombre del método y la firma del tipo JNI, lo único que falta es un puntero de función a enlazar.

Aquí es donde el *método conector* entra en juego. La tercera `[Register]` parámetro del atributo personalizado es el nombre de un método definido en el tipo registrado o una clase base del tipo registrado que no acepta parámetros y devuelve un `System.Delegate`. El valor devuelto `System.Delegate` a su vez hace referencia a un método que tiene la firma de función JNI correcta. Por último, el delegado que devuelve el método conector *debe* se ha modificado para que el GC no recopila, tal como se proporciona el delegado a Java.

```csharp
#pragma warning disable 0169
static Delegate cb_add;
// This method must match the third parameter of the [Register]
// custom attribute, must be static, must return System.Delegate,
// and must accept no parameters.
static Delegate GetAddHandler ()
{
    if (cb_add == null)
        cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
    return cb_add;
}
// This method is registered with JNI.
static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
{
    Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
    return __this.Add (a, b);
}
#pragma warning restore 0169
```

El `GetAddHandler` método crea un `Func<IntPtr, IntPtr, int, int,
int>` delegado que hace referencia a la `n_Add` , a continuación, invoca el método, [JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/).
`JNINativeWrapper.CreateDelegate` ajusta el método proporcionado en un bloque try/catch, para que las excepciones no controladas se controlan y dará como resultado de elevar el [AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/) eventos. El delegado resultante se almacena en estático `cb_add` variable para que el GC no liberará el delegado.

Por último, el `n_Add` método es responsable de serializar los parámetros JNI a los tipos administrados correspondientes, entonces el método de delegación de llamar al método.

Nota: Utilice siempre `JniHandleOwnership.DoNotTransfer` al obtener un MCW a través de una instancia de Java. Tratarlas como una referencia local (y, por tanto, una llamada a `JNIEnv.DeleteLocalRef`) interrumpirá managed -&gt; Java -&gt; administra las transiciones de pila.



### <a name="complete-adder-binding"></a>Complete el enlace Adder

Completo administrado enlace para el `mono.android.tests.Adder` es de tipo:

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public class Adder : Java.Lang.Object {

    static IntPtr class_ref = JNIEnv.FindClass ("mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    protected override Type ThresholdType {
        get {return typeof (Adder);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

#region Add
    static IntPtr id_add;

    [Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }

#pragma warning disable 0169
    static Delegate cb_add;
    static Delegate GetAddHandler ()
    {
        if (cb_add == null)
            cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
        return cb_add;
    }

    static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
    {
        Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
        return __this.Add (a, b);
    }
#pragma warning restore 0169
#endregion
}
```



### <a name="restrictions"></a>Restricciones

Al escribir un tipo que coincide con los criterios siguientes:

1.  Subclases `Java.Lang.Object`

1.  Tiene un `[Register]` atributo personalizado

1.  `RegisterAttribute.DoNotGenerateAcw` es `true`


A continuación, para el tipo de interacción de GC *no debe* tiene todos los campos que pueden hacer referencia a un `Java.Lang.Object` o `Java.Lang.Object` subclase en tiempo de ejecución. Por ejemplo, los campos de tipo `System.Object` y cualquier tipo de interfaz no se permiten. Tipos que no se pueden hacer referencia a `Java.Lang.Object` instancias están permitidas, como `System.String` y `List<int>`. Esta restricción es evitar que la colección de objetos de forma prematura por el GC.

Si el tipo debe contener un campo de instancia que puede hacer referencia a un `Java.Lang.Object` de instancia, a continuación, el tipo de campo debe ser `System.WeakReference` o `GCHandle`.



## <a name="binding-abstract-methods"></a>Enlazar métodos abstractos

Enlace `abstract` métodos es idéntica en gran medida a los métodos virtuales de enlace. Hay solo dos diferencias:

1.  El método abstracto es abstracto. Todavía mantiene el `[Register]` atributo y el registro del método asociado, el método de enlace solo se mueve a la `Invoker` tipo.

1.  No `abstract` `Invoker` se crea el tipo cuyas subclases del tipo abstracto. El `Invoker` tipo debe reemplazar abstracta de todos los métodos declarados en la clase base y la implementación invalidada es la implementación del método de enlace, aunque se puede omitir el caso de distribución que no sea virtual.


Por ejemplo, supongamos que el anterior `mono.android.test.Adder.add` método fuera `abstract`. El C# cambiaría el enlace para que `Adder.Add` eran abstracto y un nuevo `AdderInvoker` debe definir un tipo que implementa `Adder.Add`:

```csharp
partial class Adder {
    [Register ("add", "(II)I", "GetAddHandler")]
    public abstract int Add (int a, int b);

    // The Method Registration machinery is identical to the
    // virtual method case...
}

partial class AdderInvoker : Adder {
    public AdderInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    static IntPtr id_add;
    public override int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
    }
}
```

El `Invoker` tipo sólo es necesario obtener referencias JNI a instancias creadas en Java.


## <a name="binding-interfaces"></a>Interfaces de enlace

Interfaces de enlace es conceptualmente similar al enlace de las clases que contienen métodos virtuales, pero muchos de los aspectos específicos presentan algunas diferencias sutiles (y no tan sutiles). Tenga en cuenta la siguiente [la declaración de la interfaz Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Los enlaces de interfaz tienen dos partes: el C# definición de interfaz y una definición del invocador de la interfaz.



### <a name="interface-definition"></a>Definición de interfaz

El C# definición de interfaz debe cumplir los siguientes requisitos:

-   La definición de interfaz debe tener un `[Register]` atributo personalizado.

-   La definición de interfaz debe extender el `IJavaObject interface`.
    Si no lo hace evitará ACWs hereden de la interfaz de Java.

-   Cada método de interfaz debe contener un `[Register]` atributo que especifica el nombre del método Java correspondiente, la firma JNI y el método de conector.

-   El método de conector también debe especificar el tipo que se puede encontrar en el método de conector.

Al enlazar `abstract` y `virtual` métodos, el método de conector se deben buscar dentro de la jerarquía de herencia del tipo que se está registrando. Las interfaces no pueden tener ningún método que contiene los cuerpos de, por lo que esto no funciona, por lo tanto el requisito de que se especifica un tipo que indica dónde se encuentra el método de conector. El tipo se especifica dentro de la cadena de método del conector, después de dos puntos `':'`, y debe ser el nombre de tipo calificado de ensamblado del tipo que contiene el invocador.

Las declaraciones de método de interfaz son una traducción del método Java correspondiente mediante *compatible* tipos. Tipos de Java builtin, los tipos compatibles son las correspondientes C# tipos, por ejemplo, Java `int` es C# `int`. Tipos de referencia, el tipo compatible es un tipo que puede proporcionar un identificador JNI del tipo de Java adecuado.

Los miembros de interfaz no se invocará directamente mediante Java &ndash; invocación se pueda controlarse a través del tipo invocador &ndash; para que se permita cierta cantidad de flexibilidad.

La interfaz de progreso de Java puede ser [declarado en C# como](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Observe que en los pasos anteriores se asigna el Java `int[]` parámetro a un [JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/).
Esto no es necesario:, podríamos enlazamos a una C# `int[]`, o un `IList<int>`, o alguna otra cosa completamente. Se elige el tipo, el `Invoker` debe poder convertirla en un Java `int[]` tipo de invocación.


### <a name="invoker-definition"></a>Invocador de la definición

El `Invoker` definición de tipo debe heredar `Java.Lang.Object`, implemente la interfaz apropiada y proporcionar todos los métodos de conexión hace referencia en la definición de interfaz. Hay varias sugerencias que difiere de un enlace de la clase: el `class_ref` identificadores de campo y método deben ser miembros de instancia, los miembros no estáticos.

La razón para preferir a los miembros de instancia tiene que ver con `JNIEnv.GetMethodID` comportamiento en tiempo de ejecución de Android. (Esto puede ser también el comportamiento de Java; no se ha probado). `JNIEnv.GetMethodID` devuelve null cuando se buscan un método que procede de una interfaz implementada y no la interfaz declarada. Tenga en cuenta la [java.util.SortedMap&lt;K, V&gt; ](https://developer.android.com/reference/java/util/SortedMap.html) interfaz de Java, que implementa el [java.util.Map&lt;K, V&gt; ](https://developer.android.com/reference/java/util/Map.html) interfaz. Mapa proporciona un [borrar](https://developer.android.com/reference/java/util/Map.html#clear()) método, por lo tanto, un aparentemente razonable `Invoker` definición para SortedMap sería:

```csharp
// Fails at runtime. DO NOT FOLLOW
partial class ISortedMapInvoker : Java.Lang.Object, ISortedMap {
    static IntPtr class_ref = JNIEnv.FindClass ("java/util/SortedMap");
    static IntPtr id_clear;
    public void Clear()
    {
        if (id_clear == IntPtr.Zero)
            id_clear = JNIEnv.GetMethodID(class_ref, "clear", "()V");
        JNIEnv.CallVoidMethod(Handle, id_clear);
    }
     // ...
}
```

Lo anterior se producirá un error porque `JNIEnv.GetMethodID` devolverá `null` al buscar el `Map.clear` método a través de la `SortedMap` instancia de clase.

Hay dos soluciones: realizar un seguimiento de la interfaz proceden de todos los métodos y tienen un `class_ref` para cada interfaz, o mantener todo como miembros de instancia y realizar la búsqueda de método en el tipo de clase más derivada, no el tipo de interfaz. Se realiza en **Mono.Android.dll**.

La definición del invocador tiene seis secciones: el constructor, el `Dispose` método, el `ThresholdType` y `ThresholdClass` miembros, la `GetObject` método de implementación del método de interfaz y la implementación del método conector.



#### <a name="constructor"></a>Constructor

El constructor debe buscar la clase en tiempo de ejecución de la instancia que se invoca y almacenar la clase en tiempo de ejecución en la instancia `class_ref` campo:

```csharp
partial class IAdderProgressInvoker {
    IntPtr class_ref;
    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref   = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }
}
```

Nota: El `Handle` propiedad debe utilizarse dentro del cuerpo del constructor y no el `handle` parámetro, como sucede en Android v4.0 el `handle` parámetro puede ser no válido después de que el constructor base termina de ejecutarse.


#### <a name="dispose-method"></a>Dispose (Método)

El `Dispose` método necesita liberar la referencia global asignada en el constructor:

```csharp
partial class IAdderProgressInvoker {
    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }
}
```


#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType y ThresholdClass

El `ThresholdType` y `ThresholdClass` miembros son idénticos a lo que se encuentra en un enlace de la clase:

```csharp
partial class IAdderProgressInvoker {
    protected override Type ThresholdType {
        get {
            return typeof (IAdderProgressInvoker);
        }
    }
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```


#### <a name="getobject-method"></a>GetObject (Método)

Estático `GetObject` método es necesario para admitir [Extensions.JavaCast&lt;T&gt;()](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```


#### <a name="interface-methods"></a>Métodos de interfaz

Todos los métodos de la interfaz debe tener una implementación, que invoca el método correspondiente de Java a través de JNI:

```csharp
partial class IAdderProgressInvoker {
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd", "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd, new JValue (JNIEnv.ToJniHandle (values)), new JValue (currentIndex), new JValue (currentSum));
    }
}
```



#### <a name="connector-methods"></a>Métodos de conector

Los métodos de conector y la infraestructura de apoyo son responsables de serializar los parámetros adecuados de JNI C# tipos. Java `int[]` parámetro se pasará como un JNI `jintArray`, que es un `IntPtr` en C#. El `IntPtr` debe calcularse las referencias a un `JavaArray<int>` con el fin de admitir la invocación del C# interfaz:

```csharp
partial class IAdderProgressInvoker {
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
}
```

Si `int[]` sería preferible `JavaList<int>`, a continuación, [JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type)) podría usarse en su lugar:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Sin embargo, tenga en cuenta que `JNIEnv.GetArray` copia toda la matriz entre las máquinas virtuales, por lo que para las matrices de gran tamaño Esto podría resultar en una gran cantidad de presión de GC agregada.


### <a name="complete-invoker-definition"></a>Completar la definición del invocador

El [completar IAdderProgressInvoker definición](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

```csharp
class IAdderProgressInvoker : Java.Lang.Object, IAdderProgress {

    IntPtr class_ref;

    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }

    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }

    protected override Type ThresholdType {
        get {return typeof (IAdderProgressInvoker);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }

#region OnAdd
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd",
                    "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd,
                new JValue (JNIEnv.ToJniHandle (values)),
                new JValue (currentIndex),
new JValue (currentSum));
    }

#pragma warning disable 0169
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
#pragma warning restore 0169
#endregion
}
```



## <a name="jni-object-references"></a>Referencias JNI a objetos

Muchos métodos JNIEnv devuelven *JNI* *las referencias a objetos*, que son similares a `GCHandle`s. JNI proporciona tres tipos diferentes de las referencias a objetos: las referencias locales, globales y referencias débiles de globales. Las tres se representan como `System.IntPtr`, *pero* (según la sección tipos de función de JNI) no todos los `IntPtr`devuelve `JNIEnv` métodos son referencias. Por ejemplo, [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) devuelve un `IntPtr`, pero no devuelve una referencia de objeto, devuelve un `jmethodID`. Consulte la [documentación de la función JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) para obtener más información.

Referencias locales se crean mediante *mayoría* métodos de creación de referencia.
Android solo permite a un número limitado de referencias locales que exista en un momento dado, normalmente 512. Se pueden eliminar referencias locales a través de [JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/).
A diferencia de JNI, no todos, hacen referencia a los métodos de JNIEnv qué referencias de objeto de devolución devuelven referencias locales; [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/) devuelve un *global* referencia. Se recomienda encarecidamente que eliminar referencias locales tan pronto como pueda, posiblemente mediante la creación de un [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) alrededor del objeto y la especificación de `JniHandleOwnership.TransferLocalRef` a la [Java.Lang.Object (IntPtr administrar, transferencia de JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) constructor.

Se crean referencias globales por [JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/) y [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/).
Se pueden destruir con [JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/).
Los emuladores tienen un límite de 2.000 referencias globales pendientes, mientras que los dispositivos de hardware tienen un límite de aproximadamente 52.000 referencias globales.

Referencias débiles de globales solo están disponibles en Android v2.2 (Froyo) y versiones posteriores. Se pueden eliminar las referencias débiles globales con [JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr)).


### <a name="dealing-with-jni-local-references"></a>Tratamiento de las referencias JNI Local

El [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/), [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/), [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/), [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)y [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) métodos devuelven un `IntPtr` que contiene una referencia local de JNI a un objeto de Java o `IntPtr.Zero` si Java devuelve `null`. Debido al número limitado de referencias locales que pueden estar pendientes en una vez (512 entradas), resulta conveniente para asegurarse de que las referencias se eliminan de manera oportuna. Hay tres modos en que se pueden tratar referencias locales: eliminar de forma explícita, crear un `Java.Lang.Object` instancia para contener ellas y usar `Java.Lang.Object.GetObject<T>()` para crear un contenedor administrado que se puede llamar a su alrededor.



### <a name="explicitly-deleting-local-references"></a>Eliminar explícitamente referencias locales

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) se usa para eliminar referencias locales. Una vez que se ha eliminado la referencia local, no se puede usar, por lo que debe tener cuidado para asegurarse de que `JNIEnv.DeleteLocalRef` es lo último en hacerse con la referencia local.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```



### <a name="wrapping-with-javalangobject"></a>Ajuste con Java.Lang.Object

`Java.Lang.Object` Proporciona un [Java.Lang.Object (identificador IntPtr, transferencia JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) constructor que se puede utilizar para encapsular una referencia de JNI existente. El [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/) parámetro determina cómo el `IntPtr` se debe tratar el parámetro:

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash; creado `Java.Lang.Object` instancia creará una nueva referencia global desde el `handle` parámetro, y `handle` no se ha modificado.
    El llamador es responsable de liberar `handle` , si es necesario.

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; creado `Java.Lang.Object` instancia creará una nueva referencia global desde el `handle` parámetro, y `handle` se elimina con [JNIEnv.DeleteLocalRef ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . El llamador debe liberar no `handle` y no debe utilizar `handle` después de que el constructor termina de ejecutarse.

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; creado `Java.Lang.Object` instancia se ocupará de la propiedad de la `handle` parámetro. El llamador debe liberar no `handle` .


Puesto que los métodos de invocación de método JNI devuelven referencias locales, `JniHandleOwnership.TransferLocalRef` se usan normalmente:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

La referencia global creada no se liberarán hasta que el `Java.Lang.Object` instancia es recolectado. Si es posible, elimine de la instancia liberará la referencia global, lo que acelera la recolección de elementos:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```



### <a name="using-javalangobjectgetobjectlttgt"></a>Uso de Java.Lang.Object.GetObject&lt;T&gt;)

`Java.Lang.Object` Proporciona un [Java.Lang.Object.GetObject&lt;T&gt;(identificador IntPtr, transferencia JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) método que puede usarse para crear un contenedor administrado del tipo especificado.

El tipo `T` debe cumplir los requisitos siguientes:

1.  `T` debe ser un tipo de referencia.

1.  `T` debe implementar la `IJavaObject` interfaz.

1.  Si `T` no es una clase abstracta o interfaz, a continuación, `T` debe proporcionar un constructor con los tipos de parámetro `(IntPtr,
    JniHandleOwnership)` .

1.  Si `T` es una clase abstracta o una interfaz, *debe* ser un *invocador* disponibles para `T` . Un invocador de es un tipo no abstracto que hereda `T` o implementa `T` , y tiene el mismo nombre que `T` con un sufijo invocador. Por ejemplo, si T es la interfaz `Java.Lang.IRunnable` , a continuación, el tipo `Java.Lang.IRunnableInvoker` debe existir y debe contener el texto necesario `(IntPtr,
    JniHandleOwnership)` constructor.


Puesto que los métodos de invocación de método JNI devuelven referencias locales, `JniHandleOwnership.TransferLocalRef` se usan normalmente:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Buscar tipos de Java

Para buscar un campo o método de JNI, primero debe buscar el tipo declarativo del campo o método. El [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)) método se utiliza para buscar tipos de Java. El parámetro de cadena es el *simplificado de referencia de tipo* o *referencia de tipo completo* para el tipo de Java. Consulte la [sección referencias de tipo de JNI](#_JNI_Type_References) para obtener más información acerca de las referencias de tipo completo y simplificada.

Nota: A diferencia de los demás `JNIEnv` método que devuelve instancias de objeto, `FindClass` devuelve una referencia global, no una referencia local.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campos de instancia

Los campos se manipulan mediante *campos de identificadores*. Id. de campo se obtienen a través de [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), lo que requiere la clase que está definido el campo, el nombre del campo y el [signatura de tipo de JNI](#JNI_Type_Signatures) del campo.

Id. de campo no es necesario para que se libere y son válidos siempre que se carga el tipo correspondiente de Java. (Android no admite actualmente la descarga de clase.)

Hay dos conjuntos de métodos para manipular los campos de instancia: uno para leer campos de instancia y otro para la escritura de los campos de instancia. Todos los conjuntos de métodos requieren un identificador de campo leer o escribir el valor del campo.


### <a name="reading-instance-field-values"></a>Leer valores de campo de instancia

El conjunto de métodos para leer los valores de campo de instancia sigue el patrón de nomenclatura:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
donde `*` es el tipo del campo:

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash; leer el valor de cualquier campo de instancia que no sea un tipo builtin, tales como `java.lang.Object` , matrices y tipos de interfaz. El valor devuelto es una referencia local de JNI.

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash; leer el valor de `bool` campos de instancia.

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash; leer el valor de `sbyte` campos de instancia.

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash; leer el valor de `char` campos de instancia.

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash; leer el valor de `short` campos de instancia.

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash; leer el valor de `int` campos de instancia.

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash; leer el valor de `long` campos de instancia.

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash; leer el valor de `float` campos de instancia.

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash; leer el valor de `double` campos de instancia.





### <a name="writing-instance-field-values"></a>Escribiendo valores de campo de instancia

El conjunto de métodos para escribir los valores de campo de instancia sigue el patrón de nomenclatura:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

donde *tipo* es el tipo del campo:

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; escribir el valor de cualquier campo que no sea un tipo builtin, tales como `java.lang.Object` , matrices y tipos de interfaz. El `IntPtr` valor puede ser una referencia local de JNI, referencia global de JNI, referencia global débil de JNI, o `IntPtr.Zero` (para `null` ).

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; escribir el valor de `bool` campos de instancia.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; escribir el valor de `sbyte` campos de instancia.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; escribir el valor de `char` campos de instancia.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; escribir el valor de `short` campos de instancia.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; escribir el valor de `int` campos de instancia.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; escribir el valor de `long` campos de instancia.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; escribir el valor de `float` campos de instancia.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; escribir el valor de `double` campos de instancia.


<a name="_Static_Fields" />

## <a name="static-fields"></a>Campos estáticos

Los campos estáticos se manipulan mediante *campos de identificadores*. Id. de campo se obtienen a través de [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/), lo que requiere la clase que está definido el campo, el nombre del campo y el [signatura de tipo de JNI](#JNI_Type_Signatures) del campo.

Id. de campo no es necesario para que se libere y son válidos siempre que se carga el tipo correspondiente de Java. (Android no admite actualmente la descarga de clase.)

Hay dos conjuntos de métodos para manipular los campos estáticos: uno para leer campos de instancia y otro para la escritura de los campos de instancia. Todos los conjuntos de métodos requieren un identificador de campo leer o escribir el valor del campo.


### <a name="reading-static-field-values"></a>Leer los valores de campo estático

El conjunto de métodos para leer los valores de campo estático sigue el patrón de nomenclatura:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

donde `*` es el tipo del campo:

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash; leer el valor de todos los campos estáticos que no es un tipo builtin, tales como `java.lang.Object` , matrices y tipos de interfaz. El valor devuelto es una referencia local de JNI.

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash; leer el valor de `bool` campos estáticos.

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash; leer el valor de `sbyte` campos estáticos.

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash; leer el valor de `char` campos estáticos.

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash; leer el valor de `short` campos estáticos.

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash; leer el valor de `long` campos estáticos.

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash; leer el valor de `float` campos estáticos.

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash; leer el valor de `double` campos estáticos.



### <a name="writing-static-field-values"></a>Escribiendo valores de campo estático

El conjunto de métodos para escribir los valores de campo estático sigue el patrón de nomenclatura:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

donde *tipo* es el tipo del campo:

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; escribir el valor de todos los campos estáticos que no es un tipo builtin, tales como `java.lang.Object` , matrices y tipos de interfaz. El `IntPtr` valor puede ser una referencia local de JNI, referencia global de JNI, referencia global débil de JNI, o `IntPtr.Zero` (para `null` ).

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; escribir el valor de `bool` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; escribir el valor de `sbyte` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; escribir el valor de `char` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; escribir el valor de `short` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; escribir el valor de `int` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; escribir el valor de `long` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; escribir el valor de `float` campos estáticos.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; escribir el valor de `double` campos estáticos.


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Métodos de instancia

Métodos de instancia se invocan mediante *método identificadores*. Método identificadores se obtienen a través de [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/), que requiere que el tipo que el método se define en el nombre del método y el [signatura de tipo de JNI](#JNI_Type_Signatures) del método.

Identificadores de método no es necesario para que se libere y son válidos siempre que se carga el tipo correspondiente de Java. (Android no admite actualmente la descarga de clase.)

Hay dos conjuntos de métodos para invocar métodos: uno para invocar métodos virtualmente y otro para invocar métodos que no son prácticamente. Ambos conjuntos de métodos requieren un identificador de método invocar el método y no virtual invocación también requiere que especifique qué implementación de la clase se debe invocar.

Los métodos de interfaz solo se pueden buscar en el tipo declarativo; no se pueden buscar los métodos que proceden de interfaces extendido/heredado. Vea las Interfaces de enlace posterior / implementación invocador de la sección para obtener más detalles.

Cualquier método declarado en la clase o cualquier clase base o interfaz implementada se puede buscar.


### <a name="virtual-method-invocation"></a>Invocación de métodos virtuales

El conjunto de métodos para invocar métodos prácticamente sigue el patrón de nomenclatura:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método.

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash; invocar un método que devuelve un tipo no integrada, como `java.lang.Object` , matrices y las interfaces. El valor devuelto es una referencia local de JNI.

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash; invocar un método que devuelve un `bool` valor.

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash; invocar un método que devuelve un `sbyte` valor.

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash; invocar un método que devuelve un `char` valor.

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash; invocar un método que devuelve un `short` valor.

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; invocar un método que devuelve un `long` valor.

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash; invocar un método que devuelve un `float` valor.

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash; invocar un método que devuelve un `double` valor.



### <a name="non-virtual-method-invocation"></a>Invocación de método no virtual

El conjunto de métodos para invocar métodos que no son prácticamente sigue el patrón de nomenclatura:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método. Invocación de método no virtual normalmente se utiliza para invocar el método base de un método virtual.

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash; no prácticamente invocar un método que devuelve un tipo no integrada, como `java.lang.Object` , matrices y las interfaces. El valor devuelto es una referencia local de JNI.

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash; no prácticamente invocar un método que devuelve un `bool` valor.

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash; no prácticamente invocar un método que devuelve un `sbyte` valor.

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash; no prácticamente invocar un método que devuelve un `char` valor.

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash; no prácticamente invocar un método que devuelve un `short` valor.

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash; no prácticamente invocar un método que devuelve un `long` valor.

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash; no prácticamente invocar un método que devuelve un `float` valor.

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash; no prácticamente invocar un método que devuelve un `double` valor.


<a name="_Static_Methods" />

## <a name="static-methods"></a>Métodos estáticos

Los métodos estáticos se invocan mediante *método identificadores*. Método identificadores se obtienen a través de [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), que requiere que el tipo que el método se define en el nombre del método y el [signatura de tipo de JNI](#JNI_Type_Signatures) del método.

Identificadores de método no es necesario para que se libere y son válidos siempre que se carga el tipo correspondiente de Java. (Android no admite actualmente la descarga de clase.)



### <a name="static-method-invocation"></a>Invocación de método estático

El conjunto de métodos para invocar métodos prácticamente sigue el patrón de nomenclatura:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método.

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash; invocar un método estático que devuelve un tipo no integrada, como `java.lang.Object` , matrices y las interfaces. El valor devuelto es una referencia local de JNI.

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash; invocar un método estático que devuelve un `bool` valor.

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash; invocar un método estático que devuelve un `sbyte` valor.

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash; invocar un método estático que devuelve un `char` valor.

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash; invocar un método estático que devuelve un `short` valor.

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; invocar un método estático que devuelve un `long` valor.

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash; invocar un método estático que devuelve un `float` valor.

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash; invocar un método estático que devuelve un `double` valor.


<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Signaturas de tipo JNI

[Las signaturas de tipo de JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) son [las referencias de tipos de JNI](#_JNI_Type_References) (referencias de tipos aunque no simplificado), excepto en los métodos. Con los métodos, la firma de tipo de JNI es un paréntesis de apertura `'('`, seguido de las referencias de tipo para todos lo tipos concatenados (con sin puntos de separación o cualquier otra cosa), seguido por un paréntesis de cierre de parámetro `')'`, seguido de la referencia de tipo JNI del tipo de valor devuelto del método.

Por ejemplo, con el método de Java:

```java
long f(int n, String s, int[] array);
```

La firma del tipo JNI sería:

```csharp
(ILjava/lang/String;[I)J
```

En general, resulta *fuertemente* recomienda utilizar la `javap` comando para determinar las firmas JNI. Por ejemplo, la firma del tipo de JNI del [java.lang.Thread.State.valueOf(String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) método es "(Ljava/lang o cadena); Ljava/lang/subproceso$ estado;", mientras que el JNI tipo de firma de la [ java.lang.Thread.State.values](https://developer.android.com/reference/java/lang/Thread.State.html#values) método es "() [estado de $ Ljava/lang/subproceso;". Esté atento a los puntos y comas finales; esas *son* forma parte de la firma del tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Referencias de tipos JNI

Las referencias de tipos JNI son diferentes de las referencias de tipos de Java. No se puede usar como nombres completos de tipo Java `java.lang.String` con JNI, debe usar en su lugar las variaciones de JNI `"java/lang/String"` o `"Ljava/lang/String;"`, según el contexto; consulte a continuación para obtener más información.
Hay cuatro tipos de referencias de tipos JNI:

-  **built-in**
-  **simplified**
-  **type**
-  **array**


### <a name="built-in-type-references"></a>Referencias de tipos integrados

Las referencias de tipos integrados son un carácter único, que se usa para hacer referencia a tipos de valor integrados. La asignación es como sigue:

-  `"B"` para `sbyte` .
-  `"S"` para `short` .
-  `"I"` para `int` .
-  `"J"` para `long` .
-  `"F"` para `float` .
-  `"D"` para `double` .
-  `"C"` para `char` .
-  `"Z"` para `bool` .
-  `"V"` para `void` tipos devueltos de método.


<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Referencias de tipo simplificada

Solo se pueden utilizar referencias de tipo simplificada en [JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)).
Hay dos maneras para obtener una referencia de tipo simplificada:

1.  En un nombre completo de Java, reemplace cada `'.'` en el nombre del paquete y antes del nombre de tipo con `'/'` y cada `'.'` dentro de un nombre de tipo `'$'` .

1.  Leer la salida de `'unzip -l android.jar | grep JavaName'` .


Cualquiera de los dos dará como resultado el tipo de Java [java.lang.Thread.State](https://developer.android.com/reference/java/lang/Thread.State.html) va a asignar a la referencia de tipo simplificado `java/lang/Thread$State`.


### <a name="type-references"></a>Referencias de tipos

Una referencia de tipo es una referencia de tipo integrado o una referencia de tipo simplificado con un `'L'` prefijo y un `';'` sufijo. Para el tipo de Java [java.lang.String](https://developer.android.com/reference/java/lang/String.html), es la referencia de tipo simplificado `"java/lang/String"`, mientras que la referencia de tipo es `"Ljava/lang/String;"`.

Las referencias de tipos se utilizan con las referencias de tipos de matriz y con las firmas de JNI.

Una manera adicional para obtener una referencia de tipo es leer la salida de `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
Dependiendo del tipo implicados, puede usar una declaración de constructor o método devolver el tipo para determinar el nombre JNI. Por ejemplo:

```shell
$ javap -classpath android.jar -s java.lang.Thread.State
Compiled from "Thread.java"
```

```java
public final class java.lang.Thread$State extends java.lang.Enum{
public static final java.lang.Thread$State NEW;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State RUNNABLE;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State BLOCKED;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TIMED_WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TERMINATED;
  Signature: Ljava/lang/Thread$State;
public static java.lang.Thread$State[] values();
  Signature: ()[Ljava/lang/Thread$State;
public static java.lang.Thread$State valueOf(java.lang.String);
  Signature: (Ljava/lang/String;)Ljava/lang/Thread$State;
static {};
  Signature: ()V
}
```

`Thread.State` es un tipo de enumeración de Java, por lo que podemos usar la firma de la `valueOf` método para determinar que la referencia de tipo es estado de $ Ljava/lang/subproceso;.



### <a name="array-type-references"></a>Referencias de tipos de matriz

Las referencias de tipo de matriz son `'['` precede a una referencia de tipo JNI.
No se puede utilizar referencias de tipo simplificada al especificar matrices.

Por ejemplo, `int[]` es `"[I"`, `int[][]` es `"[[I"`, y `java.lang.Object[]` es `"[Ljava/lang/Object;"`.



## <a name="java-generics-and-type-erasure"></a>Borrado de tipos y genéricos de Java

*La mayoría* del tiempo, tal como se muestra a través de JNI, los genéricos de Java *no existen*.
Hay algunas "arrugas", pero esas arrugas están en cómo Java interactúa con los genéricos, no con el modo JNI busca y llama a miembros genéricos.

No hay ninguna diferencia entre un tipo genérico o miembro y un tipo no genérico o un miembro cuando interactúan a través de JNI. Por ejemplo, el tipo genérico [java.lang.Class&lt;T&gt; ](https://developer.android.com/reference/java/lang/Class.html) también es el tipo genérico "raw" `java.lang.Class`, los cuales tienen la misma referencia de tipo simplificada, `"java/lang/Class"`.


## <a name="java-native-interface-support"></a>Compatibilidad con la interfaz nativa de Java

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) es el contenedor administrado para Java Native Interface (JNI). Las funciones de JNI se declaran dentro de la [Java Native Interface Specification](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), aunque se han cambiado los métodos para quitar la configuración explícita `JNIEnv*` parámetro y `IntPtr` se utiliza en lugar de `jobject`, `jclass`, `jmethodID`, etcetera. Por ejemplo, considere la [JNI NewObject función](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Esto se expone como la [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) método:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Traducir entre las dos llamadas es bastante sencillo. En C tiene:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

El C# equivalente sería:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Una vez que tenga una instancia del objeto de Java contenida en un objeto IntPtr, probablemente deseará hacer algo con él. Puede usar los métodos JNIEnv como [ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) para hacerlo, pero si ya hay un análogo C# contenedor, a continuación, le conviene construir un contenedor a través de la referencia JNI. Puede hacerlo a través del [Extensions.JavaCast <t>()</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/) método de extensión:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

También puede usar el [Java.Lang.Object.GetObject <t>()</t> ](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) método:

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

Además, todas las funciones JNI se han modificado mediante la eliminación de la `JNIEnv*` parámetro está presente en todas las funciones JNI.


## <a name="summary"></a>Resumen

Tratar directamente con JNI es una experiencia terrible que debe evitarse a toda costa. Lamentablemente, no siempre es evitables; Espero que esta guía le proporcionará ayuda cuando se alcanza los casos de Java sin enlazar con Mono para Android.


## <a name="related-links"></a>Vínculos relacionados

- [SanityTests (ejemplo)](https://developer.xamarin.com/samples/SanityTests/)
- [Especificación de la interfaz nativa de Java](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funciones de la interfaz nativa de Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
