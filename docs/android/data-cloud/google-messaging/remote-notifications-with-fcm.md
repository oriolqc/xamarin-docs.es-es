---
title: Notificaciones remotas con Firebase Cloud Messaging
description: Este tutorial proporciona una explicación paso a paso sobre cómo utilizar Firebase Cloud Messaging para implementar notificaciones remotas (también denominadas notificaciones push) en una aplicación de Xamarin.Android. Muestra cómo implementar las distintas clases que son necesarios para las comunicaciones con Firebase Cloud Messaging (FCM), se proporcionan ejemplos de cómo configurar el manifiesto de Android para tener acceso a FCM y muestra la mensajería de nivel inferior con el Firebase Consola.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 0c84f530f759285c2cfc71f60d7b6f80fba6a03d
ms.sourcegitcommit: f890b5ec9b7c2702875070859e1a8cbf6e870e46
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/28/2018
ms.locfileid: "53814042"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notificaciones remotas con Firebase Cloud Messaging

_Este tutorial proporciona una explicación paso a paso sobre cómo utilizar Firebase Cloud Messaging para implementar notificaciones remotas (también denominadas notificaciones push) en una aplicación de Xamarin.Android. Muestra cómo implementar las distintas clases que son necesarios para las comunicaciones con Firebase Cloud Messaging (FCM), se proporcionan ejemplos de cómo configurar el manifiesto de Android para tener acceso a FCM y muestra la mensajería de nivel inferior con el Firebase Consola._

## <a name="fcm-notifications-overview"></a>Información general de las notificaciones de FCM

En este tutorial, llama a una aplicación básica **FCMClient** se crearán para ilustrar las operaciones básicas de mensajería de FCM. **FCMClient** comprueba la presencia de Google Play Services, recibe los tokens de registro de FCM, muestra las notificaciones remotas que se envía desde la consola de Firebase y se suscribe a mensajes de tema:

