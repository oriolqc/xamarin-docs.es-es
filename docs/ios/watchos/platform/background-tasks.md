---
title: watchOS tareas en segundo plano en Xamarin
description: Este documento describe cómo usar tareas en segundo plano con watchOS en Xamarin, echar un vistazo a los tipos de tareas en segundo plano, el uso de recursos, implementar tareas en segundo plano, la programación, mejores prácticas y mucho más.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/13/2017
ms.openlocfilehash: e28ba19fdc972b962f0dcd2757f1ba9087ac5c27
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831776"
---
# <a name="watchos-background-tasks-in-xamarin"></a>watchOS tareas en segundo plano en Xamarin

Con watchOS 3, hay tres maneras principales que una aplicación de inspección puede mantenerse su información: 

- Uso de una de las varias tareas en segundo plano nuevo. 
- Puede tener uno de sus complicaciones en la pantalla del reloj (dando por vez extra para actualizar). 
- Tener el pin del usuario para la aplicación a la nueva base (donde su conservan en memoria y se actualiza con frecuencia). 

## <a name="keeping-an-app-up-to-date"></a>Mantener una aplicación actualizada

Antes de tratar todas las formas en que un desarrollador puede mantener datos y la interfaz de usuario de una aplicación de watchOS actual y actualizada, en esta sección se Eche un vistazo a un conjunto típico de patrones de uso y cómo un usuario puede mover entre su iPhone y su Apple Watch a lo largo del día según  la hora del día y la actividad que actualmente hacen (por ejemplo, promover).

Considere el ejemplo siguiente:

