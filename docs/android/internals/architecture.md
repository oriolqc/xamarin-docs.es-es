---
title: Arquitectura
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: ea66cda0e2a1935a430c064c9cebd4134d295729
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954261"
---
# <a name="architecture"></a>Arquitectura

Las aplicaciones de Xamarin.Android se ejecutan dentro del entorno de ejecución Mono.
Esta ejecución entorno ejecuciones por en paralelo con la máquina virtual de Android en tiempo de ejecución (ART). Ambos entornos en tiempo de ejecución se ejecutan en el kernel de Linux y exponen varias API para el código de usuario que permite a los desarrolladores tener acceso al sistema subyacente. El tiempo de ejecución Mono se escribe en el lenguaje C.

Puede utilizar el [sistema](xref:System), [System.IO](xref:System.IO), [System.Net](xref:System.Net) y el resto de .NET de la clase bibliotecas para tener acceso a las instalaciones de sistema operativo Linux subyacentes.

En Android, la mayoría de las instalaciones del sistema, como Audio, gráficos, OpenGL y telefonía no están disponibles directamente a las aplicaciones nativas, solo se exponen a través de las API de Android en tiempo de ejecución Java que residen en uno de los [Java](https://developer.xamarin.com/api/namespace/Java.Lang/). * los espacios de nombres o el [Android](https://developer.xamarin.com/api/namespace/Android/). * espacios de nombres. La arquitectura es más o menos similar al siguiente:

[![Diagrama de Mono y material gráfico encima el kernel y por debajo de .NET/Java + enlaces](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Los desarrolladores de Xamarin.Android, tener acceso a las distintas características en el sistema operativo mediante una llamada a las API de .NET que saben (para acceso de bajo nivel) o mediante las clases que se exponen en los espacios de nombres Android que proporciona un puente a las API de Java que se exponen mediante el tiempo de ejecución de Android.

Para obtener más información sobre cómo se comunican las clases de Android con las clases de Android en tiempo de ejecución, consulte el [diseño de API](~/android/internals/api-design.md) documento.


## <a name="application-packages"></a>Paquetes de aplicación

Los paquetes de aplicación de Android son contenedores ZIP con una *.apk* la extensión de archivo. Paquetes de aplicación de Xamarin.Android tienen la misma estructura y el diseño de paquetes de Android normal, con las siguientes adiciones:

-   Los ensamblados de aplicación (que contiene IL) son *almacenados* sin comprimir en el *ensamblados* carpeta. Durante el proceso de inicio en la versión se basa el *.apk* es *mmap()* ed en el proceso y los ensamblados se cargan desde la memoria. Esto permite inicio de la aplicación más rápido, como los ensamblados no es necesario que deben extraerse antes de la ejecución.  
-   *Nota:* Información de ubicación del ensamblado como [Assembly.Location](xref:System.Reflection.Assembly.Location) y [Assembly.CodeBase](xref:System.Reflection.Assembly.CodeBase)
    *no se puede basar* en las compilaciones de versión. No existen como entradas del sistema de archivos distintos, y no tienen que ninguna ubicación utilizable.


-   Las bibliotecas nativas que contiene el tiempo de ejecución Mono están presentes en el *.apk* . Una aplicación de Xamarin.Android debe contener las bibliotecas nativas para las arquitecturas de Android deseada de destino, por ejemplo, *armeabi* , *armeabi-v7a* , *x86* . Las aplicaciones de Xamarin.Android no se pueden ejecutar en una plataforma a menos que contenga las bibliotecas en tiempo de ejecución adecuado.


Las aplicaciones de Xamarin.Android también contienen *Android contenedores RCW* para permitir que Android llamar a código administrado.



## <a name="android-callable-wrappers"></a>Contenedores que se pueden llamar de Android

- **Contenedores invocables Android** son un [JNI](https://en.wikipedia.org/wiki/Java_Native_Interface) puente que se usa cuando el tiempo de ejecución de Android necesita invocar código administrado. Contenedores invocables Android son métodos virtuales se pueden invalidar y se pueden implementar interfaces de Java. Consulte la [Java Integration Overview](~/android/platform/java-integration/index.md) doc para obtener más información.


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Administra los contenedores RCW

Contenedores invocables administrados son un puente JNI que se usa cuando necesita código administrado para invocar código de Android y proporcionar soporte técnico para reemplazar los métodos virtuales e implementar interfaces de Java. Toda la [Android](https://developer.xamarin.com/api/namespace/Android/). * y espacios de nombres relacionados son contenedores RCW administrados generados a través de [enlace .jar](~/android/platform/binding-java-library/index.md).
Contenedores CCW administrados son responsables de la conversión entre tipos administrados y Android e invocar los métodos subyacentes de la plataforma Android a través de JNI.

Cada uno creado administrado contenedor CCW contiene una referencia global de Java, que es accesible a través de la [Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) propiedad. Se usan referencias globales para proporcionar la asignación entre las instancias de Java y las instancias administradas. Referencias globales son un recurso limitado: los emuladores permiten 2000 solo referencias globales que existen simultáneamente, aunque la mayoría del hardware permite 52.000 más referencias globales que existen en un momento.

Para realizar un seguimiento cuando se crean y destruyen referencias globales, puede establecer el [debug.mono.log](~/android/troubleshooting/index.md) propiedad del sistema para contener [gref](~/android/troubleshooting/index.md).

Referencias globales se pueden liberar explícitamente mediante una llamada a [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) en el contenedor RCW administrado. Esto quitará la asignación entre la instancia de Java y la instancia administrada y permite que la instancia de Java que se recopilen. Si vuelve a se tiene acceso a la instancia de Java desde el código administrado, se creará un nuevo contenedor administrado para él.

Debe tener cuidado cuando elimine los contenedores RCW administrada si la instancia se puede compartir accidentalmente entre subprocesos, como la eliminación de la instancia afectará a las referencias desde cualquier otro subproceso. Para la máxima seguridad, sólo `Dispose()` de instancias que se han asignado a través de `new` *o* desde métodos que *saber* siempre asignar nuevas instancias y las instancias no almacenado en caché que se pueden hacer que accidental instancia compartida entre subprocesos.



## <a name="managed-callable-wrapper-subclasses"></a>Administra las subclases de contenedor CCW

Contenedor CCW administrado subclases son donde puede residir toda la lógica específica de la aplicación "interesante". Estos incluyen personalizado [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) subclases (como el [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) tipo en la plantilla de proyecto predeterminada). (En concreto, estos son cualquiera *Java.Lang.Object* subclases que hacen *no* contienen un [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado o [ RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) es *false*, que es el valor predeterminado.)

Administrado como contenedores RCW, administra las subclases de contenedor CCW también contienen una referencia global, accesible a través de la [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) propiedad. Igual que con los contenedores RCW administrados, se pueden liberar explícitamente referencias globales mediante una llamada a [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/).
A diferencia de los contenedores RCW administrados, *mucho cuidado* debe realizarse antes de eliminar las instancias de este tipo, como *Dispose()*- ing de la instancia se interrumpirá la asignación entre la instancia de Java (una instancia de un Contenedor CCW Android) y la instancia administrada.


### <a name="java-activation"></a>Activación de Java

Cuando un [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) se crea desde Java, el constructor ACW hará que el constructor correspondiente de C# que se debe invocar. Por ejemplo, el ACW para *MainActivity* contendrá un constructor predeterminado que se invocará *MainActivity*del constructor predeterminado. (Esto se realiza mediante el *TypeManager.Activate()* llamar dentro de los constructores ACW.)

Hay una firma de otro constructor tiene importancia: el *(IntPtr, JniHandleOwnership)* constructor. El *(IntPtr, JniHandleOwnership)* constructor se invoca cuando un objeto de Java se expone al código administrado y un contenedor administrado debe crearse para administrar el identificador JNI. Normalmente, esto se hace automáticamente.

Hay dos escenarios en los que el *(IntPtr, JniHandleOwnership)* constructor se debe proporcionar manualmente en una subclase de contenedor CCW administrados:

1. [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) es una subclase. *Aplicación* es especial; el valor predeterminado *aplicación* constructor le *nunca* se invoca y el [(IntPtr, JniHandleOwnership) en su lugar se debe proporcionar un constructor ](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Invocación del método virtual desde un constructor de clase base.


Tenga en cuenta que (2) es una abstracción con fugas. En Java, como en C#, las llamadas a métodos virtuales desde un constructor invocan siempre la implementación más derivada del método. Por ejemplo, el [TextView (contexto, AttributeSet, int) constructor](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/) invoca el método virtual [TextView.getDefaultMovementMethod()](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), que está enlazado como el [ Propiedad TextView.DefaultMovementMethod](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/).
Por lo tanto, si un tipo [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) tuviera que (1) [subclase TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [invalidar TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)y (3) [activar una instancia de la que clase a través de XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) invalidado *DefaultMovementMethod* sería necesario invocar una propiedad antes de que el constructor ACW tenía una oportunidad de ejecutarse y produciría antes de la C# tenido la oportunidad de constructor ejecutar.

Esto es compatible con una instancia LogTextBox a través de la [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) constructor cuando entra por primera vez la instancia ACW LogTextBox código administrado y, a continuación, invocar el [ LogTextBox (contexto, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) constructor *en la misma instancia* cuando se ejecuta el constructor ACW.

Orden de los eventos:

1.  Diseño XML se carga en un [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2.  El gráfico de objetos de diseño crea una instancia de Android y crea una instancia de *monodroid.apidemo.LogTextBox* , el ACW para *LogTextBox* .

3.  El *monodroid.apidemo.LogTextBox* constructor se ejecuta el [android.widget.TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) constructor.

4.  El *TextView* invoca el constructor *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* .

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* invoca *LogTextBox.n_getDefaultMovementMethod()* , que invoca *TextView.n_GetDefaultMovementMethod()* , que invoca [Java.Lang.Object.GetObject&lt;TextView&gt; (controlan, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;()* comprueba si ya hay un C# correspondiente instancia para *controlar* . Si no existe, se devuelve. En este escenario, no hay, lo *Object.GetObject&lt;T&gt;()* debe crear uno.

7.  *Object.GetObject&lt;T&gt;()* busca el *LogTextBox (IntPtr, JniHandleOwneship)* constructor, lo invoca, crea una asignación entre *controlar* y instancia creada y devuelve la instancia creada.

8.  *TextView.n_GetDefaultMovementMethod()* invoca el *LogTextBox.DefaultMovementMethod* captador de propiedad.

9.  El control vuelve a la *android.widget.TextView* constructor, que finaliza la ejecución.

10. El *monodroid.apidemo.LogTextBox* constructor se ejecuta, invocar *TypeManager.Activate()* .

11. El *LogTextBox (contexto, IAttributeSet, int)* constructor ejecuta *en la misma instancia que creó en (7)* .

12. Si (IntPtr, JniHandleOwnership) no se encuentra el constructor y, después, se producirá un System.MissingMethodException](xref:System.MissingMethodException).

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Llamadas de forma prematura Dispose()

Hay una asignación entre un identificador JNI y la instancia correspondiente de C#. Java.Lang.Object.Dispose() interrumpe esta asignación. Si un identificador JNI introduce código administrado después de la asignación se ha interrumpido, parece que la activación de Java y el *(IntPtr, JniHandleOwnership)* constructor estará activado para y se invoca. Si no existe el constructor, se producirá una excepción.

Por ejemplo, dada la subclase administrada que se puede llamar Wraper siguiente:

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

Si se crea una instancia, Dispose() del mismo y hacer que el contenedor RCW administrados volver a crearse:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

Se perderá el programa:

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

Si la subclase contienen un *(IntPtr, JniHandleOwnership)* constructor, un *nuevo* se creará la instancia del tipo. Como resultado, la instancia aparecerán "perder" todos los datos de instancia, ya que es una nueva instancia. (Tenga en cuenta que el valor es null.)

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Solo *Dispose()* de administra las subclases de contenedor RCW cuando se sabe que ya no se usará el objeto de Java o la subclase no contiene ningún dato de instancia y un *(IntPtr, JniHandleOwnership)* se ha proporcionado el constructor.



## <a name="application-startup"></a>Inicio de la aplicación

Cuando una actividad, servicio, etc. se inicia, Android comprobará primero si ya es un proceso en ejecución para hospedar el servicio/actividad/etcetera. Si este proceso no existe, se creará un nuevo proceso, el [AndroidManifest.xml](https://developer.android.com/guide/topics/manifest/manifest-intro.html) es de lectura y el tipo especificado en el [ /manifest/application/@android:name ](https://developer.android.com/guide/topics/manifest/application-element.html#nm) se carga y se crea una instancia de atributo. A continuación, todos los tipos especificados por el [ /manifest/application/provider/@android:name ](https://developer.android.com/guide/topics/manifest/provider-element.html#nm) se crean instancias de los valores de atributo y tiene sus [ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/) método invocado. Enlaces de Xamarin.Android en esto agregando un *mono. MonoRuntimeProvider* *ContentProvider* a AndroidManifest.xml durante el proceso de compilación. El *mono. MonoRuntimeProvider.attachInfo()* método es responsable de cargar el tiempo de ejecución Mono en el proceso.
Cualquier intento de usar Mono antes de este punto se producirá un error. ( *Nota*: Por ello los tipos que son subclase [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) debe proporcionar un [(IntPtr, JniHandleOwnership) constructor](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), tal y como se crea la instancia de la aplicación antes de que pueda inicializarse con Mono.)

Cuando se haya completado el proceso de inicialización, `AndroidManifest.xml` se consulta para buscar el nombre de clase de la actividad y servicio de los mismos para iniciar. Por ejemplo, el [ /manifest/application/activity/@android:name atributo](https://developer.android.com/guide/topics/manifest/activity-element.html#nm) se usa para determinar el nombre de una actividad para cargar. Para las actividades, este tipo debe heredar [android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/).
El tipo especificado se carga mediante [Class.forName ()](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (que requiere que el tipo sea un Java escriba, por lo tanto, los contenedores Android invocables), a continuación, crea una instancia. Creación de una instancia de contenedor CCW Android desencadenará la creación de una instancia del tipo de C# correspondiente. Android, a continuación, se invocará [Activity.onCreate(Bundle)](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , lo que hará que el correspondiente [Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) que se invocará, y está fuera de control.
