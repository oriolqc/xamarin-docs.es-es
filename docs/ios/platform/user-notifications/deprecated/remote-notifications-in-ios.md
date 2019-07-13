---
title: Notificaciones Push en iOS
description: Este documento describe cómo trabajar con las notificaciones push en iOS 9 y versiones anteriores. Describe los certificados, registrar con Apple Push notificaciones de puerta de enlace de servicio (APNS) y mucho más.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f6d059e5a30e7e3dac92a2c4e0e6079222e66b22
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865208"
---
# <a name="push-notifications-in-ios"></a>Notificaciones Push en iOS

> [!IMPORTANT]
> La información de esta sección se aplica a iOS 9 y anteriores, se ha dejado aquí para admitir versiones anteriores de iOS. Para iOS 10 y versiones posteriores, consulte el [Guía de usuario notificación Framework](~/ios/platform/user-notifications/index.md) para admitir tanto locales como remotas de notificación en un dispositivo iOS.

Notificaciones de inserción deben mantenerse breves y solo contienen suficientes datos para notificar a la aplicación móvil que debe ponerse en contacto con la aplicación de servidor para una actualización. Por ejemplo, cuando llega correo electrónico nuevo, la aplicación de servidor sólo notificará la aplicación móvil que ha llegado correo electrónico nuevo. La notificación no contendría el nuevo correo electrónico propio. La aplicación móvil, a continuación, recuperaría los correos electrónicos nuevos desde el servidor cuando fue apropiado

En el centro de inserción de notificaciones de iOS es el *servicio de puerta de enlace de Apple Push Notification (APNS)* . Se trata de un servicio proporcionado por Apple que se encarga de enrutamientos notificaciones desde un servidor de aplicaciones para dispositivos iOS.
La siguiente imagen ilustra la topología de notificación de inserción de iOS: ![](remote-notifications-in-ios-images/image4.png "Esta imagen ilustra la topología de notificación de inserción de iOS")

