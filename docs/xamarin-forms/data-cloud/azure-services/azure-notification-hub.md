---
title: Enviar y recibir notificaciones push con Azure Notification Hubs y Xamarin.Forms
description: En este artículo se explica cómo usar Azure Notification Hubs para enviar notificaciones de inserción multiplataforma para aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/23/2019
ms.openlocfilehash: 474398922bf00e3a430166d8b2e073d200e6ed6e
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659332"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>Enviar y recibir notificaciones push con Azure Notification Hubs y Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png)descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureNotificationHub)

Insertar información de entrega de notificaciones desde un sistema back-end en una aplicación móvil. Apple, Google y otras plataformas tienen su propio servicio de notificación de inserción (PNS). Azure Notification Hubs le permiten centralizar las notificaciones entre plataformas para que la aplicación de back-end pueda comunicarse con un único concentrador, que se encargará de distribuir las notificaciones para cada PNS específicos de la plataforma.

Integrar Azure Notification Hubs en aplicaciones móviles siguiendo estos pasos:

1. [Configurar los servicios de notificaciones Push y Azure Notification Hub](#set-up-push-notification-services-and-azure-notification-hub).
1. [Descubra cómo utilizar etiquetas y plantillas](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Crear una aplicación de Xamarin.Forms multiplataforma](#xamarinforms-application-functionality).
1. [Configurar el proyecto nativo de Android para notificaciones de inserción](#configure-the-android-application-for-notifications).
1. [Configurar el proyecto de iOS nativo para las notificaciones de inserción](#configure-ios-for-notifications).
1. [Probar las notificaciones mediante el centro de notificaciones de Azure](#test-notifications-in-the-azure-portal).
1. [Crear una aplicación de back-end para enviar notificaciones](#create-a-notification-dispatcher).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurar los servicios de notificaciones Push y centro de notificaciones de Azure

La integración de Azure Notification Hubs con una aplicación móvil Xamarin.Forms es similar a la integración de Azure Notification Hubs con una aplicación nativa de Xamarin. Configurar un **aplicación FCM** siguiendo los pasos la consola de Firebase [notificaciones Push en Xamarin.Android mediante Azure Notification Hubs](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Complete los pasos siguientes con el tutorial de Xamarin.Android:

1. Definir un nombre de paquete de Android, como `com.xamarin.notifysample`, que se usa en el ejemplo.
1. Descargar **google-services.json** desde la consola de Firebase. Este archivo se agregará a la aplicación Android en un paso posterior.
1. Cree una instancia de Azure Notification Hub y asígnele un nombre. Este artículo y ejemplo, utilice `xdocsnotificationhub` como nombre del centro.
1. Copie el FCM **clave de servidor** y guárdelo como el **clave de API** en **Google (GCM o FCM)** en el centro de notificaciones de Azure.

Captura de pantalla siguiente muestra la configuración de la plataforma de Google en el centro de notificaciones de Azure:

![Captura de pantalla de la configuración de Azure Notification Hub Google](azure-notification-hub-images/fcm-notification-hub-config.png "configuración de Azure Notification Hub Google")

Necesitará un equipo macOS para completar la configuración para dispositivos iOS. Configuración de APNS siguiendo los pasos de la inicial en [notificaciones de inserción a Xamarin.iOS mediante Azure Notification Hubs](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Complete los pasos siguientes con el tutorial de Xamarin.iOS:

1. Defina un identificador de lote de iOS. Este artículo y ejemplo, utilice `com.xamarin.notifysample` como el identificador de paquete.
1. Cree un archivo de solicitud de firma de certificado (CSR) y usarlo para generar un certificado de notificaciones de inserción.
1. Cargue el certificado de notificaciones de inserción en **Apple (APN)** en el centro de notificaciones de Azure.

Captura de pantalla siguiente muestra la configuración de la plataforma de Apple en el centro de notificaciones de Azure:

![Captura de pantalla de la configuración de Azure Notification Hub Apple](azure-notification-hub-images/apns-notification-hub-config.png "Azure Notification Hub Apple Configurator")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Registrar las etiquetas y plantillas con el centro de notificaciones de Azure

Centro de notificaciones de Azure requiere que las aplicaciones móviles para registrarse en el centro, definir plantillas y suscribirse a las etiquetas. Registro vincula un identificador PNS específico de plataforma con un identificador en el centro de notificaciones de Azure. Para obtener más información acerca de los registros, vea [administración de registros](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

Las plantillas permiten a los dispositivos especificar plantillas de mensajes con parámetros. Por dispositivo, por la etiqueta a la que se pueden personalizar los mensajes entrantes. Para más información acerca de las plantillas, consulte [plantillas](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

Las etiquetas se pueden usar para suscribirse a categorías de mensaje como noticias, deportes y previsiones meteorológicas. Por motivos de simplicidad, la aplicación de ejemplo define una plantilla predeterminada con un solo parámetro llamado `messageParam` y llama una sola etiqueta `default`. En sistemas más complejos, las etiquetas específicas de usuario pueden utilizarse para un usuario de mensajes entre los dispositivos para las notificaciones personalizadas. Para más información acerca de las etiquetas, consulte [expresiones de etiqueta y enrutamiento](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Para recibir correctamente los mensajes, cada aplicación nativa debe realizar estos pasos:

1. Obtener un token o identificador de PNS de la plataforma de PNS.
1. Registrar el controlador de PNS con el centro de notificaciones de Azure.
1. Especificar una plantilla que contiene los mismos parámetros que los mensajes salientes.
1. Suscribirse a la etiqueta a la que apunta a los mensajes salientes.

Estos pasos se describen con más detalle para cada plataforma en la [configurar la aplicación Android para las notificaciones](#configure-the-android-application-for-notifications) y [para las notificaciones de dispositivos IOS](#configure-ios-for-notifications) secciones.

## <a name="xamarinforms-application-functionality"></a>Funcionalidad de la aplicación de Xamarin.Forms

La aplicación de Xamarin.Forms de ejemplo muestra una lista de mensajes de notificación de inserción. Esto se logra con la `AddMessage` método, que agrega el mensaje de notificación de inserción especificado a la interfaz de usuario. Este método también impide que los mensajes duplicados se agreguen a la interfaz de usuario y se ejecuta en el subproceso principal se puede llamar desde cualquier subproceso. En el código siguiente se muestra el método `AddMessage`:

```csharp
public void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (messageDisplay.Children.OfType<Label>().Where(c => c.Text == message).Any())
        {
            // Do nothing, an identical message already exists
        }
        else
        {
            Label label = new Label()
            {
                Text = message,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.Start
            };
            messageDisplay.Children.Add(label);
        }
    });
}
```

La aplicación de ejemplo contiene un **AppConstants.cs** archivo, que define propiedades utilizadas por los proyectos de plataforma. Este archivo debe personalizarse con valores desde el centro de notificaciones de Azure. El siguiente código muestra la **AppConstants.cs** archivo:

```csharp
public static class AppConstants
{
    public static string NotificationChannelName { get; set; } = "XamarinNotifyChannel";
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string ListenConnectionString { get; set; } = "< Insert your DefaultListenSharedAccessSignature >";
    public static string DebugTag { get; set; } = "XamarinNotify";
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string FCMTemplateBody { get; set; } = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    public static string APNTemplateBody { get; set; } = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
}
```

Personalizar los siguientes valores en `AppConstants` para conectar la aplicación de ejemplo en el centro de notificaciones de Azure:

* `NotificationHubName`: Utilice el nombre del centro de notificaciones de Azure que creó en el portal de Azure.
* `ListenConnectionString`: Este valor se encuentra en el centro de notificaciones de Azure en **las directivas de acceso**.

Captura de pantalla siguiente se muestra dónde se encuentran estos valores en el portal de Azure:

![Captura de pantalla de la directiva de acceso de Azure Notification Hub](azure-notification-hub-images/notification-hub-access-policy.png "directiva de acceso del centro de notificaciones de Azure")

## <a name="configure-the-android-application-for-notifications"></a>Configurar la aplicación para notificaciones de Android

Complete los pasos siguientes para configurar la aplicación Android para recibir y procesar las notificaciones:

1. Configurar Android **nombre del paquete** para que coincida con el nombre del paquete en la consola de Firebase.
1. Instale los siguientes paquetes de NuGet para interactuar con Google Play, Firebase y Azure Notification Hubs:
    1. Xamarin.GooglePlayServices.Base.
    1. Xamarin.Firebase.Messaging.
    1. Xamarin.Azure.NotificationHubs.Android.
1. Copia el `google-services.json` archivo que descargó durante la instalación FCM al proyecto y establezca la acción de compilación en `GoogleServicesJson`.
1. [Configurar AndroidManifest.xml para comunicarse con Firebase](#configure-android-manifest).
1. [Registrar la aplicación con Firebase y centro de notificaciones de Azure mediante un `FirebaseInstanceIdService` ](#register-using-a-custom-firebaseinstanceidservice).
1. [Procesar mensajes con un `FirebaseMessagingService` ](#process-messages-with-a-firebasemessagingservice).
1. [Incorporación de notificaciones entrantes a la interfaz de usuario de Xamarin.Forms](#add-incoming-notifications-to-the-xamarinforms-ui).

> [!NOTE]
> El **GoogleServicesJson** acción de compilación es parte de la **Xamarin.GooglePlayServices.Base** paquete NuGet. 2019 de Visual Studio establece las acciones de compilación disponibles durante el inicio. Si no ve **GoogleServicesJson** como una acción de compilación, reinicie Visual Studio de 2019 después de instalar los paquetes de NuGet.

### <a name="configure-android-manifest"></a>Configurar el manifiesto de Android

El `receiver` elementos dentro de la `application` elemento permite que la aplicación se comunique con Firebase. El `uses-permission` elementos que la aplicación pueda controlar los mensajes y registrar con Azure Notification Hub. La completa **AndroidManifest.xml** debe ser similar al ejemplo siguiente:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="YOUR_PACKAGE_NAME" android:installLocation="auto">
  <uses-sdk android:minSdkVersion="21" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
  <application android:label="Notification Hub Sample">
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
      <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
      </intent-filter>
    </receiver>
  </application>
</manifest>
```

### <a name="register-using-a-custom-firebaseinstanceidservice"></a>Registrarse con un FirebaseInstanceIdService personalizado

Firebase emite tokens que identifican de forma única un dispositivo en el PNS. Los tokens tienen una duración larga, pero se actualizan ocasionalmente. Cuando un token se emitió o actualizó, la aplicación debe registrar su nuevo token con el centro de notificaciones de Azure. El registro se controla mediante una instancia de una clase que derive de `FirebaseInstanceIdService`.

En la aplicación de ejemplo `FirebaseRegistrationService` clase hereda de `FirebaseInstanceIdService`. Esta clase tiene un `IntentFilter` que incluye `com.google.firebase.INSTANCE_ID_EVENT`, lo que permite al sistema de operativo Android que llaman automáticamente a `OnTokenRefresh` cuando se emite un token por Firebase.

El código siguiente muestra personalizado `FirebaseInstanceIdService` desde la aplicación de ejemplo:

```csharp
[Service]
[IntentFilter(new [] { "com.google.firebase.INSTANCE_ID_EVENT"})]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    public override void OnTokenRefresh()
    {
        string token = FirebaseInstanceId.Instance.Token;

        // NOTE: logging the token is not recommended in production but during
        // development it is useful to test messages directly from Firebase
        Log.Info(AppConstants.DebugTag, $"Token received: {token}");

        SendRegistrationToServer(token);
    }

    void SendRegistrationToServer(string token)
    {
        try
        {
            NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

            // register device with Azure Notification Hub using the token from FCM
            Registration reg = hub.Register(token, AppConstants.SubscriptionTags);

            // subscribe to the SubscriptionTags list with a simple template.
            string pnsHandle = reg.PNSHandle;
            var cats = string.Join(", ", reg.Tags);
            var temp = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
        }
        catch (Exception e)
        {
            Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
        }
    }
}
```

El `SendRegistrationToServer` método en el `FirebaseRegistrationClass` registra el dispositivo con el centro de notificaciones de Azure y se suscribe a las etiquetas con una plantilla. La aplicación de ejemplo define una etiqueta única denominada `default` y llamar una plantilla con un solo parámetro `messageParam` en el **AppConstants.cs** archivo. Para obtener más información sobre el registro, las etiquetas y plantillas, consulte [registrar plantillas y etiquetas con el centro de notificaciones de Azure](#register-templates-and-tags-with-the-azure-notification-hub)

### <a name="process-messages-with-a-firebasemessagingservice"></a>Procesar los mensajes con un FirebaseMessagingService

Los mensajes entrantes se enrutan a un `FirebaseMessagingService` instancia, donde se puede convertir en una notificación local. El proyecto de Android en la aplicación de ejemplo contiene una clase denominada `FirebaseService` que hereda de `FirebaseMessagingService`. Esta clase tiene un `IntentFilter` que incluye `com.google.firebase.MESSAGING_EVENT`, lo que permite al sistema de operativo Android que llaman automáticamente a `OnMessageReceived` cuando se recibe un mensaje de notificación de inserción.

El ejemplo siguiente se muestra la `FirebaseService` desde la aplicación de ejemplo:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    public override void OnMessageReceived(RemoteMessage message)
    {
        base.OnMessageReceived(message);
        string messageBody = string.Empty;

        if (message.GetNotification() != null)
        {
            messageBody = message.GetNotification().Body;
        }

        // NOTE: test messages sent via the Azure portal will be received here
        else
        {
            messageBody = message.Data.Values.First();
        }

        // convert the incoming message to a local notification
        SendLocalNotification(messageBody);

        // send the incoming message directly to the MainPage
        SendMessageToMainPage(messageBody);
    }

    void SendLocalNotification(string body)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        intent.PutExtra("message", body);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetContentTitle("XamarinNotify Message")
            .SetSmallIcon(Resource.Drawable.ic_launcher)
            .SetContentText(body)
            .SetAutoCancel(true)
            .SetShowWhen(false)
            .SetContentIntent(pendingIntent);

        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            notificationBuilder.SetChannelId(AppConstants.NotificationChannelName);
        }

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }

    void SendMessageToMainPage(string body)
    {
        (App.Current.MainPage as MainPage)?.AddMessage(body);
    }
}
```

Los mensajes entrantes se convierten en una notificación local con el `SendLocalNotification` método. Este método crea un nuevo `Intent` y coloca el mensaje contenido en el `Intent` como un `string` `Extra`. Cuando el usuario pulsa la notificación local, si la aplicación está en primer plano o en segundo plano, el `MainActivity` se inicia y se tiene acceso al contenido de mensaje a través de la `Intent` objeto.

La notificación local y `Intent` ejemplo requiere que el usuario realizar la acción de puntear en la notificación. Esto es conveniente cuando el usuario debe tomar medidas antes de los cambios de estado de la aplicación. Sin embargo, es posible que desee tener acceso a los datos del mensaje sin necesidad de una acción del usuario en algunos casos. El ejemplo anterior también envía el mensaje directamente a la actual `MainPage` instancia con el `SendMessageToMainPage` método. En producción, si implementa ambos métodos para un tipo de mensaje único, el `MainPage` objeto obtendrán los mensajes duplicados si el usuario pulsa la notificación.

> [!NOTE]
> La aplicación de Android solo recibirá notificaciones de inserción si se está ejecutando en segundo plano o el primer plano. Para recibir notificaciones de inserción cuando el método main `Activity` es no se está ejecutando, debe implementar un servicio, que está fuera del ámbito de este ejemplo. Para obtener más información, consulte [crear servicios de Android](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>Incorporación de notificaciones entrantes a la interfaz de usuario de Xamarin.Forms

La `MainActivity` clase necesita obtener permiso para controlar las notificaciones y administrar los datos del mensaje entrante. El código siguiente muestra la completa `MainActivity` implementación:

```csharp
[Activity(Label = "NotificationHubSample", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, LaunchMode = LaunchMode.SingleTop)]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());

        if (IsPlayServiceAvailable() == false)
        {
            throw new Exception("This device does not have Google Play Services and cannot receive push notifications.");
        }

        CreateNotificationChannel();
    }

    protected override void OnNewIntent(Intent intent)
    {
        if (intent.Extras != null)
        {
            var message = intent.GetStringExtra("message");
            (App.Current.MainPage as MainPage)?.AddMessage(message);
        }

        base.OnNewIntent(intent);
    }

    bool IsPlayServiceAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(AppConstants.DebugTag, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(AppConstants.DebugTag, "This device is not supported");
            }
            return false;
        }
        return true;
    }

    void CreateNotificationChannel()
    {
        // Notification channels are new as of "Oreo".
        // There is no need to create a notification channel on older versions of Android.
        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            var channelName = AppConstants.NotificationChannelName;
            var channelDescription = String.Empty;
            var channel = new NotificationChannel(channelName, channelName, NotificationImportance.Default)
            {
                Description = channelDescription
            };

            var notificationManager = (NotificationManager)GetSystemService(NotificationService);
            notificationManager.CreateNotificationChannel(channel);
        }
    }
}
```

El `Activity` atributo establece la aplicación `LaunchMode` a `SingleTop`. Este modo de lanzamiento dice al SO Android para permitir únicamente una sola instancia de esta actividad. Con este modo de lanzamiento, entrante `Intent` datos se enrutan a la `OnNewIntent` método, que extrae datos del mensaje y lo envía a la `MainPage` instancia a través de la `AddMessage` método. Si la aplicación utiliza un modo de inicio diferente, debe controlar `Intent` datos de forma diferente.

El `OnCreate` método usa un método auxiliar llamado `IsPlayServiceAvailable` para asegurarse de que el dispositivo es compatible con Google Play Services. Los emuladores o dispositivos que no son compatibles con Google Play Services no pueden recibir notificaciones de inserción de Firebase.

## <a name="configure-ios-for-notifications"></a>Configurar notificaciones de iOS

Es el proceso para configurar la aplicación de iOS para recibir las notificaciones:

1. Configurar la **identificador de paquete** en el **Info.plist** archivo para que coincida con el valor utilizado en el perfil de aprovisionamiento.
1. Agregar el **habilitar las notificaciones de inserción** opción a la **Entitlements.plist** archivo.
1. Agregar el **Xamarin.Azure.NotificationHubs.iOS** paquete NuGet al proyecto.
1. [Registrarse para recibir notificaciones con APNS](#register-for-notifications-with-apns).
1. [Registrar la aplicación con Azure Notification Hub y suscribirse a las etiquetas](#register-with-azure-notification-hub-and-subscribe-to-tags).
1. [Incorporación de notificaciones de APNS para la interfaz de usuario de Xamarin.Forms](#add-apns-notifications-to-xamarinforms-ui).

La siguiente captura de pantalla muestra la **habilitar las notificaciones de inserción** opción seleccionada en el **Entitlements.plist** archivo dentro de Visual Studio:

![Captura de pantalla de los derechos de notificaciones de inserción](azure-notification-hub-images/push-notification-entitlement.png "derechos de notificaciones de inserción")

### <a name="register-for-notifications-with-apns"></a>Registro de notificaciones con APNS

El `FinishedLaunching` método en el **AppDelegate.cs** archivo debe reemplazarse para registrar las notificaciones remotas. Registro varía según la versión de iOS que se usa en el dispositivo. El proyecto de iOS en la aplicación de ejemplo reemplaza el `FinishedLaunching` método para llamar a `RegisterForRemoteNotifications` tal como se muestra en el ejemplo siguiente:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();
    LoadApplication(new App());

    base.FinishedLaunching(app, options);

    RegisterForRemoteNotifications();

    return true;
}

void RegisterForRemoteNotifications()
{
    // register for remote notifications based on system version
    if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
    {
        UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert |
            UNAuthorizationOptions.Sound |
            UNAuthorizationOptions.Sound,
            (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
    }
    else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
    {
        var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
        new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();
    }
    else
    {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
    }
}
```

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>Registrar con Azure Notification Hub y suscribirse a las etiquetas

Cuando el dispositivo registrado correctamente para las notificaciones remotas durante el `FinishedLaunching` método, llamará a iOS el `RegisteredForRemoteNotifications` método. Este método se debe invalidar para llevar a cabo las siguientes acciones:

1. Crear una instancia de la `SBNotificationHub`.
1. Anular el registro de los registros existentes.
1. Registrar el dispositivo con el centro de notificaciones.
1. Suscríbase a etiquetas específicas con una plantilla.

Para obtener más información sobre el registro del dispositivo, plantillas y etiquetas, consulte [registrar etiquetas y plantillas con Azure Notification Hub](#register-templates-and-tags-with-the-azure-notification-hub). El código siguiente muestra el registro del dispositivo y plantillas:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAllAsync(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplateAsync(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                if (errorCallback != null)
                {
                    Debug.WriteLine($"RegisterTemplateAsync error: {errorCallback}");
                }
            }
        });
    });
}
```

> [!NOTE]
> Registrar las notificaciones remotas puede producir errores en situaciones como sin conexión de red. Puede elegir invalidar el `FailedToRegisterForRemoveNotifications` método para controlar el error de registro.

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>Incorporación de notificaciones de APNS para la interfaz de usuario de Xamarin.Forms

Cuando un dispositivo recibe una notificación remota, iOS llama a la `ReceivedRemoteNotification` método. Mensaje entrante JSON se convierte en un `NSDictionary` objeto y el `ProcessNotification` método extrae los valores del diccionario y los envía a Xamarin.Forms `MainPage` instancia. El `ReceivedRemoteNotifications` se invalida el método para llamar a `ProcessNotification` tal como se muestra en el código siguiente:

```csharp
public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
{
    ProcessNotification(userInfo, false);
}

void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
{
    // make sure we have a payload
    if (options != null && options.ContainsKey(new NSString("aps")))
    {
        // get the APS dictionary and extract message payload. Message JSON will be converted
        // into a NSDictionary so more complex payloads may require more processing
        NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
        string payload = string.Empty;
        NSString payloadKey = new NSString("alert");
        if (aps.ContainsKey(payloadKey))
        {
            payload = aps[payloadKey].ToString();
        }

        if (!string.IsNullOrWhiteSpace(payload))
        {
            (App.Current.MainPage as MainPage)?.AddMessage(payload);
        }

    }
    else
    {
        Debug.WriteLine($"Received request to process notification but there was no payload.");
    }
}
```

## <a name="test-notifications-in-the-azure-portal"></a>Probar las notificaciones en el portal de Azure

Azure Notification Hubs permiten comprobar que la aplicación puede recibir mensajes de prueba. El **envío de prueba** sección en el centro de notificaciones le permite elegir la plataforma de destino y enviar un mensaje. Establecer el **enviar a expresión de etiqueta** a `default` enviará mensajes a las aplicaciones que han registrado una plantilla para el `default` etiqueta. Al hacer clic en el **enviar** botón genera un informe que incluya el número de dispositivos alcanzado con el mensaje. Captura de pantalla siguiente muestra una prueba de notificaciones de Android en el portal de Azure:

![Captura de pantalla de un mensaje de prueba de Azure Notification Hub](azure-notification-hub-images/azure-notification-hub-test-send.png "mensaje de prueba de centro de notificaciones de Azure")

### <a name="testing-tips"></a>Sugerencias para las pruebas

1. Al probar que una aplicación puede recibir notificaciones de inserción, debe usar un dispositivo físico. IOS y Android dispositivos virtuales no pueden configurarse correctamente para recibir notificaciones de inserción.
1. La aplicación de Android de ejemplo registra su símbolo (token) y las plantillas de una vez cuando se emite el token de Firebase. Durante las pruebas es posible que deba solicitar un nuevo token y vuelva a registrar con el centro de notificaciones de Azure. Es la mejor manera de forzar esto limpiar el proyecto, elimine el `bin` y `obj` carpetas y desinstalar la aplicación desde el dispositivo antes de volver a generar e implementar.
1. Muchas partes del flujo de notificaciones de inserción se ejecutan de forma asincrónica. Esto puede dar lugar a que los puntos de interrupción no es el acierto o se vea afectado en un orden inesperado. Usar el registro de dispositivo o de depuración para el seguimiento de la ejecución sin interrumpir el flujo de la aplicación. Filtrar el registro de dispositivo Android mediante el `DebugTag` especificado en `Constants`.

## <a name="create-a-notification-dispatcher"></a>Crear un distribuidor de notificación

Azure Notification Hubs permiten la aplicación de back-end enviar notificaciones a dispositivos entre plataformas. El ejemplo muestra el envío de notificaciones con el **NotificationDispatcher** aplicación de consola. La aplicación incluye el **DispatcherConstants.cs** archivo, que define las siguientes propiedades:

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

Debe configurar el **DispatcherConstants.cs** para que coincida con coincide con la configuración del centro de notificaciones de Azure. El valor de la `SubscriptionTags` propiedad debe coincidir con los valores utilizados en las aplicaciones cliente. El `NotificationHubName` propiedad es el nombre de la instancia de centro de notificaciones de Azure. El `FullAccessConnectionString` propiedad es la clave de acceso se encuentra en el centro de notificaciones **las directivas de acceso**. Captura de pantalla siguiente muestra la ubicación de la `NotificationHubName` y `FullAccessConnectionString` propiedades en el portal de Azure:

![Captura de pantalla del nombre del centro de notificaciones de Azure y FullAccessConnectionString](azure-notification-hub-images/notification-hub-full-access-policy.png "nombre del centro de notificaciones de Azure y FullAccessConnectionString")

La aplicación de consola recorre `SubscriptionTags` de valor y envía notificaciones a los suscriptores mediante una instancia de la `NotificationHubClient` clase. El código siguiente muestra la aplicación de consola `Program` clase:

``` csharp
class Program
{
    static int messageCount = 0;

    static void Main(string[] args)
    {
        Console.WriteLine($"Press the spacebar to send a message to each tag in {string.Join(", ", DispatcherConstants.SubscriptionTags)}");
        WriteSeparator();
        while (Console.ReadKey().Key == ConsoleKey.Spacebar)
        {
            SendTemplateNotificationsAsync().GetAwaiter().GetResult();
        }
    }

    private static async Task SendTemplateNotificationsAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(DispatcherConstants.FullAccessConnectionString, DispatcherConstants.NotificationHubName);
        Dictionary<string, string> templateParameters = new Dictionary<string, string>();

        messageCount++;

        // Send a template notification to each tag. This will go to any devices that
        // have subscribed to this tag with a template that includes "messageParam"
        // as a parameter
        foreach (var tag in DispatcherConstants.SubscriptionTags)
        {
            templateParameters["messageParam"] = $"Notification #{messageCount} to {tag} category subscribers!";
            try
            {
                await hub.SendTemplateNotificationAsync(templateParameters, tag);
                Console.WriteLine($"Sent message to {tag} subscribers.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Failed to send template notification: {ex.Message}");
            }
        }

        Console.WriteLine($"Sent messages to {DispatcherConstants.SubscriptionTags.Length} tags.");
        WriteSeparator();
    }

    private static void WriteSeparator()
    {
        Console.WriteLine("==========================================================================");
    }
}
```

Cuando se ejecuta la aplicación de consola de ejemplo, puede presionar la barra espaciadora para enviar mensajes. Los dispositivos que ejecutan el cliente deben recibir aplicaciones notificaciones numeradas, proporciona están configurados correctamente.

## <a name="related-links"></a>Vínculos relacionados

* [Plantillas de notificación de inserción](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).
* [Administración de registros de dispositivo](/azure/notification-hubs/notification-hubs-push-notification-registration-management).
* [Enrutamiento y expresiones de etiqueta](/azure/notification-hubs/notification-hubs-tags-segment-push-message).
* [Tutorial de Xamarin.Android Azure Notification Hubs](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm).
* [Tutorial de Xamarin.iOS Azure Notification Hubs](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started).
