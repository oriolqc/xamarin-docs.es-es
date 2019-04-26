---
title: Interfaces de usuario de notificación interactivo de Xamarin.iOS
description: Con 12 iOS, es posible crear interfaces de usuario interactiva para las notificaciones locales y remotas. Esta guía describe cómo usar estas características con Xamarin.iOS.
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: e6dc2f14b36c9d6f67f1df5ad3d118fa423e0d4d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034918"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Interfaces de usuario de notificación interactivo de Xamarin.iOS

[Las extensiones de contenido de notificación](~/ios/platform/user-notifications/advanced-user-notifications.md), introducida en iOS 10, te permiten crear interfaces de usuario personalizadas para las notificaciones. A partir de iOS 12, las interfaces de usuario de notificación pueden contener elementos interactivos como botones y los controles deslizantes.

## <a name="sample-app-redgreennotifications"></a>Aplicación de ejemplo: RedGreenNotifications

El [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) aplicación de ejemplo contiene una extensión de contenido de notificación con una interfaz de usuario interactiva.

Fragmentos de código en esta guía proceden de este ejemplo.

## <a name="notification-content-extension-infoplist-file"></a>Archivo Info.plist de extensión de contenido de notificación

En la aplicación de ejemplo, el **Info.plist** de archivos en el **RedGreenNotificationsContentExtension** proyecto contiene la siguiente configuración:

```xml
<!-- ... -->
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>UNNotificationExtensionCategory</key>
        <array>
            <string>red-category</string>
            <string>green-category</string>
        </array>
        <key>UNNotificationExtensionUserInteractionEnabled</key>
        <true/>
        <key>UNNotificationExtensionDefaultContentHidden</key>
        <true/>
        <key>UNNotificationExtensionInitialContentSizeRatio</key>
        <real>0.6</real>
    </dict>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.usernotifications.content-extension</string>
    <key></key>
    <true/>
</dict>
<!-- ... -->
```

Tenga en cuenta las siguientes características:

- El `UNNotificationExtensionCategory` matriz especifica el tipo de notificación categorías los identificadores de extensión de contenido.
- Para admitir contenido interactivo, Establece la extensión de contenido de notificación el `UNNotificationExtensionUserInteractionEnabled` clave a `true`.
- El `UNNotificationExtensionInitialContentSizeRatio` clave especifica la proporción de alto y ancho inicial de la interfaz de la extensión de contenido.

## <a name="interactive-interface"></a>Interfaz interactiva

**MainInterface.storyboard**, que define la interfaz para una extensión de contenido de notificación es un guión gráfico estándar que contiene un controlador de vista única. En la aplicación de ejemplo, el controlador de vista es de tipo `NotificationViewController`, y contiene una vista de imagen, tres botones y un control deslizante. Estos controles asocia el guión gráfico con controladores definidos en **NotificationViewController.cs**:

- El **Iniciar aplicación** llamadas del controlador de botón el `PerformNotificationDefaultAction` método de acción en `ExtensionContext`, que inicia la aplicación:

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    En de la aplicación, el centro de notificaciones de usuario `Delegate` (en la aplicación de ejemplo, el `AppDelegate`) puede responder a la interacción en el `DidReceiveNotificationResponse` método:

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- El **descartar notificación** llamadas del controlador de botón `DismissNotificationContentExtension` en `ExtensionContext`, que cierra la notificación:

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- El **quitar notificación** controlador del botón descarta la notificación y lo quita del centro de notificaciones:

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- El método que controla los cambios de valor en el control deslizante actualiza el valor alfa de la imagen se muestra en la interfaz de la notificación:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Plataforma de notificaciones de usuario en Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedimientos recomendados y cuáles son las novedades en las notificaciones de usuario (sesión WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Notificaciones enriquecidas (sesión WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [Generar una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
