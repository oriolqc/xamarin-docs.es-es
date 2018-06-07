---
title: watchOS tareas en segundo plano en Xamarin
description: Este documento describe cómo usar tareas en segundo plano con watchOS en Xamarin, echar un vistazo a tipos de tareas en segundo plano, uso de recursos, implementar tareas en segundo plano, programación, mejores prácticas y mucho más.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/13/2017
ms.openlocfilehash: 5ab53d4aea32cf41c492e286c18cbe85a619889a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792052"
---
# <a name="watchos-background-tasks-in-xamarin"></a>watchOS tareas en segundo plano en Xamarin

Con watchOS 3, hay tres maneras principales que una aplicación de inspección mantener su información actualizada: 

- Utilizando una de las varias tareas en segundo plano nuevo. 
- Tiene uno de sus complicaciones en la pantalla del reloj (dando por vez extra para actualizar). 
- Tener el pin de usuario para la aplicación que el nuevo acoplar (donde su conservan en memoria y se actualizan con frecuencia). 

## <a name="keeping-an-app-up-to-date"></a>Mantener una aplicación actualizada

Antes de hablar sobre todas las formas en que un desarrollador puede mantener datos y la interfaz de usuario de una aplicación de watchOS actual y actualizado, en esta sección se Eche un vistazo a un conjunto típico de patrones de uso y cómo el usuario puede moverse entre su iPhone y sus Apple Watch a lo largo del día en función de  la hora del día y la actividad están realizando en ese momento (por ejemplo, automóvil).

Considere el ejemplo siguiente:

