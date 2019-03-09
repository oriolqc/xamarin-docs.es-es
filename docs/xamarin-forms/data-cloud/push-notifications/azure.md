---
title: Envío de notificaciones Push desde aplicaciones móviles de Azure
description: En este artículo se explica cómo usar Azure Notification Hubs para enviar notificaciones push desde una instancia de Azure Mobile Apps a una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 92c068ceb3d382ed4612318dc987d950ec7e7ef2
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672552"
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>Envío de notificaciones Push desde aplicaciones móviles de Azure

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)

_Azure Notification Hubs proporciona una infraestructura de inserción escalable para enviar notificaciones de inserción móviles desde cualquier back-end a cualquier plataforma móvil, mientras se elimina la complejidad de un back-end de tener que comunicarse con sistemas de notificación de plataforma diferente. En este artículo se explica cómo usar Azure Notification Hubs para enviar notificaciones push desde una instancia de Azure Mobile Apps a una aplicación de Xamarin.Forms._

> [!VIDEO https://youtube.com/embed/le2lDY22xwM]

**Azure inserta Xamarin.Forms y el centro de notificaciones por [Xamarin University](https://university.xamarin.com/)**

Una notificación de inserción se utiliza para proporcionar información como, por ejemplo, un mensaje, desde un sistema back-end a una aplicación en un dispositivo móvil para aumentar el uso y la interacción de la aplicación. La notificación puede enviar en cualquier momento, incluso cuando el usuario no esté usando activamente la aplicación de destino.

Los sistemas de back-end envían notificaciones push a dispositivos móviles a través de los sistemas de notificación de plataforma (PNS), tal como se muestra en el diagrama siguiente:

[![](azure-images/pns.png "Sistemas de notificación de plataforma")](azure-images/pns-large.png#lightbox "sistemas de notificación de plataforma")

Para enviar una notificación de inserción, el sistema back-end se pone en contacto con el PNS específico de la plataforma para enviar una notificación a una instancia de la aplicación cliente. Esto aumenta significativamente la complejidad de back-end cuando se necesitan, las notificaciones de inserción multiplataforma dado que el back-end debe usar cada específicos de la plataforma API de PNS y protocolo.

Azure Notification Hubs elimina esta complejidad al abstraer los detalles de los sistemas de notificación de plataforma diferente, lo que permite una notificación multiplataforma para enviarse con una sola llamada API, tal como se muestra en el diagrama siguiente:

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png#lightbox)

Para enviar una notificación de inserción, el sistema de back-end solo contactos Azure Notification Hub, que a su vez se comunica con los sistemas de notificación de plataforma diferente, lo que disminuye la complejidad de back-end de código que notificaciones de inserción envía.

Azure Mobile Apps tiene compatibilidad integrada para las notificaciones push mediante notification hubs. El proceso para enviar una notificación de inserción desde una instancia de Azure Mobile Apps a una aplicación de Xamarin.Forms es como sigue:

1. La aplicación de Xamarin.Forms se registra con el PNS, que devuelve un identificador.
1. La instancia de Azure Mobile Apps envía una notificación a su centro de notificaciones de Azure, que especifica el identificador del dispositivo de destino.
1. El centro de notificaciones de Azure envía la notificación al PNS adecuado para el dispositivo.
1. El PNS envía la notificación al dispositivo especificado.
1. La aplicación de Xamarin.Forms procesa la notificación y muestra.

La aplicación de ejemplo muestra una aplicación de lista de tareas cuyos datos se almacenan en una instancia de Azure Mobile Apps. Cada vez que se agrega un nuevo elemento a la instancia de Azure Mobile Apps, se envía una notificación de inserción a la aplicación de Xamarin.Forms. Las capturas de pantalla siguientes muestran cada plataforma de visualización de la notificación push recibida:

[![](azure-images/screenshots.png "Recibir una notificación Push de aplicación de ejemplo")](azure-images/screenshots-large.png#lightbox "recibir una notificación Push de aplicación de ejemplo")

Para obtener más información sobre Azure Notification Hubs, consulte [Azure Notification Hubs](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/) y [agregar notificaciones push a la aplicación de Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/).

## <a name="azure-and-platform-notification-system-setup"></a>Azure y la configuración del sistema de notificación de plataforma

El proceso para integrar un centro de notificaciones de Azure en una instancia de Azure Mobile Apps es como sigue:

1. Cree una instancia de Azure Mobile Apps. Para obtener más información, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Configurar un centro de notificaciones. Para obtener más información, consulte [configurar un centro de notificaciones](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-hub).
1. Actualizar la instancia de Azure Mobile Apps para enviar notificaciones de inserción. Para obtener más información, consulte [actualizar el proyecto de servidor para enviar notificaciones push](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications).
1. Registrar con cada PNS.
1. Configurar el centro de notificaciones para comunicarse con cada PNS.

Las secciones siguientes proporcionan instrucciones de instalación adicionales para cada plataforma.

### <a name="ios"></a>iOS

Se realizarán los siguientes pasos adicionales para usar Apple Push Notification Service (APNS) desde un centro de notificaciones de Azure:

1. Generar un certificado de firma de solicitud para el certificado de inserción con la herramienta de acceso a llaves. Para obtener más información, consulte [generar el archivo de solicitud de firma de certificado para el certificado push](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate) en el centro de documentación de Azure.
1. Registrar la aplicación de Xamarin.Forms para la compatibilidad con las notificaciones push en el Centro para desarrolladores de Apple. Para obtener más información, consulte [registrar la aplicación para notificaciones de inserción](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications) en el centro de documentación de Azure.
1. Cree un inserción notificaciones Habilitar perfil de aprovisionamiento para la aplicación de Xamarin.Forms en el Centro para desarrolladores de Apple. Para obtener más información, consulte [crear un perfil de aprovisionamiento de la aplicación](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app) en el centro de documentación de Azure.
1. Configurar el centro de notificaciones para la comunicación con APNS. Para obtener más información, consulte [configurar el centro de notificaciones para APNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns).
1. Configure la aplicación de Xamarin.Forms para usar el nuevo identificador de aplicación y el perfil de aprovisionamiento. Para obtener más información, consulte [configurar el proyecto de iOS en Xamarin Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio) o [configurar el proyecto de iOS en Visual Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio) en el centro de documentación de Azure.

### <a name="android"></a>Android

Se realizarán los siguientes pasos adicionales para que use Firebase Cloud Messaging (FCM) desde un centro de notificaciones de Azure:

1. Registro de FCM. Una clave de API de servidor y un identificador de cliente son automáticamente genera y empaqueta un `google-services.json` archivo que se descarga. Para obtener más información, consulte [habilitar Firebase Cloud Messaging (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm).
1. Configurar el centro de notificaciones para comunicarse con FCM. Para obtener más información, consulte [configurar terminar la vuelta Mobile Apps para enviar solicitudes push mediante FCM](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm).

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Se realizarán los siguientes pasos adicionales para usar el servicio de notificación de Windows (WNS) desde un centro de notificaciones de Azure:

1. Registrarse en el servicio de notificación de Windows (WNS). Para obtener más información, consulte [registrar la aplicación de Windows para las notificaciones de inserción con WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns) en el centro de documentación de Azure.
1. Configurar el centro de notificaciones para la comunicación con WNS. Para obtener más información, consulte [configurar el centro de notificaciones para WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns) en el centro de documentación de Azure.

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>Agregar compatibilidad con las notificaciones Push a la aplicación de Xamarin.Forms

Las secciones siguientes describen la implementación de necesario en cada proyecto específico de plataforma para admitir las notificaciones de inserción.

### <a name="ios"></a>iOS

El proceso para implementar la compatibilidad con las notificaciones push en una aplicación de iOS es como sigue:

1. Registrar con el Apple Push Notification Service (APNS) en el `AppDelegate.FinishedLaunching` método. Para obtener más información, consulte [registrar con el sistema de notificación Push de Apple](#ios_register).
1. Implemente el `AppDelegate.RegisteredForRemoteNotifications` método para controlar la respuesta de registro. Para obtener más información, consulte [controlar la respuesta de registro](#ios_registration_response).
1. Implemente el `AppDelegate.DidReceiveRemoteNotification` método para procesar las notificaciones de inserción entrante. Para obtener más información, consulte [notificaciones de inserción de procesamiento entrante](#ios_process_incoming).

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>Registrar con el servicio de notificaciones Push de Apple

Antes de que una aplicación de iOS puede recibir notificaciones de inserción, debe registrar con el Apple Push Notification Service (APNS), que generará un token de dispositivo único y vuelva a la aplicación. Se invoca el registro en el `FinishedLaunching` invalidar en el `AppDelegate` clase:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    ...
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert, new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ...
}
```

Cuando se registra una aplicación de iOS con Apple Push Notification Service deben especificar los tipos de notificaciones push que le gustaría recibir. El `RegisterUserNotificationSettings` método registra los tipos de notificaciones de la aplicación puede recibir, con el `RegisterForRemoteNotifications` método registrarse para recibir notificaciones push de Apple Push Notification Service.

> [!NOTE]
> No se puede llamar a la `RegisterUserNotificationSettings` método dará lugar a notificaciones de inserción que se recibe la aplicación en modo silencioso.

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>La respuesta de registro de control

La solicitud de registro APNS se produce en segundo plano. Cuando se recibe la respuesta, llamará a iOS la `RegisteredForRemoteNotifications` invalidar en el `AppDelegate` clase:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyAPNS}
    };

    // Register for push with the Azure mobile app
    Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
    push.RegisterAsync(deviceToken, templates);
}
```

Este método crea una plantilla de mensaje de notificación simple como JSON y registra el dispositivo para recibir notificaciones de plantilla desde el centro de notificaciones.

> [!NOTE]
> El `FailedToRegisterForRemoteNotifications` invalidación debe implementarse para controlar situaciones como sin conexión de red. Esto es importante porque los usuarios pueden iniciar la aplicación mientras sin conexión.

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>Notificaciones de inserción de procesamiento entrante

El `DidReceiveRemoteNotification` invalidar en el `AppDelegate` clase se utiliza para procesar las notificaciones de inserción entrante cuando la aplicación se está ejecutando y se invoca cuando se recibe una notificación:

```csharp
public override void DidReceiveRemoteNotification(
    UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
    NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

    string alert = string.Empty;
    if (aps.ContainsKey(new NSString("alert")))
        alert = (aps[new NSString("alert")] as NSString).ToString();

    // Show alert
    if (!string.IsNullOrEmpty(alert))
    {
      var notificationAlert = UIAlertController.Create("Notification", alert, UIAlertControllerStyle.Alert);
      notificationAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Cancel, null));
      UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(notificationAlert, true, null);
    }
}
```

El `userInfo` diccionario contiene el `aps` clave, cuyo valor es el `alert` diccionario con los datos restantes de la notificación. Este diccionario se recupera, con el `string` que se muestra en un cuadro de diálogo de mensaje de notificación.

> [!NOTE]
> Si no se está ejecutando una aplicación cuando llega una notificación de inserción, se iniciará la aplicación, pero la `DidReceiveRemoteNotification` método no procesará la notificación. En su lugar, obtenga la carga de notificación y responder de forma adecuada de la `WillFinishLaunching` o `FinishedLaunching` invalida.

Para obtener más información acerca de Apple Push Notification Service, consulte [notificaciones Push en iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md).

### <a name="android"></a>Android

El proceso para implementar la compatibilidad con las notificaciones push en una aplicación de Android es como sigue:

1. Agregar el [Xamarin.Firebase.Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet del paquete al proyecto de Android y establecer la versión de destino de la aplicación en Android 7.0 o superior.
1. Agregar el `google-services.json` archivo descargado desde la consola Firebase, a la raíz del proyecto Android y establezca su acción de compilación en **GoogleServicesJson**. Para obtener más información, consulte [agregar el archivo de Google Services JSON](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).
1. Regístrese con Firebase Cloud Messaging (FCM) declarando un receptor en el manifiesto de Android de archivos y a implementar el `FirebaseRegistrationService.OnTokenRefresh` método. Para obtener más información, consulte [registrar con Firebase Cloud Messaging](#android_register_fcm).
1. Registrar con el centro de notificaciones de Azure en el `AzureNotificationHubService.RegisterAsync` método. Para obtener más información, consulte [registrar con Azure Notification Hub](#android_register_azure).
1. Implemente el `FirebaseNotificationService.OnMessageReceived` método para procesar las notificaciones de inserción entrante. Para obtener más información, consulte [mostrar el contenido de una notificación de inserción](#android_displaying_notification).

Para obtener más información acerca de Firebase Cloud Messaging, consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) y [notificaciones remotas con Firebase Cloud Messaging](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>Registro en Firebase Cloud Messaging

Antes de que una aplicación de Android puede recibir notificaciones de inserción, se debe registrar con FCM, que generará un token de registro y vuelva a la aplicación. Para obtener más información acerca de los tokens de registro, consulte [registro con FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration).

Esto se logra mediante:

- Declarar un receptor en el manifiesto de Android. Para obtener más información, consulte [declarar el receptor en el manifiesto de Android](#declaring_a_receiver).
- Implementa el servicio de Id. de instancia de Firebase. Para obtener más información, consulte [implementa el servicio de Id. de instancia de Firebase](#implementing-firebase-instance-id-service).

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>Declarar el receptor en el manifiesto de Android

Editar **AndroidManifest.xml** e inserte el siguiente `<receiver>` elementos en el `<application>` elemento:

```xml
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
  <intent-filter>
    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
    <category android:name="${applicationId}" />
  </intent-filter>
</receiver>
```

Este XML realiza las siguientes operaciones:

- Declara una instancia interna `FirebaseInstanceIdInternalReceiver` implementación que se utiliza para iniciar los servicios de forma segura.
- Declara un `FirebaseInstanceIdReceiver` implementación que proporciona un identificador único para cada instancia de la aplicación. También este receptor se autentica y autoriza las acciones.

El `FirebaseInstanceIdReceiver` recibe `FirebaseInstanceId` y `FirebaseMessaging` eventos y los entrega a la clase que se deriva de `FirebasesInstanceIdService`.

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>Implementa el servicio de Id. de instancia de Firebase

Registrar la aplicación con FCM se logra derivando una clase de la `FirebaseInstanceIdService` clase. Esta clase es responsable de generar tokens de seguridad que autorizar a la aplicación cliente para tener acceso a FCM. En la aplicación de ejemplo la `FirebaseRegistrationService` clase se deriva de la `FirebaseInstanceIdService` clase y se muestra en el ejemplo de código siguiente:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    const string TAG = "FirebaseRegistrationService";

    public override void OnTokenRefresh()
    {
        var refreshedToken = FirebaseInstanceId.Instance.Token;
        Log.Debug(TAG, "Refreshed token: " + refreshedToken);
        SendRegistrationTokenToAzureNotificationHub(refreshedToken);
    }

    void SendRegistrationTokenToAzureNotificationHub(string token)
    {
        // Update notification hub registration
        Task.Run(async () =>
        {
            await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
        });
    }
}
```

El `OnTokenRefresh` método se invoca cuando la aplicación recibe un token de registro de FCM. El método recupera el símbolo (token) de la `FirebaseInstanceId.Instance.Token` propiedad, que se actualiza de forma asincrónica FCM. El `OnTokenRefresh` método se invoca con poca frecuencia, porque el token solo se actualiza cuando la aplicación se instala o desinstala, cuando el usuario elimina datos de la aplicación, la aplicación borra el identificador de instancia, o cuando ha sido la seguridad del token en peligro. Además, el servicio de Id. de instancia de FCM solicitará que la aplicación actualice su token periódicamente, normalmente cada 6 meses.

El `OnTokenRefresh` también invoca el método la `SendRegistrationTokenToAzureNotificationHub` método, que se usa para asociar el token de registro del usuario con el centro de notificaciones de Azure.

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>Registro en el centro de notificaciones de Azure

El `AzureNotificationHubService` clase proporciona el `RegisterAsync` método, que asocia el token de registro del usuario con el centro de notificaciones de Azure. El siguiente ejemplo de código muestra la `RegisterAsync` método, que es invocado por el `FirebaseRegistrationService` clase cuando se cambia el token de registro del usuario:

```csharp
public class AzureNotificationHubService
{
    const string TAG = "AzureNotificationHubService";

    public static async Task RegisterAsync(Push push, string token)
    {
        try
        {
            const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBody}
            };

            await push.RegisterAsync(token, templates);
            Log.Info("Push Installation Id: ", push.InstallationId.ToString());
        }
        catch (Exception ex)
        {
            Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
        }
    }
}
```

Este método crea una plantilla de mensaje de notificación simple como JSON y registros para recibir notificaciones de plantilla desde el centro de notificaciones con el token de registro de Firebase. Esto garantiza que las notificaciones enviadas desde el centro de notificaciones de Azure destinan al dispositivo representado por el token de registro.

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>Mostrar el contenido de una notificación de inserción

Mostrar el contenido de una notificación de inserción se logra derivando una clase de la `FirebaseMessagingService` clase. Esta clase incluye el reemplazable `OnMessageReceived` proporciona el método, que se invoca cuando la aplicación recibe una notificación de FCM, que se está ejecutando la aplicación en primer plano. En la aplicación de ejemplo la `FirebaseNotificationService` clase se deriva de la `FirebaseMessagingService` clase y se muestra en el ejemplo de código siguiente:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseNotificationService : FirebaseMessagingService
{
    const string TAG = "FirebaseNotificationService";

    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);

        // Pull message body out of the template
        var messageBody = message.Data["message"];
        if (string.IsNullOrWhiteSpace(messageBody))
            return;

        Log.Debug(TAG, "Notification message body: " + messageBody);
        SendNotification(messageBody);
    }

    void SendNotification(string messageBody)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
            .SetContentTitle("New Todo Item")
            .SetContentText(messageBody)
            .SetContentIntent(pendingIntent)
            .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
            .SetAutoCancel(true);

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }
}
```

Cuando la aplicación recibe una notificación de FCM, el `OnMessageReceived` método extrae el contenido del mensaje y llama a la `SendNotification` método. Este método convierte el contenido del mensaje en una notificación local que se inicia mientras se está ejecutando la aplicación, con la notificación que aparece en el área de notificación.

##### <a name="handling-notification-intents"></a>Notificación de control de intenciones

Cuando un usuario pulsa una notificación, cualquier dato que acompaña el mensaje de notificación esté disponible en el `Intent` adicionales. Estos datos se pueden extraer con el código siguiente:

```csharp
if (Intent.Extras != null)
{
  foreach (var key in Intent.Extras.KeySet())
  {
    var value = Intent.Extras.GetString(key);
    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
  }
}
```

Iniciador de la aplicación `Intent` se desencadena cuando el usuario pulsa su mensaje de notificación, por lo que este código se registrará ningún dato adjunto en el `Intent` en la ventana de salida.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Antes de una plataforma Universal de Windows (UWP) la aplicación puede recibir notificaciones de inserción que debe registrar con el servicio de notificación de Windows (WNS), que devolverá un canal de notificación. El registro se invoca mediante el `InitNotificationsAsync` método en el `App` clase:

```csharp
private async Task InitNotificationsAsync()
{
    var channel = await PushNotificationChannelManager
          .CreatePushNotificationChannelForApplicationAsync();

    const string templateBodyWNS =
        "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

    JObject headers = new JObject();
    headers["X-WNS-Type"] = "wns/toast";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyWNS},
        {"headers", headers} // Needed for WNS.
    };

    await TodoItemManager.DefaultManager.CurrentClient.GetPush()
          .RegisterAsync(channel.Uri, templates);
}
```

Este método obtiene el canal de notificación de inserción, crea una plantilla de mensaje de notificación como JSON y registra el dispositivo para recibir notificaciones de plantilla desde el centro de notificaciones.

El `InitNotificationsAsync` método se invoca desde el `OnLaunched` invalidar en el `App` clase:

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

Esto garantiza que el registro de notificaciones push se crea o se actualizan cada vez que se inicia la aplicación, por lo tanto, lo que garantiza que el canal de inserción WNS siempre está activo.

Cuando se recibe una notificación de inserción automáticamente se mostrará como un *toast* : una ventana no modal que contiene el mensaje.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar Azure Notification Hubs para enviar notificaciones push desde una instancia de Azure Mobile Apps a una aplicación de Xamarin.Forms. Azure Notification Hubs proporciona una infraestructura de inserción escalable para enviar notificaciones de inserción móviles desde cualquier back-end a cualquier plataforma móvil, mientras se elimina la complejidad de un back-end de tener que comunicarse con sistemas de notificación de plataforma diferente.


## <a name="related-links"></a>Vínculos relacionados

- [Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Azure Notification Hubs](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [Agregar notificaciones push a la aplicación de Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [Notificaciones Push en iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Mensajería en la nube de Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
