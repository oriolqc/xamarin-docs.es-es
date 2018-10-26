---
title: Distribuidor de trabajo de firebase
description: Esta guía describe cómo programar el trabajo en segundo plano mediante la biblioteca de distribuidor de trabajo de Firebase de Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 4ae1fb71209f8116b17ee7e2cb44318ef790d831
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116184"
---
# <a name="firebase-job-dispatcher"></a>Distribuidor de trabajo de firebase

_Esta guía describe cómo programar el trabajo en segundo plano mediante la biblioteca de distribuidor de trabajo de Firebase de Google._


## <a name="overview"></a>Información general

Una de las mejores formas para que una aplicación de Android siga respondiendo al usuario es para asegurarse de que el trabajo complejo o de larga ejecución se realiza en segundo plano. Sin embargo, es importante que trabajo en segundo plano no afectará negativamente la experiencia del usuario con el dispositivo. 

Por ejemplo, un trabajo en segundo plano puede sondear un sitio Web cada tres o cuatro minutos para consultar los cambios a un determinado conjunto de datos. Esto parece ser benigno, pero tendría un impacto desastroso en duración de la batería. La aplicación se repetidamente el dispositivo de reactivación, elevar la CPU a un estado de encendido, el radio de energía, hacer que las solicitudes de red y, a continuación, procesar los resultados. Lo empeora porque el dispositivo se apague y se devolverá al estado inactivo de baja energía no inmediatamente. Trabajo en segundo plano programado mal accidentalmente puede mantener el dispositivo en un estado con requisitos de energía innecesaria y excesiva. Esta actividad aparentemente inofensivo (un sitio Web de sondeo) representará el dispositivo inutilizable en un período de tiempo relativamente corto.

Android proporciona las siguientes API para ayudar a realizar el trabajo en segundo plano, pero por sí solos no son suficientes para la programación de tareas inteligente. 

