---
title: Programador de trabajos de Android
description: Esta guía describe cómo programar el trabajo en segundo plano mediante la API del programador de trabajo Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: dc72b7e4da330185b00541f923d9c4b64b91bc95
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2018
ms.locfileid: "30005691"
---
# <a name="android-job-scheduler"></a>Programador de trabajos de Android

_Esta guía describe cómo programar el trabajo en segundo plano mediante la API de programador de trabajo Android, que está disponible en dispositivos Android que ejecuten Android 5.0 (API nivel 21) y versiones posteriores._


## <a name="overview"></a>Información general 

Una de las mejores formas de evitar que la aplicación Android respondiendo al usuario es para asegurarse de que el trabajo complejo o de larga ejecución se realiza en segundo plano. Sin embargo, es importante que trabajo en segundo plano no afectará negativamente a la experiencia del usuario con el dispositivo. 

Por ejemplo, un trabajo en segundo plano puede sondear un sitio Web cada tres o cuatro minutos para consultar para los cambios en un determinado conjunto de datos. Esto puede parecer benigno, sin embargo, tendría un impacto desastroso en duración de la batería. La aplicación se repetidamente reactivar el dispositivo, elevar el nivel de la CPU a un estado de energía más alto, las radios de energía, que las solicitudes de red y, a continuación, procesar los resultados. Obtiene un rendimiento peor porque el dispositivo se apague y se devolverá al estado de inactividad de bajo consumo de energía no inmediatamente. Trabajo en segundo plano programada mal accidentalmente puede mantener el dispositivo en un estado con requisitos de energía innecesaria y excesiva. Esta actividad aparentemente inocente (un sitio Web de sondeo) inutilizará el dispositivo en un período de tiempo relativamente corto.

Android proporciona las siguientes API para ayudar a realizar el trabajo en segundo plano, pero por sí mismos no son suficientes para la programación de trabajos inteligente. 

