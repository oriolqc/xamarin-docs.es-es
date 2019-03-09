---
title: Notificaciones provisionales en Xamarin.iOS
description: Este documento describe cómo usar Xamarin.iOS para trabajar con las notificaciones provisionales. Notificaciones provisionales, introducidas en iOS 12, permiten a las aplicaciones envíen notificaciones silenciosas sin permiso explícito del usuario.
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 434dd2e2ee7a0064b706872a228070c5114078c4
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667040"
---
# <a name="provisional-notifications-in-xamarinios"></a>Notificaciones provisionales en Xamarin.iOS

Notificaciones provisionales permiten aplicaciones entregar notificaciones sin consentimiento explícito iniciales de un usuario. Estas notificaciones llegan silenciosamente y mostrar solo en el centro de notificaciones, que permite a los usuarios obtener una vista previa antes de participar dentro o fuera de su entrega continua.

En el centro de notificaciones, los usuarios pueden especificar que una aplicación debe detener la entrega de notificaciones provisionales, continuar entregarlos provisionalmente o iniciar entregarlos más destacados.

## <a name="sample-app-redgreennotifications"></a>Aplicación de ejemplo: RedGreenNotifications

Eche un vistazo a la [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) aplicación de ejemplo que envía notificaciones provisionales.

## <a name="sending-provisional-notifications"></a>Envío de notificaciones provisional

Para enviar notificaciones provisionales, proporcionar `UNAuthorizationOptions.Provisional` como una opción para el [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
método de `UNUserNotificationCenter`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

Si el usuario promueve notificaciones provisionales hasta la entrega destacada o la `UNAuthorizationOptions` valores pasados a `RequestAuthorization` determinará la nueva configuración de entrega de notificación (en el código anterior, `UNAuthorizationOptions.Alert` y `UNAuthorizationOptions.Sound`).

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Plataforma de notificaciones de usuario en Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedimientos recomendados y cuáles son las novedades en las notificaciones de usuario (sesión WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generar una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
