---
title: Servicios iniciados con Xamarin.Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9e7dabf87314d87ab5ab335c220c0e292e56073b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110900"
---
# <a name="started-services-with-xamarinandroid"></a>Servicios iniciados con Xamarin.Android

## <a name="started-services-overview"></a>Información general de servicios iniciados

Servicios iniciados suelen realizan una unidad de trabajo sin tener que proporcionar los comentarios directos o los resultados al cliente. Un ejemplo de una unidad de trabajo es un servicio que carga un archivo en un servidor. El cliente realizará una solicitud a un servicio para cargar un archivo desde el dispositivo a un sitio Web. El servicio silenciosamente cargará el archivo (incluso si la aplicación no tiene actividades en primer plano) y finaliza automáticamente cuando finaliza la carga. Es importante tener en cuenta que un servicio iniciado se ejecutará en el subproceso de interfaz de usuario de una aplicación. Esto significa que si un servicio realizar el trabajo que se bloqueará el subproceso de interfaz de usuario, debe crear y desechar subprocesos según sea necesario.

A diferencia de un servicio dependiente, no hay ningún canal de comunicación entre un servicio iniciado "puro" y sus clientes. Esto significa que un servicio iniciado implementará algunos métodos de ciclo de vida distinto que un servicio dependiente. La siguiente lista destacan los métodos del ciclo de vida común en un servicio iniciado:

* `OnCreate` &ndash; Se llama una vez cuando se inicia por primera vez el servicio. Esto es donde se debe implementar el código de inicialización.
* `OnBind` &ndash; Este método debe implementarse por todas las clases de servicio, sin embargo, un servicio iniciado no suelen tener un cliente enlazado a él. Por este motivo, simplemente devuelve un servicio iniciado `null`. En cambio, un servicio híbrido (que es la combinación de un servicio dependiente y un servicio iniciado) tiene que implementar y devolver un `Binder` para el cliente.
* `OnStartCommand` &ndash; Se invoca para que cada solicitud iniciar el servicio, ya sea en respuesta a una llamada a `StartService` o reiniciar el sistema. Esto es donde el servicio puede iniciar cualquier tarea de larga ejecución. El método devuelve un `StartCommandResult` valor que indica cómo o si el sistema debe controlar el reinicio del servicio después del apagado debido a memoria insuficiente. Esta llamada realiza en el subproceso principal. Este método se describe más detalladamente a continuación.
* `OnDestroy` &ndash; Este método se llama cuando el servicio se está destruyendo. Se utiliza para realizar cualquier final limpieza necesaria.

Es el método importante para un servicio iniciado el `OnStartCommand` método. Se invocará cada vez que el servicio recibe una solicitud para realizar algún trabajo. El fragmento de código siguiente es un ejemplo de `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

El primer parámetro es un `Intent` objeto que contiene los metadatos sobre el trabajo que realizar. El segundo parámetro contiene un `StartCommandFlags` valor que ofrece cierta información acerca de la llamada al método. Este parámetro no tiene uno de dos valores posibles:

* `StartCommandFlag.Redelivery` &ndash; Esto significa que el `Intent` es una entrega re de una sustitución `Intent`. Este valor se proporciona cuando el servicio ha devuelto `StartCommandResult.RedeliverIntent` pero se ha detenido antes de podría ser cerrar correctamente.
* `StartCommandFlag.Retry` &dash; Este valor se recibe cuando un anterior `OnStartCommand` llamada genera el error y está intentando iniciar el servicio de nuevo con la intención misma como el anterior intento erróneo de Android.
 
Por último, el tercer parámetro es un valor entero que es único para la aplicación que identifica la solicitud. Es posible que varios de los llamadores pueden invocar el mismo objeto de servicio. Este valor se utiliza para asociar una solicitud para detener un servicio con una solicitud para iniciar un servicio determinado. Se tratarán con más detalle en la sección [Deteniendo el servicio](#Stopping_the_Service). 

El valor `StartCommandResult` devuelta por el servicio como una sugerencia para Android en qué hacer si se elimina el servicio debido a restricciones de recursos. Hay tres valores posibles para `StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash; este valor le indica a Android que no es necesario reiniciar el servicio que ha suprimido. Como ejemplo de esto, considere la posibilidad de un servicio que genera miniaturas para una galería en una aplicación. Si se elimina el servicio, no es fundamental volver a crear la miniatura inmediatamente &ndash; la miniatura puede volver a crearse la próxima vez que se ejecuta la aplicación.
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash; esto indica a Android para reiniciar el servicio, pero no para entregar la intención último que se inició el servicio. Si no hay ningún pendientes intenciones para controlar, un `null` se proporcionará el parámetro intención. Un ejemplo de esto podría ser una aplicación de Reproductor de música; el servicio se reiniciará está preparado para reproducir música, pero la última canción reproducirá. 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash; este valor estará indique a Android para reiniciar el servicio y volver a enviar el último `Intent`. Un ejemplo de esto es un servicio que descarga un archivo de datos para una aplicación. Si se elimina el servicio, el archivo de datos debe descargarse. Devolviendo `StartCommandResult.RedeliverIntent`, cuando Android reinicia el servicio también proporcionará la intención (que contiene la dirección URL del archivo para descargar) para el servicio. Esto permitirá la descarga reiniciar o reanudar (dependiendo de la implementación exacta del código).

Hay un cuarto valor `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`. Este valor es devuelto por `OnStartCommand` y describe cómo Android seguirá el servicio ha suprimido. Normalmente, este valor no se usa para iniciar un servicio.

En este diagrama se muestran los eventos de ciclo de vida de un servicio iniciado: 

![Diagrama que muestra el orden en el que se llaman los métodos del ciclo de vida](started-services-images/started-service-01.png "un diagrama que muestra el orden en el que se llaman los métodos del ciclo de vida.")


<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>Deteniendo el servicio

Un servicio iniciado seguirá ejecutándose indefinidamente. Android mantendrá el servicio que se ejecuta siempre que hay suficientes recursos del sistema. El cliente debe detener el servicio o el servicio puede detenerse cuando haya terminado su trabajo. Hay dos maneras para detener un servicio: 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash; puede solicitar un cliente (por ejemplo, una actividad), un servicio se detenga una llamada a la `StopService` método: 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash; un servicio puede cerrarse invocando el `StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>Usar startId para detener un servicio

Pueden solicitar varios de los llamadores que se inicia un servicio. Si hay una solicitud de inicio pendiente, el servicio puede utilizar el `startId` que se pasa a `OnStartCommand` para impedir que el servicio se detuvo antes de tiempo. El `startId` corresponderá a la última llamada a `StartService`y se incrementará cada vez que se llama. Por lo tanto, si una solicitud posterior para `StartService` no ha producido todavía en una llamada a `OnStartCommand`, el servicio puede llamar a `StopSelfResult`, pasando el valor más reciente de `startId` ha recibido (en lugar de simplemente llamar `StopSelf`). Si una llamada a `StartService` no ha producido todavía en una llamada correspondiente a `OnStartCommand`, el sistema no detendrá el servicio, porque el `startId` usado en el `StopSelf` llamada no se corresponderá con la versión más reciente `StartService` llamar.


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
