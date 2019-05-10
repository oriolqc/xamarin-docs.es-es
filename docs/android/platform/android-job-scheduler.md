---
title: Programador de trabajos de Android
description: Esta guía describe cómo programar el trabajo en segundo plano mediante la API del programador de trabajo Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: d69a96ee55b09ef9fcf1485ec34d986dd40e7662
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977974"
---
# <a name="android-job-scheduler"></a>Programador de trabajos de Android

_Esta guía describe cómo programar el trabajo en segundo plano mediante la API de programador de trabajo Android, que está disponible en dispositivos Android que ejecuten Android 5.0 (API nivel 21) y versiones posteriores._


## <a name="overview"></a>Información general 

Una de las mejores formas para que una aplicación de Android siga respondiendo al usuario es para asegurarse de que el trabajo complejo o de larga ejecución se realiza en segundo plano. Sin embargo, es importante que trabajo en segundo plano no afectará negativamente la experiencia del usuario con el dispositivo. 

Por ejemplo, un trabajo en segundo plano puede sondear un sitio Web cada tres o cuatro minutos para consultar los cambios a un determinado conjunto de datos. Esto parece ser benigno, pero tendría un impacto desastroso en duración de la batería. La aplicación se repetidamente el dispositivo de reactivación, elevar la CPU a un estado de encendido, el radio de energía, hacer que las solicitudes de red y, a continuación, procesar los resultados. Lo empeora porque el dispositivo se apague y se devolverá al estado inactivo de baja energía no inmediatamente. Trabajo en segundo plano programado mal accidentalmente puede mantener el dispositivo en un estado con requisitos de energía innecesaria y excesiva. Esta actividad aparentemente inofensivo (un sitio Web de sondeo) representará el dispositivo inutilizable en un período de tiempo relativamente corto.

Android proporciona las siguientes API para ayudar a realizar el trabajo en segundo plano, pero por sí solos no son suficientes para la programación de tareas inteligente. 

