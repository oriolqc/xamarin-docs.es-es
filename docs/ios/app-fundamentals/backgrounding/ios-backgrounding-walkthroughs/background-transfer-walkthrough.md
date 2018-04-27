---
title: 'Tutorial: usar NSURLSession y el servicio de transferencia de fondo'
description: En este tutorial, usamos el servicio de transferencia en segundo plano y la API de NSURLSession para iniciar la descarga de una imagen grande que continúa descargándose cuando la aplicación está en segundo plano.
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 51e1ae0a4314d0b23ec9e3236656321bd3dcb4e4
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="walkthrough---using-background-transfer-service-and-nsurlsession"></a>Tutorial: usar NSURLSession y el servicio de transferencia de fondo

_En este tutorial, usamos el servicio de transferencia en segundo plano y la API de NSURLSession para iniciar la descarga de una imagen grande que continúa descargándose cuando la aplicación está en segundo plano._

Se inicia una transferencia en segundo plano mediante la configuración de un fondo `NSURLSession` y poner en cola cargar o descargar tareas. Si las tareas se completan mientras la aplicación es backgrounded, suspendida o terminada, iOS notificará a la aplicación mediante una llamada al controlador de finalización de la aplicación *AppDelegate*. El siguiente diagrama muestra esto en acción:

 [![](background-transfer-walkthrough-images/transfer.png "Se inicia una transferencia en segundo plano mediante la configuración de un fondo NSURLSession y poner en cola cargar o descargar tareas")](background-transfer-walkthrough-images/transfer.png#lightbox)

Vamos a ver este aspecto en el código.

## <a name="configuring-a-background-session"></a>Configurar una sesión en segundo plano

Para realizar una sesión en segundo plano, cree un nuevo `NSUrlSession` y configurarla mediante un `NSUrlSessionConfiguration` objeto.

El objeto de configuración determina lo que puede hacer la sesión y los tipos de tareas pueden ejecutar.
Sesiones configuradas mediante el `CreateBackgroundSessionConfiguration` método se ejecuta en un proceso independiente y realizar discrecionales transferencias (Wi-Fi) para conservar datos y duración de batería.
El ejemplo de código siguiente muestra la configuración correcta de una sesión de transferencia de fondo mediante el `CreateBackgroundSessionConfiguration` método y un identificador de cadena único:

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate(), new NSOperationQueue());

}
```

Además de un objeto de configuración, una sesión también requiere un delegado de sesión y una cola.
La cola determina el orden en el que se completen las tareas. El delegado de la sesión chaperones el proceso de transferencia y controla la autenticación, el almacenamiento en caché y otros problemas relacionados con la sesión.

## <a name="working-with-tasks-and-delegates"></a>Trabajar con tareas y delegados

Ahora que hemos configurado una sesión en segundo plano, vamos a iniciar tareas para administrar a la transferencia. Se pueden realizar un seguimiento de estas tareas mediante el un `NSUrlSessionDelegate` llama a instancia de un delegado de la sesión. El delegado de la sesión es responsable de activar una aplicación han terminado o suspendida en segundo plano para autenticación de identificador, errores o finalización de la transferencia.

Un `NSUrlSessionDelegate` proporciona los métodos básicos siguientes para comprobar el estado de transferencia:

-  *DidFinishEventsForBackgroundSession* -obtiene llama a este método cuando han terminado de todas las tareas y la transferencia está completa.
-  *DidReceiveChallenge* : se le llama a las credenciales cuando se necesita autorización de solicitud.
-  *DidBecomeInvalidWithError* -llama si la `NSURLSession` pasa a ser invalidado.


Las sesiones de fondo requieren a más especializados delegados según los tipos de tareas que se ejecutan. Las sesiones de fondo se limitan a dos tipos de tareas:

-  *Cargar tareas* -tareas de tipo `NSUrlSessionUploadTask` usar la `NSUrlSessionTaskDelegate` , que hereda de `NSUrlSessionDelegate` . Este delegado proporciona métodos adicionales para realizar el seguimiento cargan progreso, identificador de redirección de HTTP y mucho más.
-  *Descargar tareas* -tareas de tipo `NSUrlSessionDownloadTask` usar la `NSUrlSessionDownloadDelegate` , que hereda de `NSUrlSessionTaskDelegate` . Este delegado proporciona que todos los métodos para cargar las tareas, así como métodos de específicos de la descarga para realizar un seguimiento del progreso de la descarga y determinar cuándo ha reanudado o completar una tarea de descarga.


El código siguiente define una tarea que puede usarse para descargar una imagen de una dirección URL. Empezamos la tarea mediante una llamada a `CreateDownloadTask` en la sesión en segundo plano y pasar la solicitud de dirección URL:

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

A continuación, vamos a crear un delegado de descarga de nueva sesión para realizar un seguimiento de todas las tareas de descarga en esta sesión:

```csharp
public class MySessionDelegate : NSUrlSessionDownloadDelegate
{
  public override void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

Si desea ver el progreso de una tarea de descarga, es posible invalidar el `DidWriteData` método para realizar un seguimiento del progreso e incluso actualizar la interfaz de usuario. Las actualizaciones de la interfaz de usuario aparecerán inmediatamente si la aplicación está en primer plano o estará esperando el usuario la próxima vez que abran la aplicación.

La API de delegado de sesión proporciona un amplio kit de herramientas para interactuar con las tareas. Para obtener una lista completa de sesión delegar métodos, consulte el `NSUrlSessionDelegate` la documentación de API.

> [!IMPORTANT]
> Las sesiones de segundo plano se inician en un subproceso en segundo plano, por lo que las llamadas para actualizar la interfaz de usuario se deben ejecutar explícitamente en el subproceso de interfaz de usuario mediante una llamada a `InvokeOnMainThread` para evitar iOS finaliza la aplicación. 


## <a name="handling-transfer-completion"></a>Finalización de la transferencia de control

El paso final consiste en informar a la aplicación cuando se completan todas las tareas asociadas con la sesión y controlar el contenido nuevo.

En el *AppDelegate*, suscribirse a las `HandleEventsForBackgroundUrl` eventos. Cuando la aplicación entra en segundo plano y se está ejecutando una sesión de transferencia, se llama a este método y el sistema pasa es un controlador de finalización:

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Se usará el controlador de finalización que iOS sepan cuando ha terminado nuestra aplicación procesamiento.

Recuerde que una sesión puede generar varias tareas para procesar a una transferencia. Cuando se completa la última tarea, una aplicación suspendida o terminada es volver a iniciado en segundo plano. A continuación, la aplicación vuelve a conectarse a la `NSURLSession` con el identificador de sesión único y llamadas `DidFinishEventsForBackgroundSession` en el delegado de la sesión. Este método es la oportunidad de la aplicación para controlar el contenido nuevo, incluida la actualización de la interfaz de usuario para que refleje los resultados de la transferencia:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Una vez que acabamos de control nuevo contenido, tenemos que llamar al controlador de finalización para que el sistema sepa que es seguro tomar una instantánea de la aplicación y volver al modo de suspensión:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  var appDelegate = UIApplication.SharedApplication.Delegate as AppDelegate;

  // Handle new information, update UI, etc.

  // call completion handler when you're done
  if (appDelegate.backgroundSessionCompletionHandler != null) {
    NSAction handler = appDelegate.backgroundSessionCompletionHandler;
    appDelegate.backgroundSessionCompletionHandler = null;
    handler.Invoke ();
  }
}
```

En este tutorial, se tratan los pasos básicos para implementar el servicio de transferencia en segundo plano en iOS 7.



## <a name="related-links"></a>Vínculos relacionados

- [Transferencia en segundo plano simple (ejemplo)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
