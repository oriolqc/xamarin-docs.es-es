---
title: Distribuidor de trabajo firebase
description: "Esta guía describe cómo programar el trabajo en segundo plano mediante la biblioteca de distribuidor de trabajo Firebase de Google."
ms.topic: article
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/19/2018
ms.openlocfilehash: fbcb0190f609efc4396429a7961c2d49ab82576f
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2018
---
# <a name="firebase-job-dispatcher"></a>Distribuidor de trabajo firebase

_Esta guía describe cómo programar el trabajo en segundo plano mediante la biblioteca de distribuidor de trabajo Firebase de Google._

## <a name="overview"></a>Información general

Una de las mejores formas de evitar que la aplicación Android respondiendo al usuario es para asegurarse de que el trabajo complejo o de larga ejecución se realiza en segundo plano. Sin embargo, es importante que trabajo en segundo plano no afectará negativamente a la experiencia del usuario con el dispositivo. 

Por ejemplo, un trabajo en segundo plano puede sondear un sitio Web cada tres o cuatro minutos para consultar para los cambios en un determinado conjunto de datos. Esto puede parecer benigno, sin embargo, tendría un impacto desastroso en duración de la batería. La aplicación se repetidamente reactivar el dispositivo, elevar el nivel de la CPU a un estado de energía más alto, las radios de energía, que las solicitudes de red y, a continuación, procesar los resultados. Obtiene un rendimiento peor porque el dispositivo se apague y se devolverá al estado de inactividad de bajo consumo de energía no inmediatamente. Trabajo en segundo plano programada mal accidentalmente puede mantener el dispositivo en un estado con requisitos de energía innecesaria y excesiva. Esta actividad aparentemente inocente (un sitio Web de sondeo) inutilizará el dispositivo en un período de tiempo relativamente corto.

Android proporciona las siguientes API para ayudar a realizar el trabajo en segundo plano, pero por sí mismos no son suficientes para la programación de trabajos inteligente. 

