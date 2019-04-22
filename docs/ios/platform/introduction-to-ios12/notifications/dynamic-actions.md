---
title: Botones de acción de notificación dinámico de Xamarin.iOS
description: Con iOS 12, puede agregar, quitar y actualizar los botones de acción que se muestra junto con una notificación de una extensión de contenido de notificación. Este documento describe cómo usar los botones de acción de notificación dinámico con Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 5611d673ecc7af896fd3a9e566e184e408b6b367
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870097"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Botones de acción de notificación dinámico de Xamarin.iOS

En iOS 12, las notificaciones pueden agregar, quitar y actualizar dinámicamente sus botones de acción asociada. Dicha personalización permite proporcionar a los usuarios con las acciones que son directamente relevantes para el contenido de la notificación y la interacción del usuario con él.

## <a name="sample-app-redgreennotifications"></a>Aplicación de ejemplo: RedGreenNotifications

Los fragmentos de código en esta guía proceden de la [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) aplicación de ejemplo que demuestra cómo usar Xamarin.iOS para trabajar con los botones de acción de notificación de 12 de iOS.

Esta aplicación de ejemplo envía dos tipos de notificaciones locales: rojo y verde.
Después de tener la aplicación envía una notificación, el uso de 3D Touch para ver su interfaz de usuario personalizada. A continuación, utilice los botones de acción de la notificación se va a girar la imagen que muestra. Que gira la imagen, un **restablecer giro** botón aparecerá y desaparecerá según sea necesario.

Fragmentos de código en esta guía proceden de esta aplicación de ejemplo.

## <a name="default-action-buttons"></a>Botones de acción predeterminada

Categoría de una notificación determina sus botones de acción predeterminada.

Crear y registrar las categorías de notificación mientras se inicia una aplicación.
Por ejemplo, en el [aplicación de ejemplo](#sample-app-redgreennotifications), `FinishedLaunching` método `AppDelegate` hace lo siguiente:

- Define una categoría para las notificaciones de color rojo y otro para las notificaciones de verde
- Estas categorías se registra mediante una llamada a la [`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
método de `UNUserNotificationCenter`
- Asocia un único [`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
para cada categoría

El código de ejemplo siguiente muestra cómo funciona esto:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional | UNAuthorizationOptions.ProvidesAppNotificationSettings;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
        var rotateTwentyDegreesAction = UNNotificationAction.FromIdentifier("rotate-twenty-degrees-action", "Rotate 20°", UNNotificationActionOptions.None);

        var redCategory = UNNotificationCategory.FromIdentifier(
            "red-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var greenCategory = UNNotificationCategory.FromIdentifier(
            "green-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var set = new NSSet<UNNotificationCategory>(redCategory, greenCategory);
        center.SetNotificationCategories(set);
    });
    // ...
}
```

Según este código, cualquier notificación cuya [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
estará "categoría roja" o "green-category", de forma predeterminada, mostrar un **girar 20 º** botón de acción.

## <a name="in-app-handling-of-notification-action-buttons"></a>En la aplicación control de botones de acción de notificación

`UNUserNotificationCenter` tiene un `Delegate` propiedad de tipo [ `IUNUserNotificationCenterDelegate` ](xref:UserNotifications.IUNUserNotificationCenterDelegate).

En la aplicación de ejemplo, `AppDelegate` configura a sí mismo como delegado del centro de notificaciones de usuario en `FinishedLaunching`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = // ...
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        center.Delegate = this;
        // ...
```

A continuación, `AppDelegate` implementa [`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
para controlar las derivaciones de botón de acción:

```csharp
[Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
{
    if (response.IsDefaultAction)
    {
        Console.WriteLine("ACTION: Default");
    }
    if (response.IsDismissAction)
    {
        Console.WriteLine("ACTION: Dismiss");
    }
    else
    {
        Console.WriteLine($"ACTION: {response.ActionIdentifier}");
    }

    completionHandler();
        }
```

Esta implementación de `DidReceiveNotificationResponse` no controla la notificación **girar 20 º** botón de acción. En su lugar, la extensión de contenido de la notificación controla derivaciones en este botón. Aún más la siguiente sección describe el control de botón de acción de notificación.

## <a name="action-buttons-in-the-notification-content-extension"></a>Botones de acción de la extensión de contenido de notificación

Una extensión de contenido de notificación contiene un controlador de vista que define la interfaz para una notificación personalizada.

Puede utilizar este controlador de vista el `GetNotificationActions` y `SetNotificationActions` métodos en su [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
propiedad para obtener acceso y modificar los botones de acción de la notificación.

En la aplicación de ejemplo, el controlador de vista de la extensión de contenido notificación modifica los botones de acción sólo al responder a una derivación de un botón de acción ya existente.

> [!NOTE]
> Una extensión de contenido puede responder a un toque el botón acción en su controlador de vista de notificación [ `DidReceiveNotificationResponse` ](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*) método, que se declara como parte de [IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension).
>
> Aunque comparte un nombre con el `DidReceiveNotificationResponse` método [se ha descrito anteriormente](#in-app-handling-of-notification-action-buttons), se trata de un método diferente.
>
> Después de una extensión de contenido de notificación termina de procesar solo pulsar un botón, puede elegir si desea indicar a la aplicación principal para controlar ese toque el botón mismo o no. Para ello, debe pasar un valor apropiado de [UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption) a su controlador de finalización:
>
> - `Dismiss` indica que se debe descartar la interfaz de notificación, y que la aplicación principal no es necesario controlar el toque el botón.
> - `DismissAndForwardAction` indica que se debe descartar la interfaz de notificación y que la aplicación principal también debe controlar la derivación de botón.
> - `DoNotDismiss` indica que no se debe descartar la interfaz de notificación, y que la aplicación principal no es necesario controlar el toque el botón.

La extensión de contenido `DidReceiveNotificationResponse` método determina qué botón de acción que se ha punteado, gira la imagen en la interfaz de la notificación y muestra u oculta una **restablecer** botón de acción:

```csharp
[Export("didReceiveNotificationResponse:completionHandler:")]
public void DidReceiveNotificationResponse(UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
{
    var rotationAction = ExtensionContext.GetNotificationActions()[0];

    if (response.ActionIdentifier == "rotate-twenty-degrees-action")
    {
        rotationButtonTaps += 1;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        // 9 rotations * 20 degrees = 180 degrees. No reason to
        // show the reset rotation button when the image is half
        // or fully rotated.
        if (rotationButtonTaps % 9 == 0)
        {
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
        }
        else if (rotationButtonTaps % 9 == 1)
        {
            var resetRotationAction = UNNotificationAction.FromIdentifier("reset-rotation-action", "Reset rotation", UNNotificationActionOptions.None);
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction, resetRotationAction });
        }
    }

    if (response.ActionIdentifier == "reset-rotation-action")
    {
        rotationButtonTaps = 0;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
    }

    completionHandler(UNNotificationContentExtensionResponseOption.DoNotDismiss);
}
```

En este caso, el método pasa `UNNotificationContentExtensionResponseOption.DoNotDismiss` a su controlador de finalización. Esto lo que significa que la interfaz de la notificación se mantendrá abierta.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Plataforma de notificaciones de usuario en Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Declarar los tipos de notificación que requieren acción](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedimientos recomendados y cuáles son las novedades en las notificaciones de usuario (sesión WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generar una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