* **[Servicios de intención](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; servicios de intención son excelentes para llevar a cabo el trabajo, pero que no ofrecen ninguna forma para programar el trabajo.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; estas API solo permiten que el trabajo puede programar pero no ofrecen ninguna forma para llevar a cabo el trabajo. Además, el AlarmManager solo permite las restricciones en función del tiempo, lo que significa que genera una alarma en un momento determinado o una vez transcurrido un período de tiempo determinado. 
* **[Receptores de difusión](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; An Android app puede configurar receptores de difusión para llevar a cabo trabajo en respuesta a eventos de todo el sistema o intenciones. Sin embargo, los receptores de difusión no proporcionan ningún control sobre cuándo se debe ejecutar el trabajo. También se restringirán los cambios en el sistema operativo Android cuando funcionará receptores de difusión o los tipos de trabajo que puede responder. 

Hay dos características clave para realizar eficazmente el trabajo en segundo plano (a veces se denomina un _trabajo en segundo plano_ o un _trabajo_):

1. **Programar el trabajo de forma inteligente** &ndash; es importante que cuando una aplicación está realizando trabajo en segundo plano que lo hace como un buen ciudadano. Idealmente, la aplicación no debería exigir que se ejecute un trabajo. En su lugar, la aplicación debe especificar las condiciones que deben cumplirse para cuando el trabajo puede ejecutar y, a continuación, programar que el trabajo con el sistema operativo que se llevará a cabo el trabajo cuando se cumplen las condiciones. Esto permite a ejecutar el trabajo para garantizar la máxima eficacia en el dispositivo Android. Por ejemplo, las solicitudes de red se pueden procesar por lotes para ejecutar todas al mismo tiempo para hacer un uso máximo de sobrecarga relacionados con las redes.
2. **Que encapsula el trabajo** &ndash; se debe encapsular el código para realizar el trabajo en segundo plano en un componente discreto que se puede ejecutar independientemente de la interfaz de usuario y será relativamente fácil volver a programar si no se puede completar el trabajo por algún motivo.

El programador de trabajos de Android es un marco integrado en el sistema operativo Android que proporciona una API fluida para simplificar el trabajo de programación en segundo plano.  El programador de trabajos de Android consta de los siguientes tipos:

* El `Android.App.Job.JobScheduler` es un servicio de sistema que se usa para programar, ejecutar y, si es necesario cancelar, trabajos en el nombre de una aplicación de Android.
* Un `Android.App.Job.JobService` es una clase abstracta que debe ampliarse con la lógica que se ejecutará el trabajo en el subproceso principal de la aplicación. Esto significa que el `JobService` es responsable de cómo es el trabajo se realiza de forma asincrónica.
* Un `Android.App.Job.JobInfo` objeto contiene los criterios para guiar Android cuándo se debe ejecutar el trabajo.

Para programar el trabajo con el programador de trabajos de Android, una aplicación de Xamarin.Android debe encapsular el código en una clase que extiende el `JobService` clase. `JobService` tiene tres métodos de ciclo de vida que se pueden llamar durante la duración del trabajo:

* **BOOL (parámetros JobParameters) OnStartJob** &ndash; llama a este método la `JobScheduler` para realizar el trabajo y se ejecuta en el subproceso principal de la aplicación. Es responsabilidad de la `JobService` para realizar el trabajo de forma asincrónica y `true` si hay trabajo restante, o `false` si se realiza el trabajo.
    
    Cuando el `JobScheduler` llama a este método, solicitará y conservar una wakelock de Android para la duración del trabajo. Cuando finalice el trabajo, es responsabilidad de la `JobService` para indicar el `JobScheduler` de este hecho por llamada la `JobFinished` método (que se describe a continuación).

* **JobFinished (parámetros JobParameters, bool needsReschedule)** &ndash; este método debe llamarse mediante el `JobService` para indicar el `JobScheduler` que se realiza el trabajo. Si `JobFinished` no se llama, el `JobScheduler` no quitará el wakelock, provocando la purga de la batería innecesarios. 

* **BOOL (parámetros JobParameters) OnStopJob** &ndash; se llama cuando el trabajo se detiene antes de tiempo por Android. Debe devolver `true` si se debe volver a programar el trabajo según los criterios de reintento (descritos a continuación con más detalle).

Es posible especificar _restricciones_ o _desencadenadores_ controlará cuándo un trabajo puede o debe ejecutarse. Por ejemplo, es posible restringir un trabajo para que solo se ejecutará cuando el dispositivo se está cargando o se toma iniciar un trabajo cuando una imagen.

Esta guía describirá en detalle cómo implementar un `JobService` clase y programarlo con el `JobScheduler`.

## <a name="requirements"></a>Requisitos

El programador de trabajos Android requiere Android API nivel 21 (Android 5.0) o superior. 

## <a name="using-the-android-job-scheduler"></a>Usar al programador de trabajos de Android

Hay tres pasos para usar la API de Android JobScheduler:

1. Implementar un tipo JobService para encapsular el trabajo.
2. Use un `JobInfo.Builder` objeto va a crear el `JobInfo` objeto que va a contener los criterios para la `JobScheduler` para ejecutar el trabajo. 
3. Programar el trabajo mediante `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Implementar un JobService

Todo el trabajo realizado por la biblioteca del programador de trabajo Android debe realizarse en un tipo que extiende el `Android.App.Job.JobService` clase abstracta. Creación de un `JobService` es muy similar a la creación de un `Service` con el marco de trabajo Android: 

1. Ampliar la `JobService` clase.
2. Decorar la subclase con el `ServiceAttribute` y establezca el `Name` parámetro en una cadena que está formada por el nombre del paquete y el nombre de la clase (vea el ejemplo siguiente).
3. Establecer el `Permission` propiedad en el `ServiceAttribute` a la cadena `android.permission.BIND_JOB_SERVICE`.
4. Invalidar el `OnStartJob` método, agregue el código para realizar el trabajo. Android invocará este método en el subproceso principal de la aplicación para ejecutar el trabajo. Trabajo que tarda más que unos pocos milisegundos deben realizarse en un subproceso para evitar el bloqueo de la aplicación.
5. Cuando se realiza el trabajo, el `JobService` debe llamar a la `JobFinished` método. Este método es cómo `JobService` indica la `JobScheduler` que se realiza el trabajo. Error al llamar a `JobFinished` dará como resultado la `JobService` colocar las demandas innecesarias en el dispositivo, lo que reduce la duración de la batería. 
6. Es una buena idea para invalidar también el `OnStopJob` método. Android llama a este método cuando el trabajo se está cerrando antes de que haya finalizado y proporciona el `JobService` una oportunidad para desechar correctamente todos los recursos. Este método debe devolver `true` si es necesario volver a programar el trabajo, o `false` si no es desborde sea deseable para volver a ejecutar el trabajo.
   

El código siguiente es un ejemplo de las más sencillas `JobService` para una aplicación, usando la biblioteca TPL para realizar algún trabajo de forma asincrónica:

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Creación de un elemento JobInfo para programar un trabajo

No cree instancias de las aplicaciones de Xamarin.Android un `JobService` directamente, en su lugar, pasará un `JobInfo` de objeto para el `JobScheduler`. El `JobScheduler` creará instancias solicitado `JobService` objeto, programar y ejecutar la `JobService` según los metadatos en el `JobInfo`. Un `JobInfo` objeto debe contener la siguiente información:

* **JobId** &ndash; se trata de un `int` valor que se usa para identificar un trabajo para el `JobScheduler`. Volver a usar este valor se actualizarán los trabajos existentes. El valor debe ser único para la aplicación. 
* **JobService** &ndash; este parámetro es un `ComponentName` que identifica de forma explícita el tipo que el `JobScheduler` debe usar para ejecutar un trabajo. 
  

Este método de extensión muestra cómo crear un `JobInfo.Builder` con Android `Context`, por ejemplo, una actividad:

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob and sets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creates a JobInfo object.
```

Una característica eficaz de programador de trabajos de Android es la capacidad para controlar cuándo se ejecuta un trabajo o en qué condiciones de un trabajo se pueden ejecutar. La tabla siguiente describen algunos de los métodos en `JobInfo.Builder` que permitir que una aplicación influir en la que puede ejecutarse un trabajo:  


|  Método | Descripción   |
|---|---|
| `SetMinimumLatency`  | Especifica que se ejecuta un retraso (en milisegundos) que debe tenerse en cuenta antes de un trabajo. |
| `SetOverridingDeadline`  | Declara que el trabajo debe ejecutarse antes de que haya transcurrido este tiempo (en milisegundos). |
| `SetRequiredNetworkType`  | Especifica los requisitos de red para un trabajo. |
| `SetRequiresBatteryNotLow` | El trabajo puede ejecutarse solo cuando el dispositivo no muestra una advertencia "batería baja" al usuario. |
| `SetRequiresCharging` | El trabajo puede ejecutarse solo cuando la batería se carga. |
| `SetDeviceIdle` | El trabajo se ejecutará cuando el dispositivo está ocupado. |
| `SetPeriodic` | Especifica que el trabajo se debe ejecutar con regularidad. |
| `SetPersisted` | El trabajo debe perisist en los reinicios del dispositivo. | 


El `SetBackoffCriteria` proporciona algunas instrucciones acerca de cómo mientras el `JobScheduler` debe esperar antes de intentar volver a ejecutar un trabajo. Los criterios de retroceso consta de dos partes: un retraso en milisegundos (valor predeterminado de 30 segundos) y el tipo de rechazo que debe usarse (a veces se denomina el _directiva de retroceso_ o el _directiva de reintentos_) . Las dos directivas se encapsulan en el `Android.App.Job.BackoffPolicy` enum:

* `BackoffPolicy.Exponential` &ndash; Una directiva de retroceso exponencial aumentará el valor inicial de retroceso exponencial después de cada error. La primera vez que se produce un error en un trabajo, la biblioteca esperará el intervalo inicial que se ha especificado antes de volver a programar el trabajo: ejemplo de 30 segundos. La segunda vez que se produce un error en el trabajo, la biblioteca de espera al menos 60 segundos antes de intentar ejecutar el trabajo. Después de la tercera error del intento, la biblioteca de espera de 120 segundos y así sucesivamente. Este es el valor predeterminado.
* `BackoffPolicy.Linear` &ndash; Esta estrategia es un retroceso lineal que se debe volver a programar el trabajo para ejecutarse a intervalos establecidos (hasta que se realiza correctamente). Retroceso lineal es más adecuado para el trabajo que debe completarse tan pronto como sea posible o para resolver problemas que se resolverá rápidamente a sí mismos. 

Para obtener más detalles sobre, cree un `JobInfo` objeto, lea [documentación de Google para la `JobInfo.Builder` clase](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Pasar parámetros a un trabajo a través de la JobInfo

Los parámetros se pasan a un trabajo mediante la creación de un `PersistableBundle` que se pasa junto con el `Job.Builder.SetExtras` método:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

El `PersistableBundle` se obtiene acceso desde el `Android.App.Job.JobParameters.Extras` propiedad en el `OnStartJob` método de un `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Programar un trabajo

Para programar un trabajo, una aplicación de Xamarin.Android obtendrá una referencia a la `JobScheduler` servicio del sistema y llamar a la `JobScheduler.Schedule` método con el `JobInfo` objeto que se creó en el paso anterior. `JobScheduler.Schedule` devolverá inmediatamente con uno de dos valores enteros:

* **JobScheduler.ResultSuccess** &ndash; el trabajo se programó correctamente. 
* **JobScheduler.ResultFailure** &ndash; no se puede programar el trabajo. Esto se debe normalmente a entrar en conflicto `JobInfo` parámetros.

Este código es un ejemplo de programación de un trabajo y notificar al usuario de los resultados del intento de programación:

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
}
else
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
}
```
 
### <a name="cancelling-a-job"></a>Cancelar un trabajo

Es posible cancelar todos los trabajos que se han programado, o simplemente un único trabajo mediante el `JobsScheduler.CancelAll()` método o la `JobScheduler.Cancel(jobId)` método:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Resumen

Esta guía describe cómo usar al programador de trabajos de Android para realizar el trabajo de forma inteligente en segundo plano. También se han descrito cómo encapsular el trabajo debe realizarse como una `JobService` y cómo usar el `JobScheduler` para programar ese trabajo, especificar los criterios con un `JobTrigger` y cómo se deben controlar los errores con un `RetryStrategy`.

## <a name="related-links"></a>Vínculos relacionados

- [Programación de tareas inteligente](https://developer.android.com/topic/performance/scheduling.html)
- [Referencia de API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Programación de trabajos como un profesional con JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android batería y optimizaciones de memoria: Google E/S 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler - René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