* **[Servicios intención](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; servicios de intención son excelentes para realizar el trabajo, pero no proporcionan ninguna manera de programar el trabajo.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; estas API sólo permiten que el trabajo para programarse pero se proporciona ninguna manera de realizar el trabajo. Además, la AlarmManager solo permite restricciones basado en el tiempo, lo que significa generar una alarma en un momento determinado o después de que haya transcurrido un período de tiempo determinado. 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule el es una API excelente que funciona con el sistema operativo para programar trabajos. Sin embargo, solo está disponible para las aplicaciones Android que tienen como destino la API nivel 21 o versiones posteriores. 
* **[Difusión receptores](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; Android una aplicación puede configurar receptores de difusión para realizar el trabajo en respuesta a eventos de todo el sistema o del color. Sin embargo, los receptores de difusión no proporcionan ningún control sobre cuándo se debe ejecutar el trabajo. También se limitan los cambios en el sistema operativo Android cuando funcionará receptores de difusión o los tipos de trabajo que pueden responder a. 

Hay dos características fundamentales para realizar eficazmente el trabajo en segundo plano (a veces se denomina un _trabajo en segundo plano_ o un _trabajo_):

1. **Programar el trabajo de forma inteligente** &ndash; es importante que cuando una aplicación realiza operaciones en segundo plano que lo hace como un buen ciudadano. Idealmente, la aplicación no debería exigir que se ejecute un trabajo. En su lugar, la aplicación debe especificar las condiciones que deben cumplirse para cuando el trabajo se puede ejecutar y, a continuación, programar ese trabajo que se ejecuta cuando se cumplen las condiciones. Esto permite Android realizar el trabajo de forma inteligente. Por ejemplo, las solicitudes de red se pueden procesar por lotes para ejecutarlos todos al mismo tiempo para sacar el máximo provecho de sobrecarga relacionados con las redes.
2. **Encapsula el trabajo** &ndash; se debe encapsular el código para realizar el trabajo en segundo plano en un componente discreto que puede ejecutar independientemente de la interfaz de usuario y será relativamente fáciles de programar si no se puede completar el trabajo por algún motivo.

El distribuidor de trabajo Firebase es una biblioteca de Google que proporciona una API fluida para simplificar la programación trabajo en segundo plano. Está diseñado para que sea el sustituto para el Administrador de nube de Google. El distribuidor de trabajo Firebase consta de las API siguientes:

* A `Firebase.JobDispatcher.JobService` es una clase abstracta que debe ampliarse con la lógica que se ejecutará en el trabajo en segundo plano.
* Un `Firebase.JobDispatcher.JobTrigger` declara al que se debe iniciar el trabajo. Esto normalmente se expresa como una ventana de tiempo, por ejemplo, espere al menos 30 segundos antes de iniciar el trabajo, pero ejecutar el trabajo de intervalos de 5 minutos.
* Un `Firebase.JobDispatcher.RetryStrategy` contiene información sobre lo que debe realizarse cuando se produce un error en un trabajo se ejecute correctamente. La estrategia de reintento especifica cuánto tiempo debe esperar antes de intentar volver a ejecutar el trabajo. 
* A `Firebase.JobDispatcher.Constraint` es un valor opcional que describe una condición que debe cumplirse para poder ejecutar el trabajo, como el dispositivo está en una red unmetered o está cargando.
* El `Firebase.JobDispatcher.Job` es una API que unifica las API en anterior para una unidad de trabajo que se puede programar mediante el `JobDispatcher`. El `Job.Builder` clase se utiliza para crear una instancia de un `Job`.
* Un `Firebasee.JobDispatcher.JobDispatcher` usa las API de tres anteriores para programar el trabajo con el sistema operativo y para proporcionar una manera de cancelar trabajos, si es necesario.

Para programar el trabajo con el distribuidor de trabajo Firebase, una aplicación de Xamarin.Android debe encapsular el código en un tipo que extiende el `JobService` clase. `JobService` tiene tres métodos de ciclo de vida que se puede llamar durante la duración del trabajo:

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; Este método es que el trabajo se producirá y siempre debe implementarse. Se ejecuta en el subproceso principal. Este método devolverá `true` si hay trabajo restante, o `false` si se realiza el trabajo. 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; Esto se llama cuando el trabajo se ha detenido por algún motivo. Debe devolver `true` si el trabajo se debe volver a programar para más tarde.
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; Este método se llama cuando el `JobService` ha finalizado el trabajo asincrónico. 

Para programar un trabajo, creará una instancia de la aplicación un `JobDispatcher` objeto. A continuación, `Job.Builder` se utiliza para crear un `Job` objeto, que se proporciona a los `JobDispatcher` que se intente y programar la ejecución del trabajo.

Esta guía describe cómo agregar el distribuidor de trabajo Firebase a una aplicación Xamarin.Android y utilizarla para programar trabajo en segundo plano.

## <a name="requirements"></a>Requisitos

El distribuidor de trabajo Firebase requiere el nivel de API de Android 9 o superior. La biblioteca de distribuidor de trabajo Firebase depende de algunos componentes proporcionados por servicios de Google Play; el dispositivo debe tener instalados los servicios de Google Play.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Usa la biblioteca de distribuidor de trabajo de Firebase en Xamarin.Android

Para empezar a trabajar con el distribuidor de trabajo Firebase, agregue primero la [paquete Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher/0.6.0-beta1) al proyecto Xamarin.Android. Buscar el Administrador de paquetes de NuGet para la **Xamarin.Firebase.Jobdispatcher** paquete.  

Después de agregar la biblioteca de distribuidor de trabajo Firebase, cree un `JobService` clase y, a continuación, programar que se ejecute con una instancia de la `FirebaseJobDispatcher`.

### <a name="creating-a-jobservice"></a>Crear un JobService

Todo el trabajo realizado por la biblioteca de Firebase trabajo distribuidor debe realizarse en un tipo que extiende el `Firebase.JobDispatcher.JobService` clase abstracta. Crear un `JobService` es muy similar a la creación de un `Service` con el marco de trabajo Android: 

1. Extender el `JobService` (clase)
2. Decorar la subclase con la `ServiceAttribute`. Aunque no es estrictamente necesario, se recomienda establecer explícitamente la `Name` parámetro para ayudar a depurar el `JobService`. 
3. Agregar un `IntentFilter` para declarar el `JobService` en el **AndroidManifest.xml**. Esto también ayudará a la biblioteca de distribuidor de trabajo Firebase localizar e invocar el `JobService`.

El código siguiente es un ejemplo de los más sencillos `JobService` para una aplicación, usando la biblioteca TPL para realizar algún trabajo de forma asincrónica:

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

### <a name="creating-a-firebasejobdispatcher"></a>Crear un FirebaseJobDispatcher

Antes de que se puede programar cualquier trabajo, es necesario crear un `Firebase.JobDispatcher.FirebaseJobDispatcher` objeto. El `FirebaseJobDispatcher` es responsable de programar un `JobService`. El siguiente fragmento de código es una manera de crear una instancia de la `FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

En el fragmento de código anterior, el `GooglePlayDriver` es la clase que ayuda a los `FirebaseJobDispatcher` interactuar con algunas de las API de programación de servicios de Google Play en el dispositivo. El parámetro `context` es cualquier Android `Context`, como la actividad. Actualmente el `GooglePlayDriver` es el único `IDriver` implementación en la biblioteca de Firebase trabajo distribuidor. 

El enlace Xamarin.Android para que el distribuidor de trabajo Firebase proporciona un método de extensión para crear un `FirebaseJobDispatcher` desde el `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Una vez el `FirebaseJobDispatcher` ha sido crea una instancia, es posible crear un `Job` y ejecutar el código de la `JobService` clase. El `Job` creado por un `Job.Builder` de objetos y se describen en la sección siguiente.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Crear un Firebase.JobDispatcher.Job con el Job.Builder

El `Firebase.JobDispatcher.Job` clase es responsable de encapsular los metadatos necesarios para ejecutar un `JobService`. A`Job` contiene información como cualquier restricción que debe cumplirse antes de poder ejecutar el trabajo, si la `Job` sea recurrente, o los desencadenadores que hará que el trabajo se ejecute.  Como mínimo, un `Job` debe tener un _etiqueta_ (una cadena única que identifica el trabajo para el `FirebaseJobDispatcher`) y el tipo de la `JobService` que se debe ejecutar. El distribuidor de trabajo Firebase creará una instancia de la `JobService` cuando llega el momento para ejecutar el trabajo.  A `Job` se crea mediante una instancia de la `Firebase.JobDispatcher.Job.JobBuilder` clase. 

El fragmento de código siguiente se muestra el ejemplo más simple de cómo crear un `Job` utilizando el enlace de Xamarin.Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

El `Job.Builder` llevará a cabo algunas comprobaciones de validación básicas en los valores de entrada para el trabajo. Se producirá una excepción si no es posible para la `Job.Builder` para crear un `Job`.  El `Job.Builder` creará una `Job` con los valores predeterminados siguientes:

* A `Job`del _duración_ (cuánto tiempo se programará para que se ejecute) solo es hasta que el dispositivo se reinicia &ndash; una vez que el dispositivo se reinicia el `Job` se pierde.
* A `Job` no sea recurrente &ndash; solo se ejecutará una vez.
* Un `Job` se programará para que se ejecute tan pronto como sea posible.
* La estrategia de reintento de manera predeterminada para una `Job` consiste en usar un _retroceso exponencial_ (descrito en más detalle más adelante en la sección [establecer un RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Programar un trabajo

Después de crear el `Job`, debe programarse con la `FirebaseJobDispatcher` antes de ejecutarse. Existen dos métodos para programar un `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

El valor devuelto por `FirebaseJobDispatcher.Schedule` será uno de los siguientes valores enteros:

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; El `Job` se programó correctamente.
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; Lo que impide que produjo algún problema desconocido el `Job` de programarse.
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; No es válida `IDriver` usó o `IDriver` algún modo no estaba disponible. 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; La `Trigger` no era compatible.
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; El servicio no está configurado correctamente o no está disponible.
 
### <a name="configuring-a-job"></a>Configurar un trabajo

Es posible personalizar un trabajo. Ejemplos de cómo se puede personalizar un trabajo incluyen lo siguiente:

* [Pasar parámetros a un trabajo](#Passing_Parameters_to_a_Job) &ndash; A `Job` puede requerir valores adicionales para realizar su trabajo, por ejemplo descargar un archivo.
* [Establecer restricciones](#Setting_Constraints) &ndash; puede ser necesario ejecutar solo un trabajo cuando se cumplen determinadas condiciones. Por ejemplo, solo se ejecute un `Job` cuando el dispositivo se está cargando. 
* [Especifique cuándo un `Job` debe ejecutarse](#Setting_Job_Triggers) &ndash; el distribuidor de trabajo de Firebase permite a las aplicaciones especificar una hora de cuándo se debe ejecutar el trabajo.  
* [Declarar una estrategia de reintento para trabajos con errores](#Setting_a_RetryStrategy) &ndash; A _estrategia de reintento_ proporciona orientación para la `FirebaseJobDispatcher` sobre qué hacer con `Jobs` que no se finalizan. 

Cada uno de estos temas se describen más en las secciones siguientes.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-jarameters-to-a-job"></a>Jarameters de pasar a un trabajo

Se pasan parámetros a un trabajo mediante la creación de un `Bundle` que se pasa junto con el `Job.Builder.SetExtras` método:

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

Pueden ayudar las restricciones reduce los costos o batería purga en el dispositivo. La `Firebase.JobDispatcher.Constraint` clase define estas restricciones como valores enteros:

* `Constraint.OnUnmeteredNetwork` &ndash; Sólo debe ejecutar el trabajo cuando el dispositivo está conectado a una red unmetered. Esto es útil para evitar que el usuario incurrir en cargos de datos.
* `Constraint.OnAnyNetwork` &ndash; Ejecutar el trabajo en la red al que está conectado el dispositivo. Si se especifica junto con `Constraint.OnUnmeteredNetwork`, este valor tendrá prioridad.
* `Constraint.DeviceCharging` &ndash; Ejecutar el trabajo solo cuando el dispositivo se está cargando.

Las restricciones se establecen con el `Job.Builder.SetConstraint` método: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

El `JobTrigger` proporciona una guía para el sistema operativo sobre cuándo debe comenzar el trabajo. A `JobTrigger` tiene un _ejecutar ventana_ que define una hora programada para saber cuándo el `Job` debe ejecutarse. La ventana de ejecución tiene una _iniciar ventana_ valor y un _ventana de finalización_ valor. La ventana de inicio es el número de segundos que debe esperar el dispositivo antes de ejecutar el trabajo y el valor de la ventana de finalización es el número máximo de segundos que deben transcurrir antes de ejecutar el `Job`. 

A `JobTrigger` pueden crearse con el `Firebase.Jobdispatcher.Trigger.ExecutionWindow` método.  Por ejemplo `Trigger.ExecutionWindow(15,60)` significa que debe ejecutarse el trabajo entre 15 y 60 segundos desde cuando esté programada. El `Job.Builder.SetTrigger` método se utiliza para 

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

El `Firebase.JobDispatcher.RetryStrategy` se utiliza para especificar la cantidad de un retraso de un dispositivo debe utilizar antes de intentar volver a ejecutar un trabajo con error. A `RetryStrategy` tiene un _directiva_, que define qué algoritmo de base de tiempo que se utilizará para volver a programar el trabajo con errores y una ventana de ejecución que especifica una ventana en la que se debe programar el trabajo. Esto _ventana Reprogramación_ se define por dos valores. El primer valor es el número de segundos que deben transcurrir antes de volver a programar el trabajo (el _retroceso inicial_ valor), y el segundo número es el número máximo de segundos antes de que debe ejecutar el trabajo (el _retroceso máximo_valor). 
 
Los dos tipos de directivas de reintento se identifican con estos valores de tipo int:

* `RetryStrategy.RetryPolicyExponential` &ndash; Un _retroceso exponencial_ directiva aumentará el valor inicial de retroceso exponencial después de cada error. La primera vez que se produce un error en un trabajo, la biblioteca esperará el intervalo de _initial que se ha especificado antes de volver a programar el trabajo &ndash; ejemplo 30 segundos. La segunda vez que se produce un error en el trabajo, la biblioteca de espera al menos 60 segundos antes de intentar ejecutar el trabajo. Después de la tercera error del intento, la biblioteca espera 120 segundos y así sucesivamente. El valor predeterminado `RetryStrategy` en el distribuidor de trabajo Firebase biblioteca viene representada por la `RetryStrategy.DefaultExponential` objeto. Tiene un retroceso inicial de 30 segundos y un retroceso máximo de 3600 segundos.
* `RetryStrategy.RetryPolicyLinear` &ndash; Esta estrategia es un _retroceso lineal_ que el trabajo se debe volver a programar para ejecutarse al establece los intervalos (hasta que lo consiga). Retroceso lineal es ideal para el trabajo que se debe completar tan pronto como sea posible o para resolver problemas que rápidamente se resolverán por sí mismos. La biblioteca de distribuidor de trabajo Firebase define un `RetryStrategy.DefaultLinear` que tenga una ventana Reprogramación de al menos 30 segundos y hasta 3600 segundos.

Es posible definir un personalizado `RetryStrategy` con el `FirebaseJobDispatcher.NewRetryStrategy` método. Toma tres parámetros:

1. `int policy` &ndash; El _directiva_ es uno de los procedimientos anteriores `RetryStrategy` valores, `RetryStrategy.RetryPolicyLinear`, o `RetryStrategy.RetryPolicyExponential`.
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

Es posible cancelar todos los trabajos que se han programado o simplemente un único trabajo mediante el `FirebaseJobDispatcher.CancelAll()` método o la `FirebaseJobDispatcher.Cancel(string)` método:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Cualquier método devolverá un valor entero:

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; El trabajo se canceló correctamente.
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; Un error impidió que se está cancelando el trabajo.
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; El `FirebaseJobDispatcher` no puede cancelar el trabajo ya no es válido no `IDriver` disponible.

## <a name="summary"></a>Resumen

Esta guía describe cómo utilizar el distribuidor de trabajo Firebase para realizar el trabajo de forma inteligente en segundo plano. También se han descrito cómo encapsular el trabajo que se va a llevar a cabo como una `JobService` y cómo usar el `FirebaseJobDispatcher` programar ese trabajo, especificar los criterios con un `JobTrigger` y cómo se debe controlar los errores con un `RetryStrategy`.


## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Firebase.JobDispatcher on NuGet](https://www.nuget.org/packages/Xamarin.FirebaseJobDispatcher)
- [firebase-trabajo-distribuidor en GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher Binding](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Programación de trabajos inteligente](https://developer.android.com/topic/performance/scheduling.html)
- [Android batería y optimizaciones de memoria - 2016 de E/S de Google (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