Notificaciones remotas a sí mismos son cadenas que se ajustan al formato en formato JSON y especifican los protocolos en [la carga de la notificación](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) sección de la [Local y Push Notification Programming Guide](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)en el [documentación para desarrolladores de iOS](https://developer.apple.com/devcenter/ios/index.action).

Apple mantiene dos entornos de APNS: un *Sandbox* y un *producción* entorno. El entorno de recinto de seguridad está pensado para realizar pruebas durante la fase de desarrollo y puede encontrarse en `gateway.sandbox.push.apple.com` en el puerto TCP 2195. El entorno de producción está pensado para usarse en aplicaciones que se han implementado y pueden encontrarse en `gateway.push.apple.com` en el puerto TCP 2195.

## <a name="requirements"></a>Requisitos

Notificación de inserción debe observar las reglas siguientes dictadas por la arquitectura de APNS:

-  **Límite de mensajes de 256 bytes** : el tamaño de todo el mensaje de la notificación no debe superar los 256 bytes.
-  **No hay confirmación de recepción** -APNS no proporciona al remitente cualquier notificación de que un mensaje llegó al destinatario deseado. Si el dispositivo es inaccesible y se envían varias notificaciones secuenciales, se perderán todas las notificaciones, excepto la más reciente. Solo la notificación más reciente se entregará al dispositivo.
-  **Cada aplicación requiere un certificado seguro** -comunicación con APNS debe realizarse a través de SSL.


## <a name="creating-and-using-certificates"></a>Creación y uso de certificados

Cada uno de los entornos que se mencionan en la sección anterior requiere su propio certificado. Esta sección explica cómo crear un certificado, asociarla a un perfil de aprovisionamiento y, a continuación, obtenga un certificado de intercambio de información Personal para su uso con PushSharp.

1.  Para crear un certificados, vaya a iOS Portal de aprovisionamiento en el sitio Web de Apple, tal como se muestra en la captura de pantalla siguiente (Observe que el elemento de menú de identificadores de aplicación de la izquierda):

    [![](remote-notifications-in-ios-images/image5new.png "El Portal de aprovisionamiento en el sitio Web de Apple iOS")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  A continuación, vaya a la sección de identificadores de aplicación y crear un nuevo identificador de aplicación, como se muestra en la captura de pantalla siguiente:

    [![](remote-notifications-in-ios-images/image6new.png "Vaya a la sección de identificadores de aplicación y crear un nuevo identificador de aplicación")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  Al hacer clic en el **+** botón, podrá escribir la descripción y un identificador de lote para el identificador de aplicación, como se muestra en la captura de pantalla siguiente:

    [![](remote-notifications-in-ios-images/image7new.png "Escriba la descripción y un identificador de lote para el identificador de aplicación")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Asegúrese de seleccionar **identificador de aplicación explícito** y que el identificador de paquete no termina con un `*` . Esto creará un identificador que es bueno para varias aplicaciones y certificados de la notificación de inserción deben ser para una sola aplicación.

5. En servicios de aplicaciones, seleccione **las notificaciones de inserción**:

    [![](remote-notifications-in-ios-images/image8new.png "Seleccione las notificaciones de inserción")](remote-notifications-in-ios-images/image8new.png#lightbox)

6. Y presione **enviar** para confirmar el registro de nuevo el identificador de aplicación:

    [![](remote-notifications-in-ios-images/image9new.png "Confirmar el registro de nuevo el identificador de aplicación")](remote-notifications-in-ios-images/image9new.png#lightbox)

7.  A continuación, debe crear el certificado para el identificador de aplicación. En el panel de navegación izquierdo, vaya a **certificados > todos los** y seleccione el `+` button, como se muestra en la captura de pantalla siguiente:

    [![](remote-notifications-in-ios-images/image10new.png "Crear el certificado para el identificador de aplicación")](remote-notifications-in-ios-images/image8.png#lightbox)

8. Seleccione si desea usar un certificado de desarrollo o producción:

    [![](remote-notifications-in-ios-images/image11new.png "Seleccione un certificado de desarrollo o producción")](remote-notifications-in-ios-images/image11new.png#lightbox)

9. Y, a continuación, seleccione el nuevo identificador de aplicación que acabamos de crear:

    [![](remote-notifications-in-ios-images/image12new.png "Seleccione el nuevo identificador de aplicación que acaba de crear")](remote-notifications-in-ios-images/image12new.png#lightbox)

10.  Esto mostrará las instrucciones que le llevarán a través del proceso de creación de un *solicitud de firma de certificado* utilizando el **acceso a llaves** aplicación en el equipo Mac.

11.  Ahora que se ha creado el certificado, debe usarse como parte del proceso de compilación para firmar la aplicación para que pueden registrar con APNs. Esto requiere la creación e instalación de un perfil de aprovisionamiento que utiliza el certificado.

12.  Para crear un perfil de aprovisionamiento de desarrollo, vaya a la **perfiles de aprovisionamiento** sección y siga los pasos para crearla, utilizando el identificador de aplicación que acabamos de crear.

13.  Una vez que haya creado el perfil de aprovisionamiento, abra **organizador de Xcode** y actualizarlo. Si el perfil de aprovisionamiento que creó aparecen no puede ser necesario descargar el perfil desde el Portal de aprovisionamiento de iOS e importarlo manualmente. Captura de pantalla siguiente muestra un ejemplo del organizador con el perfil de aprovisionamiento agregado:  
    [![](remote-notifications-in-ios-images/image13new.png "Esta captura de pantalla muestra un ejemplo del organizador con el perfil de aprovisionamiento agregado")](remote-notifications-in-ios-images/image13new.png#lightbox)

14.  En este momento se necesita configurar el proyecto de Xamarin.iOS para usar el recién creado el perfil de aprovisionamiento. Esto se hace desde **opciones de proyecto** cuadro de diálogo, en **firma de lote de iOS** pestaña, como se muestra en la captura de pantalla siguiente:  
    [![](remote-notifications-in-ios-images/image11.png "Configurar el proyecto de Xamarin.iOS para utilizar el recién creado el perfil de aprovisionamiento")](remote-notifications-in-ios-images/image11.png#lightbox)

En este momento la aplicación está configurada para trabajar con notificaciones de inserción. Sin embargo, hay unos pocos pasos más necesarios con el certificado. Este certificado está en formato DER que no es compatible con PushSharp, que requiere un certificado de intercambio de información Personal (PKCS12). Para convertir el certificado para que sea utilizable por PushSharp, siga estos pasos finales:

1.  **Descargue el archivo de certificado** -inicio de sesión en el Portal de aprovisionamiento de iOS elige la pestaña certificados, seleccione el certificado asociado al perfil de aprovisionamiento correcto y elegir **descargar** .
1.  **Abra el acceso a llaves** -se trata de aplicación es una interfaz gráfica de usuario para el sistema de administración de contraseñas en OS X.
1.  **Importe el certificado** : si ya no está instalado el certificado, **archivo... Importar elementos** en el menú de acceso a llaves. Navegue hasta el certificado que exportó anteriormente y selecciónelo.
1.  **Exportar el certificado** : expandir el certificado para la clave privada asociada está visible, haga doble clic en la clave y eligió la exportación. Se pedirá un nombre de archivo y una contraseña para el archivo exportado.

En este punto hemos terminado con certificados. Hemos creado un certificado que se usará para firmar aplicaciones de iOS y convierte ese certificado en un formato que puede utilizarse con PushSharp en una aplicación de servidor. Siguiente Echemos un vistazo a cómo interactúan las aplicaciones de iOS con Apple Push Notification Service.

## <a name="registering-with-apns"></a>Registrar con APNS

Antes de un iOS aplicación puede recibir notificación remota que se debe registrar con APNS. APNS generará un token de dispositivo único y vuelva a la aplicación de iOS. La aplicación de iOS, a continuación, se tomará el token del dispositivo y, a continuación, registrarse con el servidor de aplicaciones. Una vez que todo esto ocurre, el registro está completado y el servidor de aplicaciones puede enviar notificaciones push a los dispositivos móviles.

En teoría, el token del dispositivo puede cambiar cada vez que se registra una aplicación de iOS con Apple Push Notification Service, sin embargo, en la práctica esto no sucede a menudo. Como optimización una aplicación puede almacenar en caché el token del dispositivo más reciente y actualizar solo el servidor de aplicaciones cuando cambian. El diagrama siguiente ilustra el proceso de registro y obtener un token del dispositivo:

 ![](remote-notifications-in-ios-images/image12.png "Este diagrama ilustra el proceso de registro y obtener un token del dispositivo")

Registro con Apple Push Notification Service se controla en el `FinishedLaunching` método de la clase de delegado de aplicación mediante una llamada a `RegisterForRemoteNotificationTypes` en actual `UIApplication` objeto. Cuando se registra una aplicación de iOS con Apple Push Notification Service, también debe especificar qué tipos de notificaciones remotas le gustaría recibir. Estos tipos de notificación remota se declaran en la enumeración `UIRemoteNotificationType`. El fragmento de código siguiente es un ejemplo de cómo puede registrar una aplicación de iOS para recibir notificaciones de alerta y notificaciones remotas:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

La solicitud de registro APNS se produce en segundo plano: cuando se recibe la respuesta, iOS llamará al método `RegisteredForRemoteNotifications` en el `AppDelegate` clase y pasar el token de dispositivo registrado. El token se ubicará en un `NSData` objeto. El fragmento de código siguiente muestra cómo recuperar el token del dispositivo que Apple Push Notification Service que proporciona:

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

Si se produce un error en el registro por algún motivo (por ejemplo, el dispositivo no está conectado a Internet), llamará iOS `FailedToRegisterForRemoteNotifications` en la aplicación de la clase delegada. El fragmento de código siguiente muestra cómo mostrar una alerta al usuario que les informa de que el registro de error:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Mantenimiento de Token de dispositivo

Los tokens de dispositivo se expirar o cambiar con el tiempo. Por este motivo, las aplicaciones de servidor deberá realizar una limpieza de casa y purgar estos tokens caducados o modificados. Cuando una aplicación envía como notificaciones de inserción a un dispositivo que tiene un token caducado, APNS grabará y guardar ese token caducado. Los servidores, a continuación, pueden consultar APNS para averiguar qué tokens han expirado.

APNS usa para proporcionar un *comentarios servicio* -un extremo HTTPS que se autentica mediante el certificado que se creó para devolver inserción notificaciones y envía datos acerca de qué tokens han expirado. Esto se ha desusado por Apple y quitar.

En su lugar, hay un nuevo código de estado HTTP para el caso de que se ha notificado previamente por el servicio de comentarios:

> 410 - el token del dispositivo ya no está activo para el tema.

Además, un nuevo `timestamp` clave de JSON de datos estará en el cuerpo de respuesta:

> Si el valor de la: encabezado de estado es 410, el valor de esta clave es la última vez en el que APNs confirmado que el token del dispositivo ya no era válido para el tema.
>
> Detenga la inserción de notificaciones hasta que el dispositivo registra un token con una marca de tiempo posterior con el proveedor.

## <a name="summary"></a>Resumen

En esta sección se presentan los conceptos clave relacionados con las notificaciones push en iOS. Asimismo, explica el rol de la Apple Push Notification Gateway Service (APNS). Se ha descrito a continuación, la creación y uso de los certificados de seguridad que son esenciales para APNS. Por último, en este documento terminado con una explicación sobre cómo pueden usar los servidores de aplicaciones el *comentarios servicios* detener el seguimiento de los tokens de dispositivo caducados.


## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones: demostración de las notificaciones locales y remotas (ejemplo)](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [Local y notificaciones de inserción para los desarrolladores](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
