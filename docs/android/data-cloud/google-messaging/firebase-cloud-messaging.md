---
title: Firebase Cloud Messaging
description: Firebase Cloud Messaging (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Este artículo proporciona información general del funcionamiento de FCM y se explica cómo configurar los servicios de Google para que la aplicación puede usar FCM.
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/31/2018
ms.openlocfilehash: 92c402085627bbe67d4cd8ccaf60a68aa979f3e7
ms.sourcegitcommit: bf05041cc74fb05fd906746b8ca4d1403fc5cc7a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/04/2018
ms.locfileid: "39514300"
---
# <a name="firebase-cloud-messaging"></a>Firebase Cloud Messaging

_Firebase Cloud Messaging (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Este artículo proporciona información general del funcionamiento de FCM y se explica cómo configurar los servicios de Google para que la aplicación puede usar FCM._

[![Imagen de hero firebase Cloud Messaging](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

Este tema proporciona una descripción general de cómo Firebase Cloud Messaging enruta los mensajes entre la aplicación de Xamarin.Android y un servidor de aplicaciones y proporciona un procedimiento paso a paso para adquirir las credenciales para que la aplicación puede usar los servicios FCM.

## <a name="overview"></a>Información general

Firebase Cloud Messaging (FCM) es un servicio multiplataforma que controla el envío, enrutamiento y la puesta en cola de mensajes entre aplicaciones de servidor y las aplicaciones cliente móvil. FCM es el sucesor a Google Cloud Messaging (GCM), y se basa en Google Play Services.

Como se muestra en el diagrama siguiente, FCM actúa como intermediario entre los remitentes del mensaje y los clientes. Un *aplicación cliente* es una aplicación habilitada para FCM que se ejecuta en un dispositivo. El *del servidor de aplicaciones* (proporcionado por usted o su compañía) es el servidor habilita de FCM que la aplicación cliente se comunica a través de FCM. A diferencia de GCM, FCM hace posible para que pueda enviar mensajes a las aplicaciones cliente directamente a través de la GUI de las notificaciones de la consola de Firebase:

[![FCM se sitúa entre la aplicación cliente y un servidor de aplicaciones](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

Con FCM, los servidores de aplicaciones pueden enviar mensajes a un único dispositivo, a un grupo de dispositivos o a un número de dispositivos que se suscriben a un tema. Una aplicación cliente puede usar FCM para suscribirse a mensajes de nivel inferiores de un servidor de aplicaciones (por ejemplo, para recibir notificaciones remotas). Para obtener más información sobre los diferentes tipos de mensajes de Firebase, consulte [acerca de los mensajes FCM](https://firebase.google.com/docs/cloud-messaging/concept-options).

## <a name="fcm-in-action"></a>Firebase Cloud Messaging en acción

Cuando se envía un mensaje de nivel inferior para una aplicación cliente desde un servidor de aplicaciones, el servidor de la aplicación envía el mensaje a un *de conexión del servidor FCM* proporcionada por Google; el servidor de conexión FCM, a su vez, reenvía el mensaje a un dispositivo que se está ejecutando el aplicación cliente. Los mensajes pueden enviarse a través de HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (Protocolo de presencia y mensajería Extensible). Dado que las aplicaciones cliente no están siempre conectadas o está ejecutando, la conexión server pone en cola y los almacenes de los mensajes de FCM, enviarlos a las aplicaciones cliente, ya que volver a conectarse y estén disponibles. De forma similar, FCM enqueue ascendentes mensajes will desde la aplicación cliente al servidor de aplicaciones si el servidor de aplicaciones no está disponible. Para obtener más información acerca de los servidores de conexión de FCM, consulte [sobre Firebase Cloud Messaging Server](https://firebase.google.com/docs/cloud-messaging/server).

FCM utiliza las credenciales siguientes para identificar el servidor de aplicaciones y la aplicación cliente y usa estas credenciales para autorizar a las transacciones de mensajes a través de FCM:

-   <a name="fcm-in-action-sender-id"></a>**Id. de remitente** &ndash; el *Id. de remitente* es un valor numérico único que se asigna al crear el proyecto de Firebase. El Id. de remitente se utiliza para identificar cada servidor de aplicaciones que puede enviar mensajes a la aplicación cliente. El Id. de remitente también es el número de proyecto obtener el Id. de remitente en la consola de Firebase al registrar el proyecto. Un ejemplo de un Id. de remitente es `496915549731`.

-   <a name="fcm-in-action-api-key"></a>**Clave de API** &ndash; el *clave de API* permite el acceso al servidor de aplicaciones a los servicios de Firebase; FCM usa esta clave para autenticar el servidor de aplicaciones. Esta credencial se conoce también como el *clave de servidor* o *clave de API Web*. Un ejemplo de una clave de API es `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`.

-   <a name="fcm-in-action-app-id"></a>**Id. de aplicación** &ndash; la identidad de la aplicación de cliente que se registra para recibir mensajes de FCM (independientemente de cualquier dispositivo determinado). Un ejemplo de un identificador de aplicación es `1:415712510732:android:0e1eb7a661af2460`.

-   <a name="fcm-in-action-registration-token"></a>**Token de registro** &ndash; el *registro Token* (también se denomina el *Id. de instancia*) es la identidad FCM de la aplicación cliente en un dispositivo determinado. El token de registro se genera en tiempo de ejecución &ndash; la aplicación recibe un token de registro cuando registra primero con FCM mientras se ejecuta en un dispositivo. El token de registro, autoriza a una instancia de la aplicación de cliente (que se ejecutan en ese dispositivo concreto) para recibir mensajes de FCM.
    Un ejemplo de un token de registro es `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (una cadena muy larga).

[Configuración de Firebase Cloud Messaging](#setup_fcm) (más adelante en esta guía) proporciona instrucciones detalladas para crear un proyecto y generar estas credenciales. Cuando se crea un nuevo proyecto en el [consola Firebase](https://console.firebase.google.com/), un archivo de credenciales denominado **google-services.json** se crea &ndash; agregue este archivo a su proyecto de Xamarin.Android, como se explica en [ Notificaciones remotas con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

Las siguientes secciones explican cómo se utilizan estas credenciales cuando las aplicaciones cliente se comunican con los servidores de aplicaciones a través de FCM.


<a name="registration" />

### <a name="registration-with-fcm"></a>Registro con FCM

Una aplicación cliente primero debe registrar con FCM antes de mensajería puede tener lugar. La aplicación cliente debe completar los pasos de registro que se muestra en el diagrama siguiente:

[![Diagrama de los pasos de registro de aplicación](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1.  La aplicación cliente contacta con FCM para obtener un token de registro, pasando el Id. de remitente, la clave de API y el Id. de aplicación a FCM.

2.  FCM devuelve un token de registro a la aplicación cliente.

3.  La aplicación cliente (opcionalmente) reenvía el token de registro al servidor de aplicaciones.

El servidor de la aplicación almacena en caché el token de registro para las siguientes comunicaciones con la aplicación cliente. El servidor de aplicaciones puede enviar una confirmación de vuelta a la aplicación cliente para indicar que se ha recibido el token de registro. Después de producirse este protocolo de enlace, la aplicación cliente puede recibir mensajes de (o enviar mensajes a) el servidor de aplicaciones. La aplicación cliente puede recibir un nuevo token de registro si se pone en peligro el token anterior (consulte [notificaciones remotas con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) para obtener un ejemplo de cómo una aplicación recibe las actualizaciones de token de registro).

Cuando la aplicación cliente ya no desea recibir mensajes desde el servidor de aplicaciones, puede enviar una solicitud al servidor de aplicaciones para eliminar el token de registro. Si se desinstala la aplicación cliente desde un dispositivo, FCM lo detecta y notifica automáticamente al servidor de aplicaciones para eliminar el token de registro.



### <a name="downstream-messaging"></a>Mensajería de nivel inferior

El siguiente diagrama ilustra cómo Firebase Cloud Messaging almacena y reenvía los mensajes de nivel inferiores:

[![FCM usa almacenamiento y reenvío para la mensajería de nivel inferior](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

Cuando el servidor de la aplicación envía un mensaje de nivel inferior a la aplicación cliente, utiliza los siguientes pasos como enumerado en el diagrama anterior:

1.  El servidor de la aplicación envía el mensaje a FCM.

2.  Si el dispositivo cliente no está disponible, el servidor FCM almacena el mensaje en una cola para su posterior transmisión. Los mensajes se retienen en el almacenamiento FCM durante un máximo de 4 semanas (para obtener más información, consulte [establecer la duración de un mensaje](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)).

3.  Cuando el dispositivo de cliente está disponible, FCM reenvía el mensaje a la aplicación cliente en ese dispositivo.

4.  La aplicación cliente recibe el mensaje de FCM, lo procesa y lo muestra al usuario. Por ejemplo, si el mensaje es una notificación remota, se presenta al usuario en el área de notificación.

En este escenario de mensajería (donde el servidor de la aplicación envía un mensaje a una aplicación de cliente único), los mensajes pueden hasta 4kB de longitud.

Para obtener información detallada sobre cómo recibir mensajes FCM bajados en Android, consulte [notificaciones remotas con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

### <a name="topic-messaging"></a>Tema de mensajería

*Mensajería de tema* hace posible para un servidor de aplicaciones enviar un mensaje a varios dispositivos que han participado en un tema determinado. También puede redactar y enviar mensajes de tema a través de la GUI de las notificaciones de la consola de Firebase. FCM controla el enrutamiento y entrega de mensajes de tema a los clientes suscritos. Esta característica puede usarse para los mensajes como alertas meteorológicos, cotizaciones y titulares y noticias.

[![Diagrama de mensajería de tema](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

Los pasos siguientes se usan en la mensajería de tema (después de la aplicación cliente obtiene un token de registro como se explicó anteriormente):

1.  La aplicación cliente se suscribe a un tema mediante el envío de un mensaje de suscribirse a FCM.

2.  El servidor de la aplicación envía mensajes de tema a FCM para su distribución.

3.  FCM reenvía los mensajes de tema a los clientes que se han suscrito a dicho tema.

Para obtener más información acerca de la mensajería de Firebase tema, consulte de Google [tema mensajería en Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging).


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>Configuración de Firebase Cloud Messaging

Para poder usar los servicios FCM de la aplicación, debe crear un nuevo proyecto (o importar un proyecto existente) a través de la [consola Firebase](https://console.firebase.google.com/). Siga estos pasos para crear un proyecto de Firebase Cloud Messaging para la aplicación:

1.  Inicie sesión en el [consola Firebase](https://console.firebase.google.com/) con su cuenta de Google (es decir, la dirección de Gmail) y haga clic en **crear nuevo proyecto**:

    [![Crear un proyecto nuevo botón](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    Si tiene un proyecto existente, haga clic en **importar un proyecto de Google**.

2.  En el **crear un proyecto** cuadro de diálogo, escriba el nombre del proyecto y haga clic en **crear proyecto**. En el ejemplo siguiente, llama a un nuevo proyecto **XamarinFCM** se crea:

    [![Crear un cuadro de diálogo de proyecto](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3.  En la consola de Firebase **Introducción**, haga clic en **agregar Firebase a la aplicación Android**:

    [![Agregar Firebase a la aplicación Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4.  En la siguiente pantalla, escriba el nombre del paquete de la aplicación. En este ejemplo, es el nombre del paquete **com.xamarin.fcmexample**. Este valor debe coincidir con el nombre del paquete de la aplicación Android. También puede escribir un alias de la aplicación en el **sobrenombre aplicación** campo:

    [![Ejemplo de FCM se especifica como el alias de la aplicación](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5.  Si su aplicación usa enlaces dinámicos, invitaciones o Google Auth, también debe especificar el certificado de firma de depuración. Para obtener más información acerca de cómo encontrar el certificado de firma, vea [buscar su almacén de claves firma MD5 o SHA1](~/android/deploy-test/signing/keystore-signature.md).
    En este ejemplo, el certificado de firma se deja en blanco.

6.  Haga clic en **Agregar aplicación**:

    [![Al hacer clic en el botón Agregar aplicación](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    Una clave de API de servidor y un identificador de cliente se generan automáticamente para la aplicación. Esta información se empaqueta en un **google-services.json** archivo que se descarga automáticamente al hacer clic en **Agregar aplicación**.
    No olvide guardar este archivo en un lugar seguro.

Para obtener un ejemplo detallado de cómo agregar **google-services.json** a un proyecto de aplicación para recibir mensajes de notificación de inserción FCM en Android, consulte [notificaciones remotas con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).



## <a name="for-further-reading"></a>Para obtener más información

-   Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) proporciona una descripción general de funcionalidades clave de Firebase Cloud Messaging, obtener una explicación de cómo funciona y las instrucciones de configuración.

-   Google [crear servidor enviar las solicitudes de aplicación](https://firebase.google.com/docs/cloud-messaging/send-message) se explica cómo enviar mensajes con el servidor de aplicaciones.

-   [RFC 6120](https://tools.ietf.org/html/rfc6120) y [6121 RFC](https://tools.ietf.org/html/rfc6121) explicar y defina el Extensible de mensajería y el protocolo de presencia (XMPP).

-   [Acerca de los mensajes de FCM](https://firebase.google.com/docs/cloud-messaging/concept-options) describe los diferentes tipos de mensajes que se pueden enviar con Firebase Cloud Messaging.

## <a name="summary"></a>Resumen

En este artículo se proporciona información general de Firebase Cloud Messaging (FCM). Explican las credenciales distintos que se usan para identificar y autorizar a la mensajería entre los servidores de aplicaciones y las aplicaciones cliente. Ilustran el registro y los escenarios de mensajería de nivel inferiores, y detallan los pasos para registrar la aplicación con FCM para usar los servicios de FCM.


## <a name="related-links"></a>Vínculos relacionados

- [Mensajería en la nube de Firebase](https://firebase.google.com/docs/cloud-messaging/)
