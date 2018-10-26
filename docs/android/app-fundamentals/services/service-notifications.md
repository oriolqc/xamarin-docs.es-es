---
title: Notificaciones del servicio
description: Esta guía describe cómo un servicio de Android puede usar notificaciones locales para enviar información a un usuario.
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d56f67254a9eae334fa8ac3f08d3ef270800c309
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103256"
---
# <a name="service-notifications"></a>Notificaciones del servicio

_Esta guía describe cómo un servicio de Android puede usar notificaciones locales para enviar información a un usuario._


## <a name="service-notifications-overview"></a>Información general de las notificaciones del servicio

Las notificaciones del servicio, permitir que una aplicación mostrar información al usuario, incluso si la aplicación de Android no está en primer plano. Es posible que una notificación proporcionar acciones del usuario, como mostrar una actividad desde una aplicación. Ejemplo de código siguiente muestra cómo un servicio podría enviar una notificación a un usuario:

```csharp
[Service]
public class MyService: Service 
{
    // A notification requires an id that is unique to the application.
    const int NOTIFICATION_ID = 9000;
    
    public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
    {
        // Code omitted for clarity - here is where the service would do something.
    
        // Work has finished, now dispatch anotification to let the user know.
        Notification.Builder notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_notification_small_icon)
            .SetContentTitle(Resources.GetString(Resource.String.notification_content_title))
            .SetContentText(Resources.GetString(Resource.String.notification_content_text));
        
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.Notify(NOTIFICATION_ID, notificationBuilder.Build());
    }
}
```

Esta captura de pantalla es un ejemplo de la notificación de que se muestra:

[![Icono de notificación en la barra de estado](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

Cuando las diapositivas de usuario hacia abajo de la pantalla de notificación en la parte superior, se muestra la notificación completa:

![Notificación a que se muestra en la Bandeja de notificación](service-notifications-images/02-fullnotification.png)


## <a name="updating-a-notification"></a>Actualización de una notificación

Para actualizar una notificación, el servicio se volverá a publicar la notificación usando el mismo identificador de notificación. Android mostrará o actualizar la notificación en la barra de estado según sea necesario.

```csharp 
void UpdateNotification(string content)
{
   var notification = GetNotification(content, pendingIntent);

   NotificationManager notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
   notificationManager.Notify(NOTIFICATION_ID, notification);
}

Notification GetNotification(string content, PendingIntent intent)
{
   return new Notification.Builder(this)
           .SetContentTitle(tag)
           .SetContentText(content)
           .SetSmallIcon(Resource.Drawable.NotifyLg)
           .SetLargeIcon(BitmapFactory.DecodeResource(Resources, Resource.Drawable.Icon))
           .SetContentIntent(intent).Build();
}
```

Para obtener más información acerca de las notificaciones está disponible en el [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md) sección de la [notificaciones Android](~/android/app-fundamentals/notifications/index.md) guía.


## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones locales en Android](~/android/app-fundamentals/notifications/local-notifications.md)
