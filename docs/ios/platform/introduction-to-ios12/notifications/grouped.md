---
title: Notificaciones agrupadas en Xamarin.iOS
description: Con 12 iOS, es posible a notificaciones de grupo en el centro de notificaciones o la pantalla de bloqueo por aplicación o por subproceso. Este documento describe cómo enviar subprocesos y las notificaciones no enlazadas con Xamarin.iOS.
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 6798c4c5fa7502ba5e99cb8bc209468acaa4a9ec
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402426"
---
# <a name="grouped-notifications-in-xamarinios"></a>Notificaciones agrupadas en Xamarin.iOS

De forma predeterminada, iOS 12 coloca todas las notificaciones de una aplicación en un grupo. El centro de notificación y pantalla de bloqueo muestran este grupo como una pila con la notificación más reciente en la parte superior. Los usuarios pueden expandir el grupo para ver todas las notificaciones que contiene y descartar al grupo como un todo.

Las aplicaciones también pueden notificaciones de grupo por subproceso, lo que facilita a los usuarios buscar e interactuar con la información específica que le interesadas.

## <a name="sample-app-groupednotifications"></a>Aplicación de ejemplo: GroupedNotifications

Para obtener información sobre cómo usar notificaciones agrupadas con Xamarin.iOS, eche un vistazo a la [GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications) aplicación de ejemplo.

Esta aplicación de ejemplo simula las conversaciones con sus amigos distintos, enviar una notificación para cada mensaje y se agrupan por subproceso. También se muestra cómo no enlazado land notificaciones en un grupo de nivel de aplicación.

Fragmentos de código en esta guía proceden de esta aplicación de ejemplo.

## <a name="request-authorization-and-allow-foreground-notifications"></a>Solicitud de autorización y permitir las notificaciones de primer plano

Antes de que una aplicación puede enviar notificaciones locales, debe solicitar permiso para hacerlo. En la aplicación de ejemplo [ `AppDelegate` ](xref:UIKit.UIApplicationDelegate), [ `FinishedLaunching` ](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary)) método solicita este permiso:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    center.RequestAuthorization(UNAuthorizationOptions.Alert, (bool success, NSError error) =>
    {
        // Set the Delegate regardless of success; users can modify their notification
        // preferences at any time in the Settings app.
        center.Delegate = this;
    });
    return true;
}
```

El [ `Delegate` ](xref:UserNotifications.UNUserNotificationCenter.Delegate) (establecido anteriormente) para un [ `UNUserNotificationCenter` ](xref:UserNotifications.UNUserNotificationCenter) decide si una aplicación en primer plano debe mostrar una notificación entrante mediante una llamada al controlador de finalización que se pasa a [`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})):

```csharp
[Export("userNotificationCenter:willPresentotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

El [ `UNNotificationPresentationOptions.Alert` ](xref:UserNotifications.UNNotificationPresentationOptions) parámetro indica que la aplicación debe mostrar la alerta, pero no reproducir un sonido o actualizar un distintivo.

## <a name="threaded-notifications"></a>Subprocesos de notificaciones

Puntee en la aplicación de ejemplo **mensaje con Alice** botón repetidamente para que envíe notificaciones para una conversación con un amigo denominado Alice.
Puesto que las notificaciones de la conversación forman parte del mismo subproceso, la pantalla de bloqueo y el centro de notificaciones agruparlos.

Para iniciar una conversación con un amigo diferentes, pulse el **elegir un nuevo friend** botón. Las notificaciones para esta conversación aparecen en un grupo independiente.

### <a name="threadidentifier"></a>ThreadIdentifier

Siempre que la aplicación de ejemplo inicia un nuevo subproceso, crea un identificador único del subproceso:

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

Para enviar una notificación de subprocesos, la aplicación de ejemplo:

- Comprueba si la aplicación tenga autorización para enviar una notificación.
- Crea un [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
objeto para la notificación del contenido y establece su [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
en el identificador de subproceso creado anteriormente.
- Crea una solicitud y la notificación de programa:

```csharp
async partial void ScheduleThreadedNotification(UIButton sender)
{
    var center = UNUserNotificationCenter.Current;

    UNNotificationSettings settings = await center.GetNotificationSettingsAsync();
    if (settings.AuthorizationStatus != UNAuthorizationStatus.Authorized)
    {
        return;
    }

    string author =  // ...
    string message = // ...

    var content = new UNMutableNotificationContent()
    {
        ThreadIdentifier = threadId,
        Title = author,
        Body = message,
        SummaryArgument = author
    };

    var request = UNNotificationRequest.FromIdentifier(
        Guid.NewGuid().ToString(),
        content,
        UNTimeIntervalNotificationTrigger.CreateTrigger(1, false)
    );

    center.AddNotificationRequest(request, null);

    // ...
}
```

Todas las notificaciones desde la misma aplicación con el mismo identificador de subproceso aparecerá en el mismo grupo de notificación.

> [!NOTE]
> Para establecer un identificador de subproceso en una notificación remota, agregue el `thread-id` clave para la carga JSON de la notificación. Consulte Apple [generar una notificación remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) documento para obtener más detalles.

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` Especifica cómo afectará el texto de resumen que aparece en la esquina inferior izquierda de un grupo de notificación a la que pertenece la notificación a una notificación. iOS agrega texto de resumen de las notificaciones en el mismo grupo para crear una descripción general de resumen.

La aplicación de ejemplo usa el autor del mensaje como argumento de resumen. Con este enfoque, el texto de resumen para un grupo de seis notificaciones con Alicia podría ser **5 más notificaciones de Alice y Me**.

## <a name="unthreaded-notifications"></a>Notificaciones no enlazadas

Cada vez que toque de la aplicación de ejemplo **aviso de cita** botón envía una de varias notificaciones de aviso de cita. Puesto que no están encadenados estos avisos, aparecen en el grupo de notificación de nivel de aplicación en la pantalla de bloqueo y en el centro de notificaciones.

Para enviar una no enlazada, la aplicación de ejemplo la notificación `ScheduleUnthreadedNotification` método utiliza código similar que anteriormente.
Sin embargo, no establece la `ThreadIdentifier` en el `UNMutableNotificationContent` objeto.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications)
- [Plataforma de notificaciones de usuario en Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Novedades de las notificaciones de usuario (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Uso de notificaciones agrupadas (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [Procedimientos recomendados y cuáles son las novedades en las notificaciones de usuario (sesión WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generar una notificación remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