[![Captura de pantalla de ejemplo de aplicación](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Se analizarán las siguientes áreas temáticas:

1.  Notificaciones en segundo plano

2.  Mensajes del tema

3.  Notificaciones de primer plano

Durante este tutorial, va a agregar funcionalidad a incrementalmente **FCMClient** y ejecutarlo en un dispositivo o emulador para comprender cómo interactúa con FCM. Usará el registro sea testigo de transacciones de la aplicación en directo con los servidores FCM y observará cómo se generan notificaciones de los mensajes de FCM que especifique en la GUI de las notificaciones de la consola de Firebase.

## <a name="requirements"></a>Requisitos


También le resultará útil familiarizarse con la [diferentes tipos de mensajes](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) que pueden enviarse por Firebase Cloud Messaging. La carga del mensaje determinará cómo una aplicación cliente recibirá y procesará el mensaje.

Antes de continuar con este tutorial, debe adquirir las credenciales necesarias para usar servidores FCM de Google. Este proceso se explica en [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm).
En concreto, debe descargar el **google-services.json** archivo para usarlo con el código de ejemplo presentado en este tutorial. Si aún no ha creado un proyecto en la consola de Firebase (o si no ha descargado todavía el **google-services.json** archivo), consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

Para ejecutar la aplicación de ejemplo, necesitará un emulador compatible con Firebase o dispositivo de prueba de Android. Firebase Cloud Messaging es compatible con los clientes que ejecutan Android 4.0 o versiones posteriores, y estos dispositivos también deben tener instalada la aplicación de Google Play Store (Google Play Services 9.2.1 o posterior es necesario). Si aún no dispone de la aplicación de Google Play Store instalada en el dispositivo, visite la [Google Play](https://support.google.com/googleplay) sitio web para descargarlo e instalarlo. Como alternativa, puede usar el emulador de Android SDK con Google Play Services instalado en lugar de un dispositivo de prueba (no es necesario instalar el Store de Google Play si usa el emulador de Android SDK).

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Para comenzar, cree un nuevo proyecto de Xamarin.Android vacío denominado **FCMClient**. Si no está familiarizado con la creación de proyectos de Xamarin.Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
Una vez creada la nueva aplicación, el siguiente paso es establecer el nombre del paquete e instalar varios paquetes de NuGet que se usará para la comunicación con FCM.

### <a name="set-the-package-name"></a>Establece el nombre del paquete

En [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), especifica un nombre de paquete para la aplicación habilitada para FCM. Este nombre del paquete también sirve como la [ *Id. de aplicación* ](./firebase-cloud-messaging.md#fcm-in-action-app-id) que está asociado con el [clave de API](firebase-cloud-messaging.md#fcm-in-action-api-key). Configurar la aplicación para usar este nombre de paquete:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Abra las propiedades de la **FCMClient** proyecto.

2.  En el **manifiesto de Android** , establezca el nombre del paquete.

En el ejemplo siguiente, se establece el nombre del paquete en `com.xamarin.fcmexample`:

[![Establecer el nombre del paquete](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Mientras se está actualizando el **manifiesto de Android**, también una comprobación para asegurarse de que el `Internet` está habilitado el permiso.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Abra las propiedades de la **FCMClient** proyecto.

2.  En el **Aplicaciónandroid** , establezca el nombre del paquete.

En el ejemplo siguiente, se establece el nombre del paquete en `com.xamarin.fcmexample`:

[![Establecer el nombre del paquete](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Mientras se está actualizando el **manifiesto de Android**, también una comprobación para asegurarse de que el `INTERNET` está habilitado el permiso (bajo **permisos necesarios**).

-----

> [!IMPORTANT]
> La aplicación cliente no podrá recibir un token de registro de FCM si este nombre de paquete no *exactamente* coincide con el nombre del paquete que se especificó en la consola de Firebase.

### <a name="add-the-xamarin-google-play-services-base-package"></a>Agregue el paquete de la Base de Xamarin Google Play Services

Dado que Firebase Cloud Messaging depende de Google Play Services, el [Xamarin Google Play Services - Base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) paquete NuGet debe agregarse al proyecto de Xamarin.Android. Necesitará la versión 29.0.0.2 o posterior.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  En Visual Studio, haga clic en **referencias > Administrar paquetes NuGet...** .

2.  Haga clic en el **examinar** pestaña y busque **Xamarin.GooglePlayServices.Base**.

3.  Instalar este paquete en el **FCMClient** proyecto:

    [![Instalación de Base de servicios de Google Play](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  En Visual Studio para Mac, haga clic en **paquetes > Agregar paquetes...** .

2.  Busque **Xamarin.GooglePlayServices.Base**.

3.  Instalar este paquete en el **FCMClient** proyecto:

    [![Instalación de Base de servicios de Google Play](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Si se produce un error durante la instalación de NuGet, cierre el **FCMClient** del proyecto, vuelva a abrirlo y vuelva a intentar la instalación de NuGet.

Al instalar **Xamarin.GooglePlayServices.Base**, también se instalan todas las dependencias necesarias. Editar **MainActivity.cs** y agregue la siguiente `using` instrucción:

```csharp
using Android.Gms.Common;
```

Esta instrucción hace que el `GoogleApiAvailability` clase **Xamarin.GooglePlayServices.Base** disponibles para **FCMClient** código.
`GoogleApiAvailability` se usa para comprobar la presencia de Google Play Services.

### <a name="add-the-xamarin-firebase-messaging-package"></a>Agregue el paquete Xamarin Firebase Messaging

Para recibir mensajes de FCM, el [Xamarin Firebase - mensajería](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) paquete NuGet debe agregarse al proyecto de aplicación. Sin este paquete, una aplicación de Android no puede recibir mensajes desde los servidores FCM.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  En Visual Studio, haga clic en **referencias > Administrar paquetes NuGet...** .

2. Busque **Xamarin.Firebase.Messaging**.

3.  Instalar este paquete en el **FCMClient** proyecto:

    [![Instalación de mensajería de Xamarin Firebase](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  En Visual Studio para Mac, haga clic en **paquetes > Agregar paquetes...** .

2.  Busque **Xamarin.Firebase.Messaging**.

3.  Instalar este paquete en el **FCMClient** proyecto:

    [![Instalación de mensajería de Xamarin Firebase](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

Al instalar **Xamarin.Firebase.Messaging**, también se instalan todas las dependencias necesarias.

A continuación, edite **MainActivity.cs** y agregue la siguiente `using` instrucciones:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Las dos primeras instrucciones que los tipos en el **Xamarin.Firebase.Messaging** paquete NuGet disponible para **FCMClient** código. **Android.Util** agrega funcionalidad de registro que se usará para observar las transacciones con FMS.

### <a name="add-googleplayservices-json"></a>Agregue el archivo JSON de servicios de Google

El siguiente paso es agregar el **google-services.json** archivo al directorio raíz del proyecto:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Copia **google-services.json** a la carpeta del proyecto.

2.  Agregar **google-services.json** al proyecto de aplicación (haga clic en **mostrar todos los archivos** en el **el Explorador de soluciones**, haga clic en **google-services.json**, a continuación, seleccione **incluir en el proyecto**).

3.  Seleccione **google-services.json** en el **el Explorador de soluciones** ventana.

4.  En el **propiedades** panel, establezca la **acción de compilación** a **GoogleServicesJson**:

    [![Configuración de la acción de compilación para GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > Si el **GoogleServicesJson** acción de compilación no se muestra, guarde y cierre la solución, y vuelva a abrirlo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Copia **google-services.json** a la carpeta del proyecto.

2.  Agregar **google-services.json** al proyecto de aplicación.

3.  Haga clic en **google-services.json**.

4.  Establecer el **acción de compilación** a **GoogleServicesJson**:

    [![Configuración de la acción de compilación para GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

Cuando **google-services.json** se agrega al proyecto (y el **GoogleServicesJson** se establece la acción de compilación), el proceso de compilación extrae el identificador de cliente y [clave de API](./firebase-cloud-messaging.md#fcm-in-action-api-key) y, a continuación, agrega estas credenciales para combinar o generado por el **AndroidManifest.xml** que reside en **obj/Debug/android/AndroidManifest.xml**. Este proceso de mezcla agrega automáticamente los permisos y otros elementos FCM que son necesarios para la conexión a servidores FCM.


## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>Busque Google Play Services y crear un canal de notificación

Google recomienda que las aplicaciones de Android comprobación la presencia del APK de Google Play Services antes de acceder a las características de Google Play Services (para obtener más información, consulte [comprueba los servicios de Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)).

En primer lugar se creará un diseño inicial para la interfaz de usuario de la aplicación. Editar **Resources/layout/Main.axml** y reemplace su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

Esto `TextView` se usará para mostrar los mensajes que indican si Google Play Services está instalado. Guarde los cambios en **Main.axml**.


Editar **MainActivity.cs** y agregue las siguientes variables de instancia a la `MainActivity` clase:

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

Las variables `CHANNEL_ID` y `NOTIFICATION_ID` se usará en el método [ `CreateNotificationChannel` ](#create-notification-channel-code) que se agregarán a `MainActivity` más adelante en este tutorial.


En el ejemplo siguiente, la `OnCreate` método comprobará que Google Play Services está disponible antes de la aplicación intenta usar los servicios de FCM.
Agregue el método siguiente a la `MainActivity` clase:

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "This device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

Este código comprueba el dispositivo para ver si está instalado el APK de servicios de Google Play. Si no está instalado, se muestra un mensaje en el `TextBox` que indica al usuario descargar un APK del Store de Google Play (o se habilita en la configuración del dispositivo del sistema).

<a name="create-notification-channel-code"></a>Aplicaciones que se ejecutan en Android 8.0 (API nivel 26) o posterior deben crear un [ _canal de notificación_ ](~/android/app-fundamentals/notifications/local-notifications.md) para publicar sus notificaciones.  Agregue el método siguiente a la `MainActivity` clase que se creará el canal de notificación (si es necesario):

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Reemplace el método `OnCreate` con el código siguiente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable` se llama al final de `OnCreate` para que la cuenta de Google Play Services comprobar que se ejecuta cada vez que se inicie la aplicación. El método `CreateNotificationChannel` se llama para asegurarse de que existe un canal de notificación para dispositivos que ejecutan Android 8 o superior. Si la aplicación tiene un `OnResume` método, debe llamar a `IsPlayServicesAvailable` desde `OnResume` también. Completamente volver a generar y ejecutar la aplicación. Si todo está configurado correctamente, debería ver una pantalla similar a la siguiente captura de pantalla siguiente:

[![Aplicación indica que Google Play Services está disponible](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Si no obtiene este resultado, compruebe que el APK de servicios de Google Play está instalado en el dispositivo (para obtener más información, consulte [configuración de seguridad de Google Play Services](https://developers.google.com/android/guides/setup)).
Compruebe también que ha agregado el **Xamarin.Google.Play.Services.Base** del paquete para su **FCMClient** proyecto como se explicó anteriormente.


## <a name="add-the-instance-id-receiver"></a>Agregue el receptor de Id. de instancia

El siguiente paso consiste en Agregar un servicio que extiende `FirebaseInstanceIdService` para controlar la creación, rotación y la actualización de [tokens de registro de Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token). El `FirebaseInstanceIdService` servicio es necesario para FCM poder enviar mensajes al dispositivo. Cuando el `FirebaseInstanceIdService` servicio se agrega a la aplicación cliente, la aplicación automáticamente recibirá los mensajes de FCM y mostrarlos como notificaciones cada vez que la aplicación se pasa a segundo plano.

### <a name="declare-the-receiver-in-the-android-manifest"></a>Declarar el receptor en el manifiesto de Android

Editar **AndroidManifest.xml** e inserte el siguiente `<receiver>` elementos en la `<application>` sección:

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

Este XML hace lo siguiente:

-   Declara un `FirebaseInstanceIdReceiver` implementación que proporciona un [identificador único](https://developers.google.com/instance-id/) para cada instancia de la aplicación. También este receptor se autentica y autoriza las acciones.

-   Declara una instancia interna `FirebaseInstanceIdInternalReceiver` implementación que se utiliza para iniciar los servicios de forma segura.

-   El [Id. de aplicación](./firebase-cloud-messaging.md#fcm-in-action-app-id) se almacena en el **google-services.json** archivo que estaba [agregado al proyecto](#add-googleplayservices-json). Los enlaces de Xamarin.Android Firebase reemplazará el token `${applicationId}` con el identificador de aplicación; no se requiere ningún código adicional mediante la aplicación cliente para proporcionar el identificador de aplicación.

El `FirebaseInstanceIdReceiver` es un `WakefulBroadcastReceiver` que recibe `FirebaseInstanceId` y `FirebaseMessaging` eventos y los entrega a la clase que derive de `FirebaseInstanceIdService`.

### <a name="implement-the-firebase-instance-id-service"></a>Implementar el servicio de Id. de instancia de Firebase

Controla el trabajo de registro de la aplicación con FCM personalizado `FirebaseInstanceIdService` servicio que proporciona.
`FirebaseInstanceIdService` lleva a cabo los pasos siguientes:

1.  Usa el [Instance ID API](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) para generar tokens de seguridad que autorizar a la aplicación cliente para tener acceso a FCM y el servidor de aplicaciones. En cambio, la aplicación recibe un [token de registro](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) de FCM.

2.  Reenvía el token de registro al servidor de aplicaciones si así lo exige la servidor de aplicaciones.

Agregue un nuevo archivo denominado **MyFirebaseIIDService.cs** y reemplace el código de plantilla con los siguientes:

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

Este servicio implementa un `OnTokenRefresh` método que se invoca cuando el token de registro inicialmente se crea o modifica. Cuando `OnTokenRefresh` se ejecuta, recupera el token más reciente desde el `FirebaseInstanceId.Instance.Token` propiedad (que se actualiza de forma asincrónica mediante FCM). En este ejemplo, se registra el token actualizado para que se puede ver en la ventana de salida:

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` se invoca con poca frecuencia: se usa para actualizar el token en las siguientes circunstancias:

-   Cuando la aplicación se instala o desinstala.

-   Cuando el usuario elimina datos de la aplicación.

-   Cuando la aplicación borra el identificador de instancia.

-   Cuando la seguridad del token está en peligro.

Según Google [Id. de instancia](https://developers.google.com/instance-id/guides/android-implementation) documentación, el servicio Id. de instancia de FCM solicitará que la aplicación actualizar su token periódicamente (por lo general, cada 6 meses).

`OnTokenRefresh` También llama a `SendRegistrationToAppServer` para asociar el registro del usuario token con la cuenta de servidor (si existe) que se mantiene por la aplicación:

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Dado que esta implementación depende del diseño del servidor de aplicaciones, un cuerpo del método vacío se proporciona en este ejemplo. Si el servidor de la aplicación requiere información de registro FCM, modifique `SendRegistrationToAppServer` para asociar el token de identificador de instancia FCM del usuario con cualquier cuenta de servidor mantenida por la aplicación. (Tenga en cuenta que el token es opaco para la aplicación cliente.)

Cuando un token se envía al servidor de aplicaciones, `SendRegistrationToAppServer` debe mantener un valor booleano para indicar si el token se ha enviado al servidor. Si es false, este valor booleano `SendRegistrationToAppServer` envía el token al servidor de aplicaciones &ndash; en caso contrario, el token ya se envió al servidor de aplicaciones en una llamada anterior. En algunos casos (por ejemplo, esto `FCMClient` ejemplo), el servidor de la aplicación no necesita el token; por lo tanto, este método no es necesario para este ejemplo.

## <a name="implement-client-app-code"></a>Implementar el código de aplicación de cliente

Ahora que están instalados los servicios del receptor, código de la aplicación cliente puede escribirse para aprovechar las ventajas de estos servicios. En las secciones siguientes, se agrega un botón a la interfaz de usuario para iniciar el token de registro (también denominado el *token de identificador de instancia*), y se agregará más código para `MainActivity` para ver `Intent` información cuando se inicia la aplicación desde un notificación:

[![Botón de símbolo (token) de registro agregado a la pantalla de la aplicación](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Tokens de registro

El código agregado en este paso está pensado solo para fines de demostración &ndash; una aplicación de cliente de producción tendría necesario registrar los tokens de registro. Editar **Resources/layout/Main.axml** y agregue la siguiente `Button` declaración inmediatamente después de la `TextView` elemento:

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Agregue el código siguiente al final del método `MainActivity.OnCreate`:

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Este código registra el token actual en la ventana de salida cuando el **registro Token** se pulsa el botón.

### <a name="handle-notification-intents"></a>Administrar calidades de notificación

Cuando el usuario pulsa una notificación que se emiten desde **FCMClient**, cualquier dato que acompaña a esa notificación de mensaje está disponible en `Intent` adicionales. Editar **MainActivity.cs** y agregue el código siguiente a la parte superior de la `OnCreate` método (antes de llamar a `IsPlayServicesAvailable`):

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

Iniciador de la aplicación `Intent` se desencadena cuando el usuario pulsa su mensaje de notificación, por lo que este código se registrará ningún dato adjunto en el `Intent` en la ventana de salida. Si otro `Intent` debe activarse la `click_action` campo el mensaje de notificación se debe establecer en el que `Intent` (el iniciador `Intent` se usa cuando no hay ninguna `click_action` se especifica).


## <a name="background-notifications"></a>Notificaciones en segundo plano

Compilar y ejecutar el **FCMClient** app. El **registro Token** botón se muestra:

[![Se muestra el botón de símbolo (token) de registro](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Pulse el **registro Token** botón. En la ventana de salida IDE, se debe mostrar un mensaje similar al siguiente:

[![Token de identificador de instancia que aparece en la ventana de salida](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

Con la etiqueta con la cadena larga **token** es el token de Id. de instancia que se pegará en la consola de Firebase &ndash; seleccione y copie esta cadena en el Portapapeles. Si no ve un token de identificador de instancia, agregue la siguiente línea a la parte superior de la `OnCreate` método para comprobar que **google-services.json** se analizó correctamente:

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

El `google_app_id` valor registrado en la ventana de salida debe coincidir con el `mobilesdk_app_id` valor registrado en **google-services.json**.

### <a name="send-a-message"></a>Enviar un mensaje

Inicie sesión en el [consola Firebase](https://console.firebase.google.com), seleccione el proyecto, haga clic en **notificaciones**y haga clic en **enviar su primer mensaje**:

[![El primer mensaje botón Enviar](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

En el **redactar mensajes** página, escriba el texto del mensaje y seleccione **único dispositivo**. Copie el token de identificador de instancia de la ventana de salida IDE y péguelo en el **token de registro FCM** campo de la consola de Firebase:

[![Crear el cuadro de diálogo de mensaje](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

En el dispositivo Android (o emulador), en segundo plano la aplicación punteando el Android **Introducción** botón y tocar la pantalla principal. Cuando el dispositivo está listo, haga clic en **enviar mensaje** en la consola de Firebase:

[![Enviar mensajes (botón)](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Cuando el **mensaje revisión** muestra el cuadro de diálogo, haga clic en **enviar**.
El icono de notificación debe aparecer en el área de notificación del dispositivo (o emulador):

[![Se muestra el icono de notificación](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Abra el icono de notificación para ver el mensaje. El mensaje de notificación debe ser exactamente lo que se escribió en el **texto del mensaje** campo de la consola de Firebase:

[![Mensaje de notificación se muestra en el dispositivo](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Pulse el icono de notificación para iniciar el **FCMClient** app. El `Intent` adicionales que se envían a **FCMClient** aparecen en la ventana de salida IDE:

[![Listas adicionales de intención de clave, Id. de mensaje y la clave de contracción](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

En este ejemplo, el **desde** clave se establece en el número de proyecto de Firebase de la aplicación (en este ejemplo, `41590732`) y el **collapse_key** está establecido en su nombre de paquete ( **com.xamarin.fcmexample**).
Si no recibe un mensaje, intente eliminar el **FCMClient** aplicación en el dispositivo (o el emulador) y repita los pasos anteriores.


> [!NOTE]
> Si se fuerza, cierre la aplicación, FCM dejará de entrega de notificaciones. Android impide que accidentalmente o innecesariamente iniciar los componentes de las aplicaciones detenidas las difusiones de servicio en segundo plano. (Para obtener más información acerca de este comportamiento, consulte [iniciar los controles en las aplicaciones detenidas](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) Por este motivo, es necesario desinstalar manualmente la aplicación cada vez que ejecutarla y detenerla desde una sesión de depuración &ndash; Esto obliga a FCM para generar un nuevo token para que sigan poder recibir mensajes.

### <a name="add-a-custom-default-notification-icon"></a>Agregar un icono de notificación predeterminado personalizado

En el ejemplo anterior, el icono de notificación se establece en el icono de la aplicación. El código XML siguiente configura un icono predeterminado personalizado para las notificaciones. Android muestra este icono personalizada predeterminada para todos los mensajes de notificación que no se establece explícitamente el icono de notificación.

Para agregar un icono de notificación personalizada predeterminada, el icono para agregar la **recursos/drawable** directorio, editar **AndroidManifest.xml**e inserte el siguiente `<meta-data>` elemento en el `<application>`sección:

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

En este ejemplo, el icono de notificación que reside en **recursos/drawable/ic\_stat\_ic\_notification.png** se usará como el icono de notificación personalizada predeterminada. Si un icono personalizado predeterminado no está configurado en **AndroidManifest.xml** y no se establece ningún icono en la carga de notificación, Android usa el icono de aplicación como el icono de notificación (tal y como se muestra en la captura de pantalla de icono de notificación anterior).

## <a name="handle-topic-messages"></a>Controlar los mensajes de tema

El código escrito hasta ahora controla los tokens de registro y agrega funcionalidad de notificación de remoto a la aplicación. En el ejemplo siguiente se agrega código que realiza escuchas para *los mensajes del tema* y las reenvía al usuario remotas como notificaciones. Mensajes de tema son mensajes FCM que se envían a uno o varios dispositivos que se suscriben a un tema determinado. Para obtener más información acerca de los mensajes de tema, consulte [tema mensajería](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

### <a name="subscribe-to-a-topic"></a>Suscribirse a un tema

Editar **Resources/layout/Main.axml** y agregue la siguiente `Button` declaración inmediatamente después de la anterior `Button` elemento:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Este código XML agrega un **suscribirse a notificaciones** botón al diseño.
Editar **MainActivity.cs** y agregue el código siguiente al final de la `OnCreate` método:

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Este código localiza el **suscribirse a notificaciones** botón en el diseño y asigna su controlador de clic al código que llama `FirebaseMessaging.Instance.SubscribeToTopic`, pasando el tema está suscrito, _noticias_. Cuando el usuario pulsa el **Subscribe** botón, la aplicación se suscribe a la _noticias_ tema. En la sección siguiente, un _noticias_ se enviará el mensaje del tema desde la GUI de las notificaciones de la consola de Firebase.

### <a name="send-a-topic-message"></a>Enviar un mensaje de tema

Desinstalar la aplicación, vuelva a compilarlo y ejecutarlo de nuevo. Haga clic en el **suscribirse a notificaciones** botón:

[![Suscribirse al botón de notificaciones](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Si la aplicación se ha suscrito correctamente, debería ver **sincronización tema se ha realizado correctamente** en el IDE de la ventana de salida:

[![Ventana de salida muestra el mensaje del tema sincronización se realizó correctamente](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Para enviar un mensaje de tema, siga estos pasos:

1.  En la consola Firebase, haga clic en **nuevo mensaje**.

2.  En el **redactar mensajes** página, escriba el texto del mensaje y seleccione **tema**.

3.  En el **tema** menú desplegable, seleccione el tema integrado, **noticias**:

    [![Seleccionar el tema de noticias](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  En el dispositivo Android (o emulador), en segundo plano la aplicación punteando el Android **Introducción** botón y tocar la pantalla principal.

5.  Cuando el dispositivo está listo, haga clic en **enviar mensaje** en la consola de Firebase.

6.  Compruebe la ventana de salida IDE para ver **noticias ytemas/** en la salida del registro:

    [![Se muestra el mensaje de /topic/news](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Cuando este mensaje se muestra en la ventana de salida, el icono de notificación también debe aparecer en el área de notificación en el dispositivo Android. Abra el icono de notificación para ver el mensaje del tema:

[![Aparece el mensaje del tema como una notificación](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Si no recibe un mensaje, intente eliminar el **FCMClient** aplicación en el dispositivo (o el emulador) y repita los pasos anteriores.

## <a name="foreground-notifications"></a>Notificaciones de primer plano

Para recibir notificaciones en aplicaciones foregrounded, debe implementar `FirebaseMessagingService`. Este servicio también es necesario para recibir cargas de datos y para enviar mensajes de nivel superior. Los ejemplos siguientes muestran cómo implementar un servicio que extiende `FirebaseMessagingService` &ndash; la aplicación resultante será capaz de controlar las notificaciones remotas mientras se está ejecutando en primer plano.

### <a name="implement-firebasemessagingservice"></a>Implementar FirebaseMessagingService

El `FirebaseMessagingService` servicio es responsable de recibir y procesar los mensajes de Firebase. Cada aplicación debe subclase este tipo y la invalidación del `OnMessageReceived` para procesar un mensaje entrante. Cuando una aplicación está en primer plano, el `OnMessageReceived` devolución de llamada siempre tratará el mensaje.

> [!NOTE]
> Las aplicaciones sólo tienen 10 segundos en que se va a controlar un mensaje entrante de la nube de Firebase. Cualquier trabajo que tarda más de esto se debe programar para su ejecución en segundo plano mediante una biblioteca, como el [Android programador de trabajos](~/android/platform/android-job-scheduler.md) o [distribuidor de trabajo de Firebase](~/android/platform/firebase-job-dispatcher.md).

Agregue un nuevo archivo denominado **MyFirebaseMessagingService.cs** y reemplace el código de plantilla con los siguientes:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

Tenga en cuenta que el `MESSAGING_EVENT` filtro de intento se debe declarar para que los mensajes FCM nuevos se dirigen a `MyFirebaseMessagingService`:

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Cuando la aplicación cliente recibe un mensaje de FCM, `OnMessageReceived` extrae el contenido del mensaje en el pasado `RemoteMessage` objeto mediante una llamada a su `GetNotification` método. A continuación, registra el contenido del mensaje para que se puede ver en la ventana de salida IDE:

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> Si establece los puntos de interrupción `FirebaseMessagingService`, la sesión de depuración puede o no se puede alcanzar estos puntos de interrupción debido a cómo FCM entrega los mensajes.


### <a name="send-another-message"></a>Enviar otro mensaje

Desinstalar la aplicación, vuelva a compilarlo, ejecútelo de nuevo y siga estos pasos para enviar otro mensaje:

1.  En la consola Firebase, haga clic en **nuevo mensaje**.

2.  En el **redactar mensajes** página, escriba el texto del mensaje y seleccione **único dispositivo**.

3.  Copie la cadena de token desde la ventana de salida IDE y péguelo en el **token de registro FCM** campo de la consola de Firebase como antes.

4.  Asegúrese de que la aplicación se está ejecutando en primer plano y, después, haga clic en **enviar mensaje** en la consola de Firebase:

    [![Enviar otro mensaje desde la consola](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  Cuando el **mensaje revisión** muestra el cuadro de diálogo, haga clic en **enviar**.

6.  El mensaje entrante se registra en la ventana de salida IDE:

    [![El cuerpo del mensaje se imprime en la ventana de salida](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notification-sender"></a>Agregar un remitente de la notificación local

En este ejemplo restante, el mensaje entrante de FCM se convertirán en una notificación local que se inicia mientras se ejecuta la aplicación en primer plano. Editar **MyFirebaseMessageService.cs** y agregue la siguiente `using` instrucciones:

```csharp
using FCMClient;
using System.Collections.Generic;
```

Agregue el siguiente método a `MyFirebaseMessagingService`:

<a name="sendnotification-method"></a>
```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

Para distinguir esta notificación de las notificaciones en segundo plano, este código marca notificaciones con un icono que difiere en el icono de la aplicación. Agregue el archivo [ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) a **recursos/drawable** e inclúyalo en el **FCMClient** proyecto .

El `SendNotification` usos del método ` NotificationCompat.Builder` para crear la notificación, y `NotificationManagerCompat` se utiliza para iniciar la notificación. La notificación contiene un `PendingIntent` que permitirá al usuario que abra la aplicación y ver el contenido de la cadena pasada a `messageBody`. Para obtener más información acerca de `NotificationCompat.Builder`, consulte [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).

Llame a la `SendNotification` método final de la `OnMessageReceived` método:

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

Como resultado de estos cambios, `SendNotification` se ejecutará cada vez que se recibe una notificación mientras la aplicación está en primer plano y la notificación se mostrará en el área de notificación.

Cuando una aplicación está en segundo plano, el [carga del mensaje](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) determinará cómo se controla el mensaje:

* **Notificación** &ndash; los mensajes se enviarán a la **bandeja del sistema**. Una notificación local aparecerá allí. Cuando el usuario pulsa la notificación se iniciará la aplicación.
* **Datos** &ndash; los mensajes se controlarán mediante `OnMessageReceived`.
* **Ambos** &ndash; los mensajes que tienen la carga de una notificación y los datos se enviarán a la bandeja del sistema. Cuando se inicia la aplicación, la carga de datos aparecerá en el `Extras` de la `Intent` que se usó para iniciar la aplicación.

En este ejemplo, si la aplicación pasa a segundo plano, `SendNotification` se ejecutará si el mensaje tiene una carga de datos. En caso contrario, se iniciará una notificación en segundo plano (que se muestra anteriormente en este tutorial).

### <a name="send-the-last-message"></a>Enviar el último mensaje

Desinstalar la aplicación, vuelva a compilarlo, ejecútelo de nuevo y luego siga estos pasos para enviar el último mensaje:

1.  En la consola Firebase, haga clic en **nuevo mensaje**.

2.  En el **redactar mensajes** página, escriba el texto del mensaje y seleccione **único dispositivo**.

3.  Copie la cadena de token desde la ventana de salida IDE y péguelo en el **token de registro FCM** campo de la consola de Firebase como antes.

4.  Asegúrese de que la aplicación se está ejecutando en primer plano y, después, haga clic en **enviar mensaje** en la consola de Firebase:

    [![Enviar el mensaje de primer plano](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Esta vez, el mensaje que se ha registrado en la ventana de salida también se empaqueta en una nueva notificación &ndash; el icono de notificación aparece en la Bandeja de notificación mientras la aplicación se ejecuta en primer plano:

[![Icono de notificación de mensaje de primer plano](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Cuando se abre la notificación, debería ver el último mensaje enviado desde la GUI de las notificaciones de la consola de Firebase:

[![Notificación de primer plano que se muestran con el icono de primer plano](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>Desconectando de FCM

Para cancelar la suscripción a un tema, llame a la [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) método en el [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) clase. Por ejemplo, para cancelar la suscripción a la _noticias_ tema suscrito a versiones anteriores, un **Unsubscribe** botón podría agregarse al diseño con el siguiente código del controlador:

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Para anular el registro del dispositivo desde completamente FCM, eliminar el identificador de instancia mediante una llamada a la [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) método en el [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) clase. Por ejemplo:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Esta llamada al método elimina el identificador de instancia y los datos asociados con él. Como resultado, se detiene el envío periódico de datos FCM al dispositivo.


## <a name="troubleshooting"></a>Solución de problemas

El siguiente describe problemas y soluciones alternativas que pueden surgir al usar Firebase Cloud Messaging con Xamarin.Android.

### <a name="firebaseapp-is-not-initialized"></a>No se ha inicializado FirebaseApp

En algunos casos, es posible que vea este mensaje de error:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Se trata de un problema conocido que puede solucionar mediante la limpieza de la solución y volver a generar el proyecto (**compilar > Limpiar solución**, **compilar > recompilar solución**).

## <a name="summary"></a>Resumen

En este tutorial se detalla los pasos para implementar notificaciones remotas Firebase Cloud Messaging en una aplicación de Xamarin.Android. Describe cómo instalar los paquetes necesarios, es necesarios para las comunicaciones de FCM y explica cómo configurar el manifiesto de Android para tener acceso a los servidores FCM. Proporciona código de ejemplo que muestra cómo comprobar la presencia de Google Play Services. Se ha demostrado cómo implementar un servicio de escucha de Id. de instancia que negocia con FCM para un token de registro y se ha explicado cómo este código crea las notificaciones en segundo plano mientras la aplicación se pasa a segundo plano. Se ha explicado cómo suscribirse a mensajes de tema, y proporciona un ejemplo de implementación de un servicio de agente de escucha de mensajes que se usa para recibir y mostrar las notificaciones remotas mientras se ejecuta la aplicación en primer plano.


## <a name="related-links"></a>Vínculos relacionados

- [FCMNotifications (ejemplo)](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Mensajería en la nube de Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [Acerca de los mensajes de FCM](https://firebase.google.com/docs/cloud-messaging/concept-options)
