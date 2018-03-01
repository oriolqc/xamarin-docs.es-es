---
title: Servicios iniciados con Xamarin.Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b6c0e67e3411aa5b7846a1b7bc0de2473a3546fd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="started-services-with-xamarinandroid"></a>Servicios iniciados con Xamarin.Android

## <a name="started-services-overview"></a>Información general de servicios iniciados

Servicios iniciados normalmente realizan una unidad de trabajo sin tener que proporcionar los comentarios directos o los resultados al cliente. Un ejemplo de una unidad de trabajo es un servicio que carga un archivo en un servidor. El cliente realizará una solicitud a un servicio para cargar un archivo desde el dispositivo a un sitio Web. El servicio silenciosamente cargará el archivo (incluso si la aplicación no tiene actividades en primer plano) y finaliza automáticamente cuando finalice la carga. Es importante tener en cuenta que un servicio iniciado se ejecutará en el subproceso de interfaz de usuario de una aplicación. Esto significa que si un servicio es realizar un trabajo que bloquea el subproceso de interfaz de usuario, debe crear y eliminar de subprocesos según sea necesario.

A diferencia de un servicio dependiente, no hay ningún canal de comunicación entre un servicio iniciado "puro" y sus clientes. Esto significa que un servicio iniciado implementará algunos métodos de ciclo de vida distinto que un servicio dependiente. La siguiente lista resalta los métodos comunes de ciclo de vida en un servicio iniciado:

* `OnCreate` &ndash; Se llama una vez cuando se inicia el servicio por primera vez. Esto es donde se debe implementar el código de inicialización.
* `OnBind` &ndash; Este método se debe implementar en todas las clases de servicio, sin embargo, un servicio iniciado no suele tener un cliente enlazado a él. Por este motivo, un servicio iniciado simplemente devuelve `null`. En cambio, un servicio híbrido (que es la combinación de un servicio dependiente y un servicio iniciado) tiene que implementar y devolver un `Binder` para el cliente.
* `OnStartCommand` &ndash; Se invoca para que cada solicitud iniciar el servicio, ya sea en respuesta a una llamada a `StartService` o un reinicio del sistema. Esto es donde el servicio puede comenzar a cualquier tarea de ejecución prolongada. El método devuelve un `StartCommandResult` valor que indica cómo o si el sistema debe controlar el reinicio del servicio después de un cierre debido a memoria insuficiente. Esta llamada tiene lugar en el subproceso principal. Este método se describe con más detalle a continuación.
* `OnDestroy` &ndash; Se llama a este método cuando el servicio se está destruyendo. Se utiliza para realizar cualquier final necesario eliminar los archivos innecesarios.

El método importante para un servicio iniciado es el `OnStartCommand` método. Se invocará cada vez que el servicio recibe una solicitud para realizar algún trabajo. El siguiente fragmento de código es un ejemplo de `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

El primer parámetro es un `Intent` objeto que contiene los metadatos sobre el trabajo que se va a realizar. El segundo parámetro contiene un `StartCommandFlags` valor que proporciona información sobre la llamada al método. Este parámetro no tiene uno de dos valores posibles:

* `StartCommandFlag.Redelivery` &ndash; Esto significa que la `Intent` es una entrega re de anterior `Intent`. Este valor se proporciona cuando el servicio devolviera `StartCommandResult.RedeliverIntent` , pero se ha detenido antes de podría estar cerrado correctamente.
* `StartCommandFlag.Retry` &dash; Este valor se recibe cuando anterior `OnStartCommand` llamada genera el error y está intentando iniciar el servicio de nuevo con el mismo propósito como el anterior intento erróneo de Android.
 
Por último, el tercer parámetro es un valor entero que es único para la aplicación que identifica la solicitud. Es posible que los llamadores varios pueden invocar el mismo objeto de servicio. Este valor se utiliza para asociar una solicitud para detener un servicio con una solicitud para iniciar un servicio determinado. Se tratarán con más detalle en la sección [Deteniendo el servicio](#Stopping_the_Service). 

El valor `StartCommandResult` devuelta por el servicio como una sugerencia para Android en qué hacer si se elimina el servicio debido a restricciones de recursos. Hay tres valores posibles para `StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash; este valor indica Android que no es necesario reiniciar el servicio que ha suprimido. Como ejemplo de esto, considere la posibilidad de un servicio que genera vistas en miniatura para una galería en una aplicación. Si se elimina el servicio, no es fundamental para volver a crear la miniatura inmediatamente &ndash; la miniatura puede volver a crearse la próxima vez que se ejecute la aplicación.
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash; esto indica a Android para reiniciar el servicio, pero no para entregar el último intento que inició el servicio. Si no hay ningún color pendiente para controlar, un `null` se proporcionará el parámetro intención. Un ejemplo de esto podría ser una aplicación de Reproductor de música; el servicio se reiniciará listo para reproducir música, pero éste reproduzca la última canción. 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash; este valor será indicar Android para reiniciar el servicio y volver a enviar el último `Intent`. Un ejemplo de esto es un servicio que descarga un archivo de datos para una aplicación. Si se elimina el servicio, el archivo de datos debe descargarse. Devolviendo `StartCommandResult.RedeliverIntent`, cuando Android reinicia el servicio también proporcionará la intención (que contiene la dirección URL del archivo que desea descargar) para el servicio. Esto le permitirá la descarga a reiniciar o reanudar (en función de la implementación exacta del código).

Hay un cuarto valor `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`. Este valor se devuelve por `OnStartCommand` y se describe cómo Android continuará el servicio ha suprimido. Este valor no se usa normalmente para iniciar un servicio.

En este diagrama se muestran los eventos de ciclo de vida de un servicio iniciado: 

![Un diagrama que muestra el orden en el que se llaman a los métodos del ciclo de vida](started-services-images/started-service-01.png "un diagrama que muestra el orden en el que se llaman a los métodos del ciclo de vida.")


## <a name="stopping-the-service"></a>Detener el servicio

Un servicio iniciado seguirán ejecutándose indefinidamente. Android mantendrá el servicio se ejecuta siempre que hay suficientes recursos del sistema. El cliente debe detener el servicio, o el servicio puede detenerse cuando se realiza su trabajo. Hay dos maneras de detener un servicio: 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash; puede solicitar un cliente (por ejemplo, una actividad), un servicio se detenga una llamada a la `StopService` método: 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash; un servicio puede cerrarse invocando el `StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>Usar startId para detener un servicio

Varios de los llamadores pueden solicitar que se inicia un servicio. Si hay una solicitud de inicio pendiente, el servicio puede utilizar la `startId` que se haya pasado al `OnStartCommand` para impedir que el servicio se detuvo antes de tiempo. El `startId` corresponderá a la llamada más reciente a `StartService`y se incrementan cada vez que se llama. Por lo tanto, si una solicitud posterior para `StartService` no se han conseguido todavía en una llamada a `OnStartCommand`, el servicio puede llamar `StopSelfResult`, pasando el valor más reciente de `startId` ha recibido (en lugar de simplemente llamar `StopSelf`). Si una llamada a `StartService` no se han conseguido todavía en una llamada correspondiente a `OnStartCommand`, el sistema no detendrá el servicio, porque el `startId` utilizados en el `StopSelf` llamada no se corresponderá con la versión más reciente `StartService` llamar.


## <a name="related-links"></a>Vínculos relacionados

- [StartedServicesDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [Iconos de barra de estado](http://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