[![](background-tasks-images/update00.png "Cómo el usuario puede moverse entre su iPhone y sus Apple Watch durante todo el día")](background-tasks-images/update00.png#lightbox)

1. De la mañana, mientras se espera en línea para un café, el usuario examina las noticias actuales en su iPhone durante varios minutos.
2. Antes de abandonar la cafetería, comprueban rápidamente el tiempo con una complicación en su pantalla del reloj.
3. Antes de comida, usan la aplicación de asignaciones en el iPhone para buscar un restaurante cercano y hacer una reserva para satisfacer a un cliente.
4. Mientras viaja al restaurante, reciben una notificación en su Apple Watch y con una vista rápida, saben que su cita de comida se ejecuta en tiempo de ejecución.
5. Por la noche, usan la aplicación de asignaciones en el iPhone para comprobar el tráfico antes de manejar inicio.
6. La manera en particular, reciben una notificación de iMessage en sus Apple Watch pedirles para recoger algunos leche y usan la característica de respuesta rápida para enviar la respuesta "Aceptar".

Debido a la "vista rápida" (menos de tres segundos) naturaleza de cómo un usuario desea usar una aplicación de Apple Watch, existe normalmente no es suficiente tiempo para la aplicación capturar la información deseada y actualizar su interfaz de usuario antes de mostrar al usuario.

Mediante el uso de la nueva API de Apple ha incluido en watchOS 3, puede programar la aplicación para una _actualización en segundo plano_ y tener la información deseada listo antes de que el usuario lo solicita. Tome como ejemplo la complicación de tiempo descritos anteriormente:

[![](background-tasks-images/update01.png "Un ejemplo de la complicación de tiempo")](background-tasks-images/update01.png#lightbox)

1. Las programaciones de aplicación pueden reactivarse por el sistema en un momento determinado. 
2. La aplicación captura la información que será necesario para generar la actualización.
3. La aplicación vuelve a generar la interfaz de usuario para reflejar los nuevos datos.
4. Cuando el usuario se resumen en complicación la aplicación, tiene información actualizada sin que el usuario tener que esperar a la actualización.

Tal como se muestra anteriormente, el sistema watchOS activa la aplicación mediante una o varias tareas, de los cuales tiene un grupo muy limitado disponible:

[![](background-tasks-images/update02.png "El sistema watchOS activa la aplicación mediante una o más tareas")](background-tasks-images/update02.png#lightbox)

Apple recomienda sacar el máximo partido de esta tarea (ya que es un recurso limitado a la aplicación), se conservan en él hasta que la aplicación haya finalizado el proceso de actualización de sí mismo.

El sistema ofrece estas tareas mediante una llamada a la nueva `HandleBackgroundTasks` método de la `WKExtensionDelegate` delegar. Por ejemplo:

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

Cuando la aplicación ha terminado la tarea determinada, devuelve al sistema marcando que se haya completado:

[![](background-tasks-images/update03.png "La tarea se devuelve al sistema marcándolo completado")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Nuevas tareas en segundo plano

watchOS 3 incluye varias tareas en segundo plano que una aplicación puede usar para actualizar su información asegurándose de que tiene el contenido que el usuario necesita antes de abrir la aplicación, como:

- **Actualización de la aplicación en segundo plano** - la [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) tarea permite que la aplicación actualizar su estado en segundo plano. Normalmente esto incluirá otra tarea, como la descarga de nuevo contenido desde internet mediante un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Instantáneas de actualización en segundo plano** - la [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) tarea permite que la aplicación actualizar su contenido y la interfaz de usuario antes de que el sistema toma una instantánea que se usará para rellenar el área.
- **Conectividad de inspección en segundo plano** - la [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) tarea se inicia por la aplicación cuando recibe datos de fondo desde el iPhone emparejado.
- **Sesión de dirección URL en segundo plano** - la [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) tarea se inicia por la aplicación cuando una transferencia en segundo plano requiere autorización o completa (con éxito o error).

Estas tareas se explicará en detalle en las secciones siguientes.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

El `WKApplicationRefreshBackgroundTask` es una tarea genérica que se puede programar para que la aplicación activa en una fecha futura:

[![](background-tasks-images/update04.png "Un WKApplicationRefreshBackgroundTask hayan activado en el futuro")](background-tasks-images/update04.png#lightbox)

En tiempo de ejecución de la tarea, la aplicación puede realizar cualquier tipo de procesamiento local como la actualización una escala de tiempo de compilación o capturar algunos datos necesarios con un `NSUrlSession`.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

El sistema enviará un `WKURLSessionRefreshBackgroundTask` cuando los datos ha terminado de descargar y listo para ser procesado por la aplicación:

[![](background-tasks-images/update05.png "El WKURLSessionRefreshBackgroundTask cuando haya acabado la descarga de los datos")](background-tasks-images/update05.png#lightbox)

Una aplicación no queda en ejecución mientras se descarga de los datos en segundo plano. En su lugar, la aplicación de programa la solicitud de datos, a continuación, esta suspensión y el sistema controla la descarga de los datos, solo reawakening la aplicación una vez completada la descarga.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

En watchOS 3, Apple ha agregado el muelle donde los usuarios pueden anclar sus aplicaciones favoritas y acceder rápidamente a ellos. Cuando el usuario presiona el botón de lado en el de Apple Watch, se mostrará una galería de aplicaciones anclados instantáneas. El usuario puede deslizar rápidamente izquierda o derecha para buscar la aplicación deseada y después puntee en la aplicación para iniciarla, reemplazando la instantánea con la interfaz de la aplicación en ejecución.

[![](background-tasks-images/update06.png "Reemplazar la instantánea con la interfaz de aplicaciones de la ejecución")](background-tasks-images/update06.png#lightbox)

El sistema periódicamente toma instantáneas de la interfaz de usuario de la aplicación (mediante el envío de un `WKSnapshotRefreshBackgroundTask`) y esas instantáneas se utiliza para rellenar el área. watchOS ofrece la oportunidad para actualizar su contenido y la interfaz de usuario antes de esta instantánea se toma de la aplicación.

Las instantáneas son muy importantes watchOS 3 puesto que funcionan como imágenes de la vista previa y el inicio de la aplicación. Si el usuario liquida en una aplicación en el área, se expandirá a pantalla completa, escriba el primer plano y comenzar a ejecutar, por lo que es imperativo que la instantánea sea actualizada:

[![](background-tasks-images/update07.png "Si el usuario liquida en una aplicación en el área, se expandirá a pantalla completa")](background-tasks-images/update07.png#lightbox)

Una vez más, el sistema emitirá un `WKSnapshotRefreshBackgroundTask` para que la aplicación puede preparar (mediante la actualización de los datos y la interfaz de usuario) antes de se toma la instantánea:

[![](background-tasks-images/update08.png "Puede preparar la aplicación mediante la actualización de los datos y la interfaz de usuario antes de que se tomó la instantánea")](background-tasks-images/update08.png#lightbox)

Cuando la aplicación marca el `WKSnapshotRefreshBackgroundTask` completado, el sistema le llevará automáticamente una instantánea de la interfaz de usuario de la aplicación.

> [!IMPORTANT]
> Es importante siempre se programan un ` WKSnapshotRefreshBackgroundTask` después de que la aplicación ha recibido datos nuevos y actualizar su interfaz de usuario o el usuario no verá la información modificada.




Además, cuando el usuario recibe una notificación de la aplicación y puntee para hacer que la aplicación en primer plano, la instantánea debe ser actualizados ya que está actuando como pantalla de inicio:

[![](background-tasks-images/update09.png "El usuario recibe una notificación de la aplicación y puntee para hacer que la aplicación en primer plano")](background-tasks-images/update09.png#lightbox)

Si han pasado más de una hora desde que el usuario ha interactuado con una aplicación watchOS, podrá volver a su estado predeterminado. El estado predeterminado puede significar diferentes cosas para diferentes aplicaciones y, en función del diseño de una aplicación, quizás no tenga un estado predeterminado en absoluto.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

En watchOS 3, Apple ha integrado inspección conectividad con la API de actualización de fondo a través del nuevo `WKWatchConnectivityRefreshBackgroundTask`. Con esta nueva característica, una aplicación de iPhone puede entregar datos nuevos a su equivalente de aplicación de inspección, mientras se ejecuta la aplicación watchOS en segundo plano:

[![](background-tasks-images/update10.png "Una aplicación de iPhone puede entregar los datos actualizados en su equivalente de aplicación de inspección, mientras se ejecuta la aplicación watchOS en segundo plano")](background-tasks-images/update10.png#lightbox)

Iniciar una inserción de complicación, contexto de aplicación, envíe un archivo o actualizar la información de usuario de la aplicación de iPhone reactivará la aplicación de Apple Watch en segundo plano.

Cuando se activa la aplicación de supervisión a través de un `WKWatchConnectivityRefreshBackgroundTask` será necesario utilizar los métodos de API estándares para recibir los datos de la aplicación de iPhone.

[![](background-tasks-images/update11.png "El flujo de datos WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Asegúrese de que ha activado la sesión.
2. Supervisar la nueva `HasContentPending` siempre que sea el valor de la propiedad `true`, la aplicación todavía tiene datos que se va a procesar. Como antes, la aplicación debe almacenar en la tarea hasta que haya terminado de procesar todos los datos.
3. Cuando no hay ningún dato más para procesarse (`HasContentPending = false`), marque la tarea se completó para volver al sistema. Si no se agotará el tiempo de ejecución de la aplicación fondo asignado resultante en un informe de errores.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>El ciclo de vida de la API de fondo

Cuando se colocan todas las piezas de la nueva API de tareas de fondo juntos, un conjunto típico de interacciones sería similar al siguiente:

[![](background-tasks-images/update12.png "El ciclo de vida de la API de fondo")](background-tasks-images/update12.png#lightbox)

1. En primer lugar, la aplicación watchOS programa un tarea para ser activo como algún momento en el futuro de fondo.
2. La aplicación se hayan activado por el sistema y se envía una tarea.
3. La aplicación procesa la tarea para completar cualquier trabajo fue necesario.
4. Como resultado de la tarea de procesamiento, la aplicación que deba programar fondo más tareas que debe realizar más trabajo en el futuro, como una descarga más contenido con un `NSUrlSession`.
5. La aplicación marca la tarea se completó y devuelve al sistema.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Uso de recursos responsable

Es fundamental que una aplicación watchOS se comporta de forma responsable dentro de este ecosistema limitando su consumo de recursos compartidos del sistema.

Eche un vistazo en el siguiente escenario:

[![](background-tasks-images/update13.png "Una aplicación watchOS limita su carga para los recursos compartidos del sistema")](background-tasks-images/update13.png#lightbox)

1. El usuario inicia una aplicación de watchOS a la 1:00 P.M.
2. La aplicación programa una tarea para reactivar y descargar contenido nuevo en una hora a las 2:00 P.M.
3. A la 1:50 P.M. el usuario vuelve a abrir la aplicación que le permite actualizar sus datos y la interfaz de usuario en este momento.
4. En lugar de dejar que la activación de tareas la aplicación de nuevo al cabo de 10 minutos, la aplicación debe volver a programar la tarea para ejecutar una hora posterior a las 2:50 P.M.

Mientras que cada aplicación es diferente, Apple sugiere buscar patrones de uso, como los mostrados anteriormente, para ayudar a conservar los recursos del sistema.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implementar tareas en segundo plano

Por ejemplo, este documento utilizará la aplicación de deportes MonkeySoccer falsa que informa al usuario de las puntuaciones de fútbol. 

Eche un vistazo en el siguiente escenario de uso típico:

[![](background-tasks-images/update14.png "El escenario de uso típico")](background-tasks-images/update14.png#lightbox)

Equipo de fútbol favoritos del usuario está reproduciendo a una coincidencia grande de 7:00 P.M. a 9:00 P.M., por lo que la aplicación debe esperar a que el usuario puede comprobar con regularidad la puntuación y decide en un intervalo de actualización de 30 minutos.

1. El usuario abre la aplicación y programa una tarea de actualización en segundo plano 30 minutos más tarde. La API de segundo plano permite a únicamente un tipo de tarea que se esté ejecutando en un momento dado de fondo.
2. La aplicación recibe la tarea y actualiza sus datos y la interfaz de usuario, a continuación, programaciones por otra cosa en segundo plano de tarea de 30 minutos más tarde. Es importante que recuerda el programador programar fondo otra tarea o la aplicación nunca se tenido volver a obtener más actualizaciones.
3. De nuevo, la aplicación recibe la tarea y actualiza sus datos, actualiza su interfaz de usuario y programa fondo otra tarea de 30 minutos más tarde.
4. El mismo proceso se repite de nuevo.
5. El fondo última tarea se recibió y la aplicación actualiza sus datos y la interfaz de usuario. Puesto que se trata de la puntuación final no programar para una nueva actualización en segundo plano. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Programación de actualización en segundo plano

Dado el escenario anterior, la aplicación de MonkeySoccer puede utilizar el código siguiente para programar una actualización en segundo plano:

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

Crea un nuevo `NSDate` 30 minutos en el futuro cuando la aplicación desea ser tenido y crea un `NSMutableDictionary` para almacenar los detalles de la tarea solicitada. El `ScheduleBackgroundRefresh` método de la `SharedExtension` se usa para solicitar que se programe la tarea.

El sistema devolverá un `NSError` si pudo programar la tarea solicitada.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Procesamiento de la actualización

A continuación, eche un vistazo más de cerca en la ventana de 5 minutos, que muestra los pasos necesarios para actualizar la puntuación:

[![](background-tasks-images/update15.png "La ventana de 5 minutos, que muestra los pasos necesarios para actualizar la puntuación")](background-tasks-images/update15.png#lightbox)

1. A las 7:30:02 P.M. la aplicación se activó por el sistema y tiene el fondo de actualización de tarea. La prioridad de la primera consiste en obtener las puntuaciones más recientes desde el servidor. Vea [programar un NSUrlSession](#Scheduling-a-NSUrlSession) a continuación.
2. En 7:30:05 la aplicación realiza la tarea original, el sistema coloca la aplicación en modo de suspensión y continúa descargar los datos solicitados en segundo plano.
3. Cuando el sistema completa la descarga, crea una nueva tarea para reactivar la aplicación para poder procesar la información descargada. Vea [tareas en segundo plano de control](#Handling-Background-Tasks) y [control de la finalización de la descarga](#Handling-the-Download-Completing) a continuación. 
4. La aplicación guarda la información actualizada y marca la tarea se completó. El desarrollador puede verse tentado a actualizar la interfaz de usuario de la aplicación en este momento, sin embargo, Apple sugiere la programación de una tarea de instantánea para controlar ese proceso. Vea [programar una actualización de instantánea](#Scheduling-a-Snapshot-Update) a continuación.
5. La aplicación recibe la tarea de instantáneas, actualiza su interfaz de usuario y marca la tarea se completó. Vea [control de una actualización de instantánea](#Handling-a-Snapshot-Update) a continuación.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Programar un NSUrlSession

El código siguiente puede utilizarse para programar la descarga de las puntuaciones más recientes:

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

Que configura y crea un nuevo `NSUrlSession`, a continuación, utiliza esa sesión para crear una nueva descarga de tareas mediante el `CreateDownloadTask` (método). Lo llama el `Resume` método de la tarea para iniciar la sesión de descarga.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Control de tareas en segundo plano

Reemplazando la `HandleBackgroundTasks` método de la `WKExtensionDelegate`, la aplicación puede controlar la entrada tareas en segundo plano:

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

El `HandleBackgroundTasks` método recorre todas las tareas que el sistema ha enviado la aplicación (en `backgroundTasks`) buscando un `WKUrlSessionRefreshBackgroundTask`. Si encuentra alguno, se vuelve a unirse a la sesión y se conecta un `NSUrlSessionDownloadDelegate` para controlar la finalización de la descarga (vea [control de la finalización de descargar](#Handling-the-Download-Completing) a continuación):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Un identificador de la tarea mantiene hasta que se ha completado, éste se agrega a una colección:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Todas las tareas que se envían a la aplicación necesitan para completarse, para cualquier tarea que actualmente no se controla, marcar como completado:

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Control de la finalización de la descarga

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

Cuando se inicializa, mantiene un identificador a ambos el `ExtensionDelegate` y `WKRefreshBackgroundTask` que la generó. Reemplaza el `DidFinishDownloading` método para controlar la finalización de la descarga. A continuación, utiliza el `CompleteTask` método de la `ExtensionDelegate` para informar a la la tarea que se ha completado y quitar de la colección de las tareas pendientes. Vea [tareas en segundo plano de control](#Handling-Background-Tasks) anteriormente.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Programar una actualización de instantánea

El código siguiente puede utilizarse para programar una tarea de instantáneas para actualizar la interfaz de usuario con las puntuaciones más recientes:

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

Al igual que `ScheduleURLUpdateSession` método anterior, se crea un nuevo `NSDate` para cuando la aplicación desea ser tenido y crea un `NSMutableDictionary` para almacenar los detalles de la tarea solicitada. El `ScheduleSnapshotRefresh` método de la `SharedExtension` se usa para solicitar que se programe la tarea.

El sistema devolverá un `NSError` si pudo programar la tarea solicitada.

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

El método de prueba para el tipo de tarea que se va a procesar. Si es un `WKSnapshotRefreshBackgroundTask` obtiene acceso a la tarea:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

El método actualiza la interfaz de usuario, a continuación, crea un `NSDate` debe indicar al sistema cuando la instantánea será obsoleta. Crea un `NSMutableDictionary` con información de usuario para describir la nueva instantánea y la marca la tarea de instantáneas que se completó con esta información:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Además, también indica a la tarea de instantáneas que la aplicación no se devuelve al estado predeterminado (en el primer parámetro). Aplicaciones que no disponen del concepto de un estado predeterminado siempre deben establecer esta propiedad en `true`.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Funciona con eficiencia

Tal como se muestra en el ejemplo anterior de la ventana de cinco minutos que tardó la aplicación MonkeySoccer para actualizar sus puntuaciones, funciona con eficiencia y usando el nuevo watchOS 3 tareas en segundo plano, la aplicación solo estuviera activa para un total de 15 segundos: 

[![](background-tasks-images/update16.png "La aplicación solo estuviera activa para un total de 15 segundos")](background-tasks-images/update16.png#lightbox)

Esto reduce el impacto que tendrá la aplicación en recursos de Apple Watch disponibles y duración de la batería y también permite que la aplicación para que funcione mejor con otras aplicaciones que se ejecutan en el reloj.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Cómo funciona la programación

Mientras que una aplicación watchOS 3 está en primer plano, está programada siempre para ejecutar y puede realizar cualquier tipo de procesamiento necesario, como datos de actualización o volver a dibujar su interfaz de usuario. Cuando se mueve la aplicación en segundo plano, que normalmente está suspendido por el sistema y se detiene todas las operaciones en tiempo de ejecución. 

Mientras la aplicación está en segundo plano, puede ser dirigido por el sistema para ejecutar rápidamente una tarea específica. Por lo tanto, en watchOS 2, el sistema puede reactivar temporalmente una aplicación de fondo para hacer cosas como controlar la notificación de un vistazo largo o para actualizar la complicación de la aplicación. En watchOS 3, hay varias formas nuevas que se pueden ejecutar una aplicación en segundo plano.

Si bien es una aplicación en segundo plano, el sistema impone límites varias en él:

- Que se genera solo unos segundos a que se complete cualquier tarea. El sistema toma en consideración no solo de la cantidad de tiempo transcurrido, sino también cuánta potencia de CPU que la aplicación está consumiendo para derivar este límite.
- Cualquier aplicación que supera los límites se terminará con los códigos de error siguiente:
    - **CPU** -0xc51bad01
    - **Tiempo** -0xc51bad02
- El sistema impone límites diferentes según el tipo de tarea en segundo plano ha solicitado la aplicación para realizar. Por ejemplo, `WKApplicationRefreshBackgroundTask` y `WKURLSessionRefreshBackgroundTask` tareas tienen tiempos de ejecución un poco más sobre otros tipos de tareas en segundo plano.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Complicaciones y actualizaciones de aplicaciones

Además de las tareas en segundo plano nuevo que Apple haya agregado a watchOS 3, complicaciones la aplicación de watchOS pueden tener un efecto sobre cómo y cuándo la aplicación recibe actualizaciones en segundo plano.

Las complicaciones son pequeños elementos visuales que proporcionan información útil de un vistazo. Dependiendo de la pantalla del reloj seleccionada, el usuario tiene la capacidad para personalizar una pantalla watch con uno o más complicación que puede proporcionar una aplicación de inspección en watchOS 3.

Si el usuario incluye una de las complicaciones de la aplicación en su pantalla watch, proporciona la aplicación siguiente actualiza ventajas:

- Hace que el sistema debe tener la aplicación en un lista de inicio estado, donde intenta iniciar la aplicación en segundo plano, mantiene en memoria y proporciona extra de tiempo para actualizar.
- Complicaciones se garantiza que al menos 50 actualizaciones de inserción al día.

Siempre debe procurar que el programador crear atractivas complicaciones para sus aplicaciones para atraer que el usuario agrega a su pantalla watch por los motivos mencionados anteriormente.

En watchOS 2, complicaciones eran la manera principal que una aplicación recibido en tiempo de ejecución en segundo plano. En watchOS 3, una aplicación de complicación todavía puede garantizar para recibir varias actualizaciones por hora, sin embargo, puede utilizar `WKExtensions` para solicitar más tiempo de ejecución para actualizar sus complicaciones.

Eche un vistazo en el código siguiente utilizado para actualizar la complicación de la aplicación de iPhone conectado:

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

Usa el `RemainingComplicationUserInfoTransfers` propiedad de la `WCSession` ver cuántas de las principales prioridades transfiere la aplicación ha dejado al día y, a continuación, toma medidas basándose en ese número. Si la aplicación empieza a escasear en las transferencias, puede esperar antes de enviar las actualizaciones secundarias y solo enviar información cuando se produce un cambio importante.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Programación y el acoplamiento

En watchOS 3, Apple ha agregado el muelle donde los usuarios pueden anclar sus aplicaciones favoritas y acceder rápidamente a ellos. Cuando el usuario presiona el botón de lado en el de Apple Watch, se mostrará una galería de instantáneas de la aplicación anclados. El usuario puede deslizar rápidamente izquierda o derecha para buscar la aplicación deseada y después puntee en la aplicación para iniciarla, reemplazando la instantánea con la interfaz de la aplicación en ejecución.

[![](background-tasks-images/dock01.png "La vinculación")](background-tasks-images/dock01.png#lightbox)

Periódicamente, el sistema toma instantáneas de la interfaz de usuario de la aplicación y esas instantáneas utiliza para rellenar a los documentos. watchOS ofrece la oportunidad para actualizar su contenido y la interfaz de usuario antes de esta instantánea se toma de la aplicación.

Las aplicaciones que se hayan anclado a la vinculación pueden esperar el siguiente:

- Recibirán un mínimo de una actualizada por hora. Esto incluye una tarea de actualización de la aplicación y una tarea de instantánea.
- La asignación de actualización se distribuye entre las aplicaciones en el área de. Por lo que el menos aplicaciones que se ancla el usuario, las actualizaciones más probable que recibirá cada aplicación.
- La aplicación se mantendrán en la memoria, por lo que la aplicación se reanudará rápidamente cuando se selecciona en el área.

La última aplicación que se ejecutó el usuario se considerarán el _usados más recientemente_ aplicación y ocupará la última ranura en la vinculación. Desde allí, puede elegir el usuario para anclarlo permanentemente en el área. Los usados más recientemente se tratará como cualquier otra aplicación favoritos del usuario ya ha anclado a la vinculación.

> [!IMPORTANT]
> Aplicaciones que solo se han agregado a la pantalla Inicio no se proporcionará ninguna programación regular. Para recibir una programación regular y segundo plano actualiza, una aplicación _debe_ agregarse a la vinculación.

Como se mencionó anteriormente en este documento, las instantáneas son muy importantes watchOS 3 puesto que funcionan como imágenes de la vista previa y el inicio de la aplicación. Si el usuario liquida en una aplicación en el área, se expandirá a pantalla completa, escriba el primer plano y comenzar a ejecutar, por lo que es imperativo que la instantánea sea actualizada.

Puede haber ocasiones cuando el sistema decide que necesita una nueva instantánea de interfaz de usuario de la aplicación. En estos casos, la solicitud de instantánea no se descontará presupuesto en tiempo de ejecución de la aplicación. A continuación desencadenará una solicitud de instantánea del sistema:

- Una actualización de la escala de tiempo de compilación.
- Interacción del usuario con la notificación de una aplicación.
- Cambio de primer plano para el estado de segundo plano.
- Después de una hora de existir en el estado de segundo plano, por lo que la aplicación puede volver al estado predeterminado.
- Cuando watchOS arranca por primera vez.

<a name="Best-Practices" />

## <a name="best-practices"></a>Procedimientos recomendados 

Apple sugiere las siguientes prácticas recomendadas al trabajar con tareas en segundo plano:

- Programar con tanta frecuencia como la aplicación debe actualizarse. Cada vez que se ejecuta la aplicación debe volver a evaluar sus necesidades futuras y ajustar esta programación según sea necesario.
- Si el sistema envía una tarea de actualización en segundo plano y la aplicación no requiere una actualización, aplazar la hasta que una actualización sea realmente necesaria.
- Tenga en cuenta todas las oportunidades en tiempo de ejecución disponibles para una aplicación:
    - Activación de acoplamiento y de primer plano.
    - Notificaciones.
    - Actualizaciones de complicación.
    - Actualizaciones en segundo plano.
- Use `ScheduleBackgroundRefresh` para uso general de segundo plano en tiempo de ejecución como:
    - Sondear el sistema para obtener información.
    - Programar futuro `NSURLSessions` para solicitar datos de fondo. 
    - Transiciones de tiempo conocido.
    - Activar las actualizaciones de complicación.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Prácticas recomendadas de instantánea

Al trabajar con actualizaciones de instantánea, Apple realiza las siguientes sugerencias:

- Invalidar las instantáneas sólo cuando sea necesario, por ejemplo, cuando se produce un cambio de contenido significativo.
- Evitar la invalidación de la instantánea de alta frecuencia. Por ejemplo, una aplicación de temporizador no debe actualizar la instantánea de cada segundo, sólo debe realizarse si el temporizador ha finalizado.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Flujo de datos de la aplicación

Apple recomienda lo siguiente para trabajar con el flujo de datos:

[![](background-tasks-images/update17.png "Diagrama de flujo de datos de la aplicación")](background-tasks-images/update17.png#lightbox)

Un evento externo (por ejemplo, la conectividad de inspección) activa la aplicación. Esto hace que la aplicación para actualizar su modelo de datos (que representa el estado actual de aplicaciones). Como resultado del cambio de modelo de datos de la aplicación deberá actualizar sus complicaciones, solicitar una nueva instantánea, posiblemente iniciar un fondo `NSURLSession` extraer más datos y programar más segundo plano actualiza.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>El ciclo de vida de aplicación

Debido a la vinculación y la capacidad para anclar aplicaciones favoritas a él, considera de Apple que se va a mover usuarios entre aplicaciones mucho mayor, con mucha más frecuencia, a continuación, lo hacían con watchOS 2. Como resultado, la aplicación debería estar lista para controlar este cambio y moverse rápidamente entre los Estados de primer plano y fondo.

Apple tiene las siguientes sugerencias:

- Asegúrese de que la aplicación finalice cualquier tarea en segundo plano tan pronto como sea posible cuando se especifica la activación de primer plano.
- Asegúrese de finalizar todo el trabajo de primer plano antes de entrar en segundo plano mediante una llamada a `NSProcessInfo.PerformExpiringActivity`.
- Al probar una aplicación en el simulador watchOS, ninguno de los presupuestos de tareas se aplicará para que una aplicación puede actualizar tanto como sea necesario para probar correctamente una característica.
- Pruebe siempre en hardware real de Apple Watch para asegurarse de que la aplicación no se está ejecutando más allá de sus presupuestos antes de publicarlos en iTunes Connect.
- Apple sugiere manteniendo el Apple Watch en el cargador mientras se prueba y depuración.
- Asegúrese de que todos los inactivos iniciar y reanudar una aplicación throughly se prueban.
- Compruebe que se completan todas las tareas de la aplicación.
- Cambiar el número de aplicaciones que se fijan en el área para probar la mejor y el peor caso de escenarios.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata las mejoras de que Apple ha realizado watchOS y cómo puede utilizar para mantener actualizados los una aplicación de inspección. En primer lugar, lo cubre todas las nuevas Apple de la tarea de segundo plano ha agregado en watchOS 3. A continuación, lo cubre el ciclo de vida de la API de fondo y cómo implementar tareas en segundo plano en una aplicación de watchOS de Xamarin. Por último, había cubierto funciona la programación y algunas prácticas recomendadas que proporcionó.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
