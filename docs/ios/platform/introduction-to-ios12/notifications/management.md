---
title: Administración de la notificación de Xamarin.iOS
description: Este documento describe cómo usar Xamarin.iOS para aprovechar las nuevas características de administración de notificación introducida en iOS 12.
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: a7a3bb8f720f1c6a2370a2510659693bb28ea09b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111699"
---
# <a name="notification-management-in-xamarinios"></a>Administración de la notificación de Xamarin.iOS

En iOS 12, el sistema operativo puede vínculo profundo de centro de notificaciones y la aplicación de configuración a la pantalla de administración de notificación de una aplicación. Esta pantalla debe permitir que los usuarios no participan en y fuera de los distintos tipos de notificaciones envía la aplicación.

## <a name="sample-app-redgreennotifications"></a>Aplicación de ejemplo: RedGreenNotifications

Para ver un ejemplo de cómo funciona la administración de notificación, eche un vistazo a la [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) aplicación de ejemplo.

Esta aplicación de ejemplo envía dos tipos de notificaciones, rojo y verde y proporciona una pantalla que permite a los usuarios participar o no en cualquier tipo.

Fragmentos de código en esta guía proceden de esta aplicación de ejemplo.

## <a name="notification-management-screen"></a>Pantalla de administración de notificación

En la aplicación de ejemplo, `ManageNotificationsViewController` define una interfaz de usuario que permite a los usuarios habilitar y deshabilitar notificaciones rojo y verde de forma independiente. Es un estándar [`UIViewController`](https://developer.xamarin.com/api/type/UIKit.UIViewController/)
que contiene un [ `UISwitch` ](https://developer.xamarin.com/api/type/UIKit.UISwitch/) para cada tipo de notificación. Si activa o desactiva el conmutador para cualquier tipo de notificación guarda, en los valores predeterminados del usuario, las preferencias del usuario para ese tipo de notificación:

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> La pantalla de administración de la notificación también comprueba si el usuario ha deshabilitado por completo las notificaciones para la aplicación. Si es así, oculta los botones de alternancia para los tipos de notificación individual. Para ello, la pantalla de administración de notificación:
>
> - Las llamadas [ `UNUserNotificationCenter.Current.GetNotificationSettingsAsync` ](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync()/) y examina la [ `AuthorizationStatus` ](https://developer.xamarin.com/api/property/UserNotifications.UNNotificationSettings.AuthorizationStatus/) propiedad.
> - Oculta los botones de alternancia para los tipos de notificación individual si se han deshabilitado por completo las notificaciones de la aplicación.
> - Vuelva a comprobar si se han deshabilitado las notificaciones cada vez que la aplicación se mueve al primer plano, ya que el usuario puede habilitar o deshabilitar las notificaciones en la configuración de iOS en cualquier momento.

La aplicación de ejemplo `ViewController` (clase), que se envía las notificaciones, comprobación la preferencia del usuario antes de enviar una notificación local para asegurarse de que la notificación es de un tipo de usuario realmente desea recibir:

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>Vínculo profundo

iOS vínculos profundos a la pantalla de administración de notificación de una aplicación desde el centro de notificaciones y la configuración de notificación de la aplicación en la aplicación configuración. Para facilitar esto, una aplicación debe:

- Indicar que una pantalla de administración de notificación está disponible pasando `UNAuthorizationOptions.ProvidesAppNotificationSettings` a solicitud de autorización de notificación de la aplicación.
- Implemente el `OpenSettings` método [ `IUNUserNotificationCenterDelegate` ](https://developer.xamarin.com/api/type/UserNotifications.IUNUserNotificationCenterDelegate/).

### <a name="authorization-request"></a>Solicitud de autorización

Para indicar al sistema operativo que está disponible una pantalla de administración de notificación, una aplicación debe pasar el `UNAuthorizationOptions.ProvidesAppNotificationSettings` opción (junto con cualquier otras notificación las opciones de entrega que necesita) a la `RequestAuthorization` método en el `UNUserNotificationCenter`.

Por ejemplo, en la aplicación de ejemplo `AppDelegate`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.ProvidesAppNotificationSettings | UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
```

### <a name="opensettings-method"></a>Método OpenSettings

El `OpenSettings` método, llamado por el sistema al vínculo profundo de la pantalla de administración de la notificación de una aplicación, debe navegar al usuario directamente a esa pantalla.

En la aplicación de ejemplo, este método realiza el segue a la `ManageNotificationsViewController` si es necesario:

```csharp
[Export("userNotificationCenter:openSettingsForNotification:")]
public void OpenSettings(UNUserNotificationCenter center, UNNotification notification)
{
    var navigationController = Window.RootViewController as UINavigationController;
    if (navigationController != null)
    {
        var currentViewController = navigationController.VisibleViewController;
        if (currentViewController is ViewController)
        {
            currentViewController.PerformSegue(ManageNotificationsViewController.ShowManageNotificationsSegue, this);
        }

    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Plataforma de notificaciones de usuario en Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novedades de las notificaciones de usuario (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedimientos recomendados y cuáles son las novedades en las notificaciones de usuario (sesión WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generar una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
