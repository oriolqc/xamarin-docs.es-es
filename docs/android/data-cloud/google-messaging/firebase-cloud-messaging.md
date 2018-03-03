---
title: "Firebase de mensajería en la nube"
description: "Mensajería de nube de firebase (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Este artículo proporciona información general de cómo funciona la FCM y se explica cómo configurar servicios de Google para que la aplicación pueda utilizar FCM."
ms.topic: article
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/29/2017
ms.openlocfilehash: 9f084899f44e0104d0aa2d4b3c0509812bd3fdd2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="firebase-cloud-messaging"></a>Firebase de mensajería en la nube

_Mensajería de nube de firebase (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Este artículo proporciona información general de cómo funciona la FCM y se explica cómo configurar servicios de Google para que la aplicación pueda utilizar FCM._

[![Imagen de héroe de mensajería de nube firebase](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png)

Este tema proporciona una descripción general de cómo Firebase Cloud Messaging enruta los mensajes entre la aplicación Xamarin.Android y un servidor de aplicaciones, y proporciona un procedimiento paso a paso para adquirir las credenciales para que la aplicación puede usar los servicios FCM.


<a name="overview" />

## <a name="overview"></a>Información general

Mensajería de nube de firebase (FCM) es un servicio de multiplataforma que controla el envío, enrutamiento y puesta en cola de mensajes entre las aplicaciones de servidor y las aplicaciones de cliente móvil. FCM es el sucesor para Google (GCM Cloud Messaging) y se basa en servicios de Google Play.

Como se muestra en el siguiente diagrama, FCM actúa como intermediario entre los remitentes de mensajes y los clientes. A *aplicación cliente* es una aplicación habilitada para FCM que se ejecuta en un dispositivo. El *servidor de aplicaciones* (proporcionado por usted o su compañía) es el servidor habilitado FCM que la aplicación cliente se comunica con a través de FCM. A diferencia de GCM, FCM hace posible para que pueda enviar mensajes a las aplicaciones cliente directamente a través de la GUI de las notificaciones de la consola de Firebase:

[![FCM se encuentra entre la aplicación cliente y un servidor de aplicaciones](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png)

Usar FCM, los servidores de aplicaciones pueden enviar mensajes a un único dispositivo, a un grupo de dispositivos o a un número de dispositivos que están suscritos a un tema. Una aplicación cliente puede utilizar FCM para suscribirse a los mensajes desde un servidor de aplicaciones (por ejemplo, para recibir notificaciones remotas). Para obtener más información sobre los diferentes tipos de mensajes de Firebase, consulte [acerca de los mensajes FCM](https://firebase.google.com/docs/cloud-messaging/concept-options).


<a name="inaction" />

## <a name="firebase-cloud-messaging-in-action"></a>En la nube firebase mensajería en acción

Cuando se envía un mensaje de nivel inferior a una aplicación de cliente desde un servidor de aplicaciones, el servidor de aplicaciones envía el mensaje a un *de conexión del servidor FCM* proporcionado por Google; el servidor de conexión FCM, a su vez, reenvía el mensaje a un dispositivo que se está ejecutando el aplicación de cliente. Los mensajes pueden enviarse a través de HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (Protocolo de presencia y mensajería Extensible). Dado que las aplicaciones cliente no están siempre conectadas o en ejecución, el FCM conexión pone en cola y los almacenes de mensajes del servidor, enviarlos a las aplicaciones cliente cuando vuelva a conectar y estén disponibles. De forma similar, FCM va a poner en cola mensajes de nivel superior desde la aplicación cliente para el servidor de aplicaciones si el servidor de aplicaciones no está disponible. Para obtener más información acerca de los servidores de conexión FCM, consulte [sobre Firebase nube mensajería Server](https://firebase.google.com/docs/cloud-messaging/server).

FCM usa las credenciales siguientes para identificar el servidor de aplicaciones y la aplicación de cliente, y usa estas credenciales para autorizar las transacciones de mensajes a través de FCM:

-   **Id. de remitente** &ndash; el *Id. de remitente* es un valor numérico único que se asigna cuando se crea el proyecto Firebase. El identificador del remitente se usa para identificar cada servidor de aplicaciones que puede enviar mensajes a la aplicación cliente. El identificador del remitente es también el número de proyecto; obtener el identificador del remitente desde la consola de Firebase al registrar el proyecto. Un ejemplo de un Id. de remitente es `496915549731`.

-   **Clave de API** &ndash; el *clave de API* proporciona el acceso al servidor de aplicaciones para servicios de Firebase; FCM utiliza esta clave para autenticar el servidor de aplicaciones. Esta credencial también se conoce como el *clave Server* o *clave de API Web*. Un ejemplo de una clave de API es `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`.

-   **Identificador de la aplicación** &ndash; la identidad de la aplicación de cliente que se registra para recibir mensajes desde FCM (independientemente de cualquier dispositivo determinado). Un ejemplo de un identificador de aplicación es `1:415712510732:android:0e1eb7a661af2460`.

-   **Símbolo (token) de registro** &ndash; el *registro Token* (también se denomina el *Id. de instancia*) es la identidad FCM de la aplicación de cliente en un dispositivo determinado. El token de registro se genera en tiempo de ejecución &ndash; la aplicación recibe un token de registro cuando registra por primera vez FCM mientras se ejecuta en un dispositivo. El token de registro autoriza a una instancia de la aplicación de cliente (ejecutándose en ese dispositivo concreto) para recibir mensajes de FCM.
    Un ejemplo de un token de registro es `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (una cadena muy larga).

[Configuración de seguridad Firebase Cloud Messaging](#setup_fcm) (más adelante en esta guía) proporciona instrucciones detalladas para crear un proyecto y generar estas credenciales. Cuando se crea un nuevo proyecto en el [Firebase consola](https://console.firebase.google.com/), llama a un archivo de credenciales **google services.json** se crea &ndash; agregue este archivo al proyecto Xamarin.Android, como se explica en [ Notificaciones remotas con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

Las siguientes secciones explican cómo se utilizan estas credenciales cuando las aplicaciones cliente se comunican con servidores de aplicaciones a través de FCM.


<a name="registration" />

### <a name="registration-with-fcm"></a>Registro con FCM

Una aplicación cliente debe registrarse con FCM antes de que puede tener lugar la mensajería. La aplicación cliente debe completar los pasos de registro se muestra en el diagrama siguiente:

[![Diagrama de pasos de registro de aplicación](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png)

1.  La aplicación cliente se pone en contacto FCM para obtener un token de registro, pasando el Id. de remitente, la clave de API y el identificador de aplicación a FCM.

2.  FCM devuelve un token de registro a la aplicación cliente.

3.  La aplicación de cliente (opcionalmente) reenvía el símbolo (token) de registro para el servidor de aplicaciones.

El servidor de aplicaciones se almacena en memoria caché el token de registro para las siguientes comunicaciones con la aplicación cliente. El servidor de aplicaciones puede enviar una confirmación de nuevo a la aplicación cliente para indicar que se ha recibido el token de registro. Después de producirse este protocolo de enlace, la aplicación cliente puede recibir mensajes de (o enviar mensajes a) el servidor de aplicaciones. La aplicación cliente puede recibir un nuevo token de registro si se pone en peligro el token anterior (vea [notificaciones remoto con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) para obtener un ejemplo de cómo una aplicación recibe las actualizaciones de símbolo (token) de registro).

Cuando la aplicación de cliente ya no desea recibir mensajes desde el servidor de aplicaciones, puede enviar una solicitud al servidor de aplicaciones para eliminar el token de registro. Si se desinstala la aplicación de cliente desde un dispositivo, FCM detecta y notifica automáticamente el servidor de aplicaciones para eliminar el token de registro.


<a name="downstream" />

### <a name="downstream-messaging"></a>En un nivel inferior de mensajería

El siguiente diagrama ilustra cómo Firebase Cloud Messaging almacena y reenvía los mensajes de nivel inferiores:

[![FCM usa almacenamiento y reenvío para la mensajería de nivel inferior](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png)

Cuando el servidor de aplicaciones, envía un mensaje de nivel inferior a la aplicación de cliente, utiliza los siguientes pasos como enumerado en el diagrama anterior:

1.  El servidor de aplicaciones envía el mensaje a FCM.

2.  Si el dispositivo de cliente no está disponible, el servidor FCM almacena el mensaje en una cola para su transmisión posterior. Los mensajes se conservan en almacenamiento FCM durante un máximo de 4 semanas (para obtener más información, consulte [establecer la duración de un mensaje](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)).

3.  Cuando el dispositivo de cliente está disponible, FCM reenvía el mensaje a la aplicación de cliente en dicho dispositivo.

4.  La aplicación cliente recibe el mensaje de FCM, lo procesa y lo muestra al usuario. Por ejemplo, si el mensaje es una notificación remota, se presenta al usuario en el área de notificación.

En este escenario de mensajería (donde el servidor de aplicaciones envía un mensaje a una aplicación de cliente único), los mensajes pueden ser de hasta 4kB de longitud.

Para obtener información detallada sobre cómo recibir mensajes de nivel inferiores FCM en Android, consulte [notificaciones remoto con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).


<a name="topic" />

### <a name="topic-messaging"></a>Tema de mensajería

*Mensajería de tema* permite a un servidor de aplicaciones enviar un mensaje a varios dispositivos que han participado en un tema determinado. También puede redactar y enviar mensajes a través de la GUI de las notificaciones de la consola de Firebase del tema. FCM controla el enrutamiento y la entrega de mensajes del tema a los clientes suscritos. Esta característica se puede utilizar para los mensajes, como las alertas de tiempo y cotizaciones bursátiles, titulares de las noticias.

[![Diagrama de mensajería de tema](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png)

Los pasos siguientes se utilizan en la mensajería de tema (después de que la aplicación cliente obtiene un token de registro, como se explicó anteriormente):

1.  La aplicación cliente se suscribe a un tema mediante el envío de un mensaje de suscribirse a FCM.

2.  El servidor de aplicaciones envía mensajes de tema a FCM para su distribución.

3.  FCM reenvía mensajes del tema a los clientes que se han suscrito a ese tema.

Para obtener más información acerca de la mensajería de tema de Firebase, consulte de Google [tema mensajería en Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging).


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>Cómo configurar Firebase de mensajería en la nube

Antes de poder usar servicios FCM en su aplicación, debe crear un nuevo proyecto (o importar un proyecto existente) a través de la [Firebase consola](https://console.firebase.google.com/). Utilice los pasos siguientes para crear un proyecto de la aplicación Firebase Cloud Messaging:

1.  Inicie sesión en el [Firebase consola](https://console.firebase.google.com/) con su cuenta de Google (es decir, su dirección de Gmail) y haga clic en **crear nuevo proyecto**:

    [![Crear el botón nuevo proyecto](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png)

    Si tiene un proyecto existente, haga clic en **importar un proyecto de Google**.

2.  En el **crear un proyecto** cuadro de diálogo, escriba el nombre del proyecto y haga clic en **crear proyecto**. En el ejemplo siguiente, llama a un nuevo proyecto **XamarinFCM** se crea:

    [![Crear un cuadro de diálogo de proyecto](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png)

3.  En la consola de Firebase **Introducción**, haga clic en **Firebase agregar a la aplicación Android**:

    [![Agregar Firebase a la aplicación Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png)

4.  En la siguiente pantalla, escriba el nombre del paquete de la aplicación. En este ejemplo, el nombre del paquete es **com.xamarin.fcmexample**. Este valor debe coincidir con el nombre del paquete de la aplicación Android. También puede escribir un alias de la aplicación en el **sobrenombre de la aplicación** campo:

    [![En el ejemplo se FCM se especifica como el alias de la aplicación](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png)

5.  Si su aplicación usa enlaces dinámicos, invitaciones o Google Auth, también debe especificar el certificado de firma de depuración. Para obtener más información acerca de cómo buscar el certificado de firma, vea [buscar el almacén de claves MD5 o SHA1 firma](~/android/deploy-test/signing/keystore-signature.md).
    En este ejemplo, el certificado de firma se deja en blanco.

6.  Haga clic en **Agregar aplicación**:

    [![Haga clic en el botón Agregar aplicación](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png)

    Una clave de API de servidor y un identificador de cliente se generan automáticamente para la aplicación. Esta información se empaqueta en un **google services.json** archivo que se descarga automáticamente al hacer clic en **Agregar aplicación**.
    No olvide guardar este archivo en un lugar seguro.

Para obtener un ejemplo detallado de cómo agregar **google services.json** a un proyecto de aplicación para recibir mensajes de notificación de inserción FCM en Android, consulte [notificaciones remoto con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).


<a name="furtherreading" />

## <a name="for-further-reading"></a>Más información

-   Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) proporciona una visión general de las capacidades clave de mensajería de la nube en la Firebase, una explicación de cómo funciona y las instrucciones de instalación.

-   Google [crear aplicación de envío de solicitudes de servidor](https://firebase.google.com/docs/cloud-messaging/send-message) explica cómo enviar mensajes con el servidor de aplicaciones.

-   [RFC 6120](https://tools.ietf.org/html/rfc6120) y [6121 RFC](https://tools.ietf.org/html/rfc6121) explican y definir la mensajería Extensible y el protocolo de presencia (XMPP).


<a name="summary" />

## <a name="summary"></a>Resumen

Este artículo proporciona información general de Firebase en la nube de mensajería (FCM). Explican las varias credenciales que se usan para identificar y autorizar la mensajería entre aplicaciones de cliente y servidores de aplicaciones. Ilustran el registro y los escenarios de mensajería y los pasos detallados para registrar la aplicación con FCM usar FCM servicios.


## <a name="related-links"></a>Vínculos relacionados

- [Firebase de mensajería en la nube](https://firebase.google.com/docs/cloud-messaging/)
