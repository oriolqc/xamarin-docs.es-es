---
title: Notificaciones en Xamarin.iOS
description: En esta sección se muestra cómo implementar notificaciones locales en Xamarin.iOS. Se explican los distintos elementos de interfaz de usuario de una notificación de iOS y se describe la API del implicada en crear y mostrar una notificación.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: f31490a683adfb46c609f14adf08b68de0abc375
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61083005"
---
# <a name="notifications-in-xamarinios"></a>Notificaciones en Xamarin.iOS

> [!IMPORTANT]
> La información de esta sección pertenece a iOS 9 y anteriores. Para iOS 10 y versiones posteriores, consulte el [Guía de marco de trabajo de notificación de usuario](~/ios/platform/user-notifications/index.md).

iOS tiene tres formas de indicar al usuario que ha recibido una notificación:

- **Sonido o Vibración** -iOS puede reproducir un sonido para notificar a los usuarios. Si se deshabilita el sonido, el dispositivo puede configurarse para Vibrar.
- **Alertas** -es posible mostrar un cuadro de diálogo en la pantalla con información sobre la notificación.
- **Distintivos** : cuando se publica una notificación, se puede mostrar un número (distintivo) en el icono de la aplicación.

iOS también proporciona un *centro de notificaciones* todas las notificaciones tanto locales como remotas, que mostrará al usuario. Los usuarios pueden tener acceso a esto dedo hacia abajo desde la parte superior de la pantalla:

![El centro de notificaciones](local-notifications-in-ios-images/image13.png "el centro de notificaciones")

## <a name="creating-local-notifications-in-ios"></a>Creación de notificaciones locales en iOS

iOS resulta bastante sencillo crear y controlar las notificaciones locales.
En primer lugar, iOS 8 requiere que las aplicaciones para pedir permiso del usuario mostrar las notificaciones. Agregue el código siguiente a la aplicación antes de intentar enviar una notificación local: la [adjunta ejemplo](https://developer.xamarin.com/samples/monotouch/LocalNotifications/) lo coloca en el **AppDelegate**del **FinishedLaunching** método.

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![Confirmar la capacidad para enviar una notificación local](local-notifications-in-ios-images/image0-sml.png "confirmando la capacidad para enviar una notificación local")](local-notifications-in-ios-images/image0.png#lightbox)

Para programar una notificación local, cree un `UILocalNotification` de objetos, establezca el `FireDate`y programarla mediante el `ScheduleLocalNotification` método en el `UIApplication.SharedApplication` objeto. El fragmento de código siguiente muestra cómo programar una notificación que desencadenará un minuto en el futuro y mostrar una alerta con un mensaje:

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

Captura de pantalla siguiente muestra el aspecto de esta alerta:

[![](local-notifications-in-ios-images/image2-sml.png "Una alerta de ejemplo")](local-notifications-in-ios-images/image2.png#lightbox)

Tenga en cuenta que si el usuario decide *no permitir* se mostrarán las notificaciones, a continuación, nada.

Si desea aplicar un distintivo en el icono de la aplicación con un número, puede establecer, tal como se muestra en el código de la línea siguiente:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

Reproducción de orden de un sonido con el icono, establezca la propiedad de SoundName en la notificación como se muestra en el siguiente fragmento de código:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Si el sonido de notificación tiene más de 30 segundos, iOS reproducirá el valor predeterminado en su lugar.

> [!IMPORTANT]
> Hay un error en el simulador de iOS que se desencadenará dos veces la notificación de delegado. Este problema no debe producirse al ejecutar la aplicación en un dispositivo.

## <a name="handling-notifications"></a>Control de notificaciones

las aplicaciones de iOS controlan notificaciones locales y remotas de casi exactamente la misma manera. Cuando se ejecuta una aplicación, el `ReceivedLocalNotification` método o la `ReceivedRemoteNotification` método en el `AppDelegate` llamará clase y la información de notificación se pasará como un parámetro.

Una aplicación puede controlar una notificación de maneras diferentes. Por ejemplo, la aplicación podría mostrar simplemente una alerta para recordar a los usuarios sobre algún evento. O bien, la notificación se puede usar para mostrar una alerta al usuario que ha terminado un proceso, como los archivos de sincronización a un servidor.

El código siguiente muestra cómo controlar una notificación local y mostrar una alerta y restablecer el número de identificación en cero:

```csharp
public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
{
    // show an alert
    UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
    okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

    Window.RootViewController.PresentViewController(okayAlertController, true, null);

    // reset our badge
    UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
}
```

Si no se está ejecutando la aplicación, iOS se reproduzca el sonido o actualizar el distintivo de icono según corresponda. Cuando el usuario inicia la aplicación asociada con la alerta, la aplicación se iniciará y la `FinishedLaunching` se llamará al método en el delegado de la aplicación y se pasan en la información de notificación a través de la `launchOptions` parámetro. Si el diccionario de opciones contiene la clave `UIApplication.LaunchOptionsLocalNotificationKey`, el `AppDelegate` sabe que la aplicación se inició desde una notificación local. El fragmento de código siguiente muestra este proceso:

```csharp
// check for a local notification
if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
{
    var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
    if (localNotification != null)
    {
        UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        Window.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
}
```

Para una notificación remota, `launchOptions` tendrá un `LaunchOptionsRemoteNotificationKey` con un asociado `NSDictionary` que contiene la carga de notificación remota. Puede extraer la carga de notificación a través de la `alert`, `badge`, y `sound` claves. El fragmento de código siguiente muestra cómo obtener notificaciones remotas:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Resumen

En esta sección se ha explicado cómo crear y publicar una notificación en Xamarin.iOS. Muestra cómo una aplicación puede reaccionar ante las notificaciones mediante la invalidación de la `ReceivedLocalNotification` método o la `ReceivedRemoteNotification` método en el `AppDelegate`.

## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones locales (ejemplo)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Local y notificaciones de inserción para los desarrolladores](https://developer.apple.com/notifications/)
- [Local y la Guía de programación de notificaciones de inserción](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
