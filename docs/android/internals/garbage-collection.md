---
title: recolección de elementos no utilizados
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 09466cc9eed4899ef0aa1198ff0aee5cd420e110
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674632"
---
# <a name="garbage-collection"></a>recolección de elementos no utilizados

Xamarin.Android usa Mono [Simple elementos no utilizados generacional](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/). Se trata de un recolector de elementos de marcar y limpiar con dos generaciones y un *el espacio de objetos grandes*, con dos tipos de colecciones: 

-   Colecciones secundarias (recopila Gen0 montón) 
-   Colecciones principales (recopila Gen1 y objetos grandes montones de espacio). 

> [!NOTE]
> En ausencia de una colección explícita a través de [GC. Collect()](xref:System.GC.Collect) las colecciones son *petición*, según las asignaciones del montón. *Esto no es un sistema de recuento de referencias*; objetos *no se recopilarán tan pronto como hay no hay referencias pendientes*, o cuando se ha cerrado un ámbito. El GC se ejecutará cuando el montón secundario ha quedado sin memoria para nuevas asignaciones. Si no hay ninguna asignación, no se ejecutará.


Las colecciones secundarias son económicas y frecuentes y se usan para recopilar los objetos muertos y asignados recientemente. Las colecciones secundarias se realizan después de cada MB algunos de los objetos asignados. Las colecciones secundarias pueden realizarse manualmente mediante una llamada a [GC. Recopilar (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Colecciones principales son costosos y menos frecuentes y se usan para recuperar todos los objetos muertos. Las colecciones principales se realizan una vez que se agota la memoria para el tamaño actual del montón (antes de cambiar el tamaño del montón). Las colecciones principales pueden realizarse manualmente mediante una llamada a [GC. Recopilar ()](xref:System.GC.Collect) o mediante una llamada a [GC. Recopilar (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) con el argumento [GC. MaxGeneration](xref:System.GC.MaxGeneration). 



## <a name="cross-vm-object-collections"></a>Colecciones de objetos entre máquinas virtuales

Hay tres categorías de tipos de objeto.

-   **Objetos administrados**: tipos que hacen *no* heredar [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) , por ejemplo, [System.String](xref:System.String). 
    Se recopilan normalmente por el GC. 

-   **Los objetos de Java**: Tipos de Java que están presentes dentro de la máquina virtual de tiempo de ejecución Android pero no se exponen a la máquina virtual de Mono. Estos son aburridos y no se tratará más adelante. Se recopilan normalmente por el tiempo de ejecución Android máquina virtual. 

-   **Emparejar objetos**: los tipos que implementan [IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) , por ejemplo, todos [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) y [Java.Lang.Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/) subclases. Las instancias de estos tipos tienen dos "halfs" un *administrado del mismo nivel* y un *punto nativo*. El interlocutor administrado es una instancia de la C# clase. El mismo nivel nativo es una instancia de una clase de Java en el tiempo de ejecución de máquina virtual, Android y el C# [IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) propiedad contiene una referencia global de JNI al mismo nivel nativo. 


Hay dos tipos de elementos del mismo nivel nativos:

-   **Elementos del mismo nivel de marco de trabajo** : "Normal" tipos de Java que sabe nada de Xamarin.Android, por ejemplo, [android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/).

-   **Elementos del mismo nivel de usuario** : [Contenedores invocables Android](~/android/platform/java-integration/working-with-jni.md) que se generan en tiempo de compilación para cada subclase Java.Lang.Object presente dentro de la aplicación.


Como hay dos máquinas virtuales dentro de un proceso de Xamarin.Android, hay dos tipos de colecciones de elementos no utilizados:

-   Colecciones en tiempo de ejecución de Android 
-   Colecciones de mono 

Las colecciones en tiempo de ejecución de Android funcionan con normalidad, pero con una salvedad: una referencia global de JNI se trata como una raíz GC. Por lo tanto, si hay un JNI global referencia reteniendo un tiempo de ejecución de Android de objeto de máquina virtual, el objeto *no* recopilados, incluso si en caso contrario, es apto para la colección.

Las colecciones de mono son donde se produce la diversión. Objetos administrados se recopilan con normalidad. Objetos del mismo nivel se recopilan al realizar el siguiente proceso:

1.  Todos los objetos del mismo nivel aptos para la recolección de Mono tienen su referencia global de JNI sustituye por una referencia global débil de JNI. 

2.  Se invoca un tiempo de ejecución de Android GC de la máquina virtual. Se puede recopilar cualquier instancia nativa del mismo nivel. 

3.  Se comprueban las referencias globales débiles de JNI creadas en (1). Si se ha recopilado la referencia débil, se recopila el objeto del mismo nivel. Si tiene la referencia débil *no* recopilados, a continuación, la referencia débil se sustituye por una referencia global de JNI y no se recopila el objeto del mismo nivel. Nota: en la API 14 o superior, esto significa que el valor devuelto de `IJavaObject.Handle` puede cambiar después de un catálogo global. 

El resultado final de todo esto es que una instancia de un objeto del mismo nivel se encontrarán siempre y cuando se hace referencia, ya sea código administrado (por ejemplo, se almacenan en un `static` variable) o que se hace referencia mediante código Java. Además, la duración de los elementos del mismo nivel nativos se extenderá más allá de lo contrario, lo que lo harían en vivo, mientras que el mismo nivel nativo no podrán recopilable hasta el mismo nivel nativo y el administrado de mismo nivel son recopilables.


## <a name="object-cycles"></a>Ciclos de objetos

Objetos del mismo nivel son lógicamente presentes en el tiempo de ejecución de Android y Mono las máquinas virtuales. Por ejemplo, un [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) instancia administrada del mismo nivel tendrán correspondiente [android.app.Activity](https://developer.android.com/reference/android/app/Activity.html) instancia de Java del mismo nivel de marco de trabajo. Todos los objetos que heredan de [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) puede esperarse que tienen representaciones en ambas máquinas virtuales. 

Todos los objetos que tengan representación en ambas máquinas virtuales tendrán duraciones que se han ampliado en comparación con los objetos que están presentes solo dentro de una sola máquina virtual (como un [ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601)). Una llamada a [GC. Recopilar](xref:System.GC.Collect) necesariamente no recopilar estos objetos, como debe asegurarse de que el objeto no se hace referencia a cualquier máquina virtual antes de recopilar, el GC de Xamarin.Android. 

Para reducir la duración del objeto, [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) debe invocarse. Esto manualmente "eliminará" la conexión en el objeto entre las dos máquinas virtuales al liberar la referencia global, lo que permite a los objetos que se recopilen con mayor rapidez. 


## <a name="automatic-collections"></a>Colecciones automática

A partir [versión 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0), Xamarin.Android realiza automáticamente un GC completo cuando se sobrepasa un umbral de gref. Este umbral es el 90% de la conocida grefs máximo para la plataforma: 1800 grefs en el emulador (máximo de 2000) y 46800 grefs en hardware (52000 máximo). *Nota:* Xamarin.Android solo cuenta los grefs creado por [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)y no sabrá acerca de cualquier otro grefs creadas en el proceso. Se trata de un método heurístico *sólo*. 

Cuando se realiza una recopilación automática, se imprimirá un mensaje similar al siguiente en el registro de depuración:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

La aparición de este no es determinista y puede producirse en momentos inoportunos (por ejemplo, en el medio de representación de gráficos). Si ve este mensaje, es posible que desea realizar una recolección explícita en otro lugar o desea intentar [reducir la duración de objetos del mismo nivel](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Opciones de puente de GC

Xamarin.Android ofrece administración de memoria transparente con el tiempo de ejecución de Android y Android. Se implementa como una extensión para el recolector de elementos no utilizados Mono llama el *GC puente*. 

El puente de GC funciona durante una recolección de Mono y las cifras de a qué punto objetos necesita su "ejecución" comprobado con el montón en tiempo de ejecución de Android. El puente de GC toma esta decisión realizando los pasos siguientes (en orden):

1.  Inducir el gráfico de referencia de mono de objetos inalcanzables del mismo nivel en los objetos de Java que representan. 

2.  Realizar una GC de Java.

3.  Compruebe qué objetos están inactivos realmente. 

Este proceso complicado es lo que permite a las subclases de `Java.Lang.Object` para referencia libremente cualquiera los objetos; quita las restricciones sobre qué Java se pueden enlazar objetos a C#. Debido a esta complejidad, el proceso de puente puede ser muy costoso y puede provocar apreciables pausas en una aplicación. Si la aplicación está experimentando una pausa significativa, merece la pena investigar una de las siguientes tres implementaciones de puente de GC: 

-   **Tarjan** -según un diseño completamente nuevo del puente GC [algoritmo de Robert Tarjan y hacer referencia a la propagación hacia atrás](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Tiene el mejor rendimiento en nuestras cargas de trabajo simuladas, pero también tiene el recurso compartido de mayor tamaño del código experimental. 

-   **Nuevo** -una revisión importante del código original, corregir las dos instancias de comportamiento cuadrática pero conservando el algoritmo básico (basado en [algoritmo del Kosaraju](https://en.wikipedia.org/wiki/Kosaraju's_algorithm) para buscar fuertemente conectados componentes). 

-   **Antiguo** -la implementación original (se considera más estable de los tres). Este es el puente que debe usar una aplicación si el `GC_BRIDGE` pausas son aceptables. 


Es la única forma de averiguar qué funciona el puente de GC mejor al experimentar en una aplicación y analizar la salida. Hay dos maneras de recopilar los datos para pruebas comparativas: 

-   **Habilitar el registro** -habilitar el registro (como se describe en el [configuración](~/android/internals/garbage-collection.md) sección) para cada opción de puente de GC, a continuación, capturar y comparar los resultados de registro de cada opción de configuración. Inspeccionar el `GC` mensajes para cada opción; en concreto, el `GC_BRIDGE` mensajes. Pone en pausa hasta 150 MS para aplicaciones no interactivas son tolerables, pero las pausas anteriormente 60ms para aplicaciones muy interactivas (por ejemplo, juegos) son un problema. 

-   **Habilitar las cuentas de puente** -contabilidad del puente se mostrará el promedio de costo de los objetos que apunta cada objeto implicado en el proceso de puente. Esta información de ordenación por tamaño proporcionará pistas sobre lo que contiene la mayor cantidad de objetos adicionales. 


Para especificar qué `GC_BRIDGE` opción una aplicación debe usar, pase `bridge-implementation=old`, `bridge-implementation=new` o `bridge-implementation=tarjan` a la `MONO_GC_PARAMS` variable de entorno, por ejemplo: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

El valor predeterminado es **Tarjan**. Si encuentra una regresión, puede que sea necesario establecer esta opción **antiguo**. Además, se puede utilizar la más estable **antiguo** opción si **Tarjan** no produce una mejora del rendimiento. 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Ayudar a lo GC

Hay varias maneras de ayudar a lo GC para reducir los tiempos de recopilación y uso de memoria.



### <a name="disposing-of-peer-instances"></a>Eliminación de instancias del mismo nivel

El GC tiene una vista completa del proceso y que no puede ejecutarse cuando la memoria es baja porque el GC no sabe que la memoria es baja. 

Por ejemplo, una instancia de un [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) tipo o tipo derivado tiene al menos 20 bytes de tamaño (sujeta a cambios sin previo aviso, etc., etcetera.). 
[Contenedores invocables administrados](~/android/internals/architecture.md) no agregue los miembros de instancia adicional, por lo que cuando haya un [Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) instancia que hace referencia a un blob de 10MB de memoria, GC de Xamarin.Android no sabrá que &ndash; el GC verá un objeto de 20 bytes y no podrá determinar que está vinculado a Android objetos asignados por el tiempo de ejecución que se mantiene 10MB de memoria. 

Es necesario con frecuencia ayudar a lo GC. Por desgracia, *GC. AddMemoryPressure()* y *GC. RemoveMemoryPressure()* no se admiten, por lo que si se *saber* que simplemente libera un gráfico de objetos grandes asignados de Java es posible que deba llamar manualmente a [GC. Collect()](xref:System.GC.Collect) al símbolo del sistema de un catálogo global para liberar la parte Java memoria o se puede eliminar de forma explícita *Java.Lang.Object* subclases, interrumpir la asignación entre el contenedor RCW administrado y la instancia de Java. Por ejemplo, vea [1084 Error](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 


> [!NOTE]
> Debe ser *extremadamente* debe tener cuidado al desechar `Java.Lang.Object` instancias de la subclase.

Para minimizar la posibilidad de daños en la memoria, observe las siguientes directrices al llamar a `Dispose()`.


#### <a name="sharing-between-multiple-threads"></a>Uso compartido entre varios subprocesos

Si el *Java o administrados* instancia puede compartirse entre varios subprocesos, *no debe ser `Dispose()`d.* , **nunca**. Por ejemplo, [`Typeface.Create()`](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle)) 
puede devolver un *instancia almacenada en caché*. Si varios subprocesos proporcionan los mismos argumentos, obtendrá la *mismo* instancia. Por lo tanto, `Dispose()`ing de la `Typeface` instancia desde un subproceso puede invalidar otros subprocesos, lo que pueden dar lugar a `ArgumentException`desde `JNIEnv.CallVoidMethod()` (entre otros) porque se ha eliminado la instancia desde otro subproceso. 


#### <a name="disposing-bound-java-types"></a>Eliminación de los tipos de Java enlazado

Si la instancia es de un tipo de Java enlazado, se puede desechar la instancia de *mientras* no se volverá a utilizar la instancia desde el código administrado *y* la instancia de Java no se puede compartir entre subprocesos (consulte anterior `Typeface.Create()` discusión). (Esta determinación puede resultar difícil.) La próxima vez que la instancia de Java entra en código administrado, un *nuevo* contenedor se creará para él. 

Esto es útil con frecuencia en cuanto a recursos Drawable y otras instancias con mucha actividad de recursos:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

El ejemplo anterior es segura porque el mismo nivel que [Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) devuelve hará referencia a un elemento del mismo nivel de marco de trabajo, *no* un elemento del mismo nivel de usuario. El `Dispose()` llamar al final de la `using` bloque interrumpirá la relación entre los recursos administrados [Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/) y framework [Drawable](https://developer.android.com/reference/android/graphics/drawable/Drawable.html) instancias, lo que permite que la instancia de Java recopilan tan pronto como necesita el tiempo de ejecución de Android. Esto sería *no* ser seguros si la instancia del mismo nivel hace referencia a un elemento del mismo nivel de usuario; vamos a usar la información "external" para *saber* que la `Drawable` no puede hacer referencia a un elemento del mismo nivel de usuario y, por tanto, el `Dispose()` llamar de forma segura. 


#### <a name="disposing-other-types"></a>Eliminación de otros tipos 

Si la instancia se refiere a un tipo que no es un enlace de un tipo de Java (como un diccionario `Activity`), **DO NOT** llamar `Dispose()` a menos que se *saber* que ningún código de Java llamará a métodos invalidados en el que instancia de. Si no lo hace da como resultado [ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls). 

Por ejemplo, si tiene un personalizado haga clic en agente de escucha:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

Le *no debería* deshacerse de esta instancia, como Java intentará invocar métodos en él en el futuro:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Utilizar comprobaciones explícitas para evitar excepciones

Si ha implementado un [Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/) sobrecargar el método, evita tener que tocar los objetos que implican JNI. Si lo hace, puede crear un *doble dispose* situación que hace posible que el código (funestamente) intenta tener acceso a un objeto de Java subyacente que ya se ha recopilado de elementos no utilizados. Si lo hace, produce una excepción similar al siguiente: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

A menudo esta situación se produce cuando el primer dispose de un objeto hace que un miembro para convertirse en null y, a continuación, hace que un intento de acceso posteriormente en este miembro con valor null que se produzca una excepción. En concreto, del objeto `Handle` (una instancia administrada que vincula a su instancia de Java subyacente) se invalida en el primer dispose, pero continúa realizando intentos código administrado tener acceso a esta instancia de Java subyacente, incluso si ya no está disponible (vea [ Contenedores invocables administrados](~/android/internals/architecture.md#Managed_Callable_Wrappers) para obtener más información acerca de la asignación entre las instancias de Java y las instancias administradas). 

Es una buena forma de evitar esta excepción comprobar explícitamente en su `Dispose` método que la asignación entre la instancia administrada y la instancia de Java subyacente sigue siendo válida; eso es, compruebe si el objeto `Handle` es null (`IntPtr.Zero`) antes de acceder a sus miembros. Por ejemplo, la siguiente `Dispose` método tiene acceso a un `childViews` objeto: 

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

Si pasa de una inicial dispose causas `childViews` que no es válido `Handle`, el `for` acceso de bucle producirá una `ArgumentException`. Agregando explícita `Handle` null verificación antes del primer `childViews` tener acceso a la siguiente `Dispose` método evita que la excepción que se producen: 

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

Cada vez que una instancia de un `Java.Lang.Object` tipo o subclase se analiza durante el GC, toda la *gráfico de objetos* que hace referencia la instancia a también debe explorarse. El gráfico de objetos es el conjunto de instancias de objeto que hace referencia la instancia de"raíz", *plus* todo lo que la instancia de raíz al que hace referencia se refiere a, de forma recursiva. 

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

Cuando `BadActivity` está construido, el gráfico de objetos contendrá 10004 instancias (1 x `BadActivity`, 1 x `strings`, 1 x `string[]` mantenidos por `strings`, 10000 x instancias de cadenas), *todas* de lo que deberá ser cada vez que analizan el `BadActivity` se analiza la instancia. 

Esto puede tener un impacto negativo en el tiempo de recopilación, lo que aumenta tiempos de pausa de GC. 

Puede ayudar a lo GC por *reducir* el tamaño de los gráficos de objetos que se ha modificado por las instancias del mismo nivel de usuario. En el ejemplo anterior, esto puede hacerse moviendo `BadActivity.strings` en una clase independiente que no hereda de Java.Lang.Object: 

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

Las colecciones secundarias pueden realizarse manualmente mediante una llamada a [GC. Collect(0)](xref:System.GC.Collect). Las colecciones secundarias son económicas (cuando se compara con las colecciones principales), pero tienen una importante se ha corregido el costo, por lo que no desee desencadenarlo con demasiada frecuencia y debe tener un tiempo de pausa de unos pocos milisegundos. 

Si la aplicación tiene un "ciclo de aranceles" en el que lo mismo se realiza una y otra vez, puede ser aconsejable realizar manualmente una colección secundaria una vez que ha terminado el ciclo de aranceles. Ciclos de trabajo de ejemplo incluyen: 

-  El ciclo de representación de un solo fotograma del juego.
-  Toda la interacción con un cuadro de diálogo de aplicación determinada (abrir, rellenar, cerrar) 
-  Un grupo de solicitudes de red para la actualización y sincronización de datos de la aplicación.



## <a name="major-collections"></a>Colecciones principales

Las colecciones principales pueden realizarse manualmente mediante una llamada a [GC. Collect()](xref:System.GC.Collect) o `GC.Collect(GC.MaxGeneration)`. 

Se debe realizarse con poca frecuencia y puede tener un tiempo de pausa de un segundo en un dispositivo Android estilo al recopilar un montón de 512MB. 

Las colecciones principales deben solo se invoca manualmente, si alguna vez: 

-   Al final del derecho largo ciclos y cuando se pausa un valor long no suponer un problema al usuario. 

-   Dentro de un invalidado [Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/) método. 



## <a name="diagnostics"></a>Diagnóstico

Para realizar un seguimiento cuando se crean y destruyen referencias globales, puede establecer el [debug.mono.log](~/android/troubleshooting/index.md) propiedad del sistema para contener [ *gref* ](~/android/troubleshooting/index.md) o [ *gc*](~/android/troubleshooting/index.md). 



## <a name="configuration"></a>Configuración

El recolector de elementos no utilizados de Xamarin.Android puede configurarse estableciendo el `MONO_GC_PARAMS` variable de entorno. Se pueden establecer las variables de entorno con una acción de compilación [AndroidEnvironment](~/android/deploy-test/environment.md).

El `MONO_GC_PARAMS` variable de entorno es una lista separada por comas de los parámetros siguientes: 

-   `nursery-size` = *tamaño* : Establece el tamaño de la guardería. El tamaño se especifica en bytes y debe ser una potencia de dos. Los sufijos `k` , `m` y `g` puede utilizarse para especificar kilo, mega y gigabytes, respectivamente. Queda es la primera generación (de dos). Un vivero más grande normalmente acelerará el programa, pero obviamente usará más memoria. Guardería predeterminado el tamaño de 512 kb. 

-   `soft-heap-limit` = *tamaño* : El consumo máximo de memoria administrada de destino para la aplicación. Cuando el uso de memoria es inferior al valor especificado, el GC está optimizado para el tiempo de ejecución (menos colecciones). 
    Por encima de este límite, el GC está optimizado para el uso de memoria (colecciones más). 

-   `evacuation-threshold` = *umbral* : Establece el umbral de evacuación en porcentaje. El valor debe ser un entero en el intervalo de 0 a 100. El valor predeterminado es 66. Si encuentra que la fase de barrido de la colección que la ocupación de un tipo de bloque de montón concreto es menor que este porcentaje, hará una copia colección para ese tipo de bloque en la siguiente recolección principal, restaurando ocupación al próximo al 100%. Un valor de 0 desactiva la evacuación. 

-   `bridge-implementation` = *implementación de puente* : Esto establecerá la opción de puente de GC para ayudar a resolver problemas de rendimiento de GC. Hay tres valores posibles: *antiguo* , *nueva* , *tarjan*.

-   `bridge-require-precise-merge`: El Tarjan puente contiene una optimización que, en raras ocasiones, hacen que un objeto sea recopila un GC después de que primero se convierte en elementos no utilizados. Incluye esta opción deshabilita que la optimización, lo más predecible, pero potencialmente más lento de GC.

Por ejemplo, para configurar el catálogo global para tener un límite de tamaño de montón de 128MB, agregue un nuevo archivo al proyecto con un **acción de compilación** de `AndroidEnvironment` con el contenido: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
