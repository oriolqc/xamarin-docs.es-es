---
title: Servicios de primer plano
ms.topic: article
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 857c7fe47ad5fa0f50fce95672bc8ffbf6771dc9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="foreground-services"></a>Servicios de primer plano

Algunos servicios de llevar a cabo algunas tareas que los usuarios sean conscientes activamente de, estos servicios se conocen como _servicios de primer plano_. Un ejemplo de un servicio de primer plano es una aplicación que proporciona el usuario con direcciones al automóvil o a pie. Incluso si la aplicación está en segundo plano, es importante que el servicio tiene suficientes recursos para que funcione correctamente y que el usuario tiene una forma rápida y práctica para tener acceso a la aplicación. Para una aplicación Android, esto significa que un servicio de primer plano debe recibir la prioridad más alta que un servicio "normal" y un servicio de primer plano debe proporcionar un `Notification` que Android mostrará siempre y cuando el servicio se está ejecutando.
 
Un servicio de primer plano se crea e inicia al igual que cualquier otro servicio. Cuando se está iniciando el servicio, que se registre con Android como un servicio de primer plano.
 
Esta guía describe los pasos adicionales que deben tener cuidados para registrar un servicio de primer plano y para detener el servicio cuando termine.

## <a name="registering-as-a-foreground-service"></a>Registrar como un servicio de primer plano

Un servicio de primer plano es un tipo especial de un servicio dependiente o un servicio iniciado. El servicio, una vez que se ha iniciado, llama a la [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/) método registrarse a sí mismo con Android como un servicio de primer plano.   

`StartForeground` toma dos parámetros, los cuales son obligatorios:
 
* Valor entero que es único dentro de la aplicación para identificar el servicio.
* Un `Notification` objeto que Android se mostrará en la barra de estado para siempre y cuando el servicio se está ejecutando.

Android mostrará la notificación en la barra de estado para siempre y cuando el servicio se está ejecutando. La notificación, como mínimo, proporcionará una indicación visual al usuario que está ejecutando el servicio. Idealmente, la notificación debe proporcionar al usuario un acceso directo a la aplicación o posiblemente algunos botones de acción para controlar la aplicación. Un ejemplo de esto es un Reproductor de música &ndash; la notificación de que se muestra puede tener botones para detener o reproducir música, rebobinar la canción anterior, o para ir directamente a la siguiente canción. 

Este fragmento de código es un ejemplo de cómo registrar un servicio como un servicio de primer plano:   

```csharp
// This is any integer value unique to the application.
public const int SERVICE_RUNNING_NOTIFICATION_ID = 10000;

public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
{
    // Code not directly related to publishing the notification has been omitted for clarity.
    // Normally, this method would hold the code to be run when the service is started.
    
    var notification = new Notification.Builder(this)
        .SetContentTitle(Resources.GetString(Resource.String.app_name))
        .SetContentText(Resources.GetString(Resource.String.notification_text))
        .SetSmallIcon(Resource.Drawable.ic_stat_name)
        .SetContentIntent(BuildIntentToShowMainActivity())
        .SetOngoing(true)
        .AddAction(BuildRestartTimerAction())
        .AddAction(BuildStopServiceAction())
        .Build();

    // Enlist this instance of the service as a foreground service
    StartForeground(SERVICE_RUNNING_NOTIFICATION_ID, notification);
}
```

La notificación anterior mostrará una notificación de la barra de estado que es similar al siguiente:

![Imagen que muestra la notificación en la barra de estado](foreground-services-images/foreground-services-01.png "imagen que muestra la notificación en la barra de estado")

Esta captura de pantalla muestra la notificación expandida en la Bandeja de notificación con dos acciones que permiten al usuario controlar el servicio:

![Imagen que muestra la notificación expandida](foreground-services-images/foreground-services-02.png "imagen que muestra la notificación expandida.")

Para obtener más información acerca de las notificaciones está disponible en la [notificaciones Local](~/android/app-fundamentals/notifications/local-notifications.md) sección de la [notificaciones Android](~/android/app-fundamentals/notifications/index.md) guía.

## <a name="unregistering-as-a-foreground-service"></a>Al anular el registro como un servicio de primer plano

Un servicio puede anular se muestra como un servicio de primer plano llamando al método `StopForeground`. `StopForeground` no se detendrá el servicio, pero se quitará el icono de notificación y las señales de Android que este servicio puede apagar si es necesario.

La notificación de la barra de estado que se muestra también pueden quitarse pasando `true` al método: 

```csharp
StopForeground(true);
```

Si se detiene el servicio con una llamada a `StopSelf` o `StopService`, a continuación, del mismo modo se quitarán la notificación de la barra de estado.


## <a name="related-links"></a>Vínculos relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForegrond](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
