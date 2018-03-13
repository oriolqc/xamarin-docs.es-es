---
title: "Notificaciones remotas con Firebase de mensajería en la nube"
description: "Este tutorial proporciona una explicación paso a paso sobre cómo usar la mensajería de nube de Firebase para implementar notificaciones remotas (también denominadas notificaciones de inserción) en una aplicación Xamarin.Android. Muestra cómo implementar las diferentes clases que son necesarios para las comunicaciones con Firebase en la nube de mensajería (FCM), se proporcionan ejemplos de cómo configurar el manifiesto de Android para tener acceso a FCM y muestra la mensajería de nivel inferior mediante el Firebase Consola."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 7e107fc9ced62027bc793c97a388344d74becd66
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notificaciones remotas con Firebase de mensajería en la nube

_Este tutorial proporciona una explicación paso a paso sobre cómo usar la mensajería de nube de Firebase para implementar notificaciones remotas (también denominadas notificaciones de inserción) en una aplicación Xamarin.Android. Muestra cómo implementar las diferentes clases que son necesarios para las comunicaciones con Firebase en la nube de mensajería (FCM), se proporcionan ejemplos de cómo configurar el manifiesto de Android para tener acceso a FCM y muestra la mensajería de nivel inferior mediante el Firebase Consola._

## <a name="fcm-notifications-overview"></a>Información general de las notificaciones de FCM

En este tutorial, llama a una aplicación básica **FCMClient** se creará para ilustrar las operaciones básicas de mensajería FCM. **FCMClient** comprueba la presencia de servicios de Google Play, recibe los tokens de registro de FCM, muestra las notificaciones remotas que se envían desde la consola de Firebase y se suscribe a mensajes del tema:

