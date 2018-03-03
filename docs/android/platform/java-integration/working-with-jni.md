---
title: Trabajar con JNI
description: "Xamarin.Android permite escribir aplicaciones Android en C# en lugar de Java. Varios ensamblados se proporcionan con Xamarin.Android que proporcionan enlaces para las bibliotecas de Java, incluidos Mono.Android.dll y Mono.Android.GoogleMaps.dll. Sin embargo, no se proporcionan enlaces para cada biblioteca de Java posibles y no pueden enlazar los enlaces que se proporcionan todos los tipos de Java y miembro. Para usar sin enlazar tipos de Java y los miembros, se puede usar la interfaz nativa de Java (JNI). Este artículo explica cómo usar JNI para interactuar con los miembros de Xamarin.Android aplicaciones y tipos de Java."
ms.topic: article
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: d1c441de089a84c93c251588115abecb19816868
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-jni"></a>Trabajar con JNI

_Xamarin.Android permite escribir aplicaciones Android en C# en lugar de Java. Varios ensamblados se proporcionan con Xamarin.Android que proporcionan enlaces para las bibliotecas de Java, incluidos Mono.Android.dll y Mono.Android.GoogleMaps.dll. Sin embargo, no se proporcionan enlaces para cada biblioteca de Java posibles y no pueden enlazar los enlaces que se proporcionan todos los tipos de Java y miembro. Para usar sin enlazar tipos de Java y los miembros, se puede usar la interfaz nativa de Java (JNI). Este artículo explica cómo usar JNI para interactuar con los miembros de Xamarin.Android aplicaciones y tipos de Java._

<a name="_Overview" />

## <a name="overview"></a>Información general

No siempre es necesario o puede crear una administrada que se puede llamar contenedor (MCW) para invocar código de Java. En muchos casos, "alineado" JNI es perfectamente aceptable y útil para su uso único de miembros de Java sin enlazar. A menudo resulta más fácil de usar JNI para invocar un método único en una clase de Java que al generar un enlace .jar todo.

Xamarin.Android proporciona el `Mono.Android.dll` ensamblado, que proporciona un enlace para de Android `android.jar` biblioteca. Tipos y miembros no está presente en `Mono.Android.dll` y tipos no están presentes en `android.jar` pueden utilizar enlacen manualmente. Para enlazar tipos de Java y miembros, use la **Java Native Interface** (**JNI**) para buscar tipos, leer y escribir campos e invocar métodos.

La API de JNI en Xamarin.Android conceptualmente es muy similar a la `System.Reflection` API en. NET: hace posible para buscar los tipos y miembros por nombre, leer y escribir valores de campo, invocar métodos y mucho más. Puede usar JNI y `Android.Runtime.RegisterAttribute` atributo personalizado que se va a declarar métodos virtuales que se pueden enlazar para admitir reemplazar. Puede enlazar interfaces para que se pueden implementar en C#.

Este documento se explica:

-  Cómo JNI hace referencia a los tipos.
-  Cómo buscar, leer y escribir campos.
-  Cómo buscar e invocar métodos.
-  Cómo exponer métodos virtuales para permitir reemplazar desde el código administrado.
-  Cómo exponer interfaces.


<a name="_Requirements" />

## <a name="requirements"></a>Requisitos

JNI, tal como se expone a través de la [espacio de nombres de Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/), está disponible en todas las versiones de Xamarin.Android.
Para enlazar las interfaces y tipos de Java, debe utilizar Xamarin.Android 4.0 o posterior.

<a name="_Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Administrar contenedores CCW

A **administrado Callable Wrapper** (**MCW**) es una *enlace* para una clase de Java o interfaz que concluye todas la maquinaria JNI para que el código de cliente C# no tiene que preocuparse sobre la complejidad subyacente de JNI. La mayoría de `Mono.Android.dll` consta de los contenedores CCW administrados.

Contenedores invocables administrados tienen dos finalidades:

1.  Encapsulan el uso JNI para que el código de cliente no necesita conocer la complejidad subyacente.
1.  Le permite subclasifique tipos Java e implementar interfaces de Java.

El primer propósito es puramente por comodidad y encapsulación de complejidad para que los consumidores tengan un conjunto de clases que se utilizan simple y administrado. Requiere el uso de los diversos [JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) miembros tal como se describe más adelante en este artículo. Tenga en cuenta que administrar contenedores CCW no son estrictamente necesarias &ndash; "alineado" usar JNI es absolutamente aceptable y es útil para su uso único de miembros de Java sin enlazar. Subclasificando e implementación de interfaces requiere el uso de contenedores RCW administrados.


<a name="_Android_Callable_Wrappers" />

## <a name="android-callable-wrappers"></a>Android contenedores CCW

Contenedores CCW Android (ACW) son necesarios cuando el tiempo de ejecución Android (prediseñadas) necesita para llamar a código administrado; estos contenedores son necesarios porque no hay ninguna manera de registrar clases con material gráfico en tiempo de ejecución.
(En concreto, el [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) función JNI no es compatible con el tiempo de ejecución Android. Contenedores CCW Android, por tanto, forman la falta de capacidad de soporte de registro del tipo en tiempo de ejecución.)

Cada vez que el código Android debe ejecutar una máquina o método que se reemplazan o se implementa en código administrado de la interfaz, Xamarin.Android debe proporcionar a un proxy de Java para que este método obtiene envían al tipo administrado adecuado. Estos tipos de proxy de Java son código de Java que tienen el "mismo" clase base y la lista de interfaces de Java como el tipo administrado, implementar los constructores mismo y declarar cualquier clase base reemplazado y métodos de interfaz.

Contenedores CCW Android generados por el **monodroid.exe** programa durante la [proceso de compilación](~/android/deploy-test/building-apps/build-process.md)y se generan para todos los tipos que heredan (directa o indirectamente) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).


<a name="_Implementing_Interfaces" />

### <a name="implementing-interfaces"></a>Implementar interfaces

Hay veces cuando necesite implementar una interfaz Android, (como [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)).

