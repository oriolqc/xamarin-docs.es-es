---
title: Servicios de primer plano
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: df917896f901060a5518076afa859d34a03f4d6d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108358"
---
# <a name="foreground-services"></a>Servicios de primer plano

Un servicio de primer plano es un tipo especial de un servicio dependiente o un servicio iniciado. En ocasiones servicios llevará a cabo tareas que los usuarios deben tener en cuenta activamente, estos servicios se conocen como _servicios de primer plano_. Un ejemplo de un servicio de primer plano es una aplicación que proporciona el usuario con instrucciones al automóvil o a pie. Incluso si la aplicación está en segundo plano, es importante que el servicio tiene suficientes recursos para que funcione correctamente y que el usuario tiene una forma rápida y útil para tener acceso a la aplicación. Para una aplicación Android, esto significa que un servicio de primer plano debe recibir prioridad más alta que un servicio "normal" y debe proporcionar un servicio de primer plano un `Notification` que Android mostrará siempre y cuando el servicio se está ejecutando.
 
Para iniciar un servicio de primer plano, la aplicación debe enviar una intención que le indica a Android para iniciar el servicio. A continuación, el servicio se debe registrar como un servicio de primer plano con Android. Aplicaciones que se ejecutan en Android 8.0 (o posterior) deben usar el `Context.StartForegroundService` para iniciar el servicio, mientras que las aplicaciones que se ejecutan en dispositivos con una versión anterior de Android deben usar (método) `Context.StartService`

Este método de extensión de C# es un ejemplo de cómo iniciar un servicio de primer plano. En Android 8.0 y versiones posteriores utilizarán la `StartForegroundService` método, en caso contrario, el antiguo `StartService` se usará el método.  

```csharp
public static void StartForegroundServiceComapt<T>(this Context context, Bundle args = null) where T : Service
{
    var intent = new Intent(context, typeof(T));
    if (args != null) 
    {
        intent.PutExtras(args);
    }

    if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.O)
    {
        context.StartForegroundService(intent);
    }
    else
    {
        context.StartService(intent);
    }
}
```

## <a name="registering-as-a-foreground-service"></a>Registrar como un servicio de primer plano

Una vez que ha iniciado un servicio de primer plano, debe registrarse con Android invocando el [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/). Si el servicio se inicia con la `Service.StartForegroundService` método pero no se registra, Android se detenga el servicio y se marca la aplicación que no responde.

`StartForeground` toma dos parámetros, los cuales son obligatorios:
 
* Valor entero que es único dentro de la aplicación para identificar el servicio.
* Un `Notification` objeto que Android se mostrará en la barra de estado para siempre y cuando el servicio se está ejecutando.

Android mostrará la notificación en la barra de estado para siempre y cuando el servicio se está ejecutando. La notificación, como mínimo, proporcionará una indicación visual al usuario que se está ejecutando el servicio. Idealmente, la notificación debe proporcionar al usuario un acceso directo a la aplicación o, posiblemente, algunos botones de acción para controlar la aplicación. Un ejemplo de esto es un Reproductor de música &ndash; la notificación de que se muestra puede tener botones para detener o reproducir música, rebobinar la canción anterior, o para ir directamente a la siguiente canción. 

Este fragmento de código es un ejemplo de cómo registrar un servicio de un servicio de primer plano:   

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

Para obtener más información acerca de las notificaciones está disponible en el [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md) sección de la [notificaciones Android](~/android/app-fundamentals/notifications/index.md) guía.

## <a name="unregistering-as-a-foreground-service"></a>Anulando el registro como un servicio de primer plano

Un servicio puede anular se muestra como un servicio de primer plano llamando al método `StopForeground`. `StopForeground` no se detendrá el servicio, pero quitará el icono de notificación y las señales de Android que este servicio puede cerrarse si es necesario.

La notificación de la barra de estado que se muestra también pueden quitarse pasando `true` al método: 

```csharp
StopForeground(true);
```

Si se detiene el servicio con una llamada a `StopSelf` o `StopService`, se quitará la notificación de la barra de estado.

## <a name="related-links"></a>Vínculos relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForeground](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