[![Captura de pantalla de ejemplo de aplicación](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Las siguientes áreas temáticas se explorarán:

1.  Notificaciones de fondo

2.  Mensajes de tema

3.  Notificaciones de primer plano

Durante este tutorial, va a agregar funcionalidad a incrementalmente **FCMClient** y ejecutarlo en un dispositivo o emulador para comprender cómo interactúa con FCM. Va a usar el registro para las transacciones de la aplicación activa con servidores FCM de testigo y observará cómo se generan las notificaciones de mensajes FCM que escriben en la GUI de las notificaciones de la consola de Firebase. 

## <a name="requirements"></a>Requisitos

Antes de continuar con este tutorial, debe adquirir las credenciales necesarias para usar los servidores de Google FCM; Este proceso se explica en [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). En concreto, debe descargar el **services.json google** archivo para utilizarlo con el código de ejemplo en este tutorial se presentan. Si aún no ha creado un proyecto en la consola de Firebase (o si todavía no ha descargado el **google services.json** archivo), consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Para ejecutar la aplicación de ejemplo, necesitará un dispositivo de prueba Android o el emulador de Windows que sea compatible con Firebase. Mensajería de nube de firebase es compatible con los clientes que ejecutan en Android 2.3 (monigote) o posterior, y estos dispositivos también deben tener el instalada la aplicación de Google Play Store (Google Play Services 9.2.1 o posterior es necesario). Si aún no dispone de la aplicación de Google Play Store instalada en el dispositivo, visite la [Google Play](https://support.google.com/googleplay) sitio web para descargarlo e instalarlo. Como alternativa, puede utilizar el emulador de Android SDK con Android 2.3 o posterior en lugar de un dispositivo de prueba (no es necesario instalar Google Play Store si usas el emulador del SDK de Android). 

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Para empezar, cree un nuevo proyecto de Xamarin.Android vacío denominado **FCMClient**. Si no está familiarizado con la creación de proyectos de Xamarin.Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md). Después de crea la nueva aplicación, el paso siguiente es establecer el nombre del paquete e instalar varios paquetes de NuGet que se utilizará para la comunicación con FCM. 

### <a name="set-the-package-name"></a>Establece el nombre del paquete

En [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), ha especificado un nombre de paquete para la aplicación habilitada para FCM. Este nombre de paquete también sirve como la *identificador de la aplicación* que está asociado a la clave de API. Configurar la aplicación para que use este nombre de paquete:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Abra las propiedades de la **FCMClient** proyecto. 

2.  En el **manifiesto Android** , establezca el nombre del paquete. 

En el ejemplo siguiente, se establece el nombre del paquete en `com.xamarin.fcmexample`: 

[![Establecer el nombre del paquete](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Mientras está actualizando el **manifiesto Android**, active también para estar seguro de que el `Internet` está habilitado el permiso. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Abra las propiedades de la **FCMClient** proyecto. 

2.  En el **aplicación Android** , establezca el nombre del paquete. 

En el ejemplo siguiente, se establece el nombre del paquete en `com.xamarin.fcmexample`: 

[![Establecer el nombre del paquete](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Mientras está actualizando el **manifiesto Android**, active también para estar seguro de que el `INTERNET` está habilitado el permiso (en **los permisos necesarios**). 

-----

Tenga en cuenta que la aplicación cliente no podrá recibir un token de registro de FCM si este nombre de paquete no *exactamente* coincide con el nombre del paquete que se especificó en la consola de Firebase. 

### <a name="add-the-xamarin-google-play-services-base-package"></a>Agregue el paquete de Base de servicios de Xamarin Google Play

Dado que Firebase Cloud Messaging depende de servicios de Google Play, el [Xamarin servicios de Google Play - Base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) paquete NuGet debe agregarse al proyecto Xamarin.Android. Se necesita la versión 29.0.0.2 o posterior.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  En Visual Studio, haga clic en **referencias > Administrar paquetes de NuGet...** . 

2.  Haga clic en el **examinar** ficha y busque **Xamarin.GooglePlayServices.Base**. 

3.  Instalar este paquete en el **FCMClient** proyecto: 

    [![Instalar la Base de servicios de Google Play](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  En Visual Studio para Mac, haga clic en **paquetes > Agregar paquetes...** . 

2.  Busque **Xamarin.GooglePlayServices.Base**. 

3.  Instalar este paquete en el **FCMClient** proyecto: 

    [![Instalar la Base de servicios de Google Play](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Si recibe un error durante la instalación de NuGet, cierre el **FCMClient** proyecto, vuelva a abrirlo y vuelva a intentar la instalación de NuGet. 

Al instalar **Xamarin.GooglePlayServices.Base**, también se instalan todas las dependencias necesarias. Editar **MainActivity.cs** y agregue el siguiente `using` instrucción: 

```csharp
using Android.Gms.Common;
```

Esta instrucción hace que el `GoogleApiAvailability` clase **Xamarin.GooglePlayServices.Base** disponibles para **FCMClient** código. 
`GoogleApiAvailability` se usa para comprobar la presencia de servicios de Google Play. 

### <a name="add-the-xamarin-firebase-messaging-package"></a>Agregar el Xamarin Firebase mensajería paquete

Para recibir mensajes de FCM, el [Xamarin Firebase - mensajería](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) paquete NuGet debe agregarse al proyecto de la aplicación. Sin este paquete, una aplicación de Android no puede recibir mensajes de los servidores FCM.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  En Visual Studio, haga clic en **referencias > Administrar paquetes de NuGet...** . 

2.  Comprobar **incluir versión preliminar** y busque **Xamarin.Firebase.Messaging**. 

3.  Instalar este paquete en el **FCMClient** proyecto: 

    [![Instalación de Xamarin de Firebase de mensajería](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  En Visual Studio para Mac, haga clic en **paquetes > Agregar paquetes...** . 

2.  Comprobar **mostrar los paquetes de versión preliminar** y busque **Xamarin.Firebase.Messaging**. 

3.  Instalar este paquete en el **FCMClient** proyecto: 

    [![Instalación de Xamarin de Firebase de mensajería](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----
 
Al instalar **Xamarin.Firebase.Messaging**, también se instalan todas las dependencias necesarias.

A continuación, edite **MainActivity.cs** y agregue el siguiente `using` instrucciones:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Las dos primeras instrucciones que los tipos en el **Xamarin.Firebase.Messaging** paquete de NuGet disponible para **FCMClient** código. **Android.Util** agrega funcionalidad de registro que se usará para observar las transacciones con FMS. 


### <a name="add-the-google-services-json-file"></a>Agregue el archivo JSON de servicios de Google

El paso siguiente consiste en agregar el **services.json google** archivo en el directorio raíz del proyecto: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Copia **services.json google** a la carpeta del proyecto.

2.  Agregar **google services.json** para el proyecto de aplicación (haga clic en **mostrar todos los archivos** en el **el Explorador de soluciones**, haga clic en **google-services.json**, a continuación, seleccione **incluir en el proyecto**). 

3.  Seleccione **google services.json** en el **el Explorador de soluciones** ventana.

4.  En el **propiedades** panel, establezca la **acción de compilación** a **GoogleServicesJson** (si la **GoogleServicesJson** no se muestra la acción de compilación, Guarde y cierre la solución, a continuación, vuelva a abrirlo):

    [![Si se establece la acción de compilación en GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Copia **services.json google** a la carpeta del proyecto.

2.  Agregar **services.json google** para el proyecto de aplicación. 

3.  Haga clic en **services.json google**.

4.  Establecer el **acción de compilación** a **GoogleServicesJson**: 

    [![Si se establece la acción de compilación en GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)
 
-----
 

Cuando **google services.json** se agrega al proyecto (y la **GoogleServicesJson** se establece la acción de compilación), el proceso de compilación extrae la clave de identificador y la API de cliente y, a continuación, agrega estas credenciales a la combinadas / genera **AndroidManifest.xml** que reside en **obj/Debug/android/AndroidManifest.xml**. Este proceso de mezcla agrega automáticamente los permisos y otros elementos FCM que son necesarios para la conexión a servidores FCM. 


## <a name="check-for-google-play-services"></a>Busque servicios Google Play

En primer lugar se creará un diseño inicial para la interfaz de usuario de la aplicación. Editar **Resources/layout/Main.axml** y reemplazar su contenido con el siguiente código XML: 

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

Esto `TextView` se usará para mostrar los mensajes que indican si está instalado servicios de Google Play. Guardar los cambios realizados en **Main.axml**. Editar **MainActivity.cs** y agregue la siguiente declaración de variable de instancia para el `MainActivity` clase: 

```csharp
TextView msgText;
```

Google recomienda que las aplicaciones de Android comprobación la presencia de la APK de servicios de Google reproducir antes de tener acceso a características de servicios de Google Play (para obtener más información, consulte [busque servicios Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)). En el ejemplo siguiente, la `OnCreate` método comprobará que los servicios de Google Play está disponible antes de que la aplicación intenta usar FCM servicios. Agregue el método siguiente a la `MainActivity` clase: 

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

Este código comprueba el dispositivo para saber si está instalado el APK de servicios de Google reproducir. Si no está instalado, se muestra un mensaje en el `TextBox` que solicita al usuario para descargar un APK de Google Play Store (o para habilitar en configuración del sistema del dispositivo). 

Reemplace el método `OnCreate` con el código siguiente: 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);
    IsPlayServicesAvailable ();
}
```

`IsPlayServicesAvailable` se llama al final de `OnCreate` para que se ejecuta cada vez que la aplicación se inicia la comprobación de los servicios de Google Play. Si la aplicación tiene un `OnResume` método, debe llamar a `IsPlayServicesAvailable` de `OnResume` también. Volver a generar y ejecutar la aplicación por completo. Si todo está configurado correctamente, verá una pantalla similar a la captura de pantalla siguiente: 

[![Aplicación indica que los servicios de Google Play está disponible](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Si no obtiene este resultado, compruebe que el APK de servicios de Google reproducir esté instalado en el dispositivo (para obtener más información, consulte [configuración de seguridad de servicios de Google Play](https://developers.google.com/android/guides/setup)). Compruebe también que ha agregado el **Xamarin.Google.Play.Services.Base** el paquete a su **FCMClient** proyecto como se explicó anteriormente.


## <a name="add-the-instance-id-receiver"></a>Agregar el destinatario de Id. de instancia

El paso siguiente consiste en Agregar un servicio que extiende `FirebaseInstanceIdService` para controlar la creación, rotación y la actualización de tokens de registro Firebase. (Para obtener más información acerca de los tokens de registro, consulte [registro con FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).) El `FirebaseInstanceIdService` servicio es necesario para FCM poder enviar mensajes al dispositivo. Cuando el `FirebaseInstanceIdService` servicio se agrega a la aplicación cliente, la aplicación automáticamente recibirá mensajes FCM y mostrarlos como notificaciones cada vez que la aplicación es backgrounded. 

### <a name="declare-the-receiver-in-the-android-manifest"></a>Declare el receptor en el manifiesto de Android

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

Este código XML hace lo siguiente:

-   Declara un `FirebaseInstanceIdReceiver` implementación que proporciona un [identificador único](https://developers.google.com/instance-id/) para cada instancia de la aplicación. Este receptor también autentica y autoriza las acciones. 

-   Declara un interno `FirebaseInstanceIdInternalReceiver` implementación que se usa para iniciar los servicios de forma segura.

El `FirebaseInstanceIdReceiver` es un `WakefulBroadcastReceiver` que recibe `FirebaseInstanceId` y `FirebaseMessaging` eventos y los entrega a la clase que derive de `FirebaseInstanceIdService`. 

### <a name="implement-the-firebase-instance-id-service"></a>Implementar el servicio de Id. de instancia de Firebase

El trabajo de registro de la aplicación con FCM se controla mediante la opción de instalación `FirebaseInstanceIdService` servicio proporcionada por usted. 
`FirebaseInstanceIdService` realiza los pasos siguientes: 

1.  Usa el [API de Id. de instancia](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) para generar tokens de seguridad que autorizan la aplicación de cliente para tener acceso a FCM y el servidor de aplicaciones. En cambio, la aplicación obtiene de vuelta un registro de token de FCM. 

2.  Reenvía el símbolo (token) de registro para el servidor de aplicaciones si así lo requiere el servidor de aplicaciones.

Agregar un nuevo archivo denominado **MyFirebaseIIDService.cs** y reemplace el código de plantilla con lo siguiente: 

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

Este servicio implementa un `OnTokenRefresh` método que se invoca cuando el token de registro inicialmente se creó o cambió. Cuando `OnTokenRefresh` se ejecuta, recupera el símbolo (token) de la más reciente desde el `FirebaseInstanceId.Instance.Token` propiedad (que se actualiza de forma asincrónica mediante FCM). En este ejemplo, el token actualizado se registra por lo que puede verse en la ventana de salida: 

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` se invoca con poca frecuencia: se utiliza para actualizar el token en las siguientes circunstancias: 

-   Al instalar o desinstalar la aplicación.

-   Cuando el usuario elimina datos de la aplicación. 

-   Cuando la aplicación borra el identificador de instancia.

-   Cuando se afectó la seguridad del token.

Función de Google [Id. de instancia](https://developers.google.com/instance-id/guides/android-implementation) documentación, el servicio de Id. de instancia FCM solicitará que la aplicación actualizar su token periódicamente (por lo general, cada 6 meses). 

`OnTokenRefresh` También llama a `SendRegistrationToAppServer` para asociar el registro del usuario token con la cuenta de servidor (si existe) que se mantiene por la aplicación: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Dado que esta implementación depende del diseño del servidor de aplicaciones, un cuerpo del método vacío se proporciona en este ejemplo. Si el servidor de aplicaciones requiere información de registro FCM, modificar `SendRegistrationToAppServer` para asociar el token del usuario FCM instancia identificador a cualquier cuenta de servidor mantenida por la aplicación. (Tenga en cuenta que el token es opaco para la aplicación de cliente). 

Cuando se envía un token para el servidor de aplicaciones, `SendRegistrationToAppServer` debe mantener un valor booleano para indicar si el token se ha enviado al servidor. Si este valor booleano es false, `SendRegistrationToAppServer` envía el token para el servidor de aplicaciones &ndash; en caso contrario, el token ya se envió al servidor de aplicaciones en una llamada anterior. En algunos casos (por ejemplo, esto `FCMClient` ejemplo), el servidor de aplicaciones no es necesario el token; por lo tanto, este método no es necesario para este ejemplo. 

## <a name="implement-client-app-code"></a>Implementar código de la aplicación de cliente

Ahora que están instalados los servicios del receptor, se puede escribir código de aplicación de cliente para aprovechar las ventajas de estos servicios. En las secciones siguientes, se agrega un botón a la interfaz de usuario para iniciar el símbolo (token) de registro (también denominado el *token de Id. de instancia*), y se agregará más código al `MainActivity` para ver `Intent` información cuando se inicia la aplicación desde un notificación: 

[![Botón de símbolo (token) de registro agregado a la pantalla de la aplicación](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Tokens de registro

El código agregado en este paso está pensado únicamente para fines de demostración &ndash; una aplicación de cliente de producción no tendría necesidad de iniciar sesión tokens de registro. Editar **Resources/layout/Main.axml** y agregue el siguiente `Button` declaración inmediatamente después de la `TextView` elemento: 

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Editar **MainActivity.cs** y agregue la siguiente declaración de miembro para el `MainActivity` clase:

```csharp
const string TAG = "MainActivity";
```

Agregue el código siguiente al final de la `OnCreate` método: 

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Este código registra el token actual en la ventana de salida cuando el **registro Token** botón que se derivan. 

### <a name="handle-notification-intents"></a>Controlar los intentos de notificación

Cuando el usuario puntea una notificación que se emiten desde **FCMClient**, cualquier dato que acompaña a esa notificación de mensaje se pone a disposición en `Intent` extras. Editar **MainActivity.cs** y agregue el código siguiente a la parte superior de la `OnCreate` (método) (antes de llamar a `IsPlayServicesAvailable`): 

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

Selector de la aplicación `Intent` se desencadena cuando el usuario puntea el mensaje de notificación, por lo que este código registrará los datos adjuntos el `Intent` en la ventana de salida. Si otro `Intent` debe activarse el `click_action` campo el mensaje de notificación se debe establecer en el que `Intent` (el iniciador `Intent` se usa cuando no `click_action` se especifica). 


## <a name="background-notifications"></a>Notificaciones de fondo

Compile y ejecute la **FCMClient** aplicación. El **registro Token** botón se muestra:

[![Se muestra el botón de símbolo (token) de registro](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Pulse la **registro Token** botón. Debe mostrarse un mensaje similar al siguiente en la ventana de salida IDE: 

[![Símbolo (token) de Id. de instancia aparece en la ventana de salida](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

La cadena larga etiquetadas con **token** es el token de Id. de instancia que se pega en la consola de Firebase &ndash; seleccionar y copiar esta cadena en el Portapapeles. Si no ve un token de Id. de instancia, agregue la siguiente línea a la parte superior de la `OnCreate` método para comprobar que **services.json google** se analizó correctamente:

```csharp
Log.Debug(TAG, "google app id: " + Resource.String.google_app_id);
```

El `google_app_id` debe coincidir con el valor registrado en la ventana de salida el `mobilesdk_app_id` valor registrado en **services.json google**. 

### <a name="send-a-message"></a>Enviar un mensaje

Inicie sesión en el [Firebase consola](https://console.firebase.google.com), seleccione el proyecto, haga clic en **notificaciones**y haga clic en **enviar su primer mensaje**: 

[![El primer mensaje botón Enviar](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

En el **redactar mensajes** página, escriba el texto del mensaje y seleccione **único dispositivo**. Copie el token de Id. de instancia de la ventana de salida IDE y péguelo en el **token de registro FCM** campo de la consola de Firebase: 

[![Crear cuadro de diálogo de mensaje](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

En el dispositivo Android (o emulador), en segundo plano la aplicación punteando el Android **Introducción** botón y tocar la pantalla principal. Cuando el dispositivo esté listo, haga clic en **enviar mensaje** en la consola de Firebase: 

[![Enviar mensajes (botón)](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Cuando el **mensaje revisión** se muestra el cuadro de diálogo, haga clic en **enviar**.
El icono de notificación debe aparecer en el área de notificación del dispositivo (o el emulador): 

[![Se muestra el icono de notificación](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Abra el icono de notificación para ver el mensaje. El mensaje de notificación debe tener exactamente lo que se escribió en el **texto del mensaje** campo de la consola de Firebase: 

[![Mensaje de notificación se muestra en el dispositivo](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Pulse el icono de notificación para volver a la **FCMClient** aplicación. El `Intent` adicionales que se envía a **FCMClient** se muestran en la ventana de salida IDE: 

[![Listas adicionales de intención de clave, el Id. de mensaje y la clave de contraer](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

En este ejemplo, el **de** clave se establece en el número de proyecto Firebase de la aplicación (en este ejemplo, `41590732`) y el **collapse_key** está establecido en su nombre de paquete ( **com.xamarin.fcmexample**). Si no recibe un mensaje, intente eliminar el **FCMClient** aplicación en el dispositivo (o el emulador) y repita los pasos anteriores. 


> [!NOTE]
> Si se fuerza, cierre la aplicación, FCM dejará de entrega de notificaciones. Android impide que las difusiones de servicios de fondo sin darse cuenta o innecesariamente ejecuten componentes de las aplicaciones detenidas. (Para obtener más información acerca de este comportamiento, consulte [iniciar controles en aplicaciones detenidas](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) Por esta razón, es necesario que desinstale manualmente la aplicación cada vez que se ejecuta y detenerla desde una sesión de depuración &ndash; Esto obliga a FCM para generar un nuevo token de manera que se siguen recibiendo los mensajes.

### <a name="add-a-custom-default-notification-icon"></a>Agregar un icono de notificación predeterminado personalizado

En el ejemplo anterior, el icono de notificación se establece en el icono de la aplicación. El código XML siguiente configura un icono predeterminado personalizado para las notificaciones. Android muestra este icono personalizada predeterminada para todos los mensajes de notificación en el icono de notificación no se establece explícitamente. 

Para agregar un icono de notificación personalizada predeterminada, el icono para agregar la **/puede dibujar recursos** directorio, editar **AndroidManifest.xml**e inserte el siguiente `<meta-data>` elemento en el `<application>`sección: 

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

En este ejemplo, el icono de notificación que reside en **recursos, puede dibujar/ic\_stat\_ic\_notification.png** se usará como icono de notificación personalizada predeterminada. Si un icono personalizado predeterminado no está configurado en **AndroidManifest.xml** y ningún icono está establecido en la carga de notificación, Android utiliza el icono de la aplicación como el icono de notificación (como se muestra en la captura de pantalla de icono de notificación anterior). 

## <a name="handle-topic-messages"></a>Controlar los mensajes de tema

El código escrito hasta ahora administra tokens de registro y agrega la funcionalidad de notificación de remoto a la aplicación. En el ejemplo siguiente se agrega código que realiza escuchas de *mensajes del tema* y los reenvía al usuario remotas como notificaciones. Mensajes de tema son mensajes FCM que se envían a uno o varios dispositivos que se suscriben a un tema determinado. Para obtener más información acerca de los mensajes de tema, consulte [tema mensajería](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

### <a name="subscribe-to-a-topic"></a>Suscribirse a un tema

Editar **Resources/layout/Main.axml** y agregue el siguiente `Button` declaración inmediatamente después de la anterior `Button` elemento:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Este código XML agrega una **suscribirse a notificaciones** botón para el diseño.
Editar **MainActivity.cs** y agregue el código siguiente al final de la `OnCreate` método: 

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Este código localiza el **suscribirse a notificaciones** botón en el diseño y asigna su controlador de clic al código que llama a `FirebaseMessaging.Instance.SubscribeToTopic`, pasando el tema suscrito, _noticias_. Cuando el usuario puntea el **suscribir** botón, la aplicación se suscribe a la _noticias_ tema. En la sección siguiente, un _noticias_ se enviará el mensaje de tema de la GUI de las notificaciones de la consola de Firebase. 

### <a name="send-a-topic-message"></a>Enviar un mensaje de tema

Desinstalar la aplicación, vuelva a compilarlo y ejecutarlo de nuevo. Haga clic en el **suscribirse a notificaciones** botón:

[![Suscribirse al botón de notificaciones](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Si la aplicación se ha suscrito correctamente, debería ver **sincronización de tema se ha realizado correctamente** en el IDE de la ventana de salida: 

[![Ventana de salida muestra el mensaje del tema sincronización se realizó correctamente](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Siga estos pasos para enviar un mensaje de tema:

1.  En la consola Firebase, haga clic en **nuevo mensaje**. 

2.  En el **redactar mensajes** página, escriba el texto del mensaje y seleccione **tema**. 

3.  En el **tema** menú desplegable, seleccione el tema integrado, **noticias**: 

    [![Al seleccionar el tema de noticias](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  En el dispositivo Android (o emulador), en segundo plano la aplicación punteando el Android **Introducción** botón y tocar la pantalla principal. 

5.  Cuando el dispositivo esté listo, haga clic en **enviar mensaje** en la consola de Firebase. 

6.  Consulte la ventana de salida IDE para ver **/temas/noticias** en los resultados del registro: 

    [![Se muestra el mensaje de /topic/news](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Cuando este mensaje se ve en la ventana de salida, el icono de notificación también debe aparecer en el área de notificación en el dispositivo Android. Abra el icono de notificación para ver el mensaje del tema: 

[![El mensaje del tema aparece como una notificación](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Si no recibe un mensaje, intente eliminar el **FCMClient** aplicación en el dispositivo (o el emulador) y repita los pasos anteriores. 

## <a name="foreground-notifications"></a>Notificaciones de primer plano

Para recibir notificaciones en aplicaciones foregrounded, debe implementar `FirebaseMessagingService`. Este servicio también es necesario para la recepción de cargas de datos y para enviar mensajes de nivel superior. Los ejemplos siguientes muestran cómo implementar un servicio que se extienda `FirebaseMessagingService` &ndash; la aplicación resultante será capaz de administrar notificaciones remotas mientras se está ejecutando en primer plano. 

### <a name="implement-firebasemessagingservice"></a>Implementar FirebaseMessagingService

El `FirebaseMessagingService` servicio incluye una `OnMessageReceived` método que escriba para controlar los mensajes entrantes. Tenga en cuenta que `OnMessageReceived` se invoca para mensajes de notificación *sólo* cuando se ejecuta la aplicación en primer plano. Cuando la aplicación se ejecuta en segundo plano, se muestra una notificación generada automáticamente (como se muestra anteriormente en este tutorial). 

Agregar un nuevo archivo denominado **MyFirebaseMessagingService.cs** y reemplace el código de plantilla con lo siguiente: 

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

Tenga en cuenta que la `MESSAGING_EVENT` filtro intención se debe declarar para que los nuevos mensajes FCM se dirigen a `MyFirebaseMessagingService`: 

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Cuando la aplicación cliente recibe un mensaje de FCM, `OnMessageReceived` extrae el contenido del mensaje en el pasado `RemoteMessage` objeto mediante una llamada a su `GetNotification` método. A continuación, registra el contenido del mensaje para que se puede ver en la ventana de salida IDE: 

```csharp
Log.Debug(TAG, "From: " + message.From);
Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
```

> [!NOTE]
> Si establece los puntos de interrupción `FirebaseMessagingService`, la sesión de depuración puede o no se puede establecer estos puntos de interrupción debido a cómo FCM entrega mensajes.
 

### <a name="send-another-message"></a>Enviar otro mensaje

Desinstalar la aplicación, vuelva a compilarlo, vuelva a ejecutarlo y siga estos pasos para enviar otro mensaje:

1.  En la consola Firebase, haga clic en **nuevo mensaje**. 

2.  En el **redactar mensajes** página, escriba el texto del mensaje y seleccione **único dispositivo**. 

3.  Copiar la cadena de token de la ventana de salida IDE y péguelo en el **token de registro FCM** campo de la consola de Firebase como antes. 

4.  Asegúrese de que se ejecuta la aplicación en primer plano, a continuación, haga clic en **enviar mensaje** en la consola de Firebase: 

    [![Enviar otro mensaje desde la consola de](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  Cuando el **mensaje revisión** se muestra el cuadro de diálogo, haga clic en **enviar**.

6.  El mensaje entrante se registra en la ventana de salida IDE:

    [![El cuerpo del mensaje se imprime en la ventana de salida](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notifications-sender"></a>Agregar un remitente de notificaciones Local

En este ejemplo restante, el mensaje entrante de FCM se convertirá en una notificación local que se inicia mientras se ejecuta la aplicación en primer plano. Editar **MyFirebaseMessageService.cs** y agregue el siguiente `using` instrucciones: 

```csharp
using FCMClient;
using System.Collections.Generic;
```

Agregue el método siguiente a `MyFirebaseMessagingService`: 

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (string key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }
    var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new Notification.Builder(this)
        .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
        .SetContentTitle("FCM Message")
        .SetContentText(messageBody)
        .SetAutoCancel(true)
        .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManager.FromContext(this);
    notificationManager.Notify(0, notificationBuilder.Build());
}
```

Para distinguir esta notificación de las notificaciones de fondo, este código marca notificaciones con un icono diferente desde el icono de applicatiion. Agregar [ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) a **/puede dibujar recursos** e incluirla en el **FCMClient** proyecto. 

El `SendNotification` método usa `Notification.Builder` para crear la notificación, y `NotificationManager` se utiliza para iniciar la notificación. La notificación contiene un `PendingIntent` que permitirá al usuario que abra la aplicación y ver el contenido de la cadena pasada a `messageBody`. Dependiendo de las versiones de Android que piensa tener como destino con la aplicación, puede que desee utilizar `NotificationCompat.Builder` en su lugar. Para obtener más información acerca de `Notification.Builder`, consulte [notificaciones Local](~/android/app-fundamentals/notifications/local-notifications.md). 

Llame a la `SendNotification` método al final de la `OnMessageReceived` método: 

```csharp
SendNotification(message.GetNotification().Body, message.Data);
```

Como resultado de estos cambios, `SendNotification` se ejecutará cada vez que se recibe una notificación mientras la aplicación está en primer plano y la notificación aparecerá en el área de notificación. Si la aplicación es backgrounded, `SendNotification` no se ejecutará y, en su lugar, se iniciará una notificación de fondo (que se muestra anteriormente en este tutorial). 

### <a name="send-the-last-message"></a>Enviar el último mensaje

Desinstalar la aplicación, vuelva a compilarlo, ejecútelo de nuevo y luego siga estos pasos para enviar el último mensaje de error:

1.  En la consola Firebase, haga clic en **nuevo mensaje**. 

2.  En el **redactar mensajes** página, escriba el texto del mensaje y seleccione **único dispositivo**. 

3.  Copiar la cadena de token de la ventana de salida IDE y péguelo en el **token de registro FCM** campo de la consola de Firebase como antes. 

4.  Asegúrese de que se ejecuta la aplicación en primer plano, a continuación, haga clic en **enviar mensaje** en la consola de Firebase: 

    [![Enviar el mensaje de primer plano](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

En esta ocasión, el mensaje que se ha registrado en la ventana de salida también se empaqueta en una nueva notificación &ndash; el icono de notificación aparece en la Bandeja de notificación mientras se ejecuta la aplicación en primer plano: 

[![Icono de notificación de mensaje de primer plano](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Cuando se abre la notificación, debería ver el último mensaje que se envió desde la GUI de las notificaciones de la consola de Firebase: 

[![Notificación de primer plano que se muestra con el icono de primer plano](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)

 
## <a name="troubleshooting"></a>Solución de problemas

Los siguientes problemas de describir y soluciones alternativas que pueden surgir al usar la mensajería de nube de Firebase con Xamarin.Android.

### <a name="firebaseapp-is-not-initialized"></a>No se ha inicializado FirebaseApp

En algunos casos, puede aparecer este mensaje de error:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Se trata de un problema conocido que se puede solucionar mediante la limpieza de la solución y volver a generar el proyecto (**generar > Limpiar solución**, **generar > volver a generar solución**). Para obtener más información, vea este [foro de debate](https://forums.xamarin.com/discussion/96263/default-firebaseapp-is-not-initialized-in-this-process).


## <a name="summary"></a>Resumen

En este tutorial se detallan los pasos para implementar Firebase Cloud Messaging remotas notificaciones en una aplicación Xamarin.Android. Describe cómo instalar los paquetes necesarios necesarios para communications FCM y explica cómo configurar el manifiesto de Android para tener acceso a servidores FCM. Proporciona código de ejemplo que muestra cómo comprobar la presencia de servicios de Google Play. Muestra cómo implementar un servicio de escucha de Id. de instancia que negocia con FCM para obtener un token de registro, y explica cómo este código crea las notificaciones de fondo mientras la aplicación se backgrounded. Explica cómo suscribirse a mensajes del tema y proporciona un ejemplo de implementación de un servicio de agente de escucha de mensaje que se utiliza para recibir y mostrar notificaciones remotas mientras se ejecuta la aplicación en primer plano. 


## <a name="related-links"></a>Vínculos relacionados

- [FCMNotifications (ejemplo)](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Mensajería en la nube de Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
