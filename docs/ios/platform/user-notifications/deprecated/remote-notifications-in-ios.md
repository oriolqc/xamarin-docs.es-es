---
title: "Notificaciones de inserción en iOS"
description: "Esta sección describirá las notificaciones de inserción en iOS. Incluye el servicio de puerta de enlace de notificaciones Push de Apple y el papel que desempeña en las notificaciones de publicación para las aplicaciones de iOS. Explicará cómo crear los certificados de seguridad necesarios para habilitar las notificaciones de inserción y analizar. Por último, en esta sección se describe algunas de las tareas de mantenimiento de manera que los servidores de aplicaciones deben realizar para realizar un seguimiento de los dispositivos móviles de cliente."
ms.topic: article
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 8e90bc3974247066a714cb44b6648a83cdb58cf5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="push-notifications-in-ios"></a>Notificaciones de inserción en iOS

_Esta sección describirá las notificaciones de inserción en iOS. Incluye el servicio de puerta de enlace de notificaciones Push de Apple y el papel que desempeña en las notificaciones de publicación para las aplicaciones de iOS. Explicará cómo crear los certificados de seguridad necesarios para habilitar las notificaciones de inserción y analizar. Por último, en esta sección se describe algunas de las tareas de mantenimiento de manera que los servidores de aplicaciones deben realizar para realizar un seguimiento de los dispositivos móviles de cliente._

> [!IMPORTANT]
> **Nota:** la información en esta sección se aplica a iOS 9 y anteriores, se han quedado aquí para compatibilidad con versiones anteriores de iOS. Para iOS 10 y versiones posteriores, consulte la [Guía de la estructura de notificación de usuario](~/ios/platform/user-notifications/index.md) para admitir tanto locales como remotos de notificación en un dispositivo iOS.

Notificaciones de inserción se deben mantener en breves y solo contienen suficientes datos para notificar a la aplicación móvil que deben comunicarse con la aplicación de servidor para una actualización. Por ejemplo, cuando llega el correo electrónico nuevo, la aplicación de servidor sólo notificará la aplicación móvil que ha llegado nuevo correo electrónico. La notificación no contendría el nuevo correo electrónico propio. La aplicación móvil, a continuación, recuperaría los correos electrónicos nuevo desde el servidor cuando estaba adecuado

En el centro de inserción notificaciones en iOS es el *servicio de puerta de enlace de notificación de inserción (APNS) de Apple*. Se trata de un servicio proporcionado por Apple que se encarga de las notificaciones de enrutamientos de un servidor de aplicaciones para dispositivos iOS.
La siguiente imagen ilustra la topología de la notificación de inserción para iOS: ![ ] (remote-notifications-in-ios-images/image4.png "esta imagen muestra la topología de la notificación de inserción para iOS")