[![](background-tasks-images/update00.png "Cómo un usuario puede mover entre su iPhone y su Apple Watch a lo largo del día")](background-tasks-images/update00.png#lightbox)

1. En la mañana, mientras se espera en línea para un café, el usuario explora la noticia actual en su iPhone durante varios minutos.
2. Antes de salir de la cafetería, rápidamente Compruebe la información meteorológica con una complicación en su esfera del reloj.
3. Antes del almuerzo, usan la aplicación se asigna en el iPhone para buscar un restaurantes cercanos y hacer una reserva para satisfacer a un cliente.
4. Mientras está de viaje al restaurante, permite recibir una notificación en su Apple Watch y con una vista rápida, saben que su almuerzo se está ejecutando en tiempo de ejecución.
5. Por la noche, usa la aplicación de mapas en el iPhone para comprobar el tráfico antes del inicio de conducción.
6. En el modo principal, reciben una notificación de iMessage en su Apple Watch pedirle a recoger algunas leche y usan la característica de respuesta rápida para enviar la respuesta "Aceptar".

Debido a la "vista rápida" (menos de tres segundos) la naturaleza de cómo un usuario que desee usar una aplicación de Apple Watch, hay normalmente no es suficiente tiempo para la aplicación capturar la información deseada y actualizar su interfaz de usuario antes de mostrarla al usuario.

Mediante el uso de la nueva API de Apple ha incluido en watchOS 3, puede programar la aplicación para un _actualización en segundo plano_ y tiene la información deseada lista antes de que el usuario lo solicita. Tome como ejemplo la complicación de tiempo descritos anteriormente:

[![](background-tasks-images/update01.png "Un ejemplo de la complicación de tiempo")](background-tasks-images/update01.png#lightbox)

1. Las programaciones de aplicación reactive el sistema en un momento determinado. 
2. La aplicación captura la información que requiere para generar la actualización.
3. La aplicación vuelve a generar la interfaz de usuario para reflejar los nuevos datos.
4. Cuando el usuario se resumen en las complicaciones de la aplicación, tiene información reciente sin tener que esperar la actualización que el usuario.

Como hemos visto anteriormente, el sistema de watchOS activa la aplicación mediante una o varias tareas, de los cuales tiene un grupo muy limitado disponible:

[![](background-tasks-images/update02.png "El sistema de watchOS activa la aplicación mediante una o varias tareas")](background-tasks-images/update02.png#lightbox)

Apple sugiere sacar el máximo partido de esta tarea (ya que es un recurso limitado a la aplicación) manteniendo al mismo hasta que la aplicación haya finalizado el proceso de actualización en sí mismo.

El sistema entrega estas tareas mediante una llamada a la nueva `HandleBackgroundTasks` método de la `WKExtensionDelegate` delegar. Por ejemplo:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Constructors
        public ExtensionDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request here
            ...
        }
        #endregion
    }
}
```

Cuando la aplicación haya finalizado la tarea determinada, devuelve al sistema marcándolo completado:

[![](background-tasks-images/update03.png "La tarea se devuelve al sistema marcándolo completado")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Nuevas tareas en segundo plano

watchOS 3 incluye varias tareas en segundo plano que una aplicación puede usar para actualizar su información asegurarse de que tiene el contenido que el usuario necesita antes de abrir la aplicación, como:

- **Actualización de la aplicación en segundo plano** : la [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) tareas permite que la aplicación actualizar su estado en segundo plano. Normalmente esto incluirá la otra tarea, como descargar nuevo contenido desde internet mediante un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Instantáneas de actualización en segundo plano** : la [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) tareas permite que la aplicación actualizar su contenido y la interfaz de usuario antes de que el sistema toma una instantánea que se usará para rellenar el muelle.
- **En segundo plano inspección conectividad** : la [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) se inicia una tarea de la aplicación cuando recibe datos en segundo plano desde el iPhone emparejado.
- **En segundo plano de la sesión de la dirección URL** : la [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) se inicia una tarea de la aplicación cuando una transferencia en segundo plano requiere autorización o completa (éxito o error).

Estas tareas se tratarán en detalle en las secciones siguientes.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

El `WKApplicationRefreshBackgroundTask` es una tarea genérica que puede programarse para que la aplicación despertada en una fecha futura:

[![](background-tasks-images/update04.png "Un WKApplicationRefreshBackgroundTask despertado en una fecha futura")](background-tasks-images/update04.png#lightbox)

En el tiempo de ejecución de la tarea, la aplicación puede realizar cualquier tipo de procesamiento local como la actualización de una escala de tiempo de complicación o capturar algunos datos necesarios con un `NSUrlSession`.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

El sistema le enviará un `WKURLSessionRefreshBackgroundTask` cuando los datos ha terminado de descargar y listos para ser procesados por la aplicación:

[![](background-tasks-images/update05.png "El WKURLSessionRefreshBackgroundTask cuando haya acabado la descarga de los datos")](background-tasks-images/update05.png#lightbox)

Una aplicación no se deja en ejecución mientras se descargan datos en segundo plano. En su lugar, la aplicación programa la solicitud de datos, a continuación, está suspendido y el sistema controla la descarga de los datos, reawakening solo la aplicación una vez completada la descarga.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

En watchOS 3, Apple ha agregado el muelle donde los usuarios pueden anclar sus aplicaciones favoritas y acceder rápidamente a ellos. Cuando el usuario presiona el botón en el Apple Watch, se mostrará una galería de instantáneas de la aplicación anclada. El usuario puede deslizar izquierda o derecha para buscar la aplicación deseada, a continuación, puntee en la aplicación para iniciarla reemplazando la instantánea con la interfaz de la aplicación en ejecución.

[![](background-tasks-images/update06.png "Reemplazando la instantánea con la interfaz de las aplicaciones en ejecución")](background-tasks-images/update06.png#lightbox)

El sistema toma periódicamente instantáneas de la interfaz de usuario de la aplicación (mediante el envío de un `WKSnapshotRefreshBackgroundTask`) y usa esas instantáneas para rellenar el muelle. watchOS ofrece la oportunidad de actualizar su contenido y la interfaz de usuario antes de esta instantánea se toma de la aplicación.

Las instantáneas son muy importantes para watchOS 3 ya que funcionan como las imágenes de la vista previa y el inicio de la aplicación. Si el usuario necesite en una aplicación en el Dock, se expandirá a pantalla completa, escriba el primer plano y comenzar a ejecutarse, por lo que es imperativo que la instantánea esté actualizada:

[![](background-tasks-images/update07.png "Si el usuario necesite en una aplicación en el Dock, se expandirá a pantalla completa")](background-tasks-images/update07.png#lightbox)

De nuevo, el sistema emitirá un `WKSnapshotRefreshBackgroundTask` para que se puede preparar la aplicación (al actualizar los datos y la interfaz de usuario) antes de tomar la instantánea:

[![](background-tasks-images/update08.png "Puede preparar la aplicación mediante la actualización de los datos y la interfaz de usuario antes de que se tomó la instantánea")](background-tasks-images/update08.png#lightbox)

Cuando se marca la aplicación la `WKSnapshotRefreshBackgroundTask` completado, el sistema automáticamente tardará una instantánea de la interfaz de usuario de la aplicación.

> [!IMPORTANT]
> Es importante siempre se programan una `WKSnapshotRefreshBackgroundTask` después de la aplicación ha recibido datos nuevos y actualizar su interfaz de usuario o el usuario no verá la información modificada.




Además, cuando el usuario recibe una notificación de la aplicación y pulsa para hacer que la aplicación en primer plano, la instantánea debe ser actualizados ya que está actuando como pantalla de inicio:

[![](background-tasks-images/update09.png "El usuario recibe una notificación de la aplicación y pulsa para hacer que la aplicación en primer plano")](background-tasks-images/update09.png#lightbox)

Si han pasado más de una hora desde que el usuario interactúe con una aplicación para watchOS, podrá volver a su estado predeterminado. El estado predeterminado puede tener diferentes significados para diferentes aplicaciones y, en función del diseño de una aplicación, quizás no tenga un estado predeterminado en absoluto.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

En watchOS 3, Apple ha integrado inspección conectividad con la API de actualización en segundo plano a través del nuevo `WKWatchConnectivityRefreshBackgroundTask`. Con esta nueva característica, una aplicación de iPhone puede entregar datos nuevos a su equivalente de la aplicación de inspección, mientras se está ejecutando la aplicación para watchOS en segundo plano:

[![](background-tasks-images/update10.png "Una aplicación de iPhone puede entregar datos nuevos a su equivalente de la aplicación de inspección, mientras se está ejecutando la aplicación para watchOS en segundo plano")](background-tasks-images/update10.png#lightbox)

Iniciar una complicación de inserción, el contexto de aplicación, envíe un archivo o actualizar la información de usuario de la aplicación de iPhone se activará la aplicación Apple Watch en segundo plano.

Cuando la aplicación del reloj se ha activado a través de un `WKWatchConnectivityRefreshBackgroundTask` deberá usar los métodos de API estándares para recibir los datos de la aplicación de iPhone.

[![](background-tasks-images/update11.png "El flujo de datos WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Asegúrese de que ha activado la sesión.
2. Supervisar la nueva `HasContentPending` siempre que sea el valor de propiedad `true`, la aplicación todavía no tiene datos para procesar. Como antes, la aplicación debe retener la tarea hasta que haya terminado de procesar todos los datos.
3. Cuando no hay ningún dato más para procesarse (`HasContentPending = false`), marque la tarea se completó para devolverlo al sistema. Si no lo agotará el tiempo de ejecución de la aplicación asignado en segundo plano resultante en un informe de bloqueo.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>El ciclo de vida de la API en segundo plano

La colocación de todas las partes de la nueva API de tareas en segundo plano juntos, un conjunto típico de interacciones sería similar al siguiente:

[![](background-tasks-images/update12.png "El ciclo de vida de la API en segundo plano")](background-tasks-images/update12.png#lightbox)

1. En primer lugar, la aplicación para watchOS programa un tarea a estar activo en el futuro como algún momento de fondo.
2. La aplicación se ha despertado por el sistema y envía una tarea.
3. La aplicación procesa la tarea para completar cualquier trabajo era necesario.
4. Como resultado de la tarea de procesamiento, la aplicación que deba programar más en segundo plano las tareas que realizar más trabajo en el futuro, como la descarga de contenido más con un `NSUrlSession`.
5. La aplicación marca la tarea se completó y lo devuelve al sistema.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Usar recursos de forma responsable

Es fundamental que una aplicación para watchOS se comporta de forma responsable dentro de este ecosistema limitando su consumo de recursos compartidos del sistema.

Eche un vistazo en el siguiente escenario:

[![](background-tasks-images/update13.png "Una aplicación para watchOS limita su consumo de recursos compartidos del sistema")](background-tasks-images/update13.png#lightbox)

1. El usuario inicia una aplicación para watchOS a las 13:00.
2. La aplicación programa una tarea para reactivar y descargar nuevo contenido en una hora a las 2:00.
3. A las 13:50, el usuario vuelve a abre la aplicación que le permite actualizar sus datos y la interfaz de usuario en este momento.
4. En lugar de permitir la reactivación de la tarea la aplicación de nuevo en 10 minutos, la aplicación debe volver a programar la tarea para ejecutar una hora posterior a las 2:50.

Aunque cada aplicación es diferente, Apple sugiere la búsqueda de patrones de uso, como los que se muestran arriba, para ayudar a ahorrar recursos del sistema.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implementar tareas en segundo plano

Por ejemplo, este documento utilizará la aplicación de deportes MonkeySoccer falsa que informa al usuario de las puntuaciones de fútbol. 

Eche un vistazo en el siguiente escenario de uso típico:

[![](background-tasks-images/update14.png "El escenario de uso típico")](background-tasks-images/update14.png#lightbox)

Al equipo de fútbol favoritos del usuario está reproduciendo a una coincidencia grande de 7:00 a 9:00 p. M., por lo que la aplicación debe esperar el usuario para comprobar con regularidad la puntuación y decide en un intervalo de actualización de 30 minutos.

1. El usuario abre la aplicación y programa una tarea de actualización en segundo plano en 30 minutos más tarde. La API en segundo plano permite que un único tipo de tarea que se esté ejecutando en un momento dado en segundo plano.
2. La aplicación recibe la tarea y actualiza sus datos y la interfaz de usuario, a continuación, las programaciones de otro en segundo plano de tarea de 30 minutos más tarde. Es importante que el desarrollador se acuerda de programar otra tarea de en segundo plano o la aplicación nunca se activará volver a hacer más actualizaciones.
3. De nuevo, la aplicación recibe la tarea y actualiza sus datos, actualiza su interfaz de usuario y programa fondo otra tarea de 30 minutos más tarde.
4. El mismo proceso se repite.
5. El fondo de la última tarea se recibe la aplicación de nuevo las actualizaciones y sus datos y la interfaz de usuario. Puesto que es la puntuación final no programe para una nueva actualización en segundo plano. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Programación de actualización en segundo plano

En función del escenario anterior, la aplicación MonkeySoccer puede usar el código siguiente para programar una actualización en segundo plano:

```csharp
private void ScheduleNextBackgroundUpdate ()
{
    // Create a fire date 30 minutes into the future
    var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

    // Create 
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
    userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

Crea un nuevo `NSDate` 30 minutos en el futuro cuando la aplicación desea activará y crea un `NSMutableDictionary` para almacenar los detalles de la tarea solicitada. El `ScheduleBackgroundRefresh` método de la `SharedExtension` se usa para solicitar programarse la tarea.

El sistema devolverá un `NSError` si no pudo programar la tarea solicitada.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Procesamiento de la actualización

A continuación, eche un vistazo más de cerca en la ventana de 5 minutos, que muestra los pasos necesarios para actualizar la puntuación:

[![](background-tasks-images/update15.png "La ventana de 5 minutos, que muestra los pasos necesarios para actualizar la puntuación")](background-tasks-images/update15.png#lightbox)

1. A las 7:30:02 P.M., la aplicación es activada por el sistema y dada la actualización en segundo plano tarea. Su primera prioridad es obtener los últimos resultados desde el servidor. Consulte [programar un NSUrlSession](#Scheduling-a-NSUrlSession) a continuación.
2. En 7:30:05 la aplicación completa la tarea original, el sistema coloca la aplicación en modo de suspensión y continúa descargar los datos solicitados en segundo plano.
3. Cuando el sistema complete la descarga, crea una nueva tarea para reactivar la aplicación, por lo que puede procesar la información descargada. Consulte [tareas en segundo plano de control](#Handling-Background-Tasks) y [completar la descarga de control](#Handling-the-Download-Completing) a continuación. 
4. La aplicación guarda la información actualizada y marca la tarea se completó. El desarrollador puede verse tentado a actualizar la interfaz de usuario de la aplicación en este momento, sin embargo, Apple sugiere lo programar una tarea de instantánea para controlar ese proceso. Consulte [programar una actualización instantánea](#Scheduling-a-Snapshot-Update) a continuación.
5. La aplicación recibe la tarea de instantáneas, actualiza su interfaz de usuario y marca la tarea se completó. Consulte [controla una actualización instantánea](#Handling-a-Snapshot-Update) a continuación.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Programar un NSUrlSession

El código siguiente puede utilizarse para programar la descarga de los últimos resultados:

```csharp
private void ScheduleURLUpdateSession ()
{
    // Create new configuration
    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

    // Create new session
    var backgroundSession = NSUrlSession.FromConfiguration (configuration);

    // Create and start download task
    var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
    downloadTask.Resume ();
}
```

Configura y crea un nuevo `NSUrlSession`, a continuación, usa esa sesión para crear una nueva descarga de tareas mediante el `CreateDownloadTask` método. Lo llama el `Resume` método de la tarea para iniciar la sesión de descarga.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Tareas en segundo plano de control

Invalidando el `HandleBackgroundTasks` método de la `WKExtensionDelegate`, la aplicación puede controlar las tareas en segundo plano entrante:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
        #endregion

        ...
        
        #region Public Methods
        public void CompleteTask (WKRefreshBackgroundTask task)
        {
            // Mark the task completed and remove from the collection
            task.SetTaskCompleted ();
            PendingTasks.Remove (task);
        }
        #endregion 

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request
            foreach (WKRefreshBackgroundTask task in backgroundTasks) {
                // Is this a background session task?
                var urlTask = task as WKUrlSessionRefreshBackgroundTask;
                if (urlTask != null) {
                    // Create new configuration
                    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

                    // Create new session
                    var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

                    // Keep track of all pending tasks
                    PendingTasks.Add (task);
                } else {
                    // Ensure that all tasks are completed
                    task.SetTaskCompleted ();
                }
            }
        }
        #endregion
        
        ...
    }
}
```

El `HandleBackgroundTasks` método recorre todas las tareas que el sistema ha enviado la aplicación (en `backgroundTasks`) buscar un `WKUrlSessionRefreshBackgroundTask`. Si encuentra uno, se reincorpora a la sesión y se conecta un `NSUrlSessionDownloadDelegate` para controlar la finalización de la descarga (consulte [completar la descarga de control](#Handling-the-Download-Completing) a continuación):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Mantiene un identificador de la tarea hasta que haya completado, éste se agrega a una colección:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Todas las tareas que se envían a la aplicación deben realizarse para cualquier tarea que actualmente no se controla, marcar como completado:

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Completar la descarga de control

La aplicación MonkeySoccer usa las siguientes `NSUrlSessionDownloadDelegate` delegado para controlar la finalización de la descarga y procesar los datos solicitados:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
    {
        #region Computed Properties
        public ExtensionDelegate WatchExtensionDelegate { get; set; }

        public WKRefreshBackgroundTask Task { get; set; }
        #endregion

        #region Constructors
        public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
        {
            // Initialize
            this.WatchExtensionDelegate = extensionDelegate;
            this.Task = task;
        }
        #endregion

        #region Override Methods
        public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
        {
            // Handle the downloaded data
            ...

            // Mark the task completed
            WatchExtensionDelegate.CompleteTask (Task);

        }
        #endregion
    }
}
```

Cuando se inicializa, mantiene un identificador a ambos el `ExtensionDelegate` y `WKRefreshBackgroundTask` que lo generó. Invalida el `DidFinishDownloading` método para controlar la finalización de la descarga. A continuación, usa el `CompleteTask` método de la `ExtensionDelegate` para informar a la tarea que se ha completado y quitarlo de la colección de tareas pendientes. Consulte [tareas en segundo plano de control](#Handling-Background-Tasks) anteriormente.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Programar una actualización de instantánea

El código siguiente puede usarse para programar una tarea de instantánea para actualizar la interfaz de usuario con los últimos resultados:

```csharp
private void ScheduleSnapshotUpdate ()
{
    // Create a fire date of now
    var fireDate = NSDate.FromTimeIntervalSinceNow (0);

    // Create user info dictionary
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
    userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

Al igual que `ScheduleURLUpdateSession` método anterior, crea un nuevo `NSDate` cuando la aplicación desea activará y crea un `NSMutableDictionary` para almacenar los detalles de la tarea solicitada. El `ScheduleSnapshotRefresh` método de la `SharedExtension` se usa para solicitar programarse la tarea.

El sistema devolverá un `NSError` si no pudo programar la tarea solicitada.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>Control de una actualización de instantánea

Para controlar la tarea de instantáneas, el `HandleBackgroundTasks` (método) (consulte [controlar tareas en segundo plano](#Handling-Background-Tasks) anteriormente) se modifica para ser similar al siguiente:

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
    // Handle background request
    foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Take action based on task type
        if (task is WKUrlSessionRefreshBackgroundTask) {
            var urlTask = task as WKUrlSessionRefreshBackgroundTask;

            // Create new configuration
            var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

            // Create new session
            var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

            // Keep track of all pending tasks
            PendingTasks.Add (task);
        } else if (task is WKSnapshotRefreshBackgroundTask) {
            var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

            // Update UI
            ...

            // Create a expiration date 30 minutes into the future
            var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

            // Create user info dictionary
            var userInfo = new NSMutableDictionary ();
            userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
            userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

            // Mark task complete
            snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
        } else {
            // Ensure that all tasks are completed
            task.SetTaskCompleted ();
        }
    }
}
```

El método comprueba el tipo de tarea que se está procesando. Si es un `WKSnapshotRefreshBackgroundTask` obtiene acceso a la tarea:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

El método actualiza la interfaz de usuario, a continuación, crea un `NSDate` para indicar al sistema cuándo estará obsoleta la instantánea. Crea un `NSMutableDictionary` con información de usuario para describir la nueva instantánea y la marca la tarea de instantáneas que se ha completado con esta información:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Además, también indica la tarea de instantáneas que la aplicación no vuelve al estado predeterminado (en el primer parámetro). Las aplicaciones que no tienen el concepto de un estado predeterminado siempre deben establecer esta propiedad en `true`.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Funciona con eficiencia

Tal como se muestra en el ejemplo anterior de la ventana de cinco minutos que tardó la aplicación MonkeySoccer para actualizar sus puntuaciones, funciona con eficiencia y usando el nuevo watchOS 3 tareas en segundo plano, la aplicación solo estaba activa para un total de 15 segundos: 

[![](background-tasks-images/update16.png "La aplicación solo estaba activa para un total de 15 segundos")](background-tasks-images/update16.png#lightbox)

Esto reduce el impacto que tendrá la aplicación en los recursos disponibles de Apple Watch y duración de la batería y también permite que la aplicación para que funcione mejor con otras aplicaciones que se ejecutan en el reloj.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Cómo funciona la programación

Mientras que una aplicación para watchOS 3 está en primer plano, siempre está programada para ejecutarse y puede realizar cualquier tipo de procesamiento necesario, como la actualización de datos o volver a dibujar su interfaz de usuario. Cuando la aplicación se mueve en segundo plano, normalmente está suspendido por el sistema y se detiene todas las operaciones de tiempo de ejecución. 

Mientras la aplicación está en segundo plano, se puede tener como destino el sistema para ejecutar rápidamente una tarea específica. Por lo tanto, en watchOS 2, el sistema podría activar temporalmente una aplicación en segundo plano para hacer cosas como controlar la notificación de un aspecto largo o para actualizar la complicación de la aplicación. En watchOS 3, hay varias maneras nuevas que se puede ejecutar una aplicación en segundo plano.

Aunque es una aplicación en segundo plano, el sistema impone varios límites en él:

- Solo tiene unos pocos segundos para completar una tarea determinada. El sistema toma en consideración no sólo la cantidad de tiempo pasado sino también Cuánta energía de CPU que consume la aplicación para este límite se derivan.
- Cualquier aplicación que supera los límites se terminan con los siguientes códigos de error:
    - **CPU** - 0xc51bad01
    - **Tiempo** -0xc51bad02
- El sistema impondrá distintos límites según el tipo de tarea en segundo plano que solicitó la aplicación para realizar. Por ejemplo, `WKApplicationRefreshBackgroundTask` y `WKURLSessionRefreshBackgroundTask` tareas tienen tiempos de ejecución un poco más sobre otros tipos de tareas en segundo plano.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Complicaciones y actualizaciones de aplicaciones

Además de las nuevas tareas en segundo plano que Apple haya agregado a watchOS 3, las complicaciones de la aplicación de watchOS pueden tener un efecto sobre cómo y cuándo la aplicación recibe actualizaciones en segundo plano.

Las complicaciones son pequeños elementos visuales que proporcionan información útil con un solo vistazo. Dependiendo de la pantalla del reloj seleccionada, el usuario tiene la capacidad para personalizar una esfera del reloj con uno o más complicación que puede proporcionar una aplicación de inspección en watchOS 3.

Si el usuario incluye una de las complicaciones de la aplicación en su esfera del reloj, proporciona la aplicación lo siguiente actualiza las ventajas:

- Hace que el sistema debe tener la aplicación en listo para iniciar estado, donde intenta iniciar la aplicación en segundo plano, mantiene en memoria y proporciona adicional es hora de actualizar.
- Complicaciones se garantiza que al menos 50 de inserción de las actualizaciones al día.

El desarrollador esforzarse siempre para crear atractivas complicaciones para sus aplicaciones para atraer al usuario al agregar a su reloj por los motivos mencionados anteriormente.

En watchOS 2, complicaciones eran la principal manera que una aplicación en tiempo de ejecución mientras se encuentra en segundo plano ha recibido. En watchOS 3, una aplicación de complicación aún se garantiza para recibir varias actualizaciones por hora, sin embargo, puede utilizar `WKExtensions` para solicitar más tiempo de ejecución para actualizar sus complicaciones.

Eche un vistazo en el siguiente código que se usa para actualizar la complicación de la aplicación de iPhone conectados:

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

    // Get session and the number of remaining transfers
    var session = WCSession.DefaultSession;
    var transfers = session.RemainingComplicationUserInfoTransfers;

    // Create user info dictionary
    var iconattrs = new Dictionary<NSString, NSObject>
        {
            {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
            {new NSString ("reason"), new NSString ("UpdateScore")}
        };

    var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

    // Take action based on the number of transfers left
    if (transfers < 1) {
        // No transfers left, either attempt to send or inform
        // user of situation.
        ...
    } else if (transfers < 11) {
        // Running low on transfers, only send on important updates
        // else conserve for a significant change.
        ...
    } else {
        // Send data
        session.TransferCurrentComplicationUserInfo (userInfo);
    }
}
```

Usa el `RemainingComplicationUserInfoTransfers` propiedad de la `WCSession` ver cuántos prioridad alta transfiere la aplicación ha dejado al día y, a continuación, toma medidas basándose en ese número. Si la aplicación empieza a escasear en las transferencias, puede esperar antes de enviar las actualizaciones menores y enviar sólo información cuando hay un cambio significativo.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>El acoplamiento y programación

En watchOS 3, Apple ha agregado el muelle donde los usuarios pueden anclar sus aplicaciones favoritas y acceder rápidamente a ellos. Cuando el usuario presiona el botón en el Apple Watch, se mostrará una galería de instantáneas de la aplicación anclada. El usuario puede deslizar izquierda o derecha para buscar la aplicación deseada, a continuación, puntee en la aplicación para iniciarla reemplazando la instantánea con la interfaz de la aplicación en ejecución.

[![](background-tasks-images/dock01.png "El acoplamiento")](background-tasks-images/dock01.png#lightbox)

Periódicamente, el sistema toma instantáneas de la interfaz de usuario de la aplicación y las instantáneas utiliza para rellenar a los documentos. watchOS ofrece la oportunidad de actualizar su contenido y la interfaz de usuario antes de esta instantánea se toma de la aplicación.

Las aplicaciones que se hayan anclado a la base pueden esperar lo siguiente:

- Recibirán un mínimo de una actualizada por hora. Esto incluye una tarea de actualización de aplicación y una tarea de instantánea.
- La asignación de actualización se distribuye entre todas las aplicaciones en el Dock. Por lo tanto, cuanto menos aplicaciones que ha anclado el usuario, las actualizaciones más probable que recibirá cada aplicación.
- La aplicación se mantienen en memoria para que la aplicación se reanudará rápidamente cuando se selecciona en el Dock.

La última aplicación se ejecutó el usuario se considerará la _usados más recientemente_ app y ocupará la última ranura en el Dock. Desde allí, puede elegir usuario anclarlo permanentemente a la base. Los usados más recientemente se tratará como cualquier otra aplicación prefiera el usuario ya ha anclado a la base.

> [!IMPORTANT]
> Las aplicaciones que solo se han agregado a la pantalla de inicio no se proporcionará ninguna programación regular. Recibir una programación regular y en segundo plano actualiza una aplicación _debe_ agregarse a la base.

Como se indicó anteriormente en este documento, las instantáneas son muy importantes para watchOS 3 ya que funcionan como las imágenes de la vista previa y el inicio de la aplicación. Si el usuario necesite en una aplicación en el Dock, se expandirá a pantalla completa, escriba el primer plano y comenzar a ejecutarse, por lo que es imperativo que la instantánea esté actualizada.

Puede que en ocasiones cuando el sistema decide que tienen una nueva instantánea de la interfaz de usuario de la aplicación. En estos casos, la solicitud de instantánea no se descontará presupuesto en tiempo de ejecución de la aplicación. Desencadenará una solicitud de instantánea del sistema lo siguiente:

- Una actualización de escala de tiempo de complicación.
- Interacción del usuario con la notificación de una aplicación.
- Cambio de primer plano al estado en segundo plano.
- Después de una hora de estar en el estado en segundo plano, por lo que la aplicación puede volver al estado predeterminado.
- Cuando watchOS arranca por primera vez.

<a name="Best-Practices" />

## <a name="best-practices"></a>Procedimientos recomendados 

Apple sugiere las siguientes prácticas recomendadas al trabajar con tareas en segundo plano:

- Programar con la frecuencia que la aplicación debe actualizarse. Cada vez que se ejecuta la aplicación debe volver a evaluar sus necesidades futuras y ajuste esta programación según sea necesario.
- Si el sistema envía una tarea de actualización en segundo plano y la aplicación no requiere una actualización, aplazar el trabajo hasta que una actualización sea realmente necesaria.
- Tenga en cuenta todas las oportunidades en tiempo de ejecución disponibles para una aplicación:
    - Activación de acoplamiento y de primer plano.
    - Notificaciones.
    - Actualizaciones de complicación.
    - Actualizaciones en segundo plano.
- Use `ScheduleBackgroundRefresh` para uso general en segundo plano en tiempo de ejecución, como:
    - Sondear el sistema para obtener información.
    - Programar futuro `NSURLSessions` para solicitar datos de fondo. 
    - Transiciones de tiempo conocido.
    - Desencadenamiento de actualizaciones de complicación.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Prácticas recomendadas de instantánea

Cuando se trabaja con las actualizaciones de instantánea, Apple realiza las siguientes sugerencias:

- Invalidar las instantáneas sólo cuando sea necesario, por ejemplo, cuando hay un cambio significativo de contenido.
- Evitar la invalidación de la instantánea de alta frecuencia. Por ejemplo, una aplicación de temporizador no debería actualizar la instantánea cada segundo, sólo debe realizarse cuando el temporizador ha finalizado.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Flujo de datos de la aplicación

Apple sugiere lo siguiente para trabajar con el flujo de datos:

[![](background-tasks-images/update17.png "Diagrama de flujo de datos de la aplicación")](background-tasks-images/update17.png#lightbox)

Un evento externo (por ejemplo, la conectividad de inspección) activa la aplicación. Esto hace que la aplicación para actualizar su modelo de datos (que representa el estado actual de las aplicaciones). Como resultado del cambio de modelo de datos de la aplicación tendrá que actualizar sus complicaciones, solicitar una nueva instantánea, iniciar posiblemente un fondo `NSURLSession` extraer más datos y programar aún más en segundo plano actualiza.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>El ciclo de vida de aplicación

Debido a la base y la posibilidad de anclar aplicaciones favoritas a él, se considera de Apple que se va a mover usuarios entre aplicaciones mucho más, mucho más a menudo, a continuación, lo hacían con watchOS 2. Como resultado, la aplicación debe estar preparada para controlar este cambio y moverse rápidamente entre los Estados de primer y segundo plano.

Apple tiene las siguientes sugerencias:

- Asegúrese de que la aplicación finalice cualquier tarea en segundo plano tan pronto como sea posible cuando se especifica la activación de primer plano.
- Asegúrese de finalizar todo el trabajo de primer plano antes de entrar en segundo plano mediante una llamada a `NSProcessInfo.PerformExpiringActivity`.
- Al probar una aplicación en el simulador de watchOS, ninguno de los presupuestos de tarea, se exige para que una aplicación puede actualizar tanto como sea necesario para probar correctamente una característica.
- Pruebe siempre en hardware real de Apple Watch para asegurarse de que la aplicación no se está ejecutando más allá de sus presupuestos antes de publicarlos en iTunes Connect.
- Apple sugiere lo mantiene el Apple Watch en el cargador mientras se prueba y depuración.
- Asegúrese de que se han probado exhaustivamente tanto en frío inicio y reanudación de una aplicación.
- Compruebe que se completan todas las tareas de la aplicación.
- Cambiar el número de aplicaciones que están anclados en el Dock para probar el mejor y peor caso de escenarios.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo ha tratado las mejoras de que Apple ha realizado para watchOS y cómo se puede usar para mantener actualizada una aplicación de inspección. En primer lugar, se ha descrito todo del nuevo Apple de tarea en segundo plano se ha agregado en watchOS 3. A continuación, lo cubre el ciclo de vida de la API en segundo plano y cómo implementar tareas en segundo plano en una aplicación de Xamarin para watchOS. Por último, había cubierto funciona la programación y dio algunos procedimientos recomendados.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
