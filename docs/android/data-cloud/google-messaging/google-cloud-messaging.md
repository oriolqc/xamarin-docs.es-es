---
title: Mensajería de nube de Google
description: Conexión compartida a Internet (GCM, Google Cloud Messaging) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Este artículo proporciona información general del funcionamiento de GCM y se explica cómo configurar servicios de Google, por lo que puede usar su aplicación GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: 29cccf414759a79a8ba74dfc35b7ba9f6a1cc5d6
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044748"
---
# <a name="google-cloud-messaging"></a>Mensajería de nube de Google

_Conexión compartida a Internet (GCM, Google Cloud Messaging) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. Este artículo proporciona información general del funcionamiento de GCM y se explica cómo configurar servicios de Google, por lo que puede usar su aplicación GCM._

[![Logotipo de Google Cloud Messaging](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

Este tema proporciona una descripción general de cómo Google Cloud Messaging enruta los mensajes entre la aplicación y un servidor de aplicaciones, y proporciona un procedimiento paso a paso para adquirir las credenciales para que la aplicación puede usar los servicios GCM.

> [!NOTE]
> Se han producido GCM [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM API de cliente y servidor [han quedado en desuso](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) y ya no estará disponible tan pronto como el 11 de abril de 2019.

## <a name="overview"></a>Información general

Conexión compartida a Internet (GCM, Google Cloud Messaging) es un servicio que controla el envío, enrutamiento y puesta en cola de mensajes entre las aplicaciones de servidor y las aplicaciones de cliente móvil. A *aplicación cliente* es una aplicación habilitada para GCM que se ejecuta en un dispositivo. El *servidor de aplicaciones* (proporcionado por usted o su compañía) es el servidor habilitado GCM que la aplicación cliente se comunica con a través de GCM:

[![GCM reside entre la aplicación cliente y el servidor de aplicaciones](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

Usando GCM, servidores de aplicaciones pueden enviar mensajes a un único dispositivo, un grupo de dispositivos o un número de dispositivos que están suscritos a un tema. La aplicación cliente puede utilizar GCM para suscribirse a los mensajes desde un servidor de aplicaciones (por ejemplo, para recibir notificaciones remotas). Además, GCM hace posible para las aplicaciones de cliente enviar mensajes de nivel superior en el servidor de aplicaciones.

Para obtener información acerca de cómo implementar un servidor de aplicaciones para GCM, consulte [sobre el servidor de conexión de GCM](https://developers.google.com/cloud-messaging/server).



## <a name="google-cloud-messaging-in-action"></a>Nube de Google de mensajería en acción

Cuando se envían mensajes siguen en la cadena de un servidor de aplicaciones para una aplicación cliente, el servidor de aplicaciones envía el mensaje a un *conexión del servidor de GCM*; el servidor de conexión GCM, a su vez, reenvía el mensaje a un dispositivo que ejecuta la aplicación cliente. Los mensajes pueden enviarse a través de HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (Protocolo de presencia y mensajería Extensible). Dado que las aplicaciones cliente no están siempre conectadas o en ejecución, el GCM conexión pone en cola y los almacenes de mensajes del servidor, enviarlos a las aplicaciones cliente cuando vuelva a conectar y estén disponibles. De forma similar, GCM va a poner en cola mensajes de nivel superior desde la aplicación cliente para el servidor de aplicaciones si el servidor de aplicaciones no está disponible.

GCM usa las credenciales siguientes para identificar el servidor de aplicaciones y la aplicación cliente, y usa estas credenciales para autorizar las transacciones de mensajes a través de GCM:

-   **Clave de API** &ndash; el *clave de API* proporciona el acceso al servidor de aplicaciones a servicios de Google; GCM utiliza esta clave para autenticar el servidor de aplicaciones.
    Para poder usar el servicio GCM, primero debe obtener una clave de API desde el [Google Developers Console](https://console.developers.google.com/) mediante la creación de un *proyecto*. La clave de API deben mantenerse segura; Para obtener más información acerca de cómo proteger la clave de API, consulte [las prácticas recomendadas para el uso de forma segura las claves de la API](https://support.google.com/cloud/answer/6310037?hl=en).

-   **Id. de remitente** &ndash; el *Id. de remitente* autoriza el servidor de aplicaciones para la aplicación cliente &ndash; es un número único que identifica el servidor de aplicaciones que se permite para enviar mensajes a la aplicación cliente.
    El identificador del remitente es también el número de proyecto; Obtenga el identificador de remitente de Google Developers Console al registrar el proyecto.

-   **Símbolo (token) de registro** &ndash; el *registro Token* es la identidad GCM de la aplicación de cliente en un dispositivo determinado. El token de registro se genera en tiempo de ejecución &ndash; la aplicación recibe un token de registro cuando registra por primera vez GCM mientras se ejecuta en un dispositivo. El token de registro autoriza a una instancia de la aplicación de cliente (ejecutándose en ese dispositivo concreto) para recibir mensajes de GCM.

-   **Identificador de la aplicación** &ndash; la identidad de la aplicación de cliente que se registra para recibir mensajes de GCM (independientemente de cualquier dispositivo determinado). En Android, el identificador de aplicación es el nombre de paquete que se registran en **AndroidManifest.xml**, como `com.xamarin.gcmexample`.

[Configuración de seguridad de Google Cloud Messaging](#settingup) (más adelante en esta guía) proporciona instrucciones detalladas para crear un proyecto y generar estas credenciales.

Las siguientes secciones explican cómo se utilizan estas credenciales cuando las aplicaciones cliente se comunican con servidores de aplicaciones a través de GCM.



### <a name="registration-with-gcm"></a>Registro de GCM

Una aplicación de cliente instalada en un dispositivo debe registrarse con GCM antes de que puede tener lugar la mensajería. La aplicación cliente debe completar los pasos de registro se muestra en el diagrama siguiente:

[![Pasos de registro de aplicación](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  La aplicación cliente se pone en contacto GCM para obtener un token de registro, pasar el identificador del remitente a GCM.

2.  GCM devuelve un token de registro a la aplicación cliente.

3.  La aplicación cliente envía el token de registro para el servidor de aplicaciones.

El servidor de aplicaciones se almacena en memoria caché el token de registro para las siguientes comunicaciones con la aplicación cliente. Opcionalmente, el servidor de aplicaciones puede enviar una confirmación de nuevo a la aplicación cliente para indicar que se ha recibido el token de registro. Después de producirse este protocolo de enlace, la aplicación cliente puede recibir mensajes de (o enviar mensajes a) el servidor de aplicaciones.

Cuando la aplicación de cliente ya no desea recibir mensajes desde el servidor de aplicaciones, puede enviar una solicitud al servidor de aplicaciones para eliminar el token de registro. Si la aplicación cliente recibe mensajes del tema (se explica más adelante en este artículo), puede cancelar la suscripción del tema.
Si se desinstala la aplicación de cliente desde un dispositivo, GCM detecta y notifica automáticamente el servidor de aplicaciones para eliminar el token de registro.

Google [registrar las aplicaciones cliente](https://developers.google.com/cloud-messaging/registration) explica el proceso de registro con más detalle; se explican la anulación del registro y cancelación de suscripción, y describe el proceso de anulación del registro cuando se desinstala una aplicación cliente.



### <a name="downstream-messaging"></a>En un nivel inferior de mensajería

Cuando el servidor de aplicaciones, envía un mensaje de nivel inferior a la aplicación cliente, sigue los pasos que se muestran en el diagrama siguiente:

[![Almacén de mensajería de nivel inferior y diagrama hacia delante](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  El servidor de aplicaciones envía el mensaje a GCM.

2.  Si el dispositivo de cliente no está disponible, el servidor GCM almacena el mensaje en una cola para su transmisión posterior.

3.  Cuando el dispositivo de cliente está disponible, GCM envía el mensaje a la aplicación de cliente en dicho dispositivo.

4.  La aplicación cliente recibe el mensaje de GCM y administra en consecuencia. Por ejemplo, si el mensaje es una notificación remota, se presenta al usuario.

En este escenario de mensajería (donde el servidor de aplicaciones envía un mensaje a una aplicación de cliente único), los mensajes pueden ser de hasta 4kB de longitud.

Para obtener información detallada (incluidos los ejemplos de código) sobre cómo recibir mensajes siguen en la cadena de GCM en Android, consulte [notificaciones remoto](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).


#### <a name="topic-messaging"></a>Tema de mensajería

*Mensajería de tema* es un tipo de nivel inferior de mensajería que el servidor de aplicaciones envía un mensaje único en varios dispositivos de la aplicación de cliente que se suscriben a un tema (por ejemplo, un boletín meteorológico). Mensajes de tema pueden ser hasta 2KB de longitud y mensajería de tema es compatible con hasta un millón de suscripciones por aplicación. Si se utiliza solo para el tema de mensajería GCM, la aplicación cliente no debe enviar un token de registro para el servidor de aplicaciones. Google [implementar mensajería tema](https://developers.google.com/cloud-messaging/topic-messaging) explica cómo enviar mensajes desde un servidor de aplicaciones en varios dispositivos que se suscriben a un tema determinado.



#### <a name="group-messaging"></a>Grupo de mensajería

*Grupo mensajería* es un tipo de nivel inferior de mensajería que el servidor de aplicaciones envía un mensaje único en varios dispositivos de la aplicación de cliente que pertenecen a un grupo (por ejemplo, un grupo de dispositivos que pertenecen a un único usuario). Agrupar los mensajes pueden ser hasta 2KB de longitud para dispositivos iOS y hasta 4KB de longitud para dispositivos Android. Un grupo está limitado a un máximo de 20 miembros. Google [dispositivo grupo mensajería](https://developers.google.com/cloud-messaging/notifications) explica cómo los servidores de aplicación pueden enviar un mensaje único a varias instancias de la aplicación cliente ejecuta en dispositivos que pertenecen a un grupo.


### <a name="upstream-messaging"></a>Mensajería de nivel superior

Si la aplicación cliente se conecta a un servidor que admita [XMPP](https://developers.google.com/cloud-messaging/ccs), puede enviar mensajes al servidor de aplicaciones como se muestra en el diagrama siguiente:

[![Diagrama de mensajería de nivel superior](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  La aplicación cliente envía un mensaje en el servidor de conexión de GCM XMPP.

2.  Si se desconecta el servidor de aplicaciones, el servidor GCM almacena el mensaje en una cola para reenviar más adelante.

3.  Una vez conectado de nuevo el servidor de aplicaciones, GCM reenvía el mensaje hasta el servidor de aplicaciones.

4.  El servidor de aplicaciones analiza el mensaje para comprobar la identidad de la aplicación de cliente, a continuación, envía una "confirmación" a GCM para confirmar la recepción de mensajes.

5.  El servidor de aplicaciones procesa el mensaje.

Google [mensajes de nivel superior](https://developers.google.com/cloud-messaging/ccs#upstream) explica cómo estructurar los mensajes codificados con JSON y enviarlos a servidores de aplicaciones que se ejecutan XMPP nube conexión de servidor de Google.


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Configuración de mensajería de nube de Google

Antes de poder usar servicios GCM en la aplicación, debe adquirir primero las credenciales para acceder a los servidores GCM de Google. Las siguientes secciones describen los pasos necesarios para completar este proceso:



### <a name="enable-google-services-for-your-app"></a>Habilitar los servicios de Google para la aplicación

1.  Inicie sesión en el [Google Developers Console](https://developers.google.com/mobile/add?platform=android) con su Google cuenta (es decir, su dirección de gmail) y cree un nuevo proyecto. Si tiene un proyecto existente, elija el proyecto que desea convertir en habilitado GCM. En el ejemplo siguiente, llama a un nuevo proyecto **XamarinGCM** se crea:

    [![Crear el proyecto XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  A continuación, escriba el nombre del paquete de la aplicación (en este ejemplo, el nombre del paquete es **com.xamarin.gcmexample**) y haga clic en **continuar para elegir y configurar servicios de**:

    [![Escriba el nombre del paquete](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Tenga en cuenta que este nombre de paquete también es el identificador de aplicación para la aplicación.

3.  El **elegir y configurar servicios** sección enumeran los servicios de Google que se pueden agregar a la aplicación. Haga clic en **de mensajería en la nube**:

    [![Elija en la nube de mensajería](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  A continuación, haga clic en **habilitar GOOGLE CLOUD MESSAGING**:

    [![Habilitar la mensajería de nube de Google](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  A **clave de API de servidor** y un **Id. de remitente** se generan para la aplicación. Estos valores de registro y haga clic en **cerrar**:

    [![Clave de API de servidor y muestra el identificador de remitente](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Proteger la clave de API &ndash; no está pensado para uso público. Si se pone en peligro la clave de API, servidores no autorizados pudieron publicar mensajes en las aplicaciones cliente.
    [Las prácticas recomendadas para el uso de forma segura las claves de API](https://support.google.com/cloud/answer/6310037?hl=en) proporciona unas directrices prácticas para proteger la clave de API.



### <a name="view-your-project-settings"></a>Ver la configuración del proyecto

Puede ver la configuración del proyecto en cualquier momento mediante la firma en el [consola en la nube de Google](https://console.cloud.google.com/) y seleccione el proyecto. Por ejemplo, puede ver el **Id. de remitente** seleccionando el proyecto en el menú desplegable en la parte superior de la página (en este ejemplo, el proyecto se denomina **XamarinGCM**). El identificador del remitente es el número de proyecto, como se muestra en esta captura de pantalla (el Id. de remitente es **9349932736**):

[![Ver el identificador del remitente](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Para ver el **clave de API**, haga clic en **API Manager** y, a continuación, haga clic en **credenciales**:

[![Ver la clave de API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>Más información

-   Google [registrar las aplicaciones cliente](https://developers.google.com/cloud-messaging/registration) describe el proceso de registro de cliente con más detalle, y proporciona información sobre cómo configurar el reintento automático y mantener sincronizados el estado de registro.

-   [RFC 6120](https://tools.ietf.org/html/rfc6120) y [6121 RFC](https://tools.ietf.org/html/rfc6121) explican y definir la mensajería Extensible y el protocolo de presencia (XMPP).



## <a name="summary"></a>Resumen

Este artículo proporciona información general de Google (GCM Cloud Messaging). Explican las varias credenciales que se usan para identificar y autorizar la mensajería entre aplicaciones de cliente y servidores de aplicaciones. Ilustran los escenarios de mensajería más comunes y detallan los pasos para registrar la aplicación con GCM para utilizar servicios GCM.


## <a name="related-links"></a>Vínculos relacionados

- [Mensajería en la nube](https://developers.google.com/cloud-messaging/)