Las notificaciones en remotas sí son cadenas que se ajustan al formato en formato JSON y especifican los protocolos en [la carga de notificaciones](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) sección de la [locales e inserción notificación Guía de programación de](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)en la [documentación para desarrolladores de iOS](https://developer.apple.com/devcenter/ios/index.action).

Apple mantiene dos entornos de APN: una *espacio aislado* y un *producción* entorno. El entorno de espacio aislado está pensado para realizar pruebas durante la fase de desarrollo y pueden encontrarse en `gateway.sandbox.push.apple.com` 2195 de puerto TCP. El entorno de producción está pensado para usarse en aplicaciones que se han implementado y pueden encontrarse en `gateway.push.apple.com` 2195 de puerto TCP.

## <a name="requirements"></a>Requisitos

Notificación de inserción debe cumplir las reglas siguientes que dictan la arquitectura de APNS:

-  **Límite de los mensajes de 256 bytes** -el tamaño de todo el mensaje de la notificación no debe superar los 256 bytes.
-  **No hay confirmación de recibo** -APN no proporciona el remitente con cualquier notificación de que un mensaje escribieron en el destinatario previsto. Si el dispositivo está inaccesible y se envían varias notificaciones secuenciales, se perderán todas las notificaciones excepto el más reciente. Solo la notificación más reciente se entregará al dispositivo.
-  **Cada aplicación requiere un certificado de seguro** -comunicación con APNS debe realizarse a través de SSL.


## <a name="creating-and-using-certificates"></a>Creación y uso de certificados

Cada uno de los entornos mencionados en la sección anterior requiere su propio certificado. Esta sección explica cómo crear un certificado, asociar a un perfil de aprovisionamiento y, a continuación, obtener un certificado de intercambio de información Personal para su uso con PushSharp.

1.  Para crear un certificados hacia el iOS Portal de aprovisionamiento en el sitio Web de Apple, tal y como se muestra en la siguiente captura de pantalla (Observe el elemento de menú de Id. de aplicaciones de la izquierda):

    [![](remote-notifications-in-ios-images/image5new.png "El Portal de aprovisionamiento en el sitio Web de manzanas de iOS")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  A continuación, vaya a la sección de identificadores de aplicación y crear un nuevo identificador de aplicación, como se muestra en la captura de pantalla siguiente:

    [![](remote-notifications-in-ios-images/image6new.png "Vaya a la sección de Id. de aplicación y crear un nuevo ID de aplicación.")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  Al hacer clic en el  **+**  botón, podrá escribir la descripción y un identificador de paquete para el identificador de la aplicación, como se muestra en la captura de pantalla siguiente:

    [![](remote-notifications-in-ios-images/image7new.png "Escriba la descripción y un identificador de paquete para el identificador de aplicación")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Asegúrese de seleccionar **explícita Id. de aplicación** y que el identificador de paquete no termina con un `*` . Esto creará un identificador que es adecuado para varias aplicaciones, y deben ser certificados de notificación de inserción de una sola aplicación.

1. En servicios de aplicaciones, seleccione **notificaciones Push**:

    [![](remote-notifications-in-ios-images/image8new.png "Seleccione las notificaciones de inserción")](remote-notifications-in-ios-images/image8new.png#lightbox)

2. Y presione **enviar** para confirmar el registro del nuevo identificador de aplicación:

    [![](remote-notifications-in-ios-images/image9new.png "Confirmar el registro del nuevo identificador de aplicación")](remote-notifications-in-ios-images/image9new.png#lightbox)

3.  A continuación, debe crear el certificado para el identificador de aplicación. En el panel de navegación izquierdo, vaya a **certificados > todos los** y seleccione la `+` botón, como se muestra en la captura de pantalla siguiente:

    [![](remote-notifications-in-ios-images/image10new.png "Crear el certificado para el identificador de aplicación")](remote-notifications-in-ios-images/image8.png#lightbox)

4.  Seleccione si desea usar un certificado de producción o desarrollo:

    [![](remote-notifications-in-ios-images/image11new.png "Seleccione un certificado de desarrollo o de producción")](remote-notifications-in-ios-images/image11new.png#lightbox)

5. Y, a continuación, seleccione el nuevo identificador de aplicación que simplemente hemos creado:

    [![](remote-notifications-in-ios-images/image12new.png "Seleccione el nuevo identificador de aplicación que se acaba de crear")](remote-notifications-in-ios-images/image12new.png#lightbox)

6.  Esto mostrará las instrucciones que le guiará por el proceso de creación de un *solicitud de firma de certificado* mediante la **acceso a llaveros** aplicación en el equipo Mac.

7.  Ahora que se ha creado el certificado, se debe usar como parte del proceso de compilación para firmar la aplicación para que pueden registrar con APNs. Para ello cree e instale un perfil de aprovisionamiento que utiliza el certificado.

8.  Para crear un perfil de aprovisionamiento de desarrollo, vaya a la **perfiles de aprovisionamiento** sección y siga los pasos para crearla, utilizando el identificador de aplicación que hemos acabamos de crear.

9.  Una vez que haya creado el perfil de aprovisionamiento, se abrirán **Xcode organizador** y actualizarlo. Si el perfil de aprovisionamiento crea aparecen no es posible que sea necesario descargar el perfil en el Portal de aprovisionamiento de iOS e importarla manualmente. La captura de pantalla siguiente muestra un ejemplo del organizador con el perfil de aprovisionamiento agregado:

    [![](remote-notifications-in-ios-images/image13new.png "Esta captura de pantalla muestra un ejemplo del organizador con el perfil de aprovisionamiento agregado")](remote-notifications-in-ios-images/image13new.png#lightbox)

10.  En este momento, es necesario configurar el proyecto de Xamarin.iOS para poder usar este perfil de aprovisionamiento acaba de crear. Esto se realiza desde **opciones de proyecto** cuadro de diálogo, en **agrupación de firma de iOS** ficha, como se muestra en la captura de pantalla siguiente:

    [![](remote-notifications-in-ios-images/image11.png "Configurar el proyecto Xamarin.iOS para utilizar el recién creado el perfil de aprovisionamiento")](remote-notifications-in-ios-images/image11.png#lightbox)



En este momento la aplicación está configurada para trabajar con notificaciones de inserción. Sin embargo, hay algunos pasos más necesarios con el certificado. Este certificado está en formato DER que no es compatible con PushSharp, lo que requiere un certificado de intercambio de información Personal (PKCS12). Para convertir el certificado para que sea utilizable por PushSharp, siga estos pasos finales:

1.  **Descargue el archivo de certificado** -inicio de sesión en el Portal de aprovisionamiento de iOS elige la pestaña certificados, seleccione el certificado asociado con el perfil de aprovisionamiento correcto y seleccione **descargar** .
1.  **Abra el acceso a llaveros** -trata de aplicación es una interfaz gráfica de usuario para el sistema de administración de contraseñas en OS X.
1.  **Importar el certificado** : si el certificado no está ya instalado, **archivo... Importar elementos** en el menú de acceso a llaves. Navegue hasta el certificado que exportó anteriormente y selecciónelo.
1.  **Exportar el certificado** : expandir el certificado para la clave privada asociada está visible, haga doble clic en la clave y elige exportar. Se le pedirá un nombre de archivo y una contraseña para el archivo exportado.

En este momento que finalicemos con certificados. Hemos creado un certificado que se utilizará para firmar aplicaciones de iOS y convierte ese certificado en un formato que puede utilizarse con PushSharp en una aplicación de servidor. Siguiente Echemos un vistazo a cómo interactúan las aplicaciones de iOS con APNS.

## <a name="registering-with-apns"></a>Registrar con APNS

Antes de un iOS aplicación puede recibir la notificación remoto debe registrar con APNS. APN generará un token de dispositivo único y vuelva a la aplicación de iOS. La aplicación de iOS, a continuación, tomar el token del dispositivo y, a continuación, registrarse con el servidor de aplicaciones. Una vez que todo esto ocurre, el registro está completando y el servidor de aplicaciones puede notificaciones de inserción para el dispositivo móvil.

En teoría, el token del dispositivo puede cambiar cada vez que una aplicación de iOS se registra con APNS, sin embargo, en la práctica esto no sucede a menudo. Como una optimización de una aplicación puede almacenar en caché el token del dispositivo más reciente y actualizar solo el servidor de aplicaciones cuando cambia. El siguiente diagrama ilustra el proceso de registro y obtener un token del dispositivo:

 ![](remote-notifications-in-ios-images/image12.png "Este diagrama muestra el proceso de registro y obtener un token del dispositivo")

Registro con APNS se controla en el `FinishedLaunching` método de la clase de delegado de la aplicación mediante una llamada a `RegisterForRemoteNotificationTypes` en actual `UIApplication` objeto. Cuando una aplicación de iOS se registra con APNS, también debe especificar qué tipos de notificaciones remotas le gustaría recibir. Estos tipos de notificación remoto se declaran en la enumeración `UIRemoteNotificationType`. El siguiente fragmento de código es un ejemplo de cómo puede registrar una aplicación de iOS para recibir notificaciones de alerta y distintivo remotas:

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

La solicitud de registro APNS sucede en segundo plano: cuando se recibe la respuesta, iOS llamará al método `RegisteredForRemoteNotifications` en la `AppDelegate` clase y pasar el token de dispositivo registrado. El token se ubicará en un `NSData` objeto. El siguiente fragmento de código muestra cómo recuperar el token del dispositivo que APN proporciona:

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

Si se produce un error en el registro por algún motivo (como el dispositivo no está conectado a Internet), iOS llamará a `FailedToRegisterForRemoteNotifications` en la aplicación de la clase delegada. El siguiente fragmento de código muestra cómo mostrar una alerta al usuario que les informa que el registro de error:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Mantenimiento de símbolo (token) de dispositivo

Tokens de dispositivo se expirar o cambian con el tiempo. Por este motivo, las aplicaciones de servidor deberá realizar una limpieza de casa y purgar estos tokens caducados o modificados. Cuando una aplicación envía como notificación de inserción a un dispositivo que tiene un token caducado, APN se registrar y guardar ese token caducado. Servidores, a continuación, pueden consultar APN para averiguar qué tokens han expirado.

APNS se usa para proporcionar un *comentarios servicio* -un extremo HTTPS que se autentica mediante el certificado que se ha creado para enviar inserción notificaciones y envía datos sobre los tokens han expirado. Esto ha en desuso por Apple y quitar.

En su lugar, hay un nuevo código de estado HTTP para el caso de que previamente se ha notificado por el servicio de comentarios:

> 410 - el token del dispositivo ya no está activo para el tema.

Además, un nuevo `timestamp` clave de datos JSON estarán en el cuerpo de respuesta:

> Si el valor en el: encabezado de estado es 410, el valor de esta clave es la última hora a la que APN confirma que el token del dispositivo ya no es válido para el tema.
>
> Detenga la inserción notificaciones hasta que el dispositivo registra un token con una marca de tiempo posterior con el proveedor.

## <a name="summary"></a>Resumen

En esta sección se presentan los conceptos clave de notificaciones de inserción en iOS. Explica el rol de la inserción de Apple notificación de puerta de enlace de servicio (APNS). A continuación, lo cubre la creación y uso de los certificados de seguridad que son esenciales para APNS. Por último, en este documento terminado con una explicación de cómo pueden utilizar servidores de aplicaciones la *comentarios servicios* de detención del seguimiento expirado tokens de dispositivo.


## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones - Mostrar notificaciones locales y remotas (ejemplo)](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [Local y notificaciones de inserción para desarrolladores](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
