---
title: Arquitectura
ms.topic: article
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 9579acc6c070bf692b0db1bd444a31c9ea4aa7ca
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="architecture"></a>Arquitectura

Aplicaciones de Xamarin.Android se ejecutan en el entorno de ejecución Mono.
Esta ejecución entorno se ejecuta en paralelo con la máquina virtual de Android en tiempo de ejecución (prediseñadas). Ambos entornos en tiempo de ejecución se ejecutan sobre el kernel de Linux y exponen varias API en el código de usuario que permite a los desarrolladores tener acceso al sistema subyacente. El tiempo de ejecución Mono se escribe en el lenguaje C.

Puede utilizar el [System](http://msdn.microsoft.com/en-us/library/system.aspx), [System.IO](http://msdn.microsoft.com/en-us/library/system.io.aspx), [System.Net](http://msdn.microsoft.com/en-us/library/system.net.aspx) y el resto de .NET de la clase bibliotecas para tener acceso a las funciones de sistema operativo Linux subyacentes.

En Android, la mayoría de las funciones de sistema como Audio, gráficos, OpenGL y telefonía no está disponible directamente a las aplicaciones nativas, solo se exponen a través de la API de Java en tiempo de ejecución Android que residen en uno de los [Java](https://developer.xamarin.com/api/namespace/Java.Lang/). * espacios de nombres o la [Android](https://developer.xamarin.com/api/namespace/Android/). * espacios de nombres. La arquitectura de aproximadamente es similar al siguiente:

[![Diagrama de Mono y material gráfico por encima del kernel y por debajo de .NET/Java + enlaces](architecture-images/architecture1.png)](architecture-images/architecture1.png)

Xamarin.Android a los desarrolladores tener acceso a las distintas características en el sistema operativo por llamar a las API de .NET que conocen (para acceso de bajo nivel) o usando las clases que se exponen en los espacios de nombres Android que proporciona un puente a las API de Java que se exponen por el tiempo de ejecución Android.

Para obtener más información sobre cómo se comunican las clases Android con las clases de Android en tiempo de ejecución, consulte el [diseño de la API](~/android/internals/api-design.md) documento.

<a name="Application_Packages" />

## <a name="application-packages"></a>Paquetes de aplicación

Paquetes de aplicaciones Android son contenedores ZIP con un *.apk* la extensión de archivo. Paquetes de aplicación de Xamarin.Android tienen la misma estructura y diseño que paquetes Android normales, con las siguientes adiciones:

-   Los ensamblados de aplicación (que contiene IL) son *almacenado* sin comprimir en la *ensamblados* carpeta. Durante el proceso de inicio en versión compila el *.apk* es *mmap()* ed en el proceso y los ensamblados se cargan desde la memoria. Esto permite el inicio de la aplicación más rápido, como ensamblados no es necesario que deben extraerse antes de la ejecución. - *Nota:* información de ubicación del ensamblado como [Assembly.Location](https://developer.xamarin.com/api/property/System.Reflection.Assembly.Location/) y [Assembly.CodeBase](https://developer.xamarin.com/api/property/System.Reflection.Assembly.CodeBase/)
    *no se puede confiar en* de versión compilaciones. No existen como entradas del sistema de archivos distintos, y no tienen ninguna ubicación utilizable.


-   Bibliotecas nativas que contiene el tiempo de ejecución Mono están presentes en el *.apk* . Una aplicación de Xamarin.Android debe contener bibliotecas nativas de las arquitecturas Android deseado de destino, por ejemplo, *armeabi* , *armeabi v7a* , *x86* . Aplicaciones de Xamarin.Android no se pueden ejecutar en una plataforma a menos que contenga las bibliotecas de tiempo de ejecución adecuada.


Las aplicaciones de Xamarin.Android también contienen *Android Callable Wrappers* para permitir Android llamar a código administrado.


<a name="Android_Callable_Wrappers" />

## <a name="android-callable-wrappers"></a>Android contenedores CCW

- **Contenedores CCW Android** son un [JNI](http://en.wikipedia.org/wiki/Java_Native_Interface) puente que se usa cuando el tiempo de ejecución Android es necesario para llamar a código administrado. Contenedores CCW Android son métodos virtuales puede reemplazarse y pueden implementar interfaces de Java. Consulte la [descripción de la integración de Java](~/android/platform/java-integration/index.md) doc. para obtener más información.


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Administrar contenedores CCW

Contenedores invocables administrados son un puente JNI que se usa siempre al código administrado debe invocar código Android y ofrece soporte para reemplazar los métodos virtuales e implementación de interfaces de Java. Toda la [Android](https://developer.xamarin.com/api/namespace/Android/). * y espacios de nombres relacionados son contenedores CCW administrados generados a través de [enlace .jar](~/android/platform/binding-java-library/index.md).
Contenedores CCW administrados están responsables de convertir entre tipos administrados y Android e invocar los métodos de la plataforma Android subyacentes a través de JNI.

Los crean administrado contenedor CCW contiene una referencia global de Java, que es accesible a través del [Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) propiedad. Referencias globales se utilizan para proporcionar la asignación entre las instancias de Java y las instancias administradas. Referencias globales son un recurso limitado: los emuladores permiten solo 2000 referencias globales que existen simultáneamente, aunque la mayor parte del hardware permite 52.000 más referencias globales que existen en un momento.

Para realizar el seguimiento cuando se crean y destruyen referencias globales, puede establecer la [debug.mono.log](~/android/troubleshooting/index.md) propiedad del sistema para contener [gref](~/android/troubleshooting/index.md).

Referencias globales se pueden liberar explícitamente mediante una llamada a [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) en el contenedor administrado. Esto quitará la asignación entre la instancia de Java y la instancia administrada y permite que la instancia de Java que se recopilen. Si vuelve a se tiene acceso a la instancia de Java desde el código administrado, se creará un nuevo contenedor invocable administrado para él.

Se deben tomar precauciones al desechar administrados Callable Wrappers si la instancia puede compartirse accidentalmente entre subprocesos, como la eliminación de la instancia afectará a las referencias de todos los demás subprocesos. Por motivos de seguridad máximo, sólo `Dispose()` de instancias que se han asignado a través de `new` *o* de métodos que se puede *saber* siempre asignar nuevas instancias e instancias no almacenadas en caché que se pueden hacer uso compartido entre los subprocesos de instancias accidental.


<a name="Managed_Callable_Wrapper_Subclasses" />

## <a name="managed-callable-wrapper-subclasses"></a>Administra las subclases de contenedor CCW

Las subclases de contenedor CCW administrado son donde puede residir toda la lógica específica de la aplicación "interesante". Puede tratarse de personalizado [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) subclases (como el [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) tipo en la plantilla de proyecto predeterminada). (En concreto, se trata de cualquier *Java.Lang.Object* subclases que realizan *no* contienen un [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) atributo personalizado o [ RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) es *false*, que es el valor predeterminado.)

Como administrar contenedores RCW, administra las subclases de contenedor CCW también contienen una referencia global, accesible a través de la [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) propiedad. Igual que con los contenedores CCW administrados, referencias globales se pueden liberar explícitamente mediante una llamada a [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/).
A diferencia de los contenedores CCW administrados, *mucho cuidado* debe tener cuidado antes de la eliminación de instancias de este tipo, como *Dispose()*realizando una operación de la instancia interrumpirá la asignación entre la instancia de Java (una instancia de un Android Callable Wrapper) y la instancia administrada.

<a name="Java_Activation" />

### <a name="java-activation"></a>Activación de Java

Cuando un [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) se crea a partir de Java, el constructor ACW hará que el constructor correspondiente de C# que se debe invocar. Por ejemplo, el ACW para *MainActivity* contendrá un constructor predeterminado que se va a invocar *MainActivity*del constructor predeterminado. (Esto se realiza a través de la *TypeManager.Activate()* llamarse dentro de los constructores ACW.)

Hay una firma de otro constructor de consecuencia: el *(IntPtr, JniHandleOwnership)* constructor. El *(IntPtr, JniHandleOwnership)* constructor se invoca cuando un objeto de Java se expone al código administrado y un contenedor administrado debe crearse para administrar el identificador JNI. Normalmente esto se hace automáticamente.

Hay dos escenarios en los que la *(IntPtr, JniHandleOwnership)* constructor se debe proporcionar manualmente en una subclase de contenedor CCW administrados:

1. [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) es una subclase. *Aplicación* es especial; el valor predeterminado *aplicación* constructor le *nunca* invocará y el [(IntPtr, JniHandleOwnership) en su lugar, se debe proporcionar un constructor ](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Invocación de método virtual desde un constructor de clase base.


Tenga en cuenta que (2) es una abstracción fugas. En Java, como en C#, las llamadas a métodos virtuales desde un constructor siempre invocan la implementación más derivada del método. Por ejemplo, el [constructor TextView (contexto, AttributeSet, int)](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/) invoca el método virtual [TextView.getDefaultMovementMethod()](http://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), que se enlaza como el [ Propiedad TextView.DefaultMovementMethod](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/).
Por lo tanto, si un tipo [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) fuera a (1) [subclase TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [invalidar TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)y (3) [activar una instancia de ese clase a través de XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) reemplazados *DefaultMovementMethod* sería necesario invocar una propiedad antes de que el constructor ACW tenía una oportunidad de ejecutarse y produciría antes de que el constructor de C# tenía una oportunidad de ejecutarse.

Esto se admite la creación de instancias de una instancia LogTextBox a través de la [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) constructor cuando entra por primera vez la instancia ACW LogTextBox código administrado y, a continuación, invocar el [ LogTextBox (contexto, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) constructor *en la misma instancia* cuando se ejecuta el constructor ACW.

Orden de los eventos:

1.  Diseño XML se carga en un [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2.  Android crea el gráfico de objetos de diseño y crea una instancia de *monodroid.apidemo.LogTextBox* , el ACW para *LogTextBox* .

3.  El *monodroid.apidemo.LogTextBox* constructor se ejecuta el [android.widget.TextView](http://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) constructor.

4.  El *TextView* invoca el constructor *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* .

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* invoca *LogTextBox.n_getDefaultMovementMethod()* , que invoca *TextView.n_GetDefaultMovementMethod()* , que se invoca [Java.Lang.Object.GetObject&lt;TextView&gt; (controlar, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;()* comprueba si ya hay de C# correspondiente instancia para *controlar* . Si no existe, se devuelve. En este escenario, no hay, por lo que *Object.GetObject&lt;T&gt;()* deberá crear uno.

7.  *Object.GetObject&lt;T&gt;()* busca el *LogTextBox (IntPtr, JniHandleOwneship)* constructor, se invoca, crea una asignación entre *controlar* y la instancia creada y devuelve la instancia creada.

8.  *TextView.n_GetDefaultMovementMethod()* , se invoca el *LogTextBox.DefaultMovementMethod* captador de propiedad.

9.  El control vuelve a la *android.widget.TextView* constructor, que finaliza la ejecución.

10. El *monodroid.apidemo.LogTextBox* constructor se ejecuta, invocar *TypeManager.Activate()* .

11. El *LogTextBox (contexto, IAttributeSet, int)* constructor ejecuta *en la misma instancia que se creó en (7)* .

12. ...


Si (IntPtr, JniHandleOwnership) no se encuentra el constructor, un [System.MissingMethodException](https://developer.xamarin.com/api/type/System.MissingMethodException/) se iniciará.


<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Dispose() prematura llamadas

No hay una asignación entre un identificador JNI y la instancia correspondiente de C#. Java.Lang.Object.Dispose() interrumpe esta asignación. Si un identificador JNI introduce código administrado después de la asignación se ha interrumpido, parece que la activación de Java y la *(IntPtr, JniHandleOwnership)* constructor se comprueba y se invoca. Si el constructor no existe, se producirá una excepción.

Por ejemplo, dada la subclase administrado que se puede llamar Wraper siguiente:

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

Si se crea una instancia, Dispose() del mismo y hacer que el contenedor invocable administrados se vuelva a crear:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

Finalizan el programa:

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

Si la subclase contienen un *(IntPtr, JniHandleOwnership)* constructor, un *nueva* se creará la instancia del tipo. Como resultado, la instancia aparecerá "pierdan" todos los datos de instancia, ya que es una nueva instancia. (Tenga en cuenta que el valor es null).

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Solo *Dispose()* de administra las subclases de contenedor CCW cuando sepa que ya no se utilizará el objeto de Java o la subclase no contiene ningún dato de instancia y un *(IntPtr, JniHandleOwnership)* se ha proporcionado el constructor.


<a name="Application_Startup" />

## <a name="application-startup"></a>Inicio de la aplicación

Cuando una actividad, el servicio, etc. se inicia, Android comprobará primero si ya hay un proceso en ejecución para hospedar la actividad/service/etcetera. Si no existe tal proceso existe, se creará un nuevo proceso, el [AndroidManifest.xml](http://developer.android.com/guide/topics/manifest/manifest-intro.html) es de lectura y el tipo especificado en el [ /manifest/application/@android:name ](http://developer.android.com/guide/topics/manifest/application-element.html#nm) atributo se cargan y se crea una instancia. Siguiente, todos los tipos especificados por la [ /manifest/application/provider/@android:name ](http://developer.android.com/guide/topics/manifest/provider-element.html#nm) valores de atributo se crean instancias y tienen sus [ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/) método invocado. Xamarin.Android enlaces esto agregando un *mono. MonoRuntimeProvider* *ContentProvider* a AndroidManifest.xml durante el proceso de compilación. El *mono. MonoRuntimeProvider.attachInfo()* método es responsable de cargar el runtime Mono en el proceso.
Se producirá un error si se intenta usar Mono antes de este punto. ( *Nota*: se trata de por qué tipos qué subclase [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) necesita proporcionar un [(IntPtr, JniHandleOwnership) constructor](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), ya que es la instancia de aplicación creado antes de que se puede inicializar Mono.)

Cuando haya completado el proceso de inicialización, `AndroidManifest.xml` se consulta para buscar el nombre de clase de la actividad/service/etc. para iniciar. Por ejemplo, el [ /manifest/application/activity/@android:name atributo](http://developer.android.com/guide/topics/manifest/activity-element.html#nm) se usa para determinar el nombre de una actividad para cargar. Actividades de este tipo debe heredar [android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/).
El tipo especificado se carga mediante [Class.forName ()](http://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (que requiere que el tipo sea un Java escriba, por lo tanto, los contenedores CCW Android), a continuación, crea una instancia. Creación de una instancia de Android Callable Wrapper desencadenará la creación de una instancia del tipo de C# correspondiente. Android, a continuación, se invocará [Activity.onCreate(Bundle)](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , lo que hará que el correspondiente [Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) que se debe invocar, y está desactivada para la carreras.
