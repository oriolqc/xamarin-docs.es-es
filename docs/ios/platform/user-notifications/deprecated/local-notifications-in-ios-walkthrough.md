---
title: 'Tutorial: usar notificaciones Local en Xamarin.iOS'
description: En esta sección le mostraremos cómo usar notificaciones locales en una aplicación de Xamarin.iOS. Mostrarán los conceptos básicos de la creación y publicación de una notificación que se mostrará una alerta cuando recibe la aplicación.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: bb133d16f12249cbd31e4fce2b227162b4b28333
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Tutorial: usar notificaciones Local en Xamarin.iOS

_En esta sección le mostraremos cómo usar notificaciones locales en una aplicación de Xamarin.iOS. Mostrarán los conceptos básicos de la creación y publicación de una notificación que se mostrará una alerta cuando recibe la aplicación._

> [!IMPORTANT]
> La información de esta sección pertenece a iOS 9 y anteriores, se han quedado aquí para compatibilidad con versiones anteriores de iOS. Para iOS 10 y versiones posteriores, consulte la [Guía de la estructura de notificación de usuario](~/ios/platform/user-notifications/index.md) para admitir tanto locales como remotos de notificación en un dispositivo iOS.

## <a name="walkthrough"></a>Tutorial

Permiten crear una aplicación simple que se mostrará notificaciones locales en acción. Esta aplicación tendrá un solo botón en él. Cuando se haga clic en el botón, creará una notificación local. Una vez especificado ha transcurrido el período de tiempo, se verá la notificación aparecen.


1. En Visual Studio para Mac, cree una nueva solución de iOS de una vista única y llámelo `Notifications`.
1. Abra la `Main.storyboard` de archivos y arrastre un botón a la vista. Asígnele un nombre **botón**y asígnele el título **agregar notificación**. También puede establecer algunas [restricciones](~/ios/user-interface/designer/designer-auto-layout.md) al botón en este momento: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Establecer algunas restricciones en el botón")
1. Editar la `ViewController` clase y agregue el siguiente controlador de eventos para el método ViewDidLoad:

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    Este código creará una notificación que usa un sonido, Establece el valor de la notificación de icono en 1 y mostrará una alerta al usuario.

1. A continuación modifique el archivo `AppDelegate.cs`, primero agregue el código siguiente a la `FinishedLaunching` método. Nos hemos se comprueba para ver si el dispositivo ejecuta iOS 8, si estamos **necesario** para solicitar el permiso del usuario recibir notificaciones:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. Todavía en `AppDelegate.cs`, agregue el método siguiente a la que se llamará cuando se recibe una notificación:

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

1. Es necesario controlar el caso donde la notificación se inició debido a una notificación local. Editar el método `FinishedLaunching` en el `AppDelegate` para incluir el siguiente fragmento de código:


    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
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
    }

    ```

1. Por último, ejecute la aplicación. En iOS 8 deberá para permitir que las notificaciones. Haga clic en **Aceptar** y, a continuación, haga clic en el **agregar notificación** botón. Tras una breve pausa debería ver el cuadro de diálogo de alerta, como se muestra en las capturas de pantalla siguiente:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Confirmar la capacidad de enviar notificaciones") ![ ] (local-notifications-in-ios-walkthrough-images/image1.png "botón de la notificación de agregar") ![ ] (local-notifications-in-ios-walkthrough-images/image2.png "el cuadro de diálogo de alerta de notificación")

## <a name="summary"></a>Resumen

En este tutorial ha mostrado cómo usar las diversas API para crear y publicar las notificaciones en iOS. También muestra cómo actualizar el icono de la aplicación con un distintivo para proporcionar algunos comentarios específicos de la aplicación para el usuario.


## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones locales (ejemplo)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Local y Guía de programación de notificaciones de inserción](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
