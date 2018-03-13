---
title: "Enviar notificaciones de inserción desde aplicaciones móviles de Azure"
description: "Centros de notificaciones de Azure proporcionan una infraestructura escalable de inserción para enviar notificaciones de inserción móviles desde cualquier back-end para cualquier plataforma móvil, mientras se elimina la complejidad de un back-end tener para comunicarse con sistemas de notificación de plataforma diferente. En este artículo se explica cómo usar centros de notificaciones de Azure para enviar notificaciones de inserción de una instancia de aplicaciones móviles de Azure a una aplicación de Xamarin.Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: f0f767179a9280d7a6c6d7ce8125696d5e664cba
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>Enviar notificaciones de inserción desde aplicaciones móviles de Azure

_Centros de notificaciones de Azure proporcionan una infraestructura escalable de inserción para enviar notificaciones de inserción móviles desde cualquier back-end para cualquier plataforma móvil, mientras se elimina la complejidad de un back-end tener para comunicarse con sistemas de notificación de plataforma diferente. En este artículo se explica cómo usar centros de notificaciones de Azure para enviar notificaciones de inserción de una instancia de aplicaciones móviles de Azure a una aplicación de Xamarin.Forms._

> [!VIDEO https://youtube.com/embed/le2lDY22xwM]

**Azure de inserción Xamarin.Forms y el centro de notificaciones por [Universidad de Xamarin](https://university.xamarin.com/)**

Una notificación de inserción se utiliza para proporcionar información, como un mensaje desde un sistema back-end a una aplicación en un dispositivo móvil para aumentar el uso y la interacción de la aplicación. La notificación puede enviarse en cualquier momento, incluso cuando el usuario no esté usando activamente la aplicación de destino.

Sistemas de back-end envían notificaciones de inserción a dispositivos móviles a través de los sistemas de notificación de plataforma (PNS), tal como se muestra en el diagrama siguiente:

[![](azure-images/pns.png "Sistemas de notificación de plataforma")](azure-images/pns-large.png#lightbox "sistemas de notificación de plataforma")

Para enviar una notificación de inserción, el sistema back-end se pone en contacto con el PNS específicos de la plataforma para enviar una notificación a una instancia de la aplicación cliente. Esto aumenta significativamente la complejidad de back-end cuando son necesarias, las notificaciones de inserción de multiplataforma ya que el back-end debe utilizar cada protocolo y API de PNS de específico de la plataforma.

Centros de notificaciones de Azure eliminar esta complejidad al resumiendo los detalles de los sistemas de notificación de plataforma diferente, lo que permite una notificación de multiplataforma para enviarse con una sola llamada API, como se muestra en el diagrama siguiente:

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png#lightbox)

Para enviar una notificación de inserción, el código de los contactos solo el Azure centro de notificaciones, que a su vez se comunica con los sistemas de notificación de plataforma diferente, por lo tanto, reducir la complejidad de back-end del sistema de back-end que las notificaciones de inserción de envíos.

Aplicaciones móviles de Azure tienen compatibilidad integrada para las notificaciones de inserción con centros de notificaciones. El proceso para enviar una notificación de inserción de una instancia de aplicaciones móviles de Azure a una aplicación de Xamarin.Forms es como sigue:

1. La aplicación de Xamarin.Forms se registra con el PNS, que devuelve un identificador.
1. La instancia de aplicaciones móviles de Azure envía una notificación a su centro de notificaciones de Azure, que especifica el identificador del dispositivo como destino.
1. El centro de notificaciones de Azure, se envía la notificación con el PNS adecuado para el dispositivo.
1. El PNS envía la notificación al dispositivo especificado.
1. La aplicación de Xamarin.Forms procesa la notificación y lo muestra.

La aplicación de ejemplo muestra una aplicación de lista de tareas cuyos datos se almacenan en una instancia de aplicaciones móviles de Azure. Cada vez que se agrega un nuevo elemento a la instancia de aplicaciones móviles de Azure, se envía una notificación de inserción a la aplicación de Xamarin.Forms. Las capturas de pantalla siguientes muestran cada plataforma de mostrar la notificación de inserción recibido:

[![](azure-images/screenshots.png "Ejemplo de aplicación que recibe una notificación de inserción")](azure-images/screenshots-large.png#lightbox "recibir una notificación de inserción de aplicación de ejemplo")

Para obtener más información acerca de los centros de notificaciones de Azure, consulte [centros de notificaciones de Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/) y [agregar notificaciones de inserción a la aplicación de Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/).

## <a name="azure-and-platform-notification-system-setup"></a>Azure y el programa de instalación del sistema de notificación de plataforma

El proceso para integrar un centro de notificaciones de Azure en una instancia de aplicaciones móviles de Azure es como sigue:

1. Cree una instancia de aplicaciones móviles de Azure. Para obtener más información, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Configurar un centro de notificaciones. Para obtener más información, consulte [configurar un centro de notificaciones](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#create-hub).
1. Actualizar la instancia de aplicaciones móviles de Azure para enviar notificaciones de inserción. Para obtener más información, consulte [actualizar el proyecto de servidor para enviar notificaciones de inserción](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications).
1. Registrar con cada PNS.
1. Configurar el centro de notificaciones para comunicarse con cada PNS.

Las secciones siguientes proporcionan instrucciones de instalación adicionales para cada plataforma.

### <a name="ios"></a>iOS

Los siguientes pasos adicionales deben llevarse a cabo para usar el servicio de notificación de inserción (APNS) de Apple desde un centro de notificaciones de Azure:

1. Generar un certificado de firma de solicitud para el certificado de inserción con la herramienta de acceso a llaves. Para obtener más información, consulte [generar el archivo de solicitud de firma de certificado para el certificado de inserción](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate) en el centro de documentación de Azure.
1. Registrar la aplicación de Xamarin.Forms para la compatibilidad con notificaciones de inserción en el Centro para desarrolladores de Apple. Para obtener más información, consulte [registrar la aplicación para notificaciones de inserción](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications) en el centro de documentación de Azure.
1. Crear un inserción notificaciones habilitado perfil de aprovisionamiento para la aplicación de Xamarin.Forms en el Centro para desarrolladores de Apple. Para obtener más información, consulte [crear un perfil de aprovisionamiento de la aplicación](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app) en el centro de documentación de Azure.
1. Configurar el centro de notificaciones para comunicarse con APNS. Para obtener más información, consulte [configurar el centro de notificaciones para APNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns).
1. Configurar la aplicación de Xamarin.Forms para utilizar el nuevo identificador de la aplicación y el perfil de aprovisionamiento. Para obtener más información, consulte [configurar el proyecto de iOS en Xamarin Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio) o [configurar el proyecto de iOS en Visual Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio) en el centro de documentación de Azure.

### <a name="android"></a>Android

Los siguientes pasos adicionales deben llevarse a cabo para utilizar mensajería de nube de Firebase (FCM) de un centro de notificaciones de Azure:

1. Registrarse para FCM. Una clave de API de servidor y un identificador de cliente son automáticamente genera y empaqueta un `google-services.json` archivo que se descarga. Para obtener más información, consulte [habilitar Firebase en la nube de mensajería (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm).
1. Configurar el centro de notificaciones para comunicarse con FCM. Para obtener más información, consulte [configurar las aplicaciones móviles volver Finalizar para enviar solicitudes de inserción mediante FCM](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm).

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Los siguientes pasos adicionales deben llevarse a cabo para usar el servicio de notificación de Windows (WNS) de un centro de notificaciones de Azure:

1. Registrarse en el servicio de notificación de Windows (WNS). Para obtener más información, consulte [registrar la aplicación de Windows para las notificaciones de inserción con WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns) en el centro de documentación de Azure.
1. Configurar el centro de notificaciones para comunicarse con WNS. Para obtener más información, consulte [el centro de notificaciones de configuración de WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns) en el centro de documentación de Azure.

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>Agregar compatibilidad de notificación de inserción a la aplicación de Xamarin.Forms

Las secciones siguientes tratan la implementación necesaria en cada proyecto específico de plataforma para admitir las notificaciones de inserción.

### <a name="ios"></a>iOS

El proceso para implementar la compatibilidad de la notificación de inserción en una aplicación de iOS es como sigue:

1. Registrar con servicio de notificación de inserción de Apple (APNS) en el `AppDelegate.FinishedLaunching` método. Para obtener más información, consulte [registrar con el sistema de notificación de inserción de Apple](#ios_register).
1. Implemente el `AppDelegate.RegisteredForRemoteNotifications` método para controlar la respuesta de registro. Para obtener más información, consulte [controlar la respuesta de registro](#ios_registration_response).
1. Implemente el `AppDelegate.DidReceiveRemoteNotification` método para procesar las notificaciones de inserción entrante. Para obtener más información, consulte [procesar las notificaciones de inserción entrante](#ios_process_incoming).

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>Registrando con el servicio de notificación de inserción de Apple

Antes de que una aplicación de iOS puede recibir notificaciones de inserción, debe registrar con la inserción de notificación de servicio Apple (APNS), que generará un token de dispositivo único y volver a la aplicación. Se invoca el registro en el `FinishedLaunching` invalidar en la `AppDelegate` clase:

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

Cuando una aplicación de iOS se registra con APNS debe especificar los tipos de notificaciones de inserción que le gustaría recibir. El `RegisterUserNotificationSettings` método registra los tipos de notificaciones de la aplicación puede recibir, con el `RegisterForRemoteNotifications` método registrar para recibir notificaciones de inserción de APNS.

> [!NOTE]
> Llamar al método el `RegisterUserNotificationSettings` método dará lugar a notificaciones de inserción en modo silencioso recibida por la aplicación.

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>Controlar la respuesta de registro

La solicitud de registro APNS se produce en segundo plano. Cuando se recibe la respuesta, iOS llamará el `RegisteredForRemoteNotifications` invalidar en la `AppDelegate` clase:

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
> El `FailedToRegisterForRemoteNotifications` invalidación debe implementarse para controlar las situaciones como no hay ninguna conexión de red. Esto es importante porque los usuarios pueden iniciar la aplicación mientras sin conexión.

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>Notificaciones de inserción de procesamiento entrante

El `DidReceiveRemoteNotification` invalidar en la `AppDelegate` clase se utiliza para procesar las notificaciones de inserción entrante cuando la aplicación se ejecuta y se invoca cuando se recibe una notificación:

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

El `userInfo` diccionario contiene el `aps` clave, cuyo valor es el `alert` diccionario con los datos restantes de la notificación. Este diccionario se recupera, con el `string` que se muestran en un cuadro de diálogo de mensaje de notificación.

> [!NOTE]
> Si no se está ejecutando una aplicación cuando llega una notificación de inserción, se iniciará la aplicación, pero la `DidReceiveRemoteNotification` método no procesará la notificación. En su lugar, obtenga la carga de notificación y responder de forma adecuada de la `WillFinishLaunching` o `FinishedLaunching` invalida.

Para obtener más información acerca de APN, consulte [notificaciones Push en iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md).

### <a name="android"></a>Android

El proceso para implementar la compatibilidad de la notificación de inserción en una aplicación de Android es como sigue:

1. Agregar el [Xamarin.Firebase.Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet del paquete para el proyecto de Android y establecer la versión de destino de la aplicación en Android 7.0 o superior.
1. Agregar el `google-services.json` archivo descargado desde la consola de Firebase, en la raíz del proyecto de Android y establezca su acción de compilación en **GoogleServicesJson**. Para obtener más información, consulte [agregar el archivo JSON de servicios de Google](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).
1. Registrar con Firebase en la nube de mensajería (FCM) mediante la declaración de un receptor en el manifiesto de Android de archivos y la implementación por el `FirebaseRegistrationService.OnTokenRefresh` método. Para obtener más información, consulte [registrar con la mensajería de nube de Firebase](#android_register_fcm).
1. Registrar con el centro de notificaciones de Azure en la `AzureNotificationHubService.RegisterAsync` método. Para obtener más información, consulte [registrar con el centro de notificaciones de Azure](#android_register_azure).
1. Implemente el `FirebaseNotificationService.OnMessageReceived` método para procesar las notificaciones de inserción entrante. Para obtener más información, consulte [mostrar el contenido de una notificación de inserción](#android_displaying_notification).

Para obtener más información acerca de la mensajería de nube de Firebase, consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) y [notificaciones remoto con la mensajería de nube de Firebase](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrar con Firebase de mensajería en la nube

Antes de que una aplicación Android puede recibir notificaciones de inserción, debe registrar con FCM, lo que generará un token de registro y volver a la aplicación. Para obtener más información acerca de los tokens de registro, consulte [registro con FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration).

Esto se consigue mediante:

- La declaración de un receptor en el manifiesto de Android. Para obtener más información, consulte [declarar el receptor en el manifiesto de Android](#declaring_a_receiver).
- Implementar el servicio de Id. de instancia de Firebase. Para obtener más información, consulte [implementa el servicio de Id. de instancia de Firebase](#implementing-firebase-instance-id-service).

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>Declarar el receptor en el manifiesto de Android

Editar **AndroidManifest.xml** e inserte el siguiente `<receiver>` elementos en la `<application>` elemento:

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

Este código XML realiza las siguientes operaciones:

- Declara un interno `FirebaseInstanceIdInternalReceiver` implementación que se usa para iniciar los servicios de forma segura.
- Declara un `FirebaseInstanceIdReceiver` implementación que proporciona un identificador único para cada instancia de la aplicación. Este receptor también autentica y autoriza las acciones.

El `FirebaseInstanceIdReceiver` recibe `FirebaseInstanceId` y `FirebaseMessaging` eventos y los entrega a la clase que se deriva de `FirebasesInstanceIdService`.

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>Implementar el servicio de Id. de instancia de Firebase

Registrar la aplicación con FCM se logra, derive una clase de la `FirebaseInstanceIdService` clase. Esta clase es responsable de generar tokens de seguridad que autorizan a la aplicación de cliente para tener acceso a FCM. En la aplicación de ejemplo del `FirebaseRegistrationService` clase se deriva de la `FirebaseInstanceIdService` clase y se muestra en el ejemplo de código siguiente:

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

El `OnTokenRefresh` método se invoca cuando la aplicación recibe un token de registro de FCM. El método recupera el símbolo (token) de la `FirebaseInstanceId.Instance.Token` propiedad, que se actualiza de forma asincrónica mediante FCM. El `OnTokenRefresh` método se invoca con poca frecuencia, porque el token solo se actualiza cuando la aplicación se instala o desinstala, cuando el usuario elimina los datos de aplicación, cuando la aplicación borra el identificador de instancia, o cuando la seguridad del token se ha pone en peligro. Además, el servicio de Id. de instancia FCM solicitará que la aplicación actualiza su token periódicamente, normalmente cada 6 meses.

El `OnTokenRefresh` método también invoca el `SendRegistrationTokenToAzureNotificationHub` método, que se usa para asociar el token del usuario registro con el centro de notificaciones de Azure.

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>Registrar con el centro de notificaciones de Azure

El `AzureNotificationHubService` clase proporciona el `RegisterAsync` método, que asocia el token del usuario registro con el centro de notificaciones de Azure. El siguiente ejemplo de código muestra la `RegisterAsync` método, que se invoca mediante el `FirebaseRegistrationService` clase cuando se cambia el token del usuario registro:

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

Este método crea una plantilla de mensaje de notificación simple como JSON y registros para recibir notificaciones de plantilla desde el centro de notificaciones con el token de registro Firebase. Esto garantiza que las notificaciones enviadas desde el centro de notificaciones de Azure se aplicará el dispositivo representado por el token de registro.

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>Mostrar el contenido de una notificación de inserción

Mostrar el contenido de una notificación de inserción se logra, derive una clase de la `FirebaseMessagingService` clase. Esta clase incluye el reemplazable `OnMessageReceived` proporciona el método, que se invoca cuando la aplicación recibe una notificación de FCM, que se ejecuta la aplicación en primer plano. En la aplicación de ejemplo del `FirebaseNotificationService` clase se deriva de la `FirebaseMessagingService` clase y se muestra en el ejemplo de código siguiente:

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

Cuando la aplicación recibe una notificación de FCM, el `OnMessageReceived` método extrae el contenido del mensaje y llama a la `SendNotification` método. Este método convierte el contenido del mensaje en una notificación local que se inicia mientras se ejecuta la aplicación, con la notificación que aparecen en el área de notificación.

##### <a name="handling-notification-intents"></a>Intentos de notificación de control

Cuando un usuario puntee una notificación, cualquier dato que acompaña el mensaje de notificación debe ponerse a disposición de los `Intent` extras. Estos datos se pueden extraer con el código siguiente:

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

Selector de la aplicación `Intent` se desencadena cuando el usuario puntea el mensaje de notificación, por lo que este código registrará los datos adjuntos el `Intent` en la ventana de salida.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Antes de una plataforma Universal de Windows (UWP) la aplicación puede recibir notificaciones de inserción que debe registrar con el servicio de notificación de Windows (WNS), lo que devolverá un canal de notificación. Registro es invocado por el `InitNotificationsAsync` método en la `App` clase:

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

El `InitNotificationsAsync` método se invoca desde la `OnLaunched` invalidar en la `App` clase:

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

Esto garantiza que el registro de notificaciones de inserción se crea o se actualiza cada vez que se inicia la aplicación, por lo tanto, asegurándose de que el canal de inserción WNS siempre está activo.

Cuando se recibe una notificación de inserción automáticamente se mostrará como un *toast* : una ventana no modal que contiene el mensaje.

## <a name="summary"></a>Resumen

Este artículo muestra cómo usar centros de notificaciones de Azure para enviar notificaciones de inserción de una instancia de aplicaciones móviles de Azure a una aplicación de Xamarin.Forms. Centros de notificaciones de Azure proporcionan una infraestructura escalable de inserción para enviar notificaciones de inserción móviles desde cualquier back-end para cualquier plataforma móvil, mientras se elimina la complejidad de un back-end tener para comunicarse con sistemas de notificación de plataforma diferente.


## <a name="related-links"></a>Vínculos relacionados

- [Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Centros de notificaciones de Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [Agregar notificaciones de inserción a la aplicación de Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [Notificaciones de inserción en iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Mensajería en la nube de Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [Cliente móvil Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
