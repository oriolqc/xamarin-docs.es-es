---
title: Transferencia en segundo plano y NSURLSession en Xamarin.iOS
description: Este documento proporciona un tutorial que muestra cómo usar la transferencia en segundo plano y NSUrlSession para iniciar la descarga de una imagen grande y continuar con la que se descargan cuando la aplicación se coloca en segundo plano.
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4e525388290d92901e68e61f1ffa81866f5aac4d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114241"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Transferencia en segundo plano y NSURLSession en Xamarin.iOS

Se inicia una transferencia en segundo plano mediante la configuración de un fondo `NSURLSession` y poniendo en cola cargar o descargar las tareas. Si se completen tareas mientras la aplicación se pasa a segundo plano, se suspende o finaliza, iOS notificará a la aplicación mediante una llamada al controlador de finalización de la aplicación *AppDelegate*. El siguiente diagrama muestra esto en acción:

 [![](background-transfer-walkthrough-images/transfer.png "Se inicia una transferencia en segundo plano mediante la configuración de un fondo NSURLSession y poniendo en cola cargar o descargar tareas")](background-transfer-walkthrough-images/transfer.png#lightbox)

Veamos qué aspecto en el código.

## <a name="configuring-a-background-session"></a>Configurar una sesión en segundo plano

Para realizar una sesión en segundo plano, cree un nuevo `NSUrlSession` y configurarlo con una `NSUrlSessionConfiguration` objeto.

El objeto de configuración determina lo que puede hacer la sesión y los tipos de tareas se puede ejecutar.
Sesiones configuradas mediante el `CreateBackgroundSessionConfiguration` método se ejecute en un proceso independiente y realizar discrecionales transferencias (Wi-Fi) para conservar los datos y duración de batería.
Ejemplo de código siguiente muestra la configuración correcta de una sesión de transferencia en segundo plano mediante el `CreateBackgroundSessionConfiguration` método y un identificador de cadena único:

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

Además de un objeto de configuración, una sesión también requiere un delegado de la sesión y una cola.
La cola determina el orden en el que completará las tareas. El delegado de la sesión chaperones el proceso de transferencia y controla la autenticación, almacenamiento en caché y otros problemas relacionados con la sesión.

## <a name="working-with-tasks-and-delegates"></a>Trabajar con tareas y los delegados

Ahora que hemos configurado una sesión en segundo plano, vamos a iniciar tareas para administrar a la transferencia. Podemos realizar el seguimiento de estas tareas mediante el un `NSUrlSessionDelegate` instancia llama a un delegado de la sesión. El delegado de la sesión es responsable de activar una aplicación terminada o suspendida en segundo plano al controlador de autenticación, errores o finalización de la transferencia.

Un `NSUrlSessionDelegate` proporciona los métodos básicos siguientes para comprobar el estado de transferencia:

-  *DidFinishEventsForBackgroundSession* -obtiene llama a este método cuando han terminado de todas las tareas y completada la transferencia.
-  *DidReceiveChallenge* : se le llama a las credenciales cuando se necesita autorización de solicitud.
-  *DidBecomeInvalidWithError* -se llama si el `NSURLSession` pasa a ser invalidado.


Las sesiones en segundo plano necesitan a más especializados delegados según los tipos de tareas que se ejecutan. Las sesiones en segundo plano se limitan a dos tipos de tareas:

-  *Cargar tareas* -tareas de tipo `NSUrlSessionUploadTask` utilizar el `NSUrlSessionTaskDelegate` , que hereda de `NSUrlSessionDelegate` . Este delegado proporciona métodos adicionales para realizar un seguimiento de cargan de progreso, identificador de redirección de HTTP y mucho más.
-  *Descargar tareas* -tareas de tipo `NSUrlSessionDownloadTask` utilizar el `NSUrlSessionDownloadDelegate` , que hereda de `NSUrlSessionTaskDelegate` . Este delegado proporciona que todos los métodos para cargar las tareas, así como los métodos específicos de descarga para seguir el progreso de descarga y determinar cuándo se ha reanudado o completado una tarea de descarga.


El código siguiente define una tarea que puede usarse para descargar una imagen de una dirección URL. Ponemos en marcha la tarea mediante una llamada a `CreateDownloadTask` en nuestra sesión en segundo plano y pasar la solicitud de dirección URL:

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

Si queremos averiguar el progreso de una tarea de descarga, es posible invalidar el `DidWriteData` método para realizar un seguimiento del progreso e incluso actualizar la interfaz de usuario. Las actualizaciones de la interfaz de usuario aparecerán inmediatamente si la aplicación está en primer plano o estará esperando por el usuario la próxima vez que abran la aplicación.

La API de sesión delegado proporciona un amplio conjunto de herramientas para interactuar con las tareas. Para obtener una lista completa de la sesión de los métodos de delegado, consulte el `NSUrlSessionDelegate` documentación de API.

> [!IMPORTANT]
> En segundo plano para iniciar las sesiones en un subproceso en segundo plano, por lo que las llamadas para actualizar la interfaz de usuario se deben ejecutar explícitamente en el subproceso de interfaz de usuario mediante una llamada a `InvokeOnMainThread` para evitar la terminación de la aplicación de iOS. 


## <a name="handling-transfer-completion"></a>Finalización de la transferencia de control

El último paso es informar a la aplicación cuando se completan todas las tareas asociadas con la sesión y controlar el contenido nuevo.

En el *AppDelegate*, suscribirse a la `HandleEventsForBackgroundUrl` eventos. Cuando la aplicación entra en segundo plano y se está ejecutando una sesión de transferencia, se llama a este método y el sistema nos pasa un controlador de finalización:

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Usaremos el controlador de finalización para informar a iOS cuando se realiza la aplicación al procesamiento.

Recuerde que una sesión puede generar varias tareas para procesar a una transferencia. Cuando se completa la última tarea, una aplicación suspendida o terminada es volver a iniciada en segundo plano. A continuación, la aplicación vuelve a conectarse a la `NSURLSession` con el identificador de sesión único y las llamadas `DidFinishEventsForBackgroundSession` en el delegado de la sesión. Este método es la oportunidad de la aplicación controlar el nuevo contenido, incluida la actualización de la interfaz de usuario para reflejar los resultados de la transferencia:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Una vez que finalizamos control nuevo contenido, tenemos que llamar al controlador de finalización para que el sistema sepa que es seguro tomar una instantánea de la aplicación y volver al modo de suspensión:

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

En este tutorial, hemos tratado los pasos básicos para implementar el servicio de transferencia en segundo plano en iOS 7.



## <a name="related-links"></a>Vínculos relacionados

- [Transferencia en segundo plano simple (ejemplo)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
