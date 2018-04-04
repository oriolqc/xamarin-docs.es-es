---
title: recolección de elementos no utilizados
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: 49bb340edcad0c5ce39a2d9db6da72d488a114b1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="garbage-collection"></a>recolección de elementos no utilizados

Xamarin.Android usa de Mono [recolector de elementos no utilizados de generación Simple](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/). Se trata de un recopilador de elementos no utilizados de barrido de marca con dos generaciones y un *el espacio de objetos grandes*, con dos tipos de colecciones: 

-   Colecciones secundarias (recopila Gen0 montón) 
-   Colecciones principales (recopila Gen1 y objetos grandes espacio montones). 

> [!NOTE]
> En ausencia de una colección explícita a través de [GC. Collect()](xref:System.GC.Collect) colecciones son *a petición*, basándose en las asignaciones del montón. *Esto no es un sistema de recuento de referencias*; objetos *no se recopilarán en cuanto no haya referencias pendientes*, o cuando se cerró un ámbito. El catálogo global se ejecutará cuando el montón secundario ha quedado sin memoria para las asignaciones de nuevo. Si no hay ninguna asignación, no se ejecutará.


Las colecciones secundarias son barato y frecuentes y se utilizan para recopilar los objetos muertos y asignados recientemente. Las colecciones secundarias se realizan después de cada MB algunos de los objetos asignados. Las colecciones secundarias pueden realizarse manualmente mediante una llamada a [GC. Recopilar (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Colecciones principales son caros y menos frecuente y se utilizan para recuperar todos los objetos muertos. Colecciones importantes se realizan una vez que se agota la memoria para el tamaño actual del montón (antes de cambiar el tamaño del montón). Principales colecciones pueden realizarse manualmente mediante una llamada a [GC. Recopilar ()](xref:System.GC.Collect) o mediante una llamada a [GC. Recopilar (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) con el argumento [GC. MaxGeneration](xref:System.GC.MaxGeneration). 



## <a name="cross-vm-object-collections"></a>Colecciones de objetos entre VM

Hay tres categorías de tipos de objeto.

-   **Objetos administrados**: tipos que realizan *no* heredarlo [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) , por ejemplo, [System.String](xref:System.String). 
    Se recopilan normalmente por el catálogo global. 

-   **Objetos de Java**: tipos de Java que están presentes en tiempo de ejecución Android VM pero no se exponen a la máquina virtual de Mono. Se trata de una tarea aburridas y no ofrece ninguna otra explicación. Se recopilan normalmente por el tiempo de ejecución Android máquina virtual. 

-   **Del mismo nivel objetos**: los tipos que implementan [IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) , por ejemplo, todos los [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) y [Java.Lang.Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/) subclases. Las instancias de estos tipos tienen dos "halfs" un *administrado del mismo nivel* y un *punto native*. El homólogo administrado es una instancia de la clase de C#. El nivel nativo es una instancia de una clase de Java en tiempo de ejecución Android VM y C# [IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) propiedad contiene una referencia global JNI al par nativo. 


Hay dos tipos de elementos del mismo nivel nativo:

-   **Pares de Framework** : tipos de Java "Normal" que no sabe absolutamente nada de Xamarin.Android, p. ej. [android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/).

-   **Pares de usuario** : [Android Callable Wrappers](~/android/platform/java-integration/working-with-jni.md) que se generan en tiempo de compilación para cada subclase de Java.Lang.Object presente dentro de la aplicación.


Como hay dos máquinas virtuales dentro de un proceso Xamarin.Android, hay dos tipos de colecciones de elementos no utilizados:

-   Colecciones de Android en tiempo de ejecución 
-   Colecciones de mono 

Las colecciones en tiempo de ejecución de Android funcionan normalmente, pero con una salvedad: una referencia global JNI se trata como una raíz de GC. Por lo tanto, si hay un JNI globales hacen referencia a reteniendo un tiempo de ejecución Android objeto de máquina virtual, el objeto *no* se recopilan, incluso si lo contrario, es apto para la recolección.

Colecciones mono son donde se produce la diversión. Los objetos administrados se recopilan normalmente. Objetos del mismo nivel se recopilan realizando el siguiente proceso:

1.  Todos los objetos del mismo nivel aptos para la recolección Mono tienen su referencia global JNI sustituye por una referencia global de JNI débil. 

2.  Se invoca un tiempo de ejecución Android GC de la máquina virtual. Puede recopilarse cualquier instancia nativo del mismo nivel. 

3.  Se comprueban las referencias globales débiles de JNI creadas en (1). Si se ha recopilado la referencia débil, se recopila el objeto del mismo nivel. Si tiene la referencia débil *no* , a continuación, se han recopilado, se reemplaza la referencia débil con una referencia global JNI y el objeto del mismo nivel no se recopila. Nota: en 14+ de API, esto significa que el valor devuelto de `IJavaObject.Handle` pueden cambiar después de un catálogo global. 

El resultado final de todo esto es que se va a ubicar una instancia de un objeto del mismo nivel siempre que se hace referencia, ya sea código administrado (por ejemplo, se almacena en un `static` variable) o que se hace referencia mediante código Java. Además, la duración del mismo nivel nativo se extenderá más allá de lo contrario, lo que lo harían en directo, mientras que el elemento del mismo nivel nativo no será recopilable hasta que del mismo nivel nativo y administrado igual estén recopilables.


## <a name="object-cycles"></a>Ciclos de objeto

Objetos del mismo nivel son lógicamente presentes en el runtime Android y la máquina virtual de Mono. Por ejemplo, un [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) instancia administrada del mismo nivel tendrá correspondiente [android.app.Activity](http://developer.android.com/reference/android/app/Activity.html) instancia de Java de framework del mismo nivel. Todos los objetos que se heredan de [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) es previsible que tienen representaciones en ambas máquinas virtuales. 

Todos los objetos que tienen una representación en ambas máquinas virtuales tendrán duraciones que se han ampliado en comparación con los objetos que están presentes solo dentro de una sola máquina virtual (como un [ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601)). Al llamar a [GC. Recopilar](xref:System.GC.Collect) necesariamente no recopilar estos objetos, como el catálogo global Xamarin.Android debe asegurarse de que el objeto no hace referencia a cualquier máquina virtual antes de recopilar. 

Para acortar la duración de los objetos, [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) debe invocarse. Esto manualmente "eliminará" la conexión en el objeto entre las dos máquinas virtuales al liberar la referencia global, lo que permite a los objetos que se recopilen con mayor rapidez. 


## <a name="automatic-collections"></a>Colecciones automática

A partir de [versión 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0), Xamarin.Android realiza automáticamente un GC completo cuando se sobrepasa un umbral de gref. Este umbral es del 90% de los conocidos grefs máximo para la plataforma: 1800 grefs en el emulador (máximo de 2000) y 46800 grefs en hardware (máximo 52000). *Nota:* Xamarin.Android solo cuenta los grefs creado por [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)y no podrá saber acerca de cualquier otro grefs creadas en el proceso. Se trata de un método heurístico *sólo*. 

Cuando se realiza una recolección automática, se imprimirá un mensaje similar al siguiente en el registro de depuración:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

La aparición de este no es determinista y puede producirse en momentos inoportunos (por ejemplo, en el medio de representación de gráficos). Si ve este mensaje, puede que desee realizar una recolección explícita en otra parte o puede que desee intentar [reducir la duración de objetos del mismo nivel](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Opciones de puente de GC

Xamarin.Android ofrece administración de memoria transparente con el tiempo de ejecución Android y Android. Se implementa como una extensión para el recolector de elementos no utilizados Mono llama el *GC puente*. 

El puente de GC funciona durante una recolección Mono y las cifras out qué punto objetos necesita su "vida" comprobar con el montón de Android en tiempo de ejecución. El puente de GC toma esta decisión, realice los pasos siguientes (en orden):

1.  Inducir el gráfico de referencia mono de objetos inalcanzables del mismo nivel en los objetos de Java que representan. 

2.  Realizar un GC de Java.

3.  Compruebe qué objetos están realmente inactivos. 

Este proceso complicado es lo que permite a las subclases de `Java.Lang.Object` para referencia libremente cualquiera objetos; quita las restricciones en qué Java objetos se pueden enlazar a C#. Debido a esta complejidad, el proceso de puente puede resultar muy caro y puede provocar notables pausas en una aplicación. Si la aplicación está experimentando pausas significativas, merece la pena investigar una de las siguientes tres implementaciones de puente de GC: 

-   **Tarjan** -un diseño del puente GC completamente nuevo basado en [algoritmo de Robert Tarjan y hacer referencia a la propagación hacia atrás](http://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Tiene el mejor rendimiento en nuestro simuladas cargas de trabajo, pero también tiene la mayor cantidad de código experimental. 

-   **Nueva** -una revisión importante del código original, corrige dos instancias del comportamiento cuadrática pero conservando el algoritmo básico (en función de [algoritmo del Kosaraju](http://en.wikipedia.org/wiki/Kosaraju's_algorithm) para buscar fuertemente conectado los componentes). 

-   **Antiguo** -la implementación original (se considera más estable de las tres). Esto es el puente que debe usar una aplicación si el `GC_BRIDGE` pausas son aceptables. 


La única forma de averiguar qué funciona de puente de GC mejor es experimentar en una aplicación y analizar la salida. Hay dos maneras de recopilar los datos de pruebas comparativas: 

-   **Habilitar el registro** -habilitar el registro (tal y como se describe en el [configuración](~/android/internals/garbage-collection.md) sección) para cada opción de puente de GC, a continuación, capturar y comparar los resultados de registro de cada configuración. Inspeccionar el `GC` mensajes para cada opción; en concreto, la `GC_BRIDGE` mensajes. Hace una pausa hasta 150 MS para aplicaciones no interactivas son tolerables, pero las pausas anteriormente 60 MS para las aplicaciones muy interactivas (por ejemplo, juegos) son un problema. 

-   **Habilitar cuentas puente** -cuentas de puente mostrará el promedio de costo de los objetos que señala por cada objeto implicado en el proceso de puente. Esta información de ordenación por tamaño proporcionará sugerencias sobre lo que mantiene la mayor cantidad de objetos adicionales. 


Para especificar qué `GC_BRIDGE` opción una aplicación debe usar, pasar `bridge-implementation=old`, `bridge-implementation=new` o `bridge-implementation=tarjan` a la `MONO_GC_PARAMS` variable de entorno, por ejemplo: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

El valor predeterminado es **Tarjan**. Si encuentra una regresión, quizá le resulte necesario establecer esta opción **antiguo**. Además, puede elegir usar la más estable **anterior** opción si **Tarjan** no produce una mejora del rendimiento. 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Ayudar a lo GC

Hay varias maneras de ayudar a lo GC para reducir los tiempos de uso y recopilación de memoria.



### <a name="disposing-of-peer-instances"></a>Eliminación de instancias del mismo nivel

El catálogo global tiene una vista incompleta del proceso y de que no puede ejecutarse cuando la memoria es baja porque el catálogo global no sabe que la memoria es baja. 

Por ejemplo, una instancia de un [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) tipo o tipo derivado sea al menos 20 bytes de tamaño (sujeta a modificaciones sin previo aviso, etc., etcetera.). 
[Administrar contenedores CCW](~/android/internals/architecture.md) no agregue miembros de instancia adicional, por lo que cuando haya una [Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) instancia que hace referencia a un blob de 10MB de memoria, GC del Xamarin.Android no sabrá que &ndash; el catálogo global verá un objeto de 20 bytes y no podrá determinar que está vinculado a Android objetos asignados por el tiempo de ejecución que se mantiene 10MB de memoria. 

Es necesario con frecuencia ayudar a lo GC. Por desgracia, *GC. AddMemoryPressure()* y *GC. RemoveMemoryPressure()* no se admiten, por lo que si se *saber* que simplemente libera un gráfico de objetos asignados por Java grandes puede que necesite llamar manualmente [GC. Collect()](xref:System.GC.Collect) al símbolo del sistema de un catálogo global para liberar el lado de Java memoria, o bien puede eliminar de forma explícita *Java.Lang.Object* subclases, interrumpir la asignación entre el contenedor invocable administrado y la instancia de Java. Por ejemplo, vea [1084 Error](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 


> [!NOTE]
> Debe ser *muy* cuidado al desechar `Java.Lang.Object` instancias de la subclase.

Para minimizar la posibilidad de daños en la memoria, observe las siguientes directrices al llamar a `Dispose()`.


#### <a name="sharing-between-multiple-threads"></a>Compartir entre varios subprocesos

Si el *Java o administrados* instancia puede compartirse entre varios subprocesos, *no debe ser `Dispose()`d.*, **alguna vez**. Por ejemplo, [ `Typeface.Create()` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle)) puede devolver un *instancia almacenada en caché*. Si varios subprocesos proporcionan los mismos argumentos, obtendrá la *mismo* instancia. Por lo tanto, `Dispose()`realizando una operación de la `Typeface` instancia a partir de un subproceso puede invalidar otros subprocesos, lo que pueden ser `ArgumentException`s desde `JNIEnv.CallVoidMethod()` (entre otras cosas) porque se eliminó la instancia desde otro subproceso. 


#### <a name="disposing-bound-java-types"></a>Va a eliminar tipos de Java enlazado

Si la instancia es de un tipo de Java enlazado, la instancia puede eliminarse de *mientras* no se volverá a utilizar la instancia desde el código administrado *y* la instancia de Java no se puede compartir entre subprocesos (vea anterior `Typeface.Create()` discusión). (Esta determinación puede resultar difícil.) La próxima vez que la instancia de Java entra en código administrado, un *nueva* contenedor se creará para él. 

Esto resulta útil en muchas ocasiones se trata de Drawables y otras instancias de uso intensivo de recursos:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

El ejemplo anterior es seguro para la ejecución porque el mismo nivel que [Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) devuelve hará referencia a un par de Framework, *no* un par de usuario. El `Dispose()` llamar al final de la `using` bloque interrumpirá la relación entre los recursos administrados [Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/) y un marco de [Drawable](http://developer.android.com/reference/android/graphics/drawable/Drawable.html) instancias, lo que permite la instancia de Java como recopilan tan pronto como el tiempo de ejecución Android necesite. Esto podría *no* sea segura si la instancia del mismo nivel hace referencia a un par de usuario; aquí estamos usando "externo" información a *saber* que la `Drawable` no puede hacer referencia a un elemento del mismo nivel de usuario y, por tanto, la `Dispose()` llamar a es seguro. 


#### <a name="disposing-other-types"></a>Eliminación de otros tipos 

Si la instancia se refiere a un tipo que no es un enlace de un tipo de Java (como un diccionario `Activity`), **DO NOT** llamar a `Dispose()` a menos que se *saber* que ningún código Java llame a métodos invalidados en ese instancia. Si no lo hace provoca [ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls). 

Por ejemplo, si tiene un personalizado haga clic en agente de escucha:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

Se *no debería* dispose de esta instancia, tal y como se tratará de Java invocar métodos en él en el futuro:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Utilizar comprobaciones explícitas para evitar excepciones

Si ha implementado un [Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/) sobrecarga de método, no toque objetos que implican JNI. Si lo hace, puede crear un *doble dispose* situación que permite al código (abren) intenta tener acceso a un objeto de Java subyacente que ya se ha recopilado de elementos no utilizados. Si lo hace, produce una excepción similar a lo siguiente: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

A menudo esta situación se produce cuando el primer dispose de un objeto hace que un miembro para convertirse en null y, a continuación, hace que un intento de acceso subsiguiente en este miembro con valor null que se produzca una excepción. En concreto, del objeto `Handle` (una instancia administrada que vincula a su instancia de Java subyacente) se invalida en el primer dispose, pero continúa realizando intentos código administrado tener acceso a esta instancia de Java subyacente, aunque ya no está disponible (vea [ Administrar contenedores CCW](~/android/internals/architecture.md#Managed_Callable_Wrappers) para obtener más información sobre la asignación entre las instancias de Java y las instancias administradas). 

Una buena forma de evitar esta excepción es explícitamente comprobar en los `Dispose` método que la asignación entre la instancia administrada y la instancia de Java subyacente sigue siendo válida; es, compruebe si el objeto `Handle` es null (`IntPtr.Zero`) antes de acceder a sus miembros. Por ejemplo, la siguiente `Dispose` método tiene acceso a un `childViews` objeto: 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);
        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

Si una eliminación inicial pasa causas `childViews` que no es válida `Handle`, el `for` acceso de bucle, se producirá un `ArgumentException`. Agregando explícito `Handle` null verificación antes del primer `childViews` tener acceso a los siguientes `Dispose` método evita que la excepción se produjo un: 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);

        // Check for a null handle:
        if (this.childViews.Handle == IntPtr.Zero)
            return;

        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```


### <a name="reduce-referenced-instances"></a>Reducir las instancias que se hace referencia

Cada vez que una instancia de un `Java.Lang.Object` tipo o una subclase se analiza durante el catálogo global, toda la *gráfico de objetos* también se debe examinar los que hace referencia la instancia de a. El gráfico de objetos es el conjunto de instancias de objeto que hace referencia la instancia de"raíz", *más* todo lo que la instancia de raíz al que hace referencia al que hace referencia de forma recursiva. 

Tenga en cuenta la siguiente clase:

```csharp
class BadActivity : Activity {

    private List<string> strings;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

Cuando `BadActivity` está construido, el gráfico de objetos contendrá 10004 instancias (1 x `BadActivity`, 1 x `strings`, 1 x `string[]` mantenidos por `strings`, 10000 x instancias de cadenas), *todos los* del que van a necesitar examina cada vez que la `BadActivity` se examina la instancia. 

Esto puede tener un impacto negativo en el tiempo de recopilación, lo que resulta en aumentado tiempos de pausa de GC. 

Puede ayudar a lo GC por *reducir* el tamaño de los gráficos de objetos que tienen la raíz por instancias de usuario del mismo nivel. En el ejemplo anterior, esto puede hacerse moviendo `BadActivity.strings` en una clase independiente que no hereda de Java.Lang.Object: 

```csharp
class HiddenReference<T> {

    static Dictionary<int, T> table = new Dictionary<int, T> ();
    static int idgen = 0;

    int id;

    public HiddenReference ()
    {
        lock (table) {
            id = idgen ++;
        }
    }

    ~HiddenReference ()
    {
        lock (table) {
            table.Remove (id);
        }
    }

    public T Value {
        get { lock (table) { return table [id]; } }
        set { lock (table) { table [id] = value; } }
    }
}

class BetterActivity : Activity {

    HiddenReference<List<string>> strings = new HiddenReference<List<string>>();

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```


## <a name="minor-collections"></a>Colecciones secundarias

Las colecciones secundarias pueden realizarse manualmente mediante una llamada a [GC. Collect(0)](xref:System.GC.Collect). Las colecciones secundarias son económicas (en comparación con las colecciones principales), pero tiene una importante fija costo, por lo que no desea que activen ellos con demasiada frecuencia y deben tener un tiempo de pausa de unos pocos milisegundos. 

Si la aplicación tiene un "ciclo de aranceles" en el que se realiza una y otra vez lo mismo, puede ser aconsejable realizar manualmente una colección secundaria una vez que ha finalizado el ciclo de aranceles. Ciclos de trabajo de ejemplo son: 

-  El ciclo de procesamiento de un solo fotograma de juego.
-  La interacción con un cuadro de diálogo de aplicación determinada (abrir, rellenar, cerrar) 
-  Un grupo de solicitudes de red para la actualización y sincronización de datos de la aplicación.



## <a name="major-collections"></a>Colecciones de principales

Principales colecciones pueden realizarse manualmente mediante una llamada a [GC. Collect()](xref:System.GC.Collect) o `GC.Collect(GC.MaxGeneration)`. 

Se debe realizarse en raras ocasiones y puede tener un tiempo de pausa de un segundo en un dispositivo Android estilo al recopilar un montón de 512MB. 

Principales colecciones deberían solo se invoca manualmente, si alguna vez: 

-   Al final del derecho prolongado ciclos y al poner en pausa un valor largo no presentan un problema al usuario. 

-   Dentro de un invalidado [Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/) método. 



## <a name="diagnostics"></a>Diagnóstico

Para realizar el seguimiento cuando se crean y destruyen referencias globales, puede establecer la [debug.mono.log](~/android/troubleshooting/index.md) propiedad del sistema para contener [ *gref* ](~/android/troubleshooting/index.md) o [ *gc*](~/android/troubleshooting/index.md). 



## <a name="configuration"></a>Configuración

El recolector de elementos no utilizados Xamarin.Android se puede configurar estableciendo la `MONO_GC_PARAMS` variable de entorno. Las variables de entorno pueden establecerse con una acción de compilación de [AndroidEnvironment](~/android/deploy-test/environment.md).

El `MONO_GC_PARAMS` variable de entorno es una lista separada por comas de los parámetros siguientes: 

-   `nursery-size` = *tamaño* : establece el tamaño de la guardería. El tamaño se especifica en bytes y debe ser una potencia de dos. Los sufijos `k` , `m` y `g` puede utilizarse para especificar kilo, mega y gigabytes, respectivamente. La guardería es la primera generación (de dos). Una mayor guardería acelerará normalmente el programa, pero obviamente usará más memoria. La guardería predeterminado cambiar el tamaño de 512 kb. 

-   `soft-heap-limit` = *tamaño* : el número máximo de destino administrado consumo de memoria para la aplicación. Cuando el uso de memoria es inferior al valor especificado, el catálogo global está optimizado para el tiempo de ejecución (menos colecciones). 
    Por encima de este límite, el catálogo global está optimizado para el uso de memoria (más colecciones). 

-   `evacuation-threshold` = *umbral* : establece el umbral de evacuación en porcentaje. El valor debe ser un número entero en el intervalo de 0 a 100. El valor predeterminado es 66. Si la fase de barrido de la colección, se considera que la ocupación de un tipo de bloque de montón concreto es inferior a este porcentaje, que va a hacer una copia colección para ese tipo de bloque en la siguiente colección principal, restaurando la ocupación al próximo al 100%. Un valor de 0 desactiva la evacuación. 

-   `bridge-implementation` = *puente implementación* : este modo, establecerá la opción de puente de GC para ayudar a los GC de resolver los problemas de rendimiento. Hay tres valores posibles: *anterior* , *nueva* , *tarjan*.

-   `bridge-require-precise-merge`: El Tarjan puente contiene una optimización que, en raras ocasiones, causar que un objeto recopila un GC cuando estén elementos no utilizados en primer lugar. Incluyendo esta opción deshabilita que la optimización, lo más predecible pero potencialmente más lento de GC.

Por ejemplo, para configurar el catálogo global para establecer un límite de tamaño de montón de 128MB, agregue un nuevo archivo al proyecto con un **acción de compilación** de `AndroidEnvironment` con el contenido: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