* **[Servicios intención](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; servicios de intención son excelentes para realizar el trabajo, pero no proporcionan ninguna manera de programar el trabajo.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; estas API sólo permiten que el trabajo para programarse pero se proporciona ninguna manera de realizar el trabajo. Además, la AlarmManager solo permite restricciones basado en el tiempo, lo que significa generar una alarma en un momento determinado o después de que haya transcurrido un período de tiempo determinado. 
* **[Difusión receptores](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; Android una aplicación puede configurar receptores de difusión para realizar el trabajo en respuesta a eventos de todo el sistema o del color. Sin embargo, los receptores de difusión no proporcionan ningún control sobre cuándo se debe ejecutar el trabajo. También se limitan los cambios en el sistema operativo Android cuando funcionará receptores de difusión o los tipos de trabajo que pueden responder a. 

Hay dos características fundamentales para realizar eficazmente el trabajo en segundo plano (a veces se denomina un _trabajo en segundo plano_ o un _trabajo_):

1. **Programar el trabajo de forma inteligente** &ndash; es importante que cuando una aplicación realiza operaciones en segundo plano que lo hace como un buen ciudadano. Idealmente, la aplicación no debería exigir que se ejecute un trabajo. En su lugar, la aplicación debe especificar las condiciones que deben cumplirse para cuando el trabajo se puede ejecutar y, a continuación, programar que el trabajo con el sistema operativo que se llevará a cabo el trabajo cuando se cumplen las condiciones. Esto permite Android ejecutar el trabajo para garantizar la máxima eficiencia en el dispositivo. Por ejemplo, las solicitudes de red se pueden procesar por lotes para ejecutarlos todos al mismo tiempo para sacar el máximo provecho de sobrecarga relacionados con las redes.
2. **Encapsula el trabajo** &ndash; se debe encapsular el código para realizar el trabajo en segundo plano en un componente discreto que puede ejecutar independientemente de la interfaz de usuario y será relativamente fáciles de programar si no se puede completar el trabajo por algún motivo.

El programador de trabajos Android es un marco integrado en el sistema operativo Android que proporciona una API fluida para simplificar la programación trabajo en segundo plano.  El programador de trabajos Android consta de los siguientes tipos:

* El `Android.App.Job.JobScheduler` es un servicio de sistema que se usa para programar, ejecutar y, si es necesario cancelar, trabajos en nombre de una aplicación Android.
* Un `Android.App.Job.JobService` es una clase abstracta que debe ampliarse con la lógica que se ejecutará el trabajo en el subproceso principal de la aplicación. Esto significa que la `JobService` es responsable de cómo es el trabajo que se realice de forma asincrónica.
* Un `Android.App.Job.JobInfo` objeto contiene los criterios para guiar Android cuándo se debe ejecutar el trabajo.

Para programar el trabajo con el programador de trabajos Android, una aplicación de Xamarin.Android debe encapsular el código en una clase que extiende la `JobService` clase. `JobService` tiene tres métodos de ciclo de vida que se puede llamar durante la duración del trabajo:

* **BOOL OnStartJob (parámetros de JobParameters)** &ndash; este método es invocado por el `JobScheduler` para realizar el trabajo y se ejecuta en el subproceso principal de la aplicación. Es responsabilidad de la `JobService` para realizar el trabajo de forma asincrónica y `true` si hay trabajo restante, o `false` si se realiza el trabajo.
    
    Cuando el `JobScheduler` llama a este método, solicitará y conservar una wakelock de Android para la duración del trabajo. Cuando finalice el trabajo, es responsabilidad de la `JobService` para indicar el `JobScheduler` de este hecho por llamada la `JobFinished` método (que se describe a continuación).

* **JobFinished (parámetros de JobParameters, bool needsReschedule)** &ndash; debe llamar a este método el `JobService` para indicar el `JobScheduler` que realizan el trabajo. Si `JobFinished` no se llama, el `JobScheduler` no quitará el wakelock, provocando el consumo de batería innecesarios. 

* **BOOL OnStopJob (parámetros de JobParameters)** &ndash; se llama cuando el trabajo se detiene prematuramente Android. Debe devolver `true` si el trabajo se debe programar basándose en los criterios de reintento (descritos a continuación con más detalle).

Es posible especificar _restricciones_ o _desencadenadores_ que controlará cuando un trabajo puede o debe ejecutarse. Por ejemplo, es posible restringir un trabajo para que sólo se ejecutará cuando el dispositivo se está cargando o iniciar un trabajo cuando una imagen se toma.

Esta guía describe en detalle cómo implementar un `JobService` clase y programar con el `JobScheduler`.

## <a name="requirements"></a>Requisitos

El programador de trabajos Android requiere el nivel de API de Android 21 (Android 5.0) o posterior. 

## <a name="using-the-android-job-scheduler"></a>Usar al programador de trabajos de Android

Hay tres pasos para usar la API de JobScheduler Android:

1. Implementar un tipo de JobService para encapsular el trabajo.
2. Use un `JobInfo.Builder` objeto que se va a crear la `JobInfo` objeto que contendrá los criterios para la `JobScheduler` para ejecutar el trabajo. 
3. Programar el trabajo con `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Implementar un JobService

Todo el trabajo realizado por la biblioteca del programador de trabajos Android debe realizarse en un tipo que extiende el `Android.App.Job.JobService` clase abstracta. Crear un `JobService` es muy similar a la creación de un `Service` con el marco de trabajo Android: 

1. Ampliar la `JobService` clase.
2. Decorar la subclase con el `ServiceAttribute` y establezca el `Name` parámetro en una cadena que está formada por el nombre del paquete y el nombre de la clase (vea el ejemplo siguiente).
3. Establecer el `Permission` propiedad en el `ServiceAttribute` a la cadena de `android.permission.BIND_JOB_SERVICE`.
4. Invalidar el `OnStartJob` método, agregue el código para realizar el trabajo. Android invoca este método en el subproceso principal de la aplicación para ejecutar el trabajo. Trabajo que tardan más que unos pocos milisegundos deben realizarse en un subproceso para evitar el bloqueo de la aplicación.
5. Cuando se realiza el trabajo, el `JobService` debe llamar a la `JobFinished` método. Este método es cómo `JobService` indica la `JobScheduler` que se realiza el trabajo. Error al llamar a `JobFinished` dará como resultado la `JobService` colocar peticiones innecesarias en el dispositivo, lo que reduce la duración de la batería. 
6. Es una buena idea para invalidar el `OnStopJob` método. Android llama a este método cuando el trabajo se está cerrando antes de que finalice y proporciona la `JobService` con una oportunidad para eliminar correctamente el de todos los recursos. Este método debe devolver `true` si es necesario volver a programar el trabajo, o `false` si no es desireable para volver a ejecutar el trabajo.
   

El código siguiente es un ejemplo de los más sencillos `JobService` para una aplicación, usando la biblioteca TPL para realizar algún trabajo de forma asincrónica:

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

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Crear un JobInfo para programar un trabajo

No cree instancias de las aplicaciones de Xamarin.Android una `JobService` directamente, en su lugar, pasará un `JobInfo` el objeto a la `JobScheduler`. El `JobScheduler` creará una instancia solicitado `JobService` objeto, programar y ejecutar la `JobService` según los metadatos de la `JobInfo`. Un `JobInfo` objeto debe contener la siguiente información:

* **JobId** &ndash; se trata de un `int` valor que se usa para identificar un trabajo para el `JobScheduler`. Volver a usar este valor se actualizarán los trabajos existentes. El valor debe ser único para la aplicación. 
* **JobService** &ndash; este parámetro es un `ComponentName` que identifica de forma explícita el tipo que el `JobScheduler` debe usar para ejecutar un trabajo. 
  

Este método de extensión muestra cómo crear un `JobInfo.Builder` con un Android `Context`, por ejemplo, una actividad:

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

// Sample usage - creates a JobBuilder for a DownloadJob andsets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creats a JobInfo object.
```

Una característica muy eficaz del programador de trabajos Android es la capacidad para controlar cuándo se ejecuta un trabajo o en qué condiciones de un trabajo se pueden ejecutar. La tabla siguiente describen algunos de los métodos en `JobInfo.Builder` que permiten a una aplicación influir en si puede ejecutar un trabajo:  


|  Método | Descripción   |
|---|---|
| `SetMinimumLatency`  | Especifica que se ejecuta un retraso (en milisegundos) que debería tenerse en cuenta antes de un trabajo. |
| `SetOverridingDeadline`  | Declara que el trabajo debe ejecutarse antes de que haya transcurrido este tiempo (en milisegundos). |
| `SetRequiredNetworkType`  | Especifica los requisitos de red para un trabajo. |
| `SetRequiresBatteryNotLow` | Sólo puede ejecutar el trabajo cuando el dispositivo no muestra una advertencia de "batería baja" al usuario. |
| `SetRequiresCharging` | Sólo puede ejecutar el trabajo cuando la batería se está cargando. |
| `SetDeviceIdle` | El trabajo se ejecutará cuando el dispositivo está ocupado. |
| `SetPeriodic` | Especifica que el trabajo se debe ejecutar con regularidad. |
| `SetPersisted` | El trabajo debe perisist durante el reinicio del dispositivo. | 


El `SetBackoffCriteria` proporciona alguna orientación sobre cómo larga el `JobScheduler` debe esperar antes de intentar volver a ejecutar un trabajo. Hay dos partes para los criterios de retroceso: un retraso en milisegundos (valor predeterminado de 30 segundos) y el tipo de interrupción que se debe usar (a veces se denomina el _directiva de retroceso_ o _directiva de reintentos_) . Las dos directivas se encapsulan en la `Android.App.Job.BackoffPolicy` enum:

* `BackoffPolicy.Exponential` &ndash; Directiva de retroceso exponencial aumentará el valor inicial de retroceso exponencial después de cada error. La primera vez que se produce un error en un trabajo, la biblioteca esperará el intervalo inicial que se ha especificado antes de volver a programar el trabajo: ejemplo de 30 segundos. La segunda vez que se produce un error en el trabajo, la biblioteca de espera al menos 60 segundos antes de intentar ejecutar el trabajo. Después de la tercera error del intento, la biblioteca espera 120 segundos y así sucesivamente. Este es el valor predeterminado.
* `BackoffPolicy.Linear` &ndash; Esta estrategia es un retroceso lineal que el trabajo se debe volver a programar para ejecutarse a intervalos establecidos (hasta que se realiza correctamente). Retroceso lineal es ideal para el trabajo que se debe completar tan pronto como sea posible o para resolver problemas que rápidamente se resolverán por sí mismos. 

Para obtener más información acerca de creación de un `JobInfo` objeto, lea [documentación de Google para la `JobInfo.Builder` clase](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Pasar parámetros a un trabajo a través de la JobInfo

Se pasan parámetros a un trabajo mediante la creación de un `PersistableBundle` que se pasa junto con el `Job.Builder.SetExtras` método:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

El `PersistableBundle` se obtiene acceso desde el `Android.App.Job.JobParameters.Extras` propiedad en el `OnStartJob` método de una `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Programar un trabajo

Para programar un trabajo, una aplicación Xamarin.Android obtendrá una referencia a la `JobScheduler` servicio del sistema y llamar a la `JobScheduler.Schedule` método con el `JobInfo` objeto que se creó en el paso anterior. `JobScheduler.Schedule` devolverá inmediatamente con uno de dos valores enteros:

* **JobScheduler.ResultSuccess** &ndash; el trabajo ha sido correctamente programado. 
* **JobScheduler.ResultFailure** &ndash; no se pudo programar el trabajo. Esto suele deberse a conflictos `JobInfo` parámetros.

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

Es posible cancelar todos los trabajos que se han programado o simplemente un único trabajo mediante el `JobsScheduler.CancelAll()` método o la `JobScheduler.Cancel(jobId)` método:

```csharp
// Cancel all jobs
jobSchduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Resumen

Esta guía describe cómo usar al programador de trabajos Android para realizar el trabajo de forma inteligente en segundo plano. También se han descrito cómo encapsular el trabajo que se va a llevar a cabo como una `JobService` y cómo usar el `JobScheduler` programar ese trabajo, especificar los criterios con un `JobTrigger` y cómo se debe controlar los errores con un `RetryStrategy`.

## <a name="related-links"></a>Vínculos relacionados

- [Programación de trabajos inteligente](https://developer.android.com/topic/performance/scheduling.html)
- [Referencia de la API de JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Programación de trabajos como un profesional con JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android batería y optimizaciones de memoria - 2016 de E/S de Google (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Universidad de Xamarin Android JobScheduler - René Ruppert-](https://www.youtube.com/watch?v=aSjBBPYjelE)