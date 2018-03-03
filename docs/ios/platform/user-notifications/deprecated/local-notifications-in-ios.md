---
title: Notificaciones de Xamarin.iOS
description: "Esta sección muestra cómo implementar notificaciones locales en Xamarin.iOS. Se explican los distintos elementos de interfaz de usuario de una notificación de iOS y se describe la API del implica crear y mostrar una notificación."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d84473ee4379cd9a39315635017b81a2714da162
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="notifications-in-xamarinios"></a>Notificaciones de Xamarin.iOS

_Esta sección muestra cómo implementar notificaciones locales en Xamarin.iOS. Se explican los distintos elementos de interfaz de usuario de una notificación de iOS y se describe la API del implica crear y mostrar una notificación._

> [!IMPORTANT]
> **Nota:** la información en esta sección se aplica a iOS 9 y anteriores, se han quedado aquí para compatibilidad con versiones anteriores de iOS. Para iOS 10 y versiones posteriores, consulte la [Guía de la estructura de notificación de usuario](~/ios/platform/user-notifications/index.md) para admitir tanto locales como remotos de notificación en un dispositivo iOS.

iOS consta de tres maneras para indicar al usuario que se ha recibido una notificación:

-  **Sonido o Vibración** -iOS puede reproducir un sonido para notificar a los usuarios. Si se deshabilita el sonido, el dispositivo puede configurarse para Vibrar.
-  **Alertas** -es posible mostrar un cuadro de diálogo en la pantalla con información sobre la notificación.
-  **Distintivos** : cuando se publica una notificación, se puede mostrar un número (distintivo) en el icono de la aplicación.


iOS también proporciona un *centro de notificaciones* todas las notificaciones tanto locales como remotas, que mostrará al usuario. Los usuarios pueden tener acceso a esta deslizando hacia abajo desde la parte superior de la pantalla:

 ![](local-notifications-in-ios-images/image13.png "El centro de notificaciones")

## <a name="creating-local-notifications-in-ios"></a>Creación de notificaciones Local en iOS

iOS hace bastante simple crear y administrar notificaciones locales.
En primer lugar, iOS 8 requiere aplicaciones para solicitar permiso del usuario mostrar las notificaciones. Agregue el código siguiente a la aplicación antes de intentar enviar una notificación local: el ejemplo que se adjunta lo coloca en el **AppDelegate**del **FinishedLaunching** método.

```csharp
var settings = UIUserNotificationSettings.GetSettingsForTypes(
  UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound
  , null);
UIApplication.SharedApplication.RegisterUserNotificationSettings (settings);
```

  [ ![](local-notifications-in-ios-images/image0-sml.png "Confirmar la capacidad de enviar una notificación local")](local-notifications-in-ios-images/image0.png)

Para programar una notificación local se crea un `UILocalNotification` objeto, establezca el `FireDate`y programarlo a través de la `ScheduleLocalNotification` método en la `UIApplication.SharedApplication` objeto. El siguiente fragmento de código muestra cómo programar una notificación que desencadenará un minuto en el futuro y mostrar una alerta con un mensaje:

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

Captura de pantalla siguiente muestra el aspecto de esta alerta:

  [ ![](local-notifications-in-ios-images/image2-sml.png "Una alerta de ejemplo")](local-notifications-in-ios-images/image2.png)

Tenga en cuenta que si el usuario decidió *no permitir* se mostrarán las notificaciones, a continuación, nada.

Si desea aplicar un distintivo en el icono de la aplicación con un número, puede establecer, tal como se muestra en el código de la línea siguiente:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

Reproducción de orden de un sonido con el icono, establezca la propiedad de SoundName relativa a la notificación tal como se muestra en el fragmento de código siguiente:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Por las directrices de interfaz humana de Apple, si una notificación reproduce un sonido, se debe también ir acompañado de un distintivo o una alerta para ayudar al usuario a identificar la aplicación que generó la alerta. Además, si el sonido tiene más de 30 segundos, iOS reproducirá el valor predeterminado en su lugar.

> [!IMPORTANT]
> **Tenga en cuenta**: hay un error en el simulador de iOS que se activará la notificación de delegado dos veces. Este problema no surge cuando se ejecuta la aplicación en un dispositivo.

## <a name="handling-notifications"></a>Controlar notificaciones

aplicaciones de iOS Administrar notificaciones locales y remotas casi exactamente del mismo modo. Cuando se ejecuta una aplicación, el `ReceivedLocalNotification` método o `ReceivedRemoteNotification` se llama al método en la clase AppDelegate y la información de notificación se pasarán como un parámetro.

Una aplicación puede controlar una notificación de maneras diferentes. Por ejemplo, la aplicación solo puede mostrar una alerta para recordar a los usuarios sobre algún evento. O bien, la notificación podría utilizarse para mostrar una alerta al usuario que ha finalizado un proceso, como los archivos de sincronización a un servidor.

El código siguiente muestra cómo controlar una notificación local y mostrar una alerta y restablecer el número de identificación en cero:

```csharp
 public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
        {
            // show an alert
            UIAlertController okayAlertController = UIAlertController.Create (notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
            okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
            viewController.PresentViewController (okayAlertController, true, null);

            // reset our badge
            UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
        }
```

Si no se está ejecutando la aplicación, iOS se reproduce el sonido o actualizar el distintivo de icono según corresponda. Cuando el usuario inicia la aplicación asociada con la alerta, la aplicación se iniciará y se llamará al método FinishedLaunching en el delegado de la aplicación y la información de notificación se le pasará a través del parámetro de opciones. Si el diccionario de opciones contiene la clave `UIApplication.LaunchOptionsLocalNotificationKey`, la AppDelegate sabe que la aplicación se inició desde una notificación local. El fragmento de código siguiente muestra este proceso:

```csharp
// check for a local notification
if (options.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
 {
     var localNotification = options[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
     if (localNotification != null)
     {
        UIAlertController okayAlertController = UIAlertController.Create (localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
        viewController.PresentViewController (okayAlertController, true, null);

         // reset our badge
         UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
     }
 }
```

Si es una notificación remota el diccionario de opciones en su lugar contendría una `LaunchOptionsRemoteNotificationKey`, y el valor resultante para esa clave un `NSDictionary` objeto con la carga de notificación remoto. Puede extraer la carga de notificación a través de la alerta, distintivo y teclas de sonido. El fragmento de código siguiente muestra cómo obtener notificaciones remotas:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Resumen

En esta sección se ha explicado cómo crear y publicar una notificación en Xamarin.iOS. Muestra cómo una aplicación puedan reaccionar a las notificaciones mediante el reemplazo de la `ReceivedLocalNotification` método o la `ReceivedRemoteNotification` método en el `AppDelegate`.


## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones locales (ejemplo)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Local y notificaciones de inserción para desarrolladores](https://developer.apple.com/notifications/)
- [Local y Guía de programación de notificaciones de inserción](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
