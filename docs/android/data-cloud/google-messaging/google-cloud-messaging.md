---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Este artículo proporciona información general del funcionamiento de GCM y se explica cómo configurar los servicios de Google para que la aplicación pueda usar GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/02/2019
ms.openlocfilehash: 46de067e52ff141ea30a097562874ff4b2fa1c01
ms.sourcegitcommit: 53480ed32a126f88eec82e8c8ee5ed8d30616c44
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/02/2019
ms.locfileid: "65017710"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

> [!WARNING]
> Google está en desuso GCM desde el 10 de abril de 2018. Ya no se pueden mantener los proyectos de ejemplo y docs siguientes. Tan pronto como el 29 de mayo de 2019 se quitará del servidor de GCM y la API de cliente de Google. Google recomienda migrar las aplicaciones GCM para Firebase Cloud Messaging (FCM). Para obtener más información acerca de la degradación de GCM y migración, consulte [mensajería en la nube de Google en desuso](https://developers.google.com/cloud-messaging/).
>
> Para empezar a usar Firebase Cloud Messaging con Xamarin, consulte [Firebase Cloud Messaging](firebase-cloud-messaging.md).

_Google Cloud Messaging (GCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Este artículo proporciona información general del funcionamiento de GCM y se explica cómo configurar los servicios de Google para que la aplicación pueda usar GCM._

[![Logotipo de Google Cloud Messaging](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

Este tema proporciona una descripción general de cómo Google Cloud Messaging enruta los mensajes entre la aplicación y un servidor de aplicaciones y proporciona un procedimiento paso a paso para la adquisición de credenciales para que la aplicación puede usar los servicios GCM.

## <a name="overview"></a>Información general

Google Cloud Messaging (GCM) es un servicio que controla el envío, enrutamiento y la puesta en cola de mensajes entre aplicaciones de servidor y las aplicaciones cliente móvil. Un *aplicación cliente* es una aplicación habilitada para GCM que se ejecuta en un dispositivo. El *del servidor de aplicaciones* (proporcionado por usted o su compañía) es el servidor de GCM con que la aplicación cliente se comunica a través de GCM:

[![GCM reside entre la aplicación cliente y el servidor de aplicaciones](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

Los servidores de aplicaciones mediante GCM, pueden enviar mensajes a un único dispositivo, un grupo de dispositivos o un número de dispositivos que se suscriben a un tema. La aplicación cliente puede usar GCM para suscribirse a mensajes de nivel inferiores de un servidor de aplicaciones (por ejemplo, para recibir notificaciones remotas). Además, GCM hace posible que las aplicaciones cliente enviar mensajes de nivel superior al servidor de aplicación.

Para obtener información acerca de cómo implementar un servidor de aplicaciones para GCM, consulte [acerca del servidor de GCM conexión](https://developers.google.com/cloud-messaging/server).



## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging en acción

Cuando se envían mensajes de nivel inferiores desde un servidor de aplicaciones para una aplicación cliente, el servidor de la aplicación envía el mensaje a un *del servidor de GCM conexión*; el servidor de conexión GCM, a su vez, reenvía el mensaje a un dispositivo que se está ejecutando la aplicación cliente. Los mensajes pueden enviarse a través de HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (Protocolo de presencia y mensajería Extensible). Dado que las aplicaciones cliente no están siempre conectadas o están ejecutando, los GCM conexión server pone en cola y los almacenes de mensajes, enviarlos a las aplicaciones cliente, ya que volver a conectarse y estén disponibles. De forma similar, GCM enqueue ascendentes mensajes will desde la aplicación cliente al servidor de aplicaciones si el servidor de aplicaciones no está disponible.

GCM utiliza las credenciales siguientes para identificar el servidor de aplicaciones y la aplicación cliente y utiliza estas credenciales para autorizar a las transacciones de mensajes a través de GCM:

-   **Clave de API** &ndash; el *clave de API* permite el acceso al servidor de aplicaciones a los servicios de Google; GCM usa esta clave para autenticar el servidor de aplicaciones.
    Para poder usar el servicio GCM, primero debe obtener una clave de API desde el [Google Developer Console](https://console.developers.google.com/) mediante la creación de un *proyecto*. La clave de API debe mantenerse segura; Para obtener más información sobre cómo proteger la clave de API, consulte [procedimientos recomendados para el uso de forma segura las claves de API](https://support.google.com/cloud/answer/6310037?hl=en).

-   **Id. de remitente** &ndash; el *Id. de remitente* autoriza el servidor de aplicaciones a la aplicación cliente &ndash; es un número único que identifica el servidor de aplicación que tiene permiso para enviar mensajes a la aplicación cliente.
    El Id. de remitente también es el número de proyecto obtener el Id. de remitente de la consola de desarrolladores de Google al registrar el proyecto.

-   **Token de registro** &ndash; el *registro Token* es la identidad GCM de la aplicación cliente en un dispositivo determinado. El token de registro se genera en tiempo de ejecución &ndash; la aplicación recibe un token de registro cuando registra por primera vez GCM mientras se ejecuta en un dispositivo. El token de registro, autoriza a una instancia de la aplicación de cliente (que se ejecutan en ese dispositivo concreto) para recibir mensajes de GCM.

-   **Id. de aplicación** &ndash; la identidad de la aplicación de cliente que se registra para recibir mensajes de GCM (independientemente de cualquier dispositivo determinado). En Android, el identificador de aplicación es el nombre del paquete registrado en **AndroidManifest.xml**, tales como `com.xamarin.gcmexample`.

[Configuración de seguridad de Google Cloud Messaging](#settingup) (más adelante en esta guía) proporciona instrucciones detalladas para crear un proyecto y generar estas credenciales.

Las siguientes secciones explican cómo se utilizan estas credenciales cuando las aplicaciones cliente se comunican con los servidores de aplicaciones a través de GCM.



### <a name="registration-with-gcm"></a>Registro con GCM

Una aplicación de cliente instalada en un dispositivo debe registrarse con GCM antes de mensajería puede tener lugar. La aplicación cliente debe completar los pasos de registro que se muestra en el diagrama siguiente:

[![Pasos de registro de aplicación](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  La aplicación cliente contacta con GCM para obtener un token de registro, pasando el identificador del remitente a GCM.

2.  GCM devuelve un token de registro a la aplicación cliente.

3.  La aplicación cliente envía el token de registro al servidor de aplicaciones.

El servidor de la aplicación almacena en caché el token de registro para las siguientes comunicaciones con la aplicación cliente. Si lo desea, el servidor de aplicaciones puede enviar una confirmación de vuelta a la aplicación cliente para indicar que se ha recibido el token de registro. Después de producirse este protocolo de enlace, la aplicación cliente puede recibir mensajes de (o enviar mensajes a) el servidor de aplicaciones.

Cuando la aplicación cliente ya no desea recibir mensajes desde el servidor de aplicaciones, puede enviar una solicitud al servidor de aplicaciones para eliminar el token de registro. Si la aplicación cliente recibe mensajes de tema (se explica más adelante en este artículo), puede cancelar la suscripción del tema.
Si se desinstala la aplicación cliente desde un dispositivo, GCM lo detecta y notifica automáticamente al servidor de aplicaciones para eliminar el token de registro.

Google [registrar aplicaciones de cliente](https://developers.google.com/cloud-messaging/registration) explica el proceso de registro con más detalle; se explican la anulación del registro y cancelación de suscripción y describe el proceso de anulación del registro cuando se desinstala una aplicación cliente.



### <a name="downstream-messaging"></a>Mensajería de nivel inferior

Cuando el servidor de la aplicación envía un mensaje de nivel inferior a la aplicación cliente, sigue los pasos que se muestran en el diagrama siguiente:

[![Almacén de mensajería de nivel inferior y diagrama hacia delante](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  El servidor de la aplicación envía el mensaje a GCM.

2.  Si el dispositivo cliente no está disponible, el servidor GCM almacena el mensaje en una cola para su posterior transmisión.

3.  Cuando el dispositivo de cliente está disponible, GCM envía el mensaje a la aplicación cliente en ese dispositivo.

4.  La aplicación cliente recibe el mensaje de GCM y administra en consecuencia. Por ejemplo, si el mensaje es una notificación remota, se presenta al usuario.

En este escenario de mensajería (donde el servidor de la aplicación envía un mensaje a una aplicación de cliente único), los mensajes pueden hasta 4kB de longitud.

Para obtener información detallada (incluidos ejemplos de código) sobre cómo recibir mensajes de bajada GCM en Android, consulte [notificaciones remotas](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).


#### <a name="topic-messaging"></a>Tema de mensajería

*Mensajería de tema* es un tipo de mensajería de nivel inferior que el servidor de la aplicación envía un mensaje único a varios dispositivos de la aplicación de cliente que se suscriben a un tema (por ejemplo, un pronóstico meteorológico). Los mensajes del tema pueden ser hasta 2KB de longitud y mensajería de tema es compatible con hasta un millón de suscripciones por aplicación. Si se usa solo para el tema de mensajería GCM, la aplicación cliente no es necesario para enviar un token de registro al servidor de aplicaciones. Google [implementar mensajería tema](https://developers.google.com/cloud-messaging/topic-messaging) se explica cómo enviar mensajes desde un servidor de aplicaciones para varios dispositivos que se suscriben a un tema determinado.



#### <a name="group-messaging"></a>Grupo de mensajería

*Grupo mensajería* es un tipo de mensajería de nivel inferior que el servidor de la aplicación envía un mensaje único a varios dispositivos de la aplicación de cliente que pertenecen a un grupo (por ejemplo, un grupo de dispositivos que pertenecen a un único usuario). Agrupar los mensajes pueden ser hasta 2KB de longitud para dispositivos iOS y hasta 4KB de longitud para dispositivos Android. Un grupo se limita a un máximo de 20 miembros. Google [Device Messaging grupo](https://developers.google.com/cloud-messaging/notifications) explica cómo los servidores de aplicaciones pueden enviar un mensaje único a varias instancias de la aplicación cliente que se ejecutan en dispositivos que pertenecen a un grupo.


### <a name="upstream-messaging"></a>Mensajería ascendente

Si la aplicación cliente se conecta a un servidor que admita [XMPP](https://developers.google.com/cloud-messaging/ccs), puede enviar mensajes al servidor de aplicación como se muestra en el diagrama siguiente:

[![Diagrama de mensajería de nivel superior](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  La aplicación cliente envía un mensaje en el servidor de conexión de GCM XMPP.

2.  Si se desconecta el servidor de aplicaciones, el servidor GCM almacena el mensaje en una cola de transmisión más adelante.

3.  Cuando está conectado de nuevo el servidor de aplicaciones, GCM reenvía el mensaje al servidor de aplicaciones.

4.  El servidor de la aplicación analiza el mensaje para comprobar la identidad de la aplicación cliente, a continuación, envía una confirmación"" a GCM para confirmar la recepción del mensaje.

5.  El servidor de la aplicación procesa el mensaje.

Google [mensajes ascendente](https://developers.google.com/cloud-messaging/ccs#upstream) explica cómo estructurar los mensajes codificados en JSON y enviarlos a los servidores de aplicaciones que ejecutan el servidor de conexión de nube basada en XMPP de Google.


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Configuración de Google Cloud Messaging

Para poder usar los servicios GCM en la aplicación, debe adquirir primero las credenciales para tener acceso a los servidores de GCM de Google. Las secciones siguientes describen los pasos necesarios para completar este proceso:



### <a name="enable-google-services-for-your-app"></a>Habilitar servicios de Google para la aplicación

1.  Inicie sesión en el [Google Developers Console](https://developers.google.com/mobile/add?platform=android) con su Google, cuenta (es decir, su dirección de gmail) y cree un nuevo proyecto. Si tiene un proyecto existente, elija el proyecto que desee convertir en GCM habilitado. En el ejemplo siguiente, llama a un nuevo proyecto **XamarinGCM** se crea:

    [![Al crear el proyecto XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  A continuación, escriba el nombre del paquete de la aplicación (en este ejemplo, es el nombre del paquete **com.xamarin.gcmexample**) y haga clic en **continuar para elegir y configurar servicios**:

    [![Escriba el nombre del paquete](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Tenga en cuenta que este nombre del paquete también es el identificador de aplicación para la aplicación.

3.  El **elegir y configurar servicios** sección enumeran los servicios de Google que se pueden agregar a la aplicación. Haga clic en **mensajería en la nube**:

    [![Elija en la nube de mensajería](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  A continuación, haga clic en **habilitar GOOGLE CLOUD MESSAGING**:

    [![Habilitación de Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  Un **clave de API de servidor** y un **Id. de remitente** se generan para la aplicación. Estos valores de registro y haga clic en **cerrar**:

    [![Clave de API de servidor y el Id. de remitente muestra](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Proteger la clave de API &ndash; no sirve para uso público. Si se pone en peligro la clave de API, los servidores no autorizados podrían publicar mensajes en las aplicaciones cliente.
    [Procedimientos recomendados para el uso de forma segura las claves de API](https://support.google.com/cloud/answer/6310037?hl=en) proporciona unas directrices prácticas para proteger la clave de API.



### <a name="view-your-project-settings"></a>Ver la configuración del proyecto

Puede ver la configuración del proyecto en cualquier momento, inicie sesión en el [consola de Google Cloud](https://console.cloud.google.com/) y seleccionando el proyecto. Por ejemplo, puede ver el **Id. de remitente** seleccionando el proyecto en el menú desplegable en la parte superior de la página (en este ejemplo, el proyecto se denomina **XamarinGCM**). El identificador del remitente es el número de proyecto, como se muestra en esta captura de pantalla (es el Id. de remitente **9349932736**):

[![Ver el Id. de remitente](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Para ver el **clave de API**, haga clic en **Manager API** y, a continuación, haga clic en **credenciales**:

[![Visualización de la clave de API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>Más información

-   Google [registrar aplicaciones de cliente](https://developers.google.com/cloud-messaging/registration) describe el proceso de registro de cliente con más detalle, y se proporciona información sobre cómo configurar el reintento automático y mantener sincronizados el estado de registro.

-   [RFC 6120](https://tools.ietf.org/html/rfc6120) y [6121 RFC](https://tools.ietf.org/html/rfc6121) explicar y defina el Extensible de mensajería y el protocolo de presencia (XMPP).



## <a name="summary"></a>Resumen

En este artículo se proporciona información general de Google Cloud Messaging (GCM). Explican las credenciales distintos que se usan para identificar y autorizar a la mensajería entre los servidores de aplicaciones y las aplicaciones cliente. Ilustran los escenarios de mensajería más comunes, y detallan los pasos para registrar la aplicación con GCM para usar los servicios de GCM.


## <a name="related-links"></a>Vínculos relacionados

- [Mensajería en la nube](https://developers.google.com/cloud-messaging/)