* **[Servicios de intención](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; servicios de intención son excelentes para llevar a cabo el trabajo, pero que no ofrecen ninguna forma para programar el trabajo.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; estas API solo permiten que el trabajo puede programar pero no ofrecen ninguna forma para llevar a cabo el trabajo. Además, el AlarmManager solo permite las restricciones en función del tiempo, lo que significa que genera una alarma en un momento determinado o una vez transcurrido un período de tiempo determinado. 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule The es una API excelente que funciona con el sistema operativo para programar trabajos. Sin embargo, solo está disponible para las aplicaciones de Android que tienen como destino el nivel de API 21 o posterior. 
* **[Receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; An Android app puede configurar receptores de difusión para llevar a cabo trabajo en respuesta a eventos de todo el sistema o intenciones. Sin embargo, los receptores de difusión no proporcionan ningún control sobre cuándo se debe ejecutar el trabajo. También se restringirán los cambios en el sistema operativo Android cuando funcionará receptores de difusión o los tipos de trabajo que puede responder. 

Hay dos características clave para realizar eficazmente el trabajo en segundo plano (a veces se denomina un _trabajo en segundo plano_ o un _trabajo_):

1. **Programar el trabajo de forma inteligente** &ndash; es importante que cuando una aplicación está realizando trabajo en segundo plano que lo hace como un buen ciudadano. Idealmente, la aplicación no debería exigir que se ejecute un trabajo. En su lugar, la aplicación debe especificar las condiciones que deben cumplirse para cuando el trabajo puede ejecutar y, a continuación, programar ese trabajo para ejecutarlo cuando se cumplen las condiciones. Esto permite a Android realizar el trabajo de forma inteligente. Por ejemplo, las solicitudes de red se pueden procesar por lotes para ejecutar todas al mismo tiempo para hacer un uso máximo de sobrecarga relacionados con las redes.
2. **Que encapsula el trabajo** &ndash; se debe encapsular el código para realizar el trabajo en segundo plano en un componente discreto que se puede ejecutar independientemente de la interfaz de usuario y será relativamente fácil volver a programar si no se puede completar el trabajo por algún motivo.

El distribuidor de trabajo de Firebase es una biblioteca de Google que proporciona una API fluida para simplificar el trabajo de programación en segundo plano. Se sirve de reemplazo para el Administrador de la nube de Google. El distribuidor de trabajo de Firebase consta de las siguientes API:

* Un `Firebase.JobDispatcher.JobService` es una clase abstracta que debe ampliarse con la lógica que se ejecutará en el trabajo en segundo plano.
* Un `Firebase.JobDispatcher.JobTrigger` declara cuando debe iniciarse el trabajo. Esto normalmente se expresa como una ventana de tiempo, por ejemplo, espere al menos 30 segundos antes de iniciar el trabajo, pero ejecutar el trabajo en 5 minutos.
* Un `Firebase.JobDispatcher.RetryStrategy` contiene información sobre lo que debe realizarse cuando se produce un error en un trabajo se ejecute correctamente. La estrategia de reintento especifica cuánto tiempo debe esperar antes de intentar volver a ejecutar el trabajo. 
* Un `Firebase.JobDispatcher.Constraint` es un valor opcional que describe una condición que debe cumplirse antes de poder ejecutar el trabajo, como el dispositivo está en una red unmetered o cargando.
* El `Firebase.JobDispatcher.Job` es una API que unifica las API de anteriores en una unidad de trabajo que se puede programar la `JobDispatcher`. El `Job.Builder` clase se utiliza para crear una instancia de un `Job`.
* Un `Firebasee.JobDispatcher.JobDispatcher` usa las tres API anteriores para programar el trabajo con el sistema operativo y para proporcionar una manera de cancelar trabajos, si es necesario.

Para programar el trabajo con el distribuidor de trabajo de Firebase, una aplicación de Xamarin.Android debe encapsular el código en un tipo que extiende el `JobService` clase. `JobService` tiene tres métodos de ciclo de vida que se puede llamar durante la duración del trabajo:

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; Este método es donde el trabajo se realizará y siempre se debe implementar. Se ejecuta en el subproceso principal. Este método devolverá `true` si hay trabajo restante, o `false` si se realiza el trabajo. 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; Se llama cuando el trabajo se ha detenido por alguna razón. Debe devolver `true` si se debe volver a programar el trabajo para su uso posterior.
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; Este método se llama cuando el `JobService` ha finalizado el trabajo asincrónico. 

Para programar un trabajo, creará una instancia de la aplicación un `JobDispatcher` objeto. A continuación, `Job.Builder` se utiliza para crear un `Job` objeto, que se proporciona para el `JobDispatcher` que pruebe y programar la ejecución del trabajo.

Esta guía describirá cómo agregar el distribuidor de trabajo de Firebase a una aplicación de Xamarin.Android y usarla para programar trabajo en segundo plano.

## <a name="requirements"></a>Requisitos

El distribuidor de trabajo de Firebase requiere el nivel de API de Android 9 o superior. La biblioteca de distribuidor de trabajo de Firebase depende de algunos componentes de Google Play Services; el dispositivo debe tener instalados los servicios de Google Play.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Uso de la biblioteca de distribuidor de trabajo de Firebase en Xamarin.Android

Para empezar a trabajar con el distribuidor de trabajo de Firebase, agregue primero el [paquete Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) al proyecto de Xamarin.Android. Buscar el Administrador de paquetes de NuGet para la **Xamarin.Firebase.JobDispatcher** paquete (que todavía está en versión preliminar).

Después de agregar la biblioteca de distribuidor de trabajo de Firebase, cree un `JobService` clase y, a continuación, programarlo para ejecutarlo con una instancia de la `FirebaseJobDispatcher`.


### <a name="creating-a-jobservice"></a>Creación de un JobService

Todo el trabajo realizado por la biblioteca de distribuidor de trabajo de Firebase debe realizarse en un tipo que extiende el `Firebase.JobDispatcher.JobService` clase abstracta. Creación de un `JobService` es muy similar a la creación de un `Service` con el marco de trabajo Android: 

1. Ampliar la `JobService` clase
2. Decorar la subclase con el `ServiceAttribute`. Aunque no es estrictamente necesario, se recomienda establecer explícitamente el `Name` parámetro para ayudar con la depuración el `JobService`. 
3. Agregar un `IntentFilter` para declarar el `JobService` en el **AndroidManifest.xml**. Esto también ayudará a la biblioteca de distribuidor de trabajo de Firebase localizar e invocar el `JobService`.

El código siguiente es un ejemplo de las más sencillas `JobService` para una aplicación, usando la biblioteca TPL para realizar algún trabajo de forma asincrónica:

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>Creación de un FirebaseJobDispatcher

Antes de que se puede programar cualquier trabajo, es necesario crear un `Firebase.JobDispatcher.FirebaseJobDispatcher` objeto. El `FirebaseJobDispatcher` es responsable de programar un `JobService`. El siguiente fragmento de código es una forma de crear una instancia de la `FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

En el fragmento de código anterior, el `GooglePlayDriver` es la clase que ayuda a los `FirebaseJobDispatcher` interactúan algunas de las API de programación en Google Play Services en el dispositivo. El parámetro `context` es cualquier Android `Context`, por ejemplo, una actividad. Actualmente la `GooglePlayDriver` es el único `IDriver` implementación en la biblioteca de distribuidor de trabajo de Firebase. 

El enlace de Xamarin.Android para el distribuidor de trabajo de Firebase proporciona un método de extensión para crear un `FirebaseJobDispatcher` desde el `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una vez el `FirebaseJobDispatcher` ha sido de instancias, es posible crear un `Job` y ejecutar el código de la `JobService` clase. El `Job` creado por un `Job.Builder` de objeto y se describe en la sección siguiente.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Creación de un Firebase.JobDispatcher.Job con el Job.Builder

El `Firebase.JobDispatcher.Job` clase es responsable de que encapsula los metadatos necesarios para ejecutar un `JobService`. Un`Job` contiene información como cualquier restricción que debe cumplirse antes de poder ejecutar el trabajo, si la `Job` es periódico, o todos los desencadenadores que hará que el trabajo que se ejecutará.  Como mínimo, un `Job` debe tener un _etiqueta_ (una cadena única que identifica el trabajo para el `FirebaseJobDispatcher`) y el tipo de la `JobService` que se debe ejecutar. Creará una instancia del distribuidor de trabajo de Firebase el `JobService` cuando llega el momento para ejecutar el trabajo.  Un `Job` se crea mediante el uso de una instancia de la `Firebase.JobDispatcher.Job.JobBuilder` clase. 

El siguiente fragmento de código es el ejemplo más sencillo de cómo crear un `Job` utilizando el enlace de Xamarin.Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

El `Job.Builder` llevará a cabo algunas comprobaciones de validación básicas en los valores de entrada para el trabajo. Se producirá una excepción si no es posible para el `Job.Builder` para crear un `Job`.  El `Job.Builder` creará un `Job` con los valores predeterminados siguientes:

* Un `Job`del _duración_ (cuánto se programará para ejecutarse) solo es hasta que el dispositivo se reinicia &ndash; una vez que el dispositivo se reinicia el `Job` se pierde.
* Un `Job` no es periódico &ndash; sólo se ejecutará una vez.
* Un `Job` se programará para que se ejecute tan pronto como sea posible.
* La estrategia de reintento predeterminada para un `Job` consiste en usar un _retroceso exponencial_ (tratadas en más detalle más adelante en la sección [establecer un RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Programar un trabajo

Después de crear el `Job`, se debe programar con la `FirebaseJobDispatcher` antes de ejecutarse. Hay dos métodos para programar un `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

El valor devuelto por `FirebaseJobDispatcher.Schedule` será uno de los siguientes valores enteros:

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; El `Job` se programó correctamente.
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; Lo que impide que produjo algún problema desconocido el `Job` de programarse.
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; No es válido `IDriver` se usó o `IDriver` algún modo no estaba disponible. 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; El `Trigger` no era compatible.
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; El servicio no está configurado correctamente o no está disponible.
 
### <a name="configuring-a-job"></a>Configuración de un trabajo

Es posible personalizar un trabajo. Ejemplos de cómo se puede personalizar un trabajo incluyen lo siguiente:

* [Pasar parámetros a un trabajo](#Passing_Parameters_to_a_Job) &ndash; A `Job` puede requerir valores adicionales para realizar su trabajo, por ejemplo descarga un archivo.
* [Establecer restricciones](#Setting_Constraints) &ndash; puede ser necesario ejecutar solo un trabajo cuando se cumplen ciertas condiciones. Por ejemplo, solo se ejecute un `Job` cuando el dispositivo se está cargando. 
* [Especifique cuándo un `Job` debe ejecutarse](#Setting_Job_Triggers) &ndash; el distribuidor de trabajo de Firebase permite que las aplicaciones especificar un tiempo de cuándo se debe ejecutar el trabajo.  
* [Declarar una estrategia de reintento para trabajos con errores](#Setting_a_RetryStrategy) &ndash; A _estrategia de reintento_ proporciona orientación para la `FirebaseJobDispatcher` sobre qué hacer con `Jobs` que no puedan completarse. 

Cada uno de estos temas se tratarán más en las secciones siguientes.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-jarameters-to-a-job"></a>Jarameters pasar a un trabajo

Los parámetros se pasan a un trabajo mediante la creación de un `Bundle` que se pasa junto con el `Job.Builder.SetExtras` método:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

El `Bundle` se obtiene acceso desde el `IJobParameters.Extras` propiedad en el `OnStartJob` método:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Configuración de restricciones

Pueden ayudar las restricciones reduce los costos ni la batería purga en el dispositivo. La `Firebase.JobDispatcher.Constraint` clase define estas restricciones como valores enteros:

* `Constraint.OnUnmeteredNetwork` &ndash; Solo debe ejecutar el trabajo cuando el dispositivo está conectado a una red unmetered. Esto es útil para evitar que el usuario incurrir en cargos por datos.
* `Constraint.OnAnyNetwork` &ndash; Ejecute el trabajo en cualquier red que está conectado el dispositivo. Si se especifica junto con `Constraint.OnUnmeteredNetwork`, este valor tendrá prioridad.
* `Constraint.DeviceCharging` &ndash; Ejecute el trabajo solo cuando el dispositivo se está cargando.

Las restricciones se establecen con el `Job.Builder.SetConstraint` método: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

El `JobTrigger` proporciona orientación para el sistema operativo sobre cuándo debe comenzar el trabajo. Un `JobTrigger` tiene un _ejecutando ventana_ que define una hora programada para cuando el `Job` debe ejecutarse. La ventana de ejecución tiene un _iniciar ventana_ valor y un _ventana de finalización_ valor. La ventana de inicio es el número de segundos que el dispositivo debe esperar antes de ejecutar el trabajo y el valor de la ventana de finalización es el número máximo de segundos que deben transcurrir antes de ejecutar el `Job`. 

Un `JobTrigger` pueden crearse con el `Firebase.Jobdispatcher.Trigger.ExecutionWindow` método.  Por ejemplo `Trigger.ExecutionWindow(15,60)` significa que debe ejecutarse el trabajo entre 15 y 60 segundos desde cuando esté programada. El `Job.Builder.SetTrigger` método se usa para 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

El valor predeterminado `JobTrigger` para un trabajo está representado por el valor `Trigger.Now`, que especifica que un trabajo se ejecute tan pronto como sea posible después de estar programado...

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Establecer un RetryStrategy

El `Firebase.JobDispatcher.RetryStrategy` se usa para especificar qué parte de un retraso de un dispositivo debe usar antes de intentar volver a ejecutar un trabajo con error. Un `RetryStrategy` tiene un _directiva_, que define qué algoritmo de la base de tiempo que se utilizará para volver a programar el trabajo con error y una ventana de ejecución que especifica una ventana en la que se debe programar el trabajo. Esto _ventana Reprogramación_ se define mediante dos valores. El primer valor es el número de segundos que deben transcurrir antes de volver a programar el trabajo (el _retroceso inicial_ valor), y el segundo número es el número máximo de segundos antes de que el trabajo debe ejecutar (el _retroceso máximo_valor). 
 
Los dos tipos de directivas de reintento se identifican con estos valores de tipo int:

* `RetryStrategy.RetryPolicyExponential` &ndash; Un _retroceso exponencial_ directiva aumentará el valor inicial de retroceso exponencial después de cada error. La primera vez que se produce un error en un trabajo, la biblioteca esperará el intervalo de _inicial que se ha especificado antes de volver a programar el trabajo &ndash; ejemplo 30 segundos. La segunda vez que se produce un error en el trabajo, la biblioteca de espera al menos 60 segundos antes de intentar ejecutar el trabajo. Después de la tercera error del intento, la biblioteca de espera de 120 segundos y así sucesivamente. El valor predeterminado `RetryStrategy` para el distribuidor de trabajo de Firebase biblioteca está representada por la `RetryStrategy.DefaultExponential` objeto. Tiene un retroceso inicial de 30 segundos y un retroceso máximo de 3600 segundos.
* `RetryStrategy.RetryPolicyLinear` &ndash; Esta estrategia es un _retroceso lineal_ que se debe volver a programar el trabajo para ejecutarse al establece los intervalos (hasta que se realiza correctamente). Retroceso lineal es más adecuado para el trabajo que debe completarse tan pronto como sea posible o para resolver problemas que se resolverá rápidamente a sí mismos. La biblioteca de distribuidor de trabajo de Firebase define un `RetryStrategy.DefaultLinear` que tiene una ventana Reprogramación de al menos 30 segundos y hasta 3600 segundos.

Es posible definir un personalizado `RetryStrategy` con el `FirebaseJobDispatcher.NewRetryStrategy` método. Acepta tres parámetros:

1. `int policy` &ndash; El _directiva_ es uno de los anteriores `RetryStrategy` valores, `RetryStrategy.RetryPolicyLinear`, o `RetryStrategy.RetryPolicyExponential`.
2. `int intialBackoffSeconds` &ndash; El _retroceso inicial_ es un retraso, en segundos, que es necesario antes de intentar volver a ejecutar el trabajo. El valor predeterminado es 30 segundos. 
3. `int maximumBackoffSeconds` &ndash; El _retroceso máximo_ valor declara el número máximo de segundos de retraso antes de intentar volver a ejecutar el trabajo. El valor predeterminado es 3600 segundos. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Cancelar un trabajo

Es posible cancelar todos los trabajos que se han programado, o simplemente un único trabajo mediante el `FirebaseJobDispatcher.CancelAll()` método o la `FirebaseJobDispatcher.Cancel(string)` método:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Devuelven un valor entero cualquiera de estos métodos:

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; El trabajo se canceló correctamente.
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; Un error impidió que se está cancelando el trabajo.
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; El `FirebaseJobDispatcher` no puede cancelar el trabajo ya no es válido no `IDriver` disponibles.

## <a name="summary"></a>Resumen

Esta guía describe cómo usar el distribuidor de trabajo de Firebase para realizar el trabajo de forma inteligente en segundo plano. También se han descrito cómo encapsular el trabajo debe realizarse como una `JobService` y cómo usar el `FirebaseJobDispatcher` para programar ese trabajo, especificar los criterios con un `JobTrigger` y cómo se deben controlar los errores con un `RetryStrategy`.


## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Firebase.JobDispatcher en NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-trabajo-dispatcher en GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Enlace Xamarin.Firebase.JobDispatcher](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Programación de tareas inteligente](https://developer.android.com/topic/performance/scheduling.html)
- [Android batería y optimizaciones de memoria: Google E/S 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
