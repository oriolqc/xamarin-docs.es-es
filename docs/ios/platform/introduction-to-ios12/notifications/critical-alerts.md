---
title: Alertas críticas en Xamarin.iOS
description: Este documento describe cómo usar las alertas críticas con Xamarin.iOS. Alertas críticas, introducidas con iOS 12, son las notificaciones de interrupciones que reproducción un sonido independientemente de si no molestar está en o el conmutador de timbre está desactivada.
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 4f847a86f3f92bcf7168c2e104471e1ca052969c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131870"
---
# <a name="critical-alerts-in-xamarinios"></a>Alertas críticas en Xamarin.iOS

Con iOS 12, las aplicaciones puedan enviar alertas críticas. Alertas críticas reproducción un sonido independientemente de si no molestar está habilitada o el conmutador de timbre está desactivada. Estas notificaciones son perjudiciales y solo deben usarse cuando los usuarios deben realizar una acción inmediata.

## <a name="custom-critical-alert-entitlement"></a>Derechos de alerta crítica personalizado

Mostrar las alertas críticas en su aplicación, en primer lugar [solicitar un derecho personalizado notificaciones de alerta crítica](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/) de Apple.

Después de recibir este derecho de Apple y siga cualquier asociadas instrucciones sobre cómo configurar la aplicación para usarla, agregar personalizado [derechos](~/ios/deploy-test/provisioning/entitlements.md) a la aplicación **Entitlements.plist** archivos. A continuación, configure su **firma de lote de iOS** opciones para usar **Entitlements.plist** al iniciar la aplicación en el simulador y dispositivo.

## <a name="request-authorization"></a>Solicitud de autorización

Solicitud de autorización de notificación de una aplicación solicita al usuario que habilita o deshabilita las notificaciones de una aplicación. Si la solicitud de autorización de notificación solicita permiso para enviar alertas críticas, la aplicación también le proporcionará al usuario una oportunidad de participar en las alertas críticas.

El siguiente código solicita permiso para enviar alertas críticas y notificaciones estándares y sonidos pasando adecuado [`UNAuthorizationOptions`](https://developer.xamarin.com/api/type/UserNotifications.UNAuthorizationOptions/)
los valores para [ `RequestAuthorization` ](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenter.RequestAuthorization/):

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.CriticalAlert;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

## <a name="local-critical-alerts"></a>Alertas críticas locales

Para enviar una alerta crítica local, cree una [`UNMutableNotificationContent`](https://developer.xamarin.com/api/type/UserNotifications.UNMutableNotificationContent/)
y establezca su `Sound` propiedad como:

- `UNNotificationSound.DefaultCriticalSound`, que utiliza el sonido de notificación crítica de forma predeterminada.
- `UNNotificationSound.GetCriticalSound`, que le permite especificar un personalizado de sonido que se incluye con la aplicación y un volumen.

A continuación, cree un `UNNotificationRequest` desde la notificación de contenido y agregarlo al centro de notificaciones:

```csharp
var content = new UNMutableNotificationContent()
{
    Title = "Critical alert title",
    Body = "Text of the critical alert",
    CategoryIdentifier = "my-critical-alert-category",
    // Sound = UNNotificationSound.DefaultCriticalSound
    Sound = UNNotificationSound.GetCriticalSound("my_critical_sound.m4a", 1.0f)
};

var request = UNNotificationRequest.FromIdentifier(
    Guid.NewGuid().ToString(),
    content,
    UNTimeIntervalNotificationTrigger.CreateTrigger(3, false)
);

var center = UNUserNotificationCenter.Current;
center.AddNotificationRequest(request, null);
```

> [!IMPORTANT]
> No se enviarán las alertas críticas si no están habilitadas para la aplicación. Junto con el mensaje que aparece la primera vez que una aplicación solicita permiso para enviar alertas críticas, un usuario también puede habilitar o deshabilitar las alertas críticas en su aplicación **notificaciones** sección de iOS **configuración**app.

## <a name="remote-critical-alerts"></a>Alertas críticas remotas

Para obtener información acerca de las alertas críticas remotas, consulte el [¿qué es nuevo en las notificaciones de usuario](https://developer.apple.com/videos/play/wwdc2018/710/) sesión desde la sesión WWDC 2018 y el [generar una notificación remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) documento.

## <a name="related-links"></a>Vínculos relacionados

- [Plataforma de notificaciones de usuario en Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedimientos recomendados y cuáles son las novedades en las notificaciones de usuario (sesión WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generar una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
