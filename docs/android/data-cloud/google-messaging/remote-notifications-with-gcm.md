---
title: "Notificaciones remotas con la mensajería de nube de Google"
description: "Este tutorial proporciona una explicación paso a paso de cómo usar Google Cloud Messaging para implementar notificaciones remotas (también denominadas notificaciones de inserción) en una aplicación Xamarin.Android. Describe las diferentes clases que deben implementar para comunicarse con Google (GCM Cloud Messaging), se explica cómo establecer permisos en el manifiesto de Android para tener acceso a GCM y muestra-to-end de mensajería con un programa de prueba de ejemplo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: 823fad163e837adab5490446c23ab2f492679114
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Notificaciones remotas con la mensajería de nube de Google

_Este tutorial proporciona una explicación paso a paso de cómo usar Google Cloud Messaging para implementar notificaciones remotas (también denominadas notificaciones de inserción) en una aplicación Xamarin.Android. Describe las diferentes clases que deben implementar para comunicarse con Google (GCM Cloud Messaging), se explica cómo establecer permisos en el manifiesto de Android para tener acceso a GCM y muestra-to-end de mensajería con un programa de prueba de ejemplo._

## <a name="gcm-notifications-overview"></a>Información general de las notificaciones de GCM

En este tutorial, vamos a crear una aplicación de Xamarin.Android que usa Conexión compartida a Internet (GCM, Google Cloud Messaging) para implementar notificaciones remotas (también conocido como *notificaciones de inserción*). Implementaremos los diversos servicios de intento y el agente de escucha que use GCM para mensajería remoto y lo probaremos nuestra implementación con un programa de línea de comandos que simula un servidor de aplicaciones. 

Tenga en cuenta que la mensajería de nube de Firebase (FCM) es la nueva versión de GCM &ndash; Google recomienda encarecidamente usar FCM en lugar de GCM. Si actualmente utilizas GCM, se recomienda actualizar a FCM. Para obtener más información acerca de FCM, consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Antes de continuar con este tutorial, debe adquirir las credenciales necesarias para utilizar servidores de GCM de Google; Este proceso se explica en [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md). En concreto, necesitará un *clave de API* y un *Id. de remitente* para insertar en el código de ejemplo en este tutorial se presentan. 

Vamos a usar los pasos siguientes para crear una aplicación de cliente habilitada GCM Xamarin.Android:

1.  Instalar paquetes adicionales necesarios para las comunicaciones con servidores de GCM.
2.  Configurar los permisos de aplicación para el acceso a servidores de GCM.
3.  Implemente el código para comprobar la presencia de servicios de Google Play. 
4.  Implementar un servicio de intención de registro que negocia con GCM para obtener un token de registro.
5.  Implementar un servicio de agente de escucha de Id. de instancia que realiza escuchas para las actualizaciones de símbolo (token) de registro de GCM.
6.  Implementar un servicio de agente de escucha GCM que reciba mensajes remotos desde el servidor de aplicaciones a través de GCM.

Esta aplicación usará una nueva característica GCM conocida como *tema mensajería*. En el tema de mensajería, el servidor de aplicaciones envía un mensaje a un tema, en lugar de a una lista de dispositivos individuales. Dispositivos que se suscriben a ese tema pueden recibir mensajes del tema como notificaciones de inserción. Para obtener más información acerca de la mensajería de tema GCM, consulte de Google [implementar mensajería tema](https://developers.google.com/cloud-messaging/topic-messaging). 

Cuando la aplicación cliente está listo, se implementará una aplicación de línea de comandos de C# que envía una notificación de inserción a nuestra aplicación de cliente a través de GCM. 

## <a name="walkthrough"></a>Tutorial

Para empezar, vamos a crear una nueva solución vacía denominada **RemoteNotifications**. A continuación, vamos a agregar un nuevo proyecto Android a esta solución que se basa en el **aplicación Android** plantilla. Vamos a llamar a este proyecto **ClientApp**. (Si no está familiarizado con la creación de proyectos de Xamarin.Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).) El **ClientApp** proyecto contendrá el código de la aplicación de cliente de Xamarin.Android que recibe notificaciones de remotas a través de GCM. 