Todas las interfaces y clases Android amplían la [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interfaz; por lo tanto, deben implementar todos los tipos de Android `IJavaObject`.
Xamarin.Android aprovecha las ventajas de este hecho &ndash; utiliza `IJavaObject` para proporcionar Android con un proxy de Java (un contenedor CCW Android) para el tipo administrado especificado. Dado que **monodroid.exe** solo busca `Java.Lang.Object` subclases (que debe implementar `IJavaObject`), el uso de subclases `Java.Lang.Object` nos proporciona un mecanismo para implementar interfaces en código administrado. Por ejemplo:

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

<a name="_Implementation_Details" />

### <a name="implementation-details"></a>Detalles de implementación

*El resto de este artículo proporciona detalles de implementación sujeta a cambios sin previo aviso* (y se presenta aquí sólo debido a los desarrolladores pueden obtener información acerca de lo que ocurre en segundo plano).

Por ejemplo, dado el código fuente de C# siguiente:

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

Tenga en cuenta que la clase base se conserva, y las declaraciones de método nativo se proporcionan para cada método que se haya reemplazado dentro de código administrado.


<a name="_ExportAttribute_and_ExportFieldAttribute" />

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute y ExportFieldAttribute

Por lo general, Xamarin.Android genera automáticamente el código de Java que comprende el ACW; Esta generación se basa en los nombres de clase y método cuando una clase se deriva de una clase de Java e invalida los métodos existentes de Java. Sin embargo, en algunos casos, la generación de código no es el adecuado, tal como se describe a continuación:

-   Acción de Android admiten nombres de atributos xml de diseño, por ejemplo la [android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) atributo XML. Cuando se especifica, la instancia de vista aumentada intenta buscar el método de Java.

-   El [java.io.Serializable](http://developer.android.com/reference/java/io/Serializable.html) interfaz requiere `readObject` y `writeObject` métodos. Puesto que no son miembros de esta interfaz, nuestra implementación administrada correspondiente no exponer estos métodos al código de Java.

-   El [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/) interfaz espera que una clase de implementación debe tener un campo estático `CREATOR` de tipo `Parcelable.Creator`. El código Java generado requiere algún campo explícito. Con nuestro escenario estándar, no hay ninguna manera de campo de salida en el código de Java desde el código administrado.


Dado que la generación de código no proporciona una solución para generar métodos de Java arbitrarios con nombres arbitrarios, a partir de Xamarin.Android 4.2, el [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/) y [ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/) estaban se introdujo para ofrecer una solución para los escenarios anteriores. Ambos atributos residen en el `Java.Interop` espacio de nombres:

-   `ExportAttribute` &ndash; Especifica un nombre de método y sus tipos de excepción esperada (para dar explícita "inicia" en Java). Cuando se utiliza en un método, el método "exportará" un método de Java que generan un código de envío a la invocación de JNI correspondiente al método administrado. Esto puede utilizarse con `android:onClick` y `java.io.Serializable`.

-   `ExportFieldAttribute` &ndash; Especifica un nombre de campo. Este archivo reside en un método que funciona como un inicializador de campo. Esto puede utilizarse con `android.os.Parcelable`.

El [ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/) proyecto de ejemplo muestra cómo utilizar estos atributos.

<a name="_Troubleshooting_ExportAttribute_and_ExportFieldAttribute" />

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Solución de problemas ExportAttribute y ExportFieldAttribute

-   Empaquetado se produce un error debido a que faltan **Mono.Android.Export.dll** &ndash; si usó `ExportAttribute` o `ExportFieldAttribute` en algunos métodos en su propio código o las bibliotecas dependientes, tiene que agregar  **Mono.Android.Export.dll**. Este ensamblado se aísla para admitir el código de devolución de llamada de Java. Es independiente de **Mono.Android.dll** mientras agrega tamaño adicional a la aplicación.

-   En la versión de lanzamiento, `MissingMethodException` se produce para los métodos de exportación &ndash; en la versión de lanzamiento, `MissingMethodException` se produce para los métodos de exportación. (Este problema se corrigió en la versión más reciente de Xamarin.Android).


<a name="_ExportParameterAttribute" />

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` y `ExportFieldAttribute` proporcionan funcionalidad que puede usar código en tiempo de ejecución de Java. Este código de tiempo de ejecución tiene acceso a código administrado a través de los métodos generados de JNI controlados por los atributos. Como resultado, no hay ningún método de Java existente que se enlaza al método administrado; por lo tanto, el método de Java se genera a partir de un prototipo administrado del método.

Sin embargo, en este caso no es totalmente determinante. En concreto, esto es cierto en algunas asignaciones avanzadas entre los tipos administrados y los tipos de Java, como:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

Cuando se necesiten tipos como los siguientes métodos exportado, el `ExportParameterAttribute` se debe usar para dar el parámetro correspondiente de forma explícita o un tipo de valor devuelto.

 <a name="_Annotation_Attribute" />


### <a name="annotation-attribute"></a>Atributo de anotación

En el punto 4.2 Xamarin.Android, convertimos `IAnnotation` tipos de implementación en atributos (System.Attribute) y compatibilidad agregada para la generación de anotación en contenedores de Java.

Esto significa que los siguientes cambios direccionales:

-   El generador de enlace genera `Java.Lang.DeprecatedAttribute` de `java.Lang.Deprecated` (aunque debería ser `[Obsolete]` en código administrado).

-   Esto no significa existentes `Java.Lang.Deprecated` clase desaparecerá. Estos objetos basados en Java pudieron usarse todavía como objetos de Java habituales (si existe dicho uso). Habrá `Deprecated` y `DeprecatedAttribute` clases.

-   El `Java.Lang.DeprecatedAttribute` clase está marcada como `[Annotation]` . Cuando hay un atributo personalizado que se hereda de esta `[Annotation]` atributo, tarea msbuild generará una anotación de Java para dicho atributo personalizado (@Deprecated) en el contenedor invocable Android (ACW).

-   Las anotaciones se puede generar en clases, métodos y exportan campos (que es un método en código administrado).

Si la clase contenedora (la propia clase anotada o la clase que contiene a los miembros anotados) no está registrada, todo el origen de clase de Java no se genera en absoluto, incluidas las anotaciones. Para los métodos, puede especificar el `ExportAttribute` para obtener el método generado y anotar explícitamente. Además, no es una característica para una definición de clase de Java anotación "generar". En otras palabras, si define un atributo personalizado administrado para un determinado anotación, tendrá que agregar otra biblioteca. jar que contiene la clase de anotación correspondiente de Java. Agregar un archivo de código fuente de Java que define el tipo de anotación no es suficiente. El compilador de Java no funciona de la misma manera que **apt**.

Además se aplican las siguientes limitaciones:

-   Este proceso de conversión no se considera `@Target` anotación en el tipo de anotación hasta ahora.

-   Atributos en una propiedad no funciona. Usar atributos para captador o establecedor en su lugar.


<a name="_Class_Binding" />

## <a name="class-binding"></a>Enlace de clase

Una clase de enlace significa la escritura de un contenedor administrado que se puede llamar para simplificar la invocación del tipo subyacente de Java.

Enlazar métodos abstractos y virtuales para permitir el reemplazo de C# requiere Xamarin.Android 4.0. Sin embargo, cualquier versión de Xamarin.Android puede enlazar métodos no virtuales, métodos estáticos o métodos virtuales sin permitir invalidaciones.

Normalmente, un enlace contiene los siguientes elementos:

-  A [JNI controlar para el tipo de Java que se enlaza](#_Looking_up_Java_Types).

-  [JNI campo identificadores y las propiedades de cada campo enlazado](#_Instance_Fields).

-  [Identificadores de método JNI y métodos para cada uno de ellos enlazan método](#_Instance_Methods).

-  Si es necesario subclasificando, el tipo debe tener un [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado en la declaración de tipos con [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) establecido en `true`.


<a name="_Declaring_Type_Handle" />

### <a name="declaring-type-handle"></a>Identificador del tipo declarativo

Los métodos de búsqueda de campo y método requieren una referencia de objeto que hace referencia a su tipo declarativo. Por convención, se mantiene en una `class_ref` campo:

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Consulte la [las referencias de tipos de JNI](#_JNI_Type_References) sección para obtener más información acerca de la `CLASS` símbolo (token).

<a name="_Binding_Fields" />

### <a name="binding-fields"></a>Enlazar campos

Campos de Java se exponen como propiedades de C#, por ejemplo el campo de Java [java.lang.System.in](http://developer.android.com/reference/java/lang/System.html#in) se enlaza como la propiedad de C# [Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/).
Además, puesto que JNI distingue entre los campos estáticos y campos de instancia, métodos diferentes usarse al implementar las propiedades.

El enlace de campo implica tres conjuntos de métodos:

1.  El *obtener Id. de campo* método. El *obtener Id. de campo* método es responsable de devolver un campo de identificador que el *obtener valor de campo* y *establecer el valor del campo* métodos va a usar. Obtener el identificador de campo es necesario conocer la declaración de tipo, el nombre del campo y el [signatura de tipo JNI](#_JNI_Type_Signatures) del campo.

1.  El *obtener valor de campo* métodos. Estos métodos requieren el identificador de campo y son responsables de leer el valor del campo de Java.
    El método debe usar depende del tipo del campo.

1.  El *establecer el valor del campo* métodos. Estos métodos requieren el identificador de campo y están responsables de escribir el valor del campo dentro de Java. El método debe usar depende del tipo del campo.


 [Los campos estáticos](#_Static_Fields) usar la [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), `JNIEnv.GetStatic*Field`, y [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/) métodos.

 [Campos de instancia](#_Instance_Fields) usar la [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), `JNIEnv.Get*Field`, y [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/) métodos.

Por ejemplo, la propiedad estática `JavaSystem.In` pueden implementarse como:

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

Nota: Estamos usando [InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para convertir la referencia JNI en un `System.IO.Stream` instancia y nos vamos a usar `JniHandleOwnership.TransferLocalRef` porque [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) Devuelve una referencia local.

Muchos de los [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) tipos tienen `FromJniHandle` hacen referencia a los métodos que se convertirán un JNI en el tipo deseado.

 <a name="_Method_Binding" />


### <a name="method-binding"></a>Enlace (método)

Métodos de Java se exponen como métodos de C# y como propiedades de C#. Por ejemplo, el método de Java [java.lang.Runtime.runFinalizersOnExit](http://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) método se enlaza como el [Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/) método y el [java.lang.Object.getClass ](http://developer.android.com/reference/java/lang/Object.html#getClass) método se enlaza como el [Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/) propiedad.

La invocación del método es un proceso de dos pasos:

1.  El *obtener Id. de método* para el método que se invoca. El *obtener Id. de método* método es responsable de devolver un identificador de método que se va a usar los métodos de invocación de método. Obtener el identificador de método es necesario conocer la declaración de tipo, el nombre del método y el [signatura de tipo JNI](#_JNI_Type_Signatures) del método.

1.  Invoque al método.

Al igual que con campos, los métodos se utiliza para obtener el identificador de método e invocar el método difieren entre métodos estáticos y métodos de instancia.

[Métodos estáticos](#_Static_Methods_1) usar [JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/) para buscar el identificador de método y usar el `JNIEnv.CallStatic*Method` familia de métodos de llamada.

[Métodos de instancia](#_Instance_Methods) usar [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) para buscar el identificador de método y usar la `JNIEnv.Call*Method` y `JNIEnv.CallNonvirtual*Method` familias de métodos de llamada.

Enlace de método es potencialmente algo más que la invocación del método. Enlace de método también incluye lo que un método para que se invalide (para los métodos abstractos y no final) o implementa (para métodos de interfaz). El [compatibilidad con herencia, Interfaces](#_Supporting_Inheritance,_Interfaces_1) sección tratan las complejidades de admitir métodos virtuales y los métodos de interfaz.

 <a name="_Static_Methods" />


#### <a name="static-methods"></a>Métodos estáticos

Enlazar un método estático implica el uso de `JNIEnv.GetStaticMethodID` para obtener un identificador de método, a continuación, con la correspondiente `JNIEnv.CallStatic*Method` método, según el tipo de valor devuelto del método. El siguiente es un ejemplo de un enlace para el [Runtime.getRuntime](http://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) método:

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

 <a name="_Non-virtual_Instance_Method_Binding" />


#### <a name="non-virtual-instance-method-binding"></a>Enlace de método de instancia no virtuales

Enlazar un `final` método de instancia o un método de instancia que no requiere invalidar, implica el uso de `JNIEnv.GetMethodID` para obtener un identificador de método, a continuación, con la correspondiente `JNIEnv.Call*Method` método, según el tipo de valor devuelto del método. El siguiente es un ejemplo de un enlace para el `Object.Class` propiedad:

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

<a name="_Binding_Constructors" />

### <a name="binding-constructors"></a>Constructores de enlace

Los constructores son métodos de Java con el nombre `"<init>"`. Al igual que con los métodos de instancia de Java, `JNIEnv.GetMethodID` se usa para buscar el identificador del constructor. A diferencia de los métodos de Java, el [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/) métodos se usan para invocar el identificador de método de constructor. El valor devuelto de `JNIEnv.NewObject` es una referencia local JNI:


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalmente un enlace de clase creará una subclase [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).
Cuando se crean subclases `Java.Lang.Object`, un adicionales semántica entra en juego: una `Java.Lang.Object` instancia mantiene una referencia global a una instancia de Java a través de la `Java.Lang.Object.Handle` propiedad.

1.  El `Java.Lang.Object` constructor predeterminado, asignará una instancia de Java.

1.  Si el tipo tiene un `RegisterAttribute` , y `RegisterAttribute.DoNotGenerateAcw` es `true` , a continuación, una instancia de la `RegisterAttribute.Name` se crea el tipo a través de su constructor predeterminado.

1.  En caso contrario, el [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) correspondiente a `this.GetType` se crea una instancia a través de su constructor predeterminado. Contenedores CCW Android se generan durante la creación del paquete para cada `Java.Lang.Object` subclase para el que `RegisterAttribute.DoNotGenerateAcw` no está establecido en `true`.

Para tipos que son no clase enlaces, se trata de la semántica: crear instancias de un `Mono.Samples.HelloWorld.HelloAndroid` C# instancia debe construir un Java `mono.samples.helloworld.HelloAndroid` instancia que es un contenedor CCW Android generado.

Para los enlaces de clase, puede tratarse el comportamiento correcto si el tipo de Java contiene un constructor predeterminado y ningún otro constructor necesita que se debe invocar. En caso contrario, se debe proporcionar un constructor que realiza las siguientes acciones:

1.  Invocar la [Java.Lang.Object (IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) en lugar del predeterminado `Java.Lang.Object` constructor. Esto es necesario para evitar la creación de una nueva instancia de Java.

1.  Compruebe el valor de [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) antes de crear las instancias de Java. El `Object.Handle` propiedad tendrá un valor distinto de `IntPtr.Zero` si se construyó a un contenedor CCW Android en el código de Java y se construye el enlace de la clase para que contenga la instancia de contenedor CCW Android creada. Por ejemplo, cuando se crea Android un `mono.samples.helloworld.HelloAndroid` instancia, el primero y el de Java, se creará el contenedor invocable Android `HelloAndroid` constructor creará una instancia de los correspondientes `Mono.Samples.HelloWorld.HelloAndroid` tipo, con el `Object.Handle` propiedad se establece en la instancia de Java antes de la ejecución del constructor.

1.  Si el tipo de tiempo de ejecución actual no es el mismo que el tipo declarativo tipo y, a continuación, una instancia del contenedor invocable Android correspondiente se deben crear y usar [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para almacenar el identificador devuelto por [ JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/).

1.  Si el tipo en tiempo de ejecución actual es el mismo que el tipo declarativo, a continuación, invocar el constructor de Java y usar [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) para almacenar el identificador devuelto por `JNIEnv.NewInstance` .


Por ejemplo, considere la [java.lang.Integer(int)](http://developer.android.com/reference/java/lang/Integer.html#Integer(int)) constructor. Esto se enlaza como:

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

 <a name="_Supporting_Inheritance,_Interfaces" />


### <a name="supporting-inheritance-interfaces"></a>Compatibilidad con herencia, Interfaces

Creación de subclases de un tipo de Java o implementar una interfaz Java requiere la generación de [Android Callable Wrappers](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) que se generan para cada `Java.Lang.Object` subclase durante el proceso de empaquetado. Generación de ACW se controla mediante la [Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado.

Para los tipos de C#, la `[Register]` constructor de atributos personalizados requiere un argumento: el [JNI simplifica la referencia de tipo](#_Simplified_Type_References_1) para el correspondiente Java escriba. Esto permite proporcionar nombres diferentes entre Java y C#.

Antes de Xamarin.Android 4.0, la `[Register]` atributo personalizado no estuvo disponible para tipos de Java "alias" existentes. Esto es porque el proceso de generación de ACW generaría ACWs para cada `Java.Lang.Object` subclase encontrado.

Xamarin.Android 4.0 introducidas el [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) propiedad. Esta propiedad indica al proceso de generación de ACW a *omitir* el tipo anotado, lo que permite la declaración de nuevos contenedores CCW administrados que no se producirá en ACWs se genera en el momento de creación del paquete. Esto permite que los tipos de Java de enlace existentes. Por ejemplo, considere la siguiente clase de Java simple, `Adder`, que contiene un método, `add`, que agrega a enteros y devuelve el resultado:

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

En este caso, el `Adder` tipo de C# *alias* el `Adder` tipo de Java. El `[Register]` atributo se utiliza para especificar el nombre JNI de la `mono.android.test.Adder` tipo de Java y el `DoNotGenerateAcw` propiedad se utiliza para desactivar la generación de ACW. Esto dará como resultado la generación de un ACW para la `ManagedAdder` escribe, lo que correctamente las subclases el `mono.android.test.Adder` tipo. Si el `RegisterAttribute.DoNotGenerateAcw` propiedad no había sido utiliza, a continuación, el proceso de compilación Xamarin.Android habría generado un nuevo `mono.android.test.Adder` tipo de Java. Eso resultaría en errores de compilación, como la `mono.android.test.Adder` tipo estará presente dos veces, en dos archivos independientes.

 <a name="_Binding_Virtual_Methods" />


### <a name="binding-virtual-methods"></a>Enlazar métodos virtuales

`ManagedAdder` las subclases el Java `Adder` tipo, pero no es especialmente interesante: C# `Adder` tipo no define ningún método virtual, por lo que `ManagedAdder` no se puede reemplazar cualquier elemento.

Enlace `virtual` métodos para permitir el reemplazo por las subclases requiere varios aspectos que deben realizarse en la que se dividen en las dos categorías siguientes:

1.  **Enlace (método)**

1.  **Registro (método)**

<a name="_Method_Binding" />

#### <a name="method-binding"></a>Enlace (método)

El enlace de un método requiere la adición de dos miembros de compatibilidad con C# `Adder` definición: `ThresholdType`, y `ThresholdClass`.

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

`ThresholdType` se utiliza en el método de enlace para determinar cuándo debe realizar virtual frente a expedir no virtual. Siempre debe devolver un `System.Type` instancia que se corresponde con el tipo declarativo de C#.

##### <a name="thresholdclass"></a>ThresholdClass

El `ThresholdClass` propiedad devuelve la referencia de clase JNI para el tipo de enlace:

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` se utiliza en el método de enlace al invocar métodos no virtuales.

#### <a name="binding-implementation"></a>Implementación del enlace

La implementación del enlace de método es responsable de invocación en tiempo de ejecución del método de Java. También contiene un `[Register]` declaración de atributo personalizado que forma parte del registro de método y se describen en la sección registro de método:

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

Una vez que se obtiene el identificador de método, `GetType` se compara con `ThresholdType` para determinar si es necesario no máquina virtual o de envío. Distribución virtual es necesaria cuando `GetType` coincide con `ThresholdType`, como `Handle` pueden hacer referencia a una subclase asignada de Java que invalida el método.

Cuando `GetType` no coincide con `ThresholdType`, `Adder` derivado (por ejemplo, por `ManagedAdder`) y el `Adder.Add` implementación solo se invocará si invoca la subclase `base.Add`. Este es el caso de envío no virtual, que es donde `ThresholdClass` entra en juego. `ThresholdClass` Especifica qué clase de Java proporcionará la implementación del método que se va a invocar.

 <a name="_Method_Registration" />


#### <a name="method-registration"></a>Registro (método)

Supongamos que tenemos un controlador actualizado, `ManagedAdder` definición que invalida el `Adder.Add` método:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Recuerde que `Adder.Add` tenía un `[Register]` atributos personalizados:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

El `[Register]` constructor de atributo personalizado acepta tres valores:

1.  El nombre del método de Java, `"add"` en este caso.

1.  La firma de tipo JNI del método `"(II)I"` en este caso.

1.  El *método conector* , `GetAddHandler` en este caso.
    Métodos de conector se explicará más adelante.


Los dos primeros parámetros permiten que el proceso de generación de ACW generar una declaración de método para invalidar el método. El resultante ACW contendría parte del código siguiente:

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

Tenga en cuenta que un `@Override` se declara el método, que delega en un `n_`-como prefijo el método del mismo nombre. Garantizar que, cuando se invoca el código Java `ManagedAdder.add`, `ManagedAdder.n_add` se invocará, lo que permitirá el reemplazo de C# `ManagedAdder.Add` método va a ejecutar.

Por lo tanto, la cuestión más importante: ¿cómo es `ManagedAdder.n_add` enlaza al `ManagedAdder.Add`?

Java `native` métodos están registrados con el tiempo de ejecución de Java (el tiempo de ejecución de Android) a través de la [RegisterNatives JNI función](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` toma una matriz de estructuras que contiene el nombre del método de Java, la firma de tipo JNI y un puntero a función para invocar que sigue [JNI convención de llamada](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
El puntero de función debe ser una función que toma dos argumentos de puntero, seguidos de los parámetros del método. El Java `ManagedAdder.n_add` método debe implementarse a través de una función que tiene el siguiente prototipo C:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android no expone un `RegisterNatives` método. En su lugar, el ACW y el MCW juntos proporcionan la información necesaria para invocar `RegisterNatives`: el ACW contiene el nombre del método y la firma de tipo JNI, la falta de algo solo es un puntero de función a enlazar.

Aquí es donde el *método conector* entra en juego. La tercera `[Register]` parámetro de atributo personalizado es el nombre de un método definido en el tipo registrado o una clase base del tipo registrado que no acepta parámetros y devuelve un `System.Delegate`. El valor devuelto `System.Delegate` a su vez, hace referencia a un método que tiene la firma de función JNI correcta. Por último, el delegado que devuelve el método conector *debe* modificado para que el GC no recopila, tal y como se proporciona el delegado para Java.

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
`JNINativeWrapper.CreateDelegate` ajusta el método proporcionado en un bloque try/catch, para que las excepciones no controladas se controlan y se producirá cuando se genera el [AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/) eventos. El delegado resultante se almacena en el método estático `cb_add` variable para que el GC no liberará el delegado.

Por último, el `n_Add` método es responsable de serializar los parámetros JNI a los tipos administrados correspondientes, utilizamos delegar el método.

Nota: Use siempre `JniHandleOwnership.DoNotTransfer` al obtener una MCW a través de una instancia de Java. Tratándolos como una referencia local (y, por tanto, al llamar a `JNIEnv.DeleteLocalRef`) se interrumpirá administrados -&gt; Java -&gt; pila transiciones administradas.

 <a name="_Complete_Adder_Binding" />


### <a name="complete-adder-binding"></a>Completar agregador enlace

Completo administrados enlace para el `mono.android.tests.Adder` es de tipo:

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

 <a name="_Restrictions" />


### <a name="restrictions"></a>Restricciones

Al escribir un tipo que coincide con los criterios siguientes:

1.  Subclases `Java.Lang.Object`

1.  Tiene un `[Register]` atributos personalizados

1.  `RegisterAttribute.DoNotGenerateAcw` es `true`


A continuación, para la interacción de GC del tipo *no debe* tienen todos los campos que pueden hacer referencia a un `Java.Lang.Object` o `Java.Lang.Object` subclase en tiempo de ejecución. Por ejemplo, los campos de tipo `System.Object` y cualquier tipo de interfaz no están permitidas. Tipos que no se pueden hacer referencia a `Java.Lang.Object` instancias están permitidas, como `System.String` y `List<int>`. Esta restricción es evitar que la colección de objetos que el catálogo global.

Si el tipo debe contener un campo de instancia que se puede hacer referencia a un `Java.Lang.Object` de instancias, el tipo de campo debe ser `System.WeakReference` o `GCHandle`.


 <a name="_Binding_Abstract_Methods" />

## <a name="binding-abstract-methods"></a>Enlazar métodos abstractos

Enlace `abstract` métodos es idéntica en gran medida a los métodos virtuales de enlace. Hay solo dos diferencias:

1.  El método abstracto es abstracto. Todavía mantiene el `[Register]` atributo y el registro de método asociado, el método de enlace solo se mueve a la `Invoker` tipo.

1.  No `abstract` `Invoker` se crea subclases de qué tipo el tipo abstracto. El `Invoker` tipo debe reemplazar abstractos todos los métodos declarados en la clase base, y la implementación invalidada es la implementación del método de enlace, aunque se puede omitir el caso de envío no virtual.


Por ejemplo, supongamos que el anterior `mono.android.test.Adder.add` método fuera `abstract`. El enlace de C#, también cambian para que `Adder.Add` fuese abstracta y un nuevo `AdderInvoker` debe definir un tipo que implementa `Adder.Add`:

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

El `Invoker` tipo solo es necesaria cuando se obtienen las referencias JNI a instancias creadas para Java.

 <a name="_Binding_Interfaces" />

## <a name="binding-interfaces"></a>Interfaces de enlace

Interfaces de enlace es conceptualmente similar a las clases que contienen métodos virtuales de enlace, pero muchas de las características tienen diferencias sutiles (y no tan imperceptibles). Tenga en cuenta la siguiente [declaración de interfaz de Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Enlaces de interfaz tienen dos partes: la definición de interfaz de C# y una definición de invocador para la interfaz.


<a name="_Interface_Definition" />

### <a name="interface-definition"></a>Definición de la interfaz

La definición de interfaz de C# debe cumplir los siguientes requisitos:

-   La definición de interfaz debe tener un `[Register]` atributo personalizado.

-   La definición de interfaz debe extender el `IJavaObject interface`.
    Si no lo hace evitará ACWs heredar la interfaz Java.

-   Cada método de interfaz debe contener un `[Register]` atributo que especifica el nombre del método Java correspondiente, la firma JNI y el método de conector.

-   El método de conector también debe especificar el tipo que el método de conector puede estar ubicado en.

Al enlazar `abstract` y `virtual` métodos, el método del conector se buscarán en la jerarquía de herencia del tipo que se va a registrar. Las interfaces no pueden tener ningún método que contiene cuerpos, por lo que esto no funciona, lo que el requisito de que se especifique un tipo que indica dónde se encuentra el método de conector. El tipo se especifica dentro de la cadena de método conector, después de dos puntos `':'`, y debe ser el nombre de tipo calificado de ensamblado del tipo que contiene el invocador.

Declaraciones de método de interfaz son una traducción del método Java correspondiente mediante *compatible* tipos. Para los tipos de Java builtin, los tipos compatibles son los tipos correspondientes C#, por ejemplo, Java `int` es C# `int`. Para los tipos de referencia, el tipo compatible es un tipo que puede proporcionar un identificador JNI del tipo adecuado de Java.

Los miembros de interfaz no se invocará directamente Java &ndash; invocación se pueda controlarse a través del tipo invocador &ndash; por lo que se permite una cantidad de flexibilidad.

Puede ser la interfaz de progreso de Java [declarado en C# como](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Observe que en los pasos anteriores se asigna el Java `int[]` parámetro a un [JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/).
Esto no es necesario: se puede enlazarlo con C# `int[]`, o un `IList<int>`, u otra cosa por completo. Cualquier tipo que se selecciona, el `Invoker` debe ser capaz de traducir en un Java `int[]` tipo de llamada.

<a name="_Invoker_Definition" />

### <a name="invoker-definition"></a>Definición del invocador

El `Invoker` definición de tipo debe heredar `Java.Lang.Object`, implemente la interfaz apropiada y proporcionar todos los métodos de conexión que se hace referencia en la definición de interfaz. Hay más sugerencias que difiere de un enlace de clase: el `class_ref` identificadores de campo y método deben ser miembros de instancia, no los miembros estáticos.

El motivo de que prefieren los miembros de instancia tiene que ver con `JNIEnv.GetMethodID` comportamiento en tiempo de ejecución Android. (Esto puede ser también el comportamiento de Java; no se han probado). `JNIEnv.GetMethodID` devuelve el valor null al buscar un método que procede de una interfaz implementada y no la interfaz declarada. Tenga en cuenta el [java.util.SortedMap&lt;K, V&gt; ](http://developer.android.com/reference/java/util/SortedMap.html) interfaz Java, que implementa el [java.util.Map&lt;K, V&gt; ](http://developer.android.com/reference/java/util/Map.html) interfaz. Mapa proporciona un [borrar](http://developer.android.com/reference/java/util/Map.html#clear()) método, lo que aparentemente razonable `Invoker` definición para SortedMap sería:

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

Los pasos anteriores se producirá un error porque `JNIEnv.GetMethodID` devolverá `null` al buscar el `Map.clear` método a través de la `SortedMap` instancia de la clase.

Hay dos posibles soluciones a este: realizar un seguimiento de la interfaz que proceden de todos los métodos y tener un `class_ref` para cada interfaz, o mantener todo el contenido como miembros de instancia y realizar la búsqueda de método en el tipo de clase más derivada, no el tipo de interfaz. Se realiza en **Mono.Android.dll**.

La definición de invocador tiene seis secciones: el constructor, el `Dispose` método, el `ThresholdType` y `ThresholdClass` miembros, la `GetObject` método, implementación de método de interfaz y la implementación del método de conector.

 <a name="_Constructor" />


#### <a name="constructor"></a>Constructor

El constructor debe buscar la clase en tiempo de ejecución de la instancia que se va a invocar y almacenar la clase en tiempo de ejecución de la instancia `class_ref` campo:

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

Nota: La `Handle` propiedad debe utilizarse dentro del cuerpo del constructor y no el `handle` parámetro, como sucede en v4.0 Android el `handle` parámetro puede ser válido después de que el constructor base termina de ejecutarse.

<a name="_Dispose_Method" />

#### <a name="dispose-method"></a>Dispose (Método)

El `Dispose` método necesario liberar la referencia global asignada en el constructor:

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

<a name="_ThresholdType_and_ThresholdClass" />

#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType y ThresholdClass

El `ThresholdType` y `ThresholdClass` los miembros son idénticos a lo que se encuentra en un enlace de clase:

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

<a name="_GetObject_Method" />

#### <a name="getobject-method"></a>GetObject (Método)

Una variable static `GetObject` método es necesario para admitir [Extensions.JavaCast&lt;T&gt;()](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

<a name="_Interface_Methods" />

#### <a name="interface-methods"></a>Métodos de interfaz

Cada método de la interfaz debe tener una implementación, que invoca al método correspondiente de Java a través de JNI:

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

 <a name="_Connector_Methods" />


#### <a name="connector-methods"></a>Métodos de conector

Los métodos de conector y la infraestructura de soporte son responsables de serializar los parámetros JNI en tipos adecuados de C#. El Java `int[]` parámetro se pasa como un JNI `jintArray`, que es un `IntPtr` en C#. El `IntPtr` deben calcularse hacia un `JavaArray<int>` para admitir la invocación de la interfaz de C#:

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

Sin embargo, tenga en cuenta que `JNIEnv.GetArray` copia toda la matriz entre las máquinas virtuales, por lo que para matrices de gran tamaño Esto podría resultar en una gran cantidad de presión de GC agregada.

<a name="_Complete_Invoker_Definition" />

### <a name="complete-invoker-definition"></a>Completar la definición del invocador

El [completar la definición de IAdderProgressInvoker](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

 <a name="_JNI_Object_References" />


## <a name="jni-object-references"></a>Referencias a objetos JNI

Muchos métodos de JNIEnv devuelven *JNI* *referencias a objetos*, que son similares a `GCHandle`s. JNI proporciona tres tipos diferentes de referencias de objeto: las referencias locales, globales y referencias débiles de globales. Las tres se representan como `System.IntPtr`, *pero* (según la sección tipos de función de JNI) no todos los `IntPtr`s devuelto desde `JNIEnv` métodos son referencias. Por ejemplo, [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) devuelve un `IntPtr`, pero que no devuelve una referencia de objeto, devuelve un `jmethodID`. Consulte la [documentación sobre la función JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) para obtener más información.

Se crean referencias locales *mayoría* métodos de creación de referencia.
Android solo permite a un número limitado de referencias locales que existe en un momento dado, normalmente 512. Referencias locales se pueden eliminar a través de [JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/).
A diferencia de JNI, no todos hacen referencia a los métodos de JNIEnv qué referencias de objeto de devolución devuelven referencias locales; [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/) devuelve un *global* referencia. Se recomienda encarecidamente que eliminar referencias locales tan pronto como pueda, posiblemente mediante la creación de un [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) alrededor del objeto y la especificación de `JniHandleOwnership.TransferLocalRef` a la [Java.Lang.Object (IntPtr administrar, transferencia de JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) constructor.

Se crean referencias globales [JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/) y [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/).
Se puedan destruir con [JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/).
Emuladores tienen un límite de 2.000 referencias globales pendientes, mientras que los dispositivos de hardware tienen un límite de aproximadamente 52.000 referencias globales.

Referencias parciales de globales solo están disponibles en Android v2.2 (Froyo) y versiones posteriores. Se pueden eliminar las referencias débiles globales con [JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr)).

<a name="_Dealing_With_JNI_Local_References" />

### <a name="dealing-with-jni-local-references"></a>Tratamiento de las referencias JNI Local

El [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/), [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/), [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/), [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)y [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) métodos devuelven un `IntPtr` que contiene una referencia local JNI a un objeto de Java, o `IntPtr.Zero` si Java devuelve `null`. Debido al número limitado de referencias locales que pueden estar pendientes en una vez (512 entradas) es deseable para asegurarse de que las referencias se eliminan de manera oportuna. Hay tres maneras de referencias locales puedan abordarse: eliminar de forma explícita, crear un `Java.Lang.Object` instancia para contener usarlas y que estás usando `Java.Lang.Object.GetObject<T>()` para crear un contenedor administrado que se puede llamar a su alrededor.

 <a name="_Explicitly_Deleting_Local_References" />


### <a name="explicitly-deleting-local-references"></a>Eliminar explícitamente referencias locales

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) se usa para eliminar referencias locales. Una vez que se ha eliminado la referencia local, no puede utilizarse ya, por lo que se debe tener cuidado para asegurarse de que `JNIEnv.DeleteLocalRef` es lo último que hace con la referencia local.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

 <a name="_Wrapping_with_Java.Lang.Object" />


### <a name="wrapping-with-javalangobject"></a>Ajuste con Java.Lang.Object

`Java.Lang.Object` Proporciona un [Java.Lang.Object (identificador IntPtr, transferencia de JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) constructor que se puede usar para incluir una referencia JNI existente. El [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/) parámetro determina cómo el `IntPtr` parámetro debería ser tratado:

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash; creado `Java.Lang.Object` instancia creará una nueva referencia global de la `handle` parámetro, y `handle` no se ha modificado.
    El llamador es responsable de liberación `handle` , si es necesario.

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; creado `Java.Lang.Object` instancia creará una nueva referencia global de la `handle` parámetro, y `handle` se elimina con [JNIEnv.DeleteLocalRef ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . El llamador debe liberar no `handle` y no debe utilizar `handle` después de que el constructor termina de ejecutarse.

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; creado `Java.Lang.Object` instancia se ocupará de la propiedad de la `handle` parámetro. El llamador debe liberar no `handle` .


Puesto que los métodos de invocación de método JNI devuelven refs locales, `JniHandleOwnership.TransferLocalRef` se usan normalmente:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

La referencia global creada no se liberarán hasta que el `Java.Lang.Object` instancia es la recolección. Si es posible, elimine la instancia liberará la referencia global, lo que acelera la recolección de elementos:

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

 <a name="_Using_Java.Lang.Object.GetObject&lt;T&gt;()" />


### <a name="using-javalangobjectgetobjectlttgt"></a>Usar Java.Lang.Object.GetObject&lt;T&gt;)

`Java.Lang.Object` Proporciona un [Java.Lang.Object.GetObject&lt;T&gt;(identificador IntPtr, transferencia de JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) método que puede usarse para crear un contenedor invocable administrado del tipo especificado.

El tipo `T` debe cumplir los siguientes requisitos:

1.  `T` debe ser un tipo de referencia.

1.  `T` debe implementar la `IJavaObject` interfaz.

1.  Si `T` no es una clase abstracta o interfaz, a continuación, `T` deben proporcionar un constructor con los tipos de parámetro `(IntPtr,
    JniHandleOwnership)` .

1.  Si `T` es una clase abstracta o una interfaz, *debe* ser un *invocador* disponibles para `T` . Un invocador de es un tipo no abstracto que hereda `T` o implementa `T` , y tiene el mismo nombre que `T` con un sufijo invocador. Por ejemplo, si T es la interfaz `Java.Lang.IRunnable` , a continuación, el tipo de `Java.Lang.IRunnableInvoker` debe existir y debe contener el necesario `(IntPtr,
    JniHandleOwnership)` constructor.


Puesto que los métodos de invocación de método JNI devuelven refs locales, `JniHandleOwnership.TransferLocalRef` se usan normalmente:

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Buscar tipos de Java

Para buscar un campo o método de JNI, primero debe buscar el tipo declarativo para el campo o método. El [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)) método se usa para buscar tipos de Java. El parámetro de cadena es el *simplificado de referencia de tipo* o *referencia de tipo completo* para el tipo de Java. Consulte la [sección referencias de tipo JNI](#_JNI_Type_References) para obtener más información acerca de las referencias de tipo simplificado y completa.

Nota: a diferencia de los demás `JNIEnv` método que devuelve instancias de objeto, `FindClass` devuelve una referencia global, no una referencia local.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Campos de instancia

Campos se manipulan mediante *campo identificadores*. Id. de campo se obtienen a través de [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), lo que requiere la clase que el campo está definido en el nombre del campo y el [JNI tipo firma](#_JNI_Type_Signatures) del campo.

Id. de campo no es necesario que se libere y son válidos, siempre y cuando se carga el tipo de Java correspondiente. (Android no admite actualmente la descarga de clase.)

Hay dos conjuntos de métodos para manipular los campos de instancia: uno para leer campos de instancia y otro para la escritura de campos de instancia. Todos los conjuntos de métodos requieren un identificador de campo leer o escribir el valor del campo.

<a name="_Reading_Instance_Field_Values" />

### <a name="reading-instance-field-values"></a>Leer valores de campo de instancia

El conjunto de métodos para leer los valores de campo de instancia sigue el patrón de nomenclatura:

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
donde `*` es el tipo del campo:

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash; leer el valor de cualquier campo de instancia que no es un tipo de integrada, como `java.lang.Object` , matrices y tipos de interfaz. El valor devuelto es una referencia local JNI.

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash; leer el valor de `bool` campos de instancia.

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash; leer el valor de `sbyte` campos de instancia.

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash; leer el valor de `char` campos de instancia.

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash; leer el valor de `short` campos de instancia.

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash; leer el valor de `int` campos de instancia.

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash; leer el valor de `long` campos de instancia.

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash; leer el valor de `float` campos de instancia.

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash; leer el valor de `double` campos de instancia.



 <a name="_Writing_Instance_Field_Values" />


### <a name="writing-instance-field-values"></a>Escribiendo valores de campo de instancia

El conjunto de métodos para escribir valores de campo de instancia sigue el patrón de nomenclatura:

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

donde *tipo* es el tipo del campo:

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; escribir el valor de cualquier campo que no es un tipo de integrada, como `java.lang.Object` , matrices y tipos de interfaz. El `IntPtr` valor puede ser una referencia local JNI, referencia global JNI, una referencia global débil JNI, o `IntPtr.Zero` (para `null` ).

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

Los campos estáticos se manipulan mediante *campo identificadores*. Id. de campo se obtienen a través de [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/), lo que requiere la clase que el campo está definido en el nombre del campo y el [JNI tipo firma](#JNI%20Type%20Signatures) del campo.

Id. de campo no es necesario que se libere y son válidos, siempre y cuando se carga el tipo de Java correspondiente. (Android no admite actualmente la descarga de clase.)

Hay dos conjuntos de métodos para manipular los campos estáticos: uno para leer campos de instancia y otro para la escritura de campos de instancia. Todos los conjuntos de métodos requieren un identificador de campo leer o escribir el valor del campo.

<a name="_Reading_Static_Field_Values" />

### <a name="reading-static-field-values"></a>Lectura de los valores de campo estático

El conjunto de métodos para leer los valores de campo estático sigue el patrón de nomenclatura:

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

donde `*` es el tipo del campo:

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash; leer el valor de cualquier campo estático que no es un tipo de integrada, como `java.lang.Object` , matrices y tipos de interfaz. El valor devuelto es una referencia local JNI.

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash; leer el valor de `bool` campos estáticos.

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash; leer el valor de `sbyte` campos estáticos.

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash; leer el valor de `char` campos estáticos.

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash; leer el valor de `short` campos estáticos.

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash; leer el valor de `long` campos estáticos.

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash; leer el valor de `float` campos estáticos.

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash; leer el valor de `double` campos estáticos.


<a name="_Writing_Static_Field_Values" />

### <a name="writing-static-field-values"></a>Escribiendo valores de campo estático

El conjunto de métodos para escribir valores de campo estático sigue el patrón de nomenclatura:

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

donde *tipo* es el tipo del campo:

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; escribir el valor de cualquier campo estático que no es un tipo de integrada, como `java.lang.Object` , matrices y tipos de interfaz. El `IntPtr` valor puede ser una referencia local JNI, referencia global JNI, una referencia global débil JNI, o `IntPtr.Zero` (para `null` ).

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

Métodos de instancia se invocan mediante *método identificadores*. Método identificadores se obtienen a través de [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/), que requiere que el tipo que está definido el método en el nombre del método y el [JNI tipo firma](#_JNI_Type_Signatures) del método.

Id. de método no es necesario que se libere y son válidos, siempre y cuando se carga el tipo de Java correspondiente. (Android no admite actualmente la descarga de clase.)

Hay dos conjuntos de métodos para invocar métodos: uno para invocar métodos prácticamente y otro para invocar métodos de forma no virtual. Ambos conjuntos de métodos requieren un identificador de método invocar el método y no virtual invocación también requiere que especifique qué implementación de la clase se debe invocar.

Métodos de interfaz sólo se pueden buscar dentro del tipo declarativo; no se pueden buscar métodos que proceden de interfaces ampliada o se ha heredado. Vea las Interfaces de enlace posterior / section invocador de implementación para obtener más detalles.

Cualquier método declarado en la clase o cualquier clase base o interfaz implementada se puede buscar.

<a name="_Virtual_Method_Invocation" />

### <a name="virtual-method-invocation"></a>Invocación del método virtual

El conjunto de métodos para invocar métodos prácticamente sigue el patrón de nomenclatura:

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método.

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash; invocar un método que devuelve un tipo no integrada, como `java.lang.Object` , las matrices y las interfaces. El valor devuelto es una referencia local JNI.

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash; invocar un método que devuelve un `bool` valor.

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash; invocar un método que devuelve un `sbyte` valor.

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash; invocar un método que devuelve un `char` valor.

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash; invocar un método que devuelve un `short` valor.

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; invocar un método que devuelve un `long` valor.

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash; invocar un método que devuelve un `float` valor.

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash; invocar un método que devuelve un `double` valor.


<a name="_Non-virtual_Method_Invocation" />

### <a name="non-virtual-method-invocation"></a>Invocación del método no virtual

El conjunto de métodos para invocar métodos no prácticamente sigue el patrón de nomenclatura:

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método. La invocación del método virtual no se utiliza normalmente para invocar el método base de un método virtual.

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash; no prácticamente invocar un método que devuelve un tipo no integrada, como `java.lang.Object` , las matrices y las interfaces. El valor devuelto es una referencia local JNI.

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash; no prácticamente invocar un método que devuelve un `bool` valor.

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash; no prácticamente invocar un método que devuelve un `sbyte` valor.

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash; no prácticamente invocar un método que devuelve un `char` valor.

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash; no prácticamente invocar un método que devuelve un `short` valor.

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash; no prácticamente invocar un método que devuelve un `long` valor.

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash; no prácticamente invocar un método que devuelve un `float` valor.

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash; no prácticamente invocar un método que devuelve un `double` valor.


 <a name="_Static_Methods" />

## <a name="static-methods"></a>Métodos estáticos

Métodos estáticos se invocan a través de *método identificadores*. Método identificadores se obtienen a través de [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), que requiere que el tipo que está definido el método en el nombre del método y el [JNI tipo firma](#JNI%20Type%20Signatures) del método.

Id. de método no es necesario que se libere y son válidos, siempre y cuando se carga el tipo de Java correspondiente. (Android no admite actualmente la descarga de clase.)

 <a name="_Static_Method_Invocation" />


### <a name="static-method-invocation"></a>Invocación de métodos estáticos

El conjunto de métodos para invocar métodos prácticamente sigue el patrón de nomenclatura:

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

donde `*` es el tipo de valor devuelto del método.

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash; invocar un método estático que devuelve un tipo no integrada, como `java.lang.Object` , las matrices y las interfaces. El valor devuelto es una referencia local JNI.

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash; invocar un método estático que devuelve un `bool` valor.

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash; invocar un método estático que devuelve un `sbyte` valor.

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash; invocar un método estático que devuelve un `char` valor.

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash; invocar un método estático que devuelve un `short` valor.

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; invocar un método estático que devuelve un `long` valor.

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash; invocar un método estático que devuelve un `float` valor.

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash; invocar un método estático que devuelve un `double` valor.


 <a name="_JNI_Type_Signatures" />


## <a name="jni-type-signatures"></a>Signaturas de tipo JNI

[Las signaturas de tipo JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) son [las referencias de tipos de JNI](#_JNI_Type_References) (referencias de tipos aunque no simplificada), excepto en los métodos. Con los métodos, la firma de tipo de JNI es un paréntesis de apertura `'('`, seguido de las referencias de tipo para todos lo tipos se concatenan juntos (con sin comas separar o cualquier otra cosa), seguido por un paréntesis de cierre de parámetro `')'`, seguido de la referencia de tipo JNI del tipo de valor devuelto del método.

Por ejemplo, con el método de Java:

```java
long f(int n, String s, int[] array);
```

La firma de tipo JNI sería:

```csharp
(ILjava/lang/String;[I)J
```

En general, resulta *fuertemente* recomienda utilizar la `javap` comando para determinar las firmas JNI. Por ejemplo, la firma de tipo JNI de la [java.lang.Thread.State.valueOf(String)](http://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) método es "(Ljava/lang/String); Ljava/lang/Thread$ estado;", mientras que la JNI tipo de firma de la [ java.lang.Thread.State.values](http://developer.android.com/reference/java/lang/Thread.State.html#values) método es "() [Ljava/lang/Thread$ estado;". Esté atento a los puntos y comas finales; esas *son* forma parte de la firma de tipo JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Referencias de tipos JNI

Las referencias de tipos JNI son diferentes de las referencias de tipos de Java. No se puede utilizar nombres completos de tipo de Java como `java.lang.String` con JNI, debe usar en su lugar las variaciones de JNI `"java/lang/String"` o `"Ljava/lang/String;"`, según el contexto; vea a continuación para obtener más información.
Hay cuatro tipos de referencias de tipo JNI:

-  **built-in**
-  **simplified**
-  **type**
-  **array**


 <a name="_Built-in_Type_References" />


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
-  `"V"` para `void` tipos de valor devuelto de método.


 <a name="_Simplified_Type_References" />


### <a name="simplified-type-references"></a>Referencias de tipos simplificada

Solo se pueden utilizar referencias de tipo simplificado en [JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)).
Hay dos maneras para obtener una referencia de tipo simplificada:

1.  En un nombre completo de Java, reemplace cada `'.'` en el nombre del paquete y antes del nombre de tipo con `'/'` y cada `'.'` dentro de un nombre de tipo con `'$'` .

1.  Leer la salida de `'unzip -l android.jar | grep JavaName'` .


Cualquiera de los dos se producirá en el tipo de Java [java.lang.Thread.State](http://developer.android.com/reference/java/lang/Thread.State.html) que se asigna a la referencia de tipo simplificado `java/lang/Thread$State`.

<a name="_Type_References" />

### <a name="type-references"></a>Referencias de tipos

Una referencia de tipo es una referencia de tipo integrado o una referencia de tipo simplificada con un `'L'` prefijo y un `';'` sufijo. Para el tipo de Java [java.lang.String](http://developer.android.com/reference/java/lang/String.html), la referencia de tipo simplificado es `"java/lang/String"`, mientras que la referencia de tipo `"Ljava/lang/String;"`.

Las referencias de tipos se utilizan con las referencias de tipos de matriz y con firmas de JNI.

Es un método adicional para obtener una referencia de tipo, lea la salida de `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
Dependiendo del tipo implicados, puede usar una declaración de constructor o método devuelve el tipo para determinar el nombre JNI. Por ejemplo:

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

`Thread.State` es un tipo de enumeración de Java, por lo que podemos usar la firma de la `valueOf` método para determinar que la referencia de tipo es Ljava/lang/Thread$ estado;.


<a name="_Array_Type_References" />

### <a name="array-type-references"></a>Referencias de tipos de matriz

Las referencias de tipos de matriz son `'['` como prefijo a una referencia de tipo JNI.
No se pueden usar referencias de tipo simplificado al especificar matrices.

Por ejemplo, `int[]` es `"[I"`, `int[][]` es `"[[I"`, y `java.lang.Object[]` es `"[Ljava/lang/Object;"`.

<a name="_Java_Generics_and_Type_Erasure" />


## <a name="java-generics-and-type-erasure"></a>Genéricos de Java y borrado de tipos

*La mayoría* de los casos, tal como se muestra a través de JNI, genéricos de Java *no existen*.
Hay algunos "arrugas", pero esas arrugas están en cómo Java interactúa con los genéricos, no con cómo JNI busca y miembros genéricos, invoca.

No hay ninguna diferencia entre un tipo genérico o miembro y un tipo no genérico o un miembro al interactuar a través de JNI. Por ejemplo, el tipo genérico [java.lang.Class&lt;T&gt; ](http://developer.android.com/reference/java/lang/Class.html) también es el tipo genérico "sin formato" `java.lang.Class`, ambos de los cuales tienen la misma referencia de tipo simplificado, `"java/lang/Class"`.

<a name="Java_Native_Interface_Support" />

## <a name="java-native-interface-support"></a>Compatibilidad con la interfaz nativa de Java

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) es contenedor administrado para el Java JNI (interfaz nativa). JNI funciones se declaran dentro de la [Java Native Interface Specification](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), aunque han cambiado los métodos para quitar el explícita `JNIEnv*` parámetro y `IntPtr` se utiliza en lugar de `jobject`, `jclass`, `jmethodID`, etcetera. Por ejemplo, considere la [JNI NewObject función](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Esto se expone como la [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) método:

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Traducir entre las dos llamadas es bastante sencillo. En C deberá:

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

El equivalente en C# sería:

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Una vez que tenga una instancia de objeto de Java que se mantienen en un objeto IntPtr, probablemente deseará hacer algo con él. Puede usar los métodos de JNIEnv como [ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) a tal efecto, pero si ya hay un contenedor de C# analógico, entonces deberá construir un contenedor a través de la referencia JNI. Puede hacer por lo que a través del [Extensions.JavaCast <t>()</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/) método de extensión:

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

Además, todas las funciones JNI se han modificado mediante la eliminación de la `JNIEnv*` parámetro está presente en cada función JNI.

<a name="_Summary" />

## <a name="summary"></a>Resumen

Tratar directamente con JNI es una experiencia terrible que debe evitarse a toda costa. Por desgracia, no siempre es evitables; Espero que esta guía le proporcionará ayuda al llegar a los casos de Java independientes con Mono para Android.


## <a name="related-links"></a>Vínculos relacionados

- [SanityTests (ejemplo)](https://developer.xamarin.com/samples/SanityTests/)
- [Especificación de la interfaz nativa de Java](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Funciones de la interfaz nativa de Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