### <a name="add-required-packages"></a>Agregar paquetes necesarios

Antes de que podemos implementar el código de aplicación de cliente, deberemos instalar varios paquetes que vamos a usar para la comunicación con GCM. Además, debemos agregar la aplicación de Google Play Store en el dispositivo si aún no está instalado.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Agregue el paquete GCM Xamarin Google Play Services

Para recibir mensajes de mensajería de nube de Google, el [servicios de Google Play](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) framework debe estar presente en el dispositivo. Sin este marco de trabajo, una aplicación de Android no puede recibir mensajes de los servidores GCM. Servicios de Google Play se ejecuta en segundo plano mientras el dispositivo Android está encendido, silenciosamente realizar escuchas de mensajes de GCM. Cuando llegan estos mensajes, servicios de Google Play convierte los mensajes en intentos y, a continuación, transmite estos intentos para las aplicaciones que se han registrado en ellos. 

En Visual Studio, haga clic en **referencias > Administrar paquetes de NuGet...** ; en Visual Studio para Mac, haga clic en **paquetes > Agregar paquetes...** . Busque **Xamarin servicios de Google Play - GCM** e instalar este paquete en el **ClientApp** proyecto: 

[![Instalar servicios de Google Play](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

Al instalar **Xamarin servicios de Google Play - GCM**, **Xamarin servicios de Google Play - Base** se instala automáticamente. Si recibe un error, cambie el proyecto *mínimo Android al destino* si se establece en un valor distinto de **compilar con la versión SDK** y vuelva a intentar la instalación de NuGet. 

A continuación, edite **MainActivity.cs** y agregue el siguiente `using` instrucciones:

```csharp
using Android.Gms.Common;
using Android.Util;
```

De esta forma tipos en el paquete GMS de Google Play Services disponibles para el código y agrega la funcionalidad de registro que se usará para realizar el seguimiento de nuestras transacciones con GMS. 

#### <a name="google-play-store"></a>Google Play Store

Para recibir mensajes de GCM, la aplicación de Google Play Store debe instalarse en el dispositivo. (Cada vez que una aplicación de Google Play está instalada en un dispositivo, Google Play Store también está instalado, por lo que es probable que ya está instalado en el dispositivo de prueba.) Sin Google Play, una aplicación de Android no puede recibir mensajes de GCM. Si aún no dispone de la aplicación de Google Play Store instalada en el dispositivo, visite la [Google Play](https://support.google.com/googleplay) sitio web para descargar e instalar Google Play. 

Como alternativa, puede usar un emulador de Android con Android 2.2 o posterior en lugar de un dispositivo de prueba (no es necesario instalar Google Play Store en un emulador de Android). Sin embargo, si usa un emulador, debe usar Wi-Fi para conectarse a GCM y debe abrir varios puertos en el firewall de Wi-Fi tal y como se explica más adelante en este tutorial. 

### <a name="set-the-package-name"></a>Establece el nombre del paquete

En [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md), se especifica un nombre de paquete para la aplicación habilitada para GCM (este nombre de paquete también sirve como la *identificador de la aplicación* que está asociado a la clave de API y el Id. de remitente). Abra las propiedades de la **ClientApp** del proyecto y establezca el nombre del paquete para esta cadena. En este ejemplo, se establece el nombre del paquete en `com.xamarin.gcmexample`:

[![Establecer el nombre del paquete](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Tenga en cuenta que la aplicación cliente no podrá recibir un token de registro de GCM si este nombre de paquete no *exactamente* coincide con el nombre del paquete que se especificó en la consola de Google para desarrolladores. 

### <a name="add-permissions-to-the-android-manifest"></a>Agregar permisos al manifiesto de Android

Una aplicación de Android debe tener los siguientes permisos configurados para poder recibir notificaciones de Google Cloud Messaging: 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; Concede permiso para nuestra aplicación para registrar y recibir mensajes de mensajería de nube de Google. (¿Qué `c2dm` significa? Esto es el acrónimo _en la nube a Device Messaging_, que es el predecesor ahora desusado de GCM. 
    GCM sigue usando `c2dm` en muchos de sus cadenas de permiso.) 

-   `android.permission.WAKE_LOCK` &ndash; (Opcional) Impide que se pase al modo de suspensión mientras escuchaba de un mensaje de la CPU de dispositivo. 

-   `android.permission.INTERNET` &ndash; Concede acceso a internet de forma que la aplicación cliente puede comunicarse con GCM. 

-   *nombreDePaquete* `.permission.C2D_MESSAGE` &ndash; registra la aplicación en Android y solicita permiso para recibir exclusivamente C2D todos los mensajes (en la nube al dispositivo). El *nombreDePaquete* prefijo es el mismo que el identificador de aplicación. 

Estos permisos se configuran en el manifiesto de Android. Editar **AndroidManifest.xml** y reemplace el contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
    package="YOUR_PACKAGE_NAME" 
    android:versionCode="1" 
    android:versionName="1.0" 
    android:installLocation="auto">
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />
    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" 
                android:protectionLevel="signature" />
    <application android:label="ClientApp" android:icon="@drawable/Icon">
    </application>
</manifest>
```

En el XML anterior, cambie *YOUR_PACKAGE_NAME* para el nombre del paquete para el proyecto de aplicación de cliente. Por ejemplo: `com.xamarin.gcmexample`. 

### <a name="check-for-google-play-services"></a>Busque servicios Google Play

En este tutorial, estamos creando una aplicación básica con una sola `TextView` en la interfaz de usuario. Esta aplicación no indica directamente la interacción con GCM. En su lugar, se podrá ver la ventana de salida para ver cómo nuestro protocolos de aplicación con GCM, y comprobaremos la Bandeja de notificación para las notificaciones de la nueva medida que llegan. 

En primer lugar, vamos a crear un diseño para el área de mensajes. Editar **Resources.layout.Main.axml** y reemplace el contenido con el siguiente código XML: 

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

Guardar **Main.axml** y ciérrelo.

Cuando se inicia la aplicación cliente, es deseable para comprobar que los servicios de Google Play está disponible antes de que se intente ponerse en contacto con GCM. Editar **MainActivity.cs** y reemplace el ``count`` declaración con la siguiente declaración de variable de instancia de la variable de instancia: 

```csharp
TextView msgText;
```

A continuación, agregue el método siguiente a la **MainActivity** clase: 

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
            msgText.Text = "Sorry, this device is not supported";
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

Este código comprueba el dispositivo para saber si está instalado el APK de servicios de Google reproducir. Si no está instalado, se muestra un mensaje en el área de mensaje que solicita al usuario que descargue un APK de Google Play Store (o habilitarlo en configuración del sistema del dispositivo). Puesto que deseamos ejecutar esta comprobación cuando se inicia la aplicación cliente, agregaremos una llamada a este método al final de `OnCreate`. 


A continuación, reemplace el `OnCreate` método por el código siguiente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

Este código comprueba la presencia de la APK de servicios de Google reproducir y escribe el resultado en el área de mensajes. 

Completamente vamos a volver a generar y ejecutar la aplicación. Debería ver una pantalla similar a la captura de pantalla siguiente: 

[![Servicios de Google Play está disponible](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Si no obtiene este resultado, compruebe que el APK de servicios de Google reproducir esté instalado en el dispositivo y que la **Xamarin servicios de Google Play - GCM** paquete se agrega a su **ClientApp** proyecto tal como se describe versiones anteriores. Si se produce un error de compilación, intente limpiar la solución y volver a compilar el proyecto de nuevo. 

A continuación, se escribirá código para ponerse en contacto con GCM y obtener un token de registro.

### <a name="register-with-gcm"></a>Registrar con GCM

Antes de que la aplicación puede recibir notificaciones remotas desde el servidor de aplicaciones, debe registrar con GCM y regresar un token de registro. El trabajo de registro de la aplicación con GCM se controla mediante un `IntentService` que creamos. Nuestro `IntentService` realiza los pasos siguientes: 

1.  Usa el [InstanceID](https://developers.google.com/instance-id/) API para generar tokens de seguridad que autorizan a nuestra aplicación de cliente para tener acceso al servidor de aplicaciones. En cambio, obtenemos un registro de token de GCM.

2.  Envía el token de registro para el servidor de aplicaciones (si lo requiere el servidor de aplicaciones).

3.  Se suscribe a uno o varios canales de tema de notificación.

Después de que implementamos este `IntentService`, se probará para ver si obtenemos un registro de token de GCM.

Agregar un nuevo archivo denominado **RegistrationIntentService.cs** y reemplace el código de plantilla con el siguiente:


```csharp
using System;
using Android.App;
using Android.Content;
using Android.Util;
using Android.Gms.Gcm;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false)]
    class RegistrationIntentService : IntentService
    {
        static object locker = new object();

        public RegistrationIntentService() : base("RegistrationIntentService") { }

        protected override void OnHandleIntent (Intent intent)
        {
            try
            {
                Log.Info ("RegistrationIntentService", "Calling InstanceID.GetToken");
                lock (locker)
                {
                    var instanceID = InstanceID.GetInstance (this);
                    var token = instanceID.GetToken (
                        "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);

                    Log.Info ("RegistrationIntentService", "GCM Registration Token: " + token);
                    SendRegistrationToAppServer (token);
                    Subscribe (token);
                }
            }
            catch (Exception e)
            {
                Log.Debug("RegistrationIntentService", "Failed to get a registration token");
                return;
            }
        }

        void SendRegistrationToAppServer (string token)
        {
            // Add custom implementation here as needed.
        }

        void Subscribe (string token)
        {
            var pubSub = GcmPubSub.GetInstance(this);
            pubSub.Subscribe(token, "/topics/global", null);
        }
    }
}
```

En el ejemplo de código anterior, cambie *YOUR_SENDER_ID* en el número de Id. de remitente para el proyecto de aplicación de cliente. Para obtener el identificador del remitente para el proyecto: 

1.  Inicie sesión en el [consola en la nube de Google](https://console.cloud.google.com/) y seleccione el nombre del proyecto en el menú desplegable. En el **información del proyecto** panel que se muestra para el proyecto, haga clic en **vaya a configuración del proyecto**:

    [![Al seleccionar proyecto XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  En el **configuración** página, busque el **número del proyecto** &ndash; es el identificador de remitente para el proyecto:

    [![Muestra el número de proyecto](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Desea iniciar nuestro `RegistrationIntentService` cuando nuestra aplicación empieza a ejecutarse. Editar **MainActivity.cs** y modificar el `OnCreate` método para que nuestro `RegistrationIntentService` se inicia después de que se compruebe la presencia de servicios de Google Play: 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView(Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    if (IsPlayServicesAvailable ())
    {
        var intent = new Intent (this, typeof (RegistrationIntentService));
        StartService (intent);
    }
}
```

Ahora Echemos un vistazo a cada sección de `RegistrationIntentService` para entender cómo funciona. 

En primer lugar, se anote nuestro `RegistrationIntentService` con el siguiente atributo para indicar que nuestro servicio no se puede crear una instancia por el sistema: 

```csharp
[Service (Exported = false)]
```

El `RegistrationIntentService` constructor nombres el subproceso de trabajo *RegistrationIntentService* para simplificar la depuración. 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

La funcionalidad principal de `RegistrationIntentService` reside en el `OnHandleIntent` método. Analicemos este código para ver cómo registra la aplicación con GCM.


##### <a name="request-a-registration-token"></a>Solicitar un Token de registro

`OnHandleIntent` llama primero de Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) método para solicitar un token de registro de GCM. Se ajusta este código en un `lock` para protegerse contra la posibilidad de que se están produciendo simultáneamente varios intentos de registro &ndash; el `lock` garantiza que estos intentos se procesan secuencialmente. Si no se puede obtener un token de registro, se produce una excepción y se registrará un error. Si el registro es satisfactorio, `token` se establece en el símbolo (token) de registro que recuperamos de GCM: 

```csharp
static object locker = new object ();
...
try
{
    lock (locker)
    {
        var instanceID = InstanceID.GetInstance (this);
        var token = instanceID.GetToken (
            "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);
        ...
    }
}
catch (Exception e)
{
    Log.Debug ...
```

##### <a name="forward-the-registration-token-to-the-app-server"></a>Reenviar el Token de registro para el servidor de aplicaciones

Si se produce un token de registro (es decir, se ha producido ninguna excepción), llamamos a `SendRegistrationToAppServer` para asociar el registro del usuario token con la cuenta de servidor (si existe) que se mantiene por nuestra aplicación. Dado que esta implementación depende del diseño del servidor de aplicación, aquí se proporciona un método vacío: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

En algunos casos, el servidor de aplicaciones no es necesario el token del usuario registro; en ese caso, se puede omitir este método. Cuando se envía un token de registro para el servidor de aplicaciones, `SendRegistrationToAppServer` debe mantener un valor booleano para indicar si el token se ha enviado al servidor. Si este valor booleano es false, `SendRegistrationToAppServer` envía el token para el servidor de aplicaciones &ndash; en caso contrario, el token ya se envió al servidor de aplicaciones en una llamada anterior. 


##### <a name="subscribe-to-the-notification-topic"></a>Suscribirse en el tema de notificación

A continuación, llamamos a nuestro `Subscribe` método para indicar a GCM que se va a suscribirse a un tema de notificación. En `Subscribe`, llamamos a la [GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) API para suscribirse a nuestra aplicación de cliente para todos los mensajes en `/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

El servidor de aplicaciones debe enviar mensajes de notificación `/topics/global` si estamos para recibirlos. Tenga en cuenta que el nombre del tema en `/topics` puede ser que desee, siempre y cuando el servidor de aplicaciones y la aplicación cliente acuerden estos nombres. (En este caso, se ha elegido el nombre `global` para indicar que desea recibir mensajes en todos los temas compatibles con el servidor de aplicaciones.) 

Para obtener información sobre el tema GCM en el servidor de mensajería, consulte de Google [enviar mensajes a temas](https://developers.google.com/cloud-messaging/topic-messaging). 


#### <a name="implement-an-instance-id-listener-service"></a>Implementar un servicio de agente de escucha de Id. de instancia

Tokens de registro son únicos y segura; Sin embargo, la aplicación de cliente (o GCM) que necesite actualizar el token de registro en el caso de reinstalación de la aplicación o un problema de seguridad. Por este motivo, debemos implementar un `InstanceIdListenerService` que responde a las solicitudes de actualización del token de GCM. 

Agregar un nuevo archivo denominado **InstanceIdListenerService.cs** y reemplace el código de plantilla con el siguiente: 

```csharp
using Android.App;
using Android.Content;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
    class MyInstanceIDListenerService : InstanceIDListenerService
    {
        public override void OnTokenRefresh()
        {
            var intent = new Intent (this, typeof (RegistrationIntentService));
            StartService (intent);
        }
    }
}
```

Anotar `InstanceIdListenerService` con el siguiente atributo para indicar que el servicio no se puede crear una instancia por el sistema y que se puede recibir el token de registro GCM (también denominada *Id. de instancia*) solicitudes de actualización: 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

El `OnTokenRefresh` método en nuestro servicio inicia el `RegistrationIntentService` para que puede interceptar el nuevo token de registro.


#### <a name="test-registration-with-gcm"></a>Registro de prueba con GCM

Completamente vamos a volver a generar y ejecutar la aplicación. Si se recibe correctamente un token de registro de GCM, el token de registro debe mostrarse en la ventana de salida. Por ejemplo: 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Controlar los mensajes de nivel inferiores 

El código que hemos implementado hasta el momento es solo el código de "configuración"; realiza una comprobación para ver si Servicios de Google Play está instalado y negocia con GCM y el servidor de aplicaciones para preparar la aplicación de cliente para recibir notificaciones remotas. Sin embargo, todavía se tiene que implementar código que verdaderamente recibe y procesa los mensajes de notificación de nivel inferior. Para hacer esto, debemos implementar un *servicio de agente de escucha de GCM*. Este servicio recibe los mensajes de tema desde el servidor de aplicaciones y localmente los difunde como notificaciones. Después de que implementamos este servicio, vamos a crear un programa de prueba para enviar mensajes a GCM, por lo que podemos ver si nuestra implementación funciona correctamente. 


#### <a name="add-a-notification-icon"></a>Agregar un icono de notificación

Vamos a agregar un icono pequeño que va a aparecer en el área de notificación cuando se inicia la notificación. Puede copiar [este icono](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) al proyecto o crear su propio icono personalizado. El archivo de icono **ic_stat_button_click.png** y cópielo en el **/puede dibujar recursos** carpeta. Recuerde que debe usar **Agregar > elemento existente...**  para incluir este archivo de icono en su proyecto.


#### <a name="implement-a-gcm-listener-service"></a>Implementar un servicio de agente de escucha GCM

Agregar un nuevo archivo denominado **GcmListenerService.cs** y reemplace el código de plantilla con el siguiente:

```csharp
using Android.App;
using Android.Content;
using Android.OS;
using Android.Gms.Gcm;
using Android.Util;

namespace ClientApp
{
    [Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
    public class MyGcmListenerService : GcmListenerService
    {
        public override void OnMessageReceived (string from, Bundle data)
        {
            var message = data.GetString ("message");
            Log.Debug ("MyGcmListenerService", "From:    " + from);
            Log.Debug ("MyGcmListenerService", "Message: " + message);
            SendNotification (message);
        }

        void SendNotification (string message)
        {
            var intent = new Intent (this, typeof(MainActivity));
            intent.AddFlags (ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity (this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                .SetSmallIcon (Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle ("GCM Message")
                .SetContentText (message)
                .SetAutoCancel (true)
                .SetContentIntent (pendingIntent);

            var notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
            notificationManager.Notify (0, notificationBuilder.Build());
        }
    }
}
```

¡Eche un vistazo a cada sección de nuestro `GcmListenerService` para entender cómo funciona. 

En primer lugar, se anote `GcmListenerService` con un atributo para indicar que este servicio no se puede crear una instancia por el sistema e incluimos un filtro intención para indicar que recibe mensajes GCM: 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Cuando `GcmListenerService` recibe un mensaje de GCM, el `OnMessageReceived` se invoca el método. Este método extrae el contenido del mensaje en el pasado `Bundle`, registra el contenido del mensaje (por lo que podemos verlo en la ventana de salida) y las llamadas `SendNotification` para iniciar una notificación local con el contenido del mensaje recibido: 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

El `SendNotification` método usa `Notification.Builder` crear la notificación y, a continuación, utiliza el `NotificationManager` para iniciar la notificación. De hecho, esto convierte el mensaje de notificación remoto en una notificación local debe presentarse al usuario.
Para obtener más información sobre el uso de `Notification.Builder` y `NotificationManager`, consulte [notificaciones Local](~/android/app-fundamentals/notifications/local-notifications.md).


#### <a name="declare-the-receiver-in-the-manifest"></a>Declare el receptor en el manifiesto

Antes de que se pueden recibir mensajes de GCM, nos debemos declarar el agente de escucha GCM en el manifiesto de Android. Editar **AndroidManifest.xml** y reemplace el `<application>` sección con el siguiente código XML: 

```xml
<application android:label="RemoteNotifications" android:icon="@drawable/Icon">
    <receiver android:name="com.google.android.gms.gcm.GcmReceiver" 
              android:exported="true" 
              android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="YOUR_PACKAGE_NAME" />
        </intent-filter>
    </receiver>
</application>
```

En el XML anterior, cambie *YOUR_PACKAGE_NAME* para el nombre del paquete para el proyecto de aplicación de cliente. En nuestro ejemplo de tutorial, el nombre del paquete es `com.xamarin.gcmexample`. 

Echemos un vistazo a lo que hace cada configuración en este código XML:

|Parámetro|Descripción|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Declara que nuestra aplicación implementa un receptor GCM que captura y procesa los mensajes entrantes de notificaciones de inserción.|
|`com.google.android.c2dm.permission.SEND`|Declara que sólo los servidores de GCM pueden enviar mensajes directamente a la aplicación.|
|`com.google.android.c2dm.intent.RECEIVE`|Filtro intención publicidad que nuestra aplicación controla los mensajes de difusión de GCM.|
|`com.google.android.c2dm.intent.REGISTRATION`|Filtro intención publicidad que nuestra aplicación trata los nuevos intentos de registro (es decir, hemos implementado un servicio de escucha de Id. de instancia).|

Como alternativa, puede decorar `GcmListenerService` con estos atributos en lugar de especificarlos en XML; aquí se especifican en **AndroidManifest.xml** para que los ejemplos de código son más fáciles de seguir. 


### <a name="create-a-message-sender-to-test-the-app"></a>Crear un remitente del mensaje para probar la aplicación

Vamos a agregar un proyecto de aplicación de escritorio de consola de C# a la solución y llámelo **MessageSender**. Vamos a usar esta aplicación de consola para simular un servidor de aplicaciones &ndash; enviará mensajes de notificación a **ClientApp** a través de GCM. 


#### <a name="add-the-jsonnet-package"></a>Agregue el paquete de Json.NET

En esta aplicación de consola, estamos creando una carga JSON que contiene el mensaje de notificación que desea enviar a la aplicación cliente. Vamos a usar la **Json.NET** empaquetar en **MessageSender** para que resulten más fáciles de crear el objeto JSON necesario GCM. En Visual Studio, haga clic en **referencias > Administrar paquetes de NuGet...** ; en Visual Studio para Mac, haga clic en **paquetes > Agregar paquetes...** . 

Busquemos la **Json.NET** empaquetar e instalarlo en el proyecto: 

[![Instalar el paquete de Json.NET](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>Agregue una referencia a System.Net.Http

También se deberá agregar una referencia a `System.Net.Http` para que se puede crear una instancia de un `HttpClient` para enviar el mensaje de prueba a GCM. En el **MessageSender** proyecto contextual **referencias > Agregar referencia** y desplácese hacia abajo hasta que vea **System.Net.Http**. Coloque una marca de verificación junto a **System.Net.Http** y haga clic en **Aceptar**. 


#### <a name="implement-code-that-sends-a-test-message"></a>Implementar el código que envía un mensaje de prueba

En **MessageSender**, editar **Program.cs** y reemplace el contenido con el código siguiente:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

namespace MessageSender
{
    class MessageSender
    {
        public const string API_KEY = "YOUR_API_KEY";
        public const string MESSAGE = "Hello, Xamarin!";

        static void Main (string[] args)
        {
            var jGcmData = new JObject();
            var jData = new JObject();

            jData.Add ("message", MESSAGE);
            jGcmData.Add ("to", "/topics/global");
            jGcmData.Add ("data", jData);

            var url = new Uri ("https://gcm-http.googleapis.com/gcm/send");
            try
            {
                using (var client = new HttpClient())
                {
                    client.DefaultRequestHeaders.Accept.Add(
                        new MediaTypeWithQualityHeaderValue("application/json"));

                    client.DefaultRequestHeaders.TryAddWithoutValidation (
                        "Authorization", "key=" + API_KEY);

                    Task.WaitAll(client.PostAsync (url,
                        new StringContent(jGcmData.ToString(), Encoding.Default, "application/json"))
                            .ContinueWith(response =>
                            {
                                Console.WriteLine(response);
                                Console.WriteLine("Message sent: check the client device notification tray.");
                            }));
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Unable to send GCM message:");
                Console.Error.WriteLine(e.StackTrace);
            }
        }
    }
}
```

En el código anterior, cambie *YOUR_API_KEY* a la clave de API para el proyecto de aplicación de cliente. 

Este servidor de aplicación de prueba, envía el siguiente mensaje con formato JSON a GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM, reenvía a su vez, este mensaje a la aplicación cliente. Comencemos a construir **MessageSender** y abra una ventana de consola donde poder ejecutarlo desde la línea de comandos.



### <a name="try-it"></a>¡Inténtelo!

Ahora estamos listos para probar la aplicación cliente. Si está usando un emulador o si el dispositivo se comunique con GCM a través de Wi-Fi, debe abrir los siguientes puertos TCP en el firewall obtener a través de los mensajes de GCM: 5228, 5229 y 5230.

Iniciar la aplicación de cliente y observe la ventana de salida. Después de la `RegistrationIntentService` recibe correctamente un registro de token de GCM, la ventana de salida debe mostrar el token con salida de registro similar a lo siguiente:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

En este momento la aplicación cliente está lista para recibir un mensaje de notificación remoto. Desde la línea de comandos, ejecute el **MessageSender.exe** programa envíe un mensaje de notificación "Hola, Xamarin" a la aplicación cliente.
Si todavía no ha compilado el **MessageSender** project, hágalo ahora.

Para ejecutar **MessageSender.exe** en Visual Studio, abra un símbolo del sistema, cambie a la **MessageSender/bin/Debug** directorio y ejecute el comando directamente:

```cmd
MessageSender.exe
```

Para ejecutar **MessageSender.exe** en Visual Studio para Mac, abra una sesión de Terminal, cambie al **MessageSender/bin/Debug** el directorio y mono de uso para ejecutar **MessageSender.exe** 

```bash
mono MessageSender.exe
```

Puede tardar un minuto para el mensaje en propagarse a través de GCM y regresa a la aplicación cliente. Si el mensaje se recibe correctamente, debemos vemos salida similar al siguiente en la ventana de salida: 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

Además, debe tener en cuenta que ha aparecido un nuevo icono de notificación en la Bandeja de notificación: 

[![Icono de Notiication aparece en el dispositivo](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

Cuando se abre la Bandeja de notificación para ver las notificaciones, verá la notificación remoto:

[![Se muestra el mensaje de notificación](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Enhorabuena, la aplicación ha recibido su primera notificación remoto.

Tenga en cuenta que ya no se recibirán mensajes GCM si la aplicación está detenido en vigor. Para reanudar las notificaciones después de una detención force, la aplicación debe ser manully reiniciar. Para obtener más información acerca de esta directiva de Android, consulte [iniciar controles en aplicaciones detenidas](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) y esto [post de desbordamiento de pila](http://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267). 

 
## <a name="summary"></a>Resumen

En este tutorial se detallan los pasos para implementar notificaciones remotas en una aplicación Xamarin.Android. Describe cómo instalar paquetes adicionales necesarios para las comunicaciones de GCM y explica cómo configurar permisos de aplicación para el acceso a servidores de GCM. Se proporciona código de ejemplo que muestra cómo comprobar la presencia de servicios de Google Play, cómo implementar un servicio de intención de registro y el servicio de agente de escucha de Id. de instancia que negocia con GCM para obtener un token de registro y cómo implementar un agente de escucha GCM servicio que recibe y procesa los mensajes de notificación remoto. Por último, se implementa un programa de prueba de línea de comandos para enviar notificaciones de prueba a nuestra aplicación de cliente a través de GCM. 


## <a name="related-links"></a>Vínculos relacionados

- [GCM RemoteNotifications (ejemplo)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
