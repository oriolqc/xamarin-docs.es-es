---
title: "Introducción a iOS 10"
description: "Este artículo detallan todas las API y características disponibles en iOS 10 nueva y modificada para desarrolladores de Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: d5618ad4477cadfe8977faa9616f5ab6201f14ae
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-10"></a>Introducción a iOS 10

_Este artículo detallan todas las API y características disponibles en iOS 10 nueva y modificada para desarrolladores de Xamarin.iOS._

## <a name="introducing-ios-10"></a>Introducción a iOS 10

Con el nuevo iOS 10 SDK, Apple ha incluido nuevas API y servicios que permiten a los desarrolladores crear nuevas categorías de aplicaciones y características. Una aplicación de iOS ahora puede extender las aplicaciones de mensajes, Siri, teléfono y asignaciones para proporcionar la funcionalidad enriquecida y atractiva para el usuario final que no estaba disponible anteriormente.

Para obtener más información sobre iOS 10, vea de Apple [iOS + aplicaciones](https://developer.apple.com/ios/) documentación.

## <a name="whats-new-in-ios-10"></a>What's New en iOS 10

Apple ha agregado varias nuevas API y servicios en iOS 10 junto con muchas mejoras en las características existentes, incluyendo:


## <a name="adapting-to-the-true-tone-display"></a>Adaptar a la pantalla de tono True

La tecnología de tono True muestre de Apple usa el sensor de luz ambiental en un dispositivo iOS para ajustar dinámicamente el color y la intensidad de la pantalla para que coincida con las condiciones de iluminación actuales. iOS 10 proporciona las nuevas [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) clave que se pueden agregar a la aplicación `Info.plist` de archivos y controla cómo tono True se aplica el cambio de color estándar. 

Están disponibles los siguientes valores:

- `UIWhitePointAdaptivityStyleStandard` **Predeterminado** -usar la adaptabilidad punto blanco estándar.
- `UIWhitePointAdaptivityStyleReading` : Se utiliza para las aplicaciones de lectura y centrado.
- `UIWhitePointAdaptivityStyleGame` : Se utiliza para las aplicaciones centrada en el juego.
- `UIWhitePointAdaptivityStyleVideo` : Se utiliza para las aplicaciones de vídeo y centrado.
- `UIWhitePointAdaptivityStylePhoto` : Se utiliza para aplicaciones de la fotografía y centrado donde es más importante que los ajustes de punto de blanco medioambientales fidelidad del color.

<a name="app-extensions" />

## <a name="app-extensions"></a>Extensiones de aplicaciones

Apple ha proporcionado varios nuevos puntos de extensión de aplicación de iOS 10:

- Llame al directorio
- Propósitos y calidades de interfaz de usuario
- Mensajes
- Contenido de la notificación
- Notification Services
- Módulo de etiqueta

Además, 3er extensiones de aplicación de teclado de terceros tienen las siguientes mejoras:

- El nuevo `DocumentInputMode` propiedad de la `UITextDocumentProxy` clase puede determinar el idioma de entrada de un documento y permitir la extensión de teclado alinear con ese idioma.
- El nuevo `HandleInputModeList` método permite la extensión de teclado Mostrar menú del selector de teclado del sistema en respuesta a la clave del mundo que se puntea.

Para obtener más información, vea nuestra [Introducción a las extensiones](~/ios/platform/extensions.md), [integración de aplicaciones de mensaje](~/ios/platform/message-app-integration/index.md), [Introducción a sugerencias automático](~/ios/platform/search/proactive-suggestions.md), [ Introducción a SiriKit](~/ios/platform/sirikit/index.md), [Introducción a las notificaciones de usuario](~/ios/platform/user-notifications/index.md) y de Apple [Guía de programación de la extensión de la aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

## <a name="app-search-enhancements"></a>Mejoras en la búsqueda de la aplicación

Noticias destacadas de núcleos en iOS 10 incluye varias mejoras en la búsqueda de la aplicación como:

- **Popularidad de vínculo profundo de Crowdsourced (con privacidad diferencial)** -proporciona una manera para promover el contenido de la aplicación con vínculo profundo en resultados de búsqueda.
- **Buscar en la aplicación** -usar el nuevo `CSSearchQuery` clase para proporcionar capacidad de búsqueda de Spotlight de aplicación similar al funcionan de las aplicaciones de correo electrónico, mensajes y notas.
- **Buscar continuación** : permite a un usuario iniciar una búsqueda de Spotlight o Safari, a continuación, abrir una aplicación y continuar la búsqueda.
- **Visualización de resultados de validación** -de Apple [herramienta de validación de API de búsqueda de aplicación](https://search.developer.apple.com/appsearch-validation-tool) ahora muestra una representación visual de la vinculación profunda y marcado de un sitio Web cuando preforming pruebas.
- **Imagen de aplicación de uso compartido de mensaje** -permite populares imágenes de aplicación proporcionadas para el uso compartido de mensajes (a través de una extensión de la aplicación de mensaje) para que aparezca en las búsquedas de servicios.

Para obtener más información, visite nuestro [mejoras en la aplicación de búsqueda](~/ios/platform/search/app-search-enhancements.md) guía.

## <a name="apple-pay-enhancements"></a>Mejoras de pago de Apple

Apple ha realizado varias mejoras en Apple Pay en iOS 10 que permiten al usuario realizar pagos seguros de sitios Web y a través de la interacción con Siri y mapas.

Con iOS 10, varias nuevas API se han agregado que funcionan con iOS y watchOS para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

Además, el marco de trabajo PassKit se ha ampliado para admitir Apple Pay fuera de `UIKit` y deben permitir los emisores de tarjeta presentar sus tarjetas desde dentro de sus aplicaciones.

Para obtener más información, visite nuestro [Apple pagar mejoras](~/ios/platform/apple-pay.md) guía.

## <a name="alternate-app-icons"></a>Iconos de aplicación alternativo

Apple ha agregado varias mejoras para iOS 10.3 que permiten a una aplicación administrar su icono:

 - `ApplicationIconBadgeNumber` -Obtiene o establece el identificador del icono de aplicación en el Springboard.
 - `SupportsAlternateIcons` -If `true` la aplicación tiene un conjunto alternativo de iconos.
 - `AlternateIconName` -Devuelve el nombre del icono alternativo seleccionado actualmente o `null` si utilizando el icono principal.
 - `SetAlternameIconName` : Utilice este método para cambiar el icono de la aplicación al icono alternativo determinado.

Para obtener más información, visite nuestro [alternativo iconos de aplicación](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) guía.


## <a name="introduction-to-callkit"></a>Introducción a CallKit

La nueva API de CallKit en iOS 10 proporciona una manera para las aplicaciones VOIP para integrarse con la interfaz de usuario de iPhone y proporcionar una interfaz conocida y experiencia para el usuario final. Con esta API, los usuarios pueden ver e interactúa con las llamadas VOIP desde la pantalla de bloqueo del dispositivo iOS y administrar contactos mediante la aplicación de teléfono **favoritos** y **recientes** vistas.

Además, la API de CallKit proporciona la capacidad para crear extensiones de aplicación que se puede asociar un número de teléfono con un nombre (identificador de llamada) o indique el sistema cuando debe ser un número bloqueado (llamadas de bloqueo).

Para obtener más información, visite nuestro [Introducción a Callkit](~/ios/platform/callkit.md) guía.

## <a name="message-app-integration"></a>Integración de aplicaciones de mensaje

iOS 10 permite la inclusión de una extensión de la aplicación de mensaje en la solución de Xamarin.iOS que se integra con la **mensajes** aplicación y presenta nueva funcionalidad al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos. Existen dos tipos de extensión de la aplicación de mensaje:

- **Módulos de etiqueta** -contiene una colección de etiquetas que el usuario puede agregar a un mensaje. Módulos de etiqueta se pueden crear sin escribir ningún código.
- **iMessage aplicación** -puede presentar una interfaz de usuario personalizada dentro de la aplicación de mensajes para seleccionar adhesivos, escribir texto, incluidos los archivos de medios (con las conversiones de tipo opcional) y crear, modificar y enviar mensajes de interacción.

Para obtener más información, visite nuestro [integración de aplicaciones de mensaje](~/ios/platform/message-app-integration/index.md) guía.

## <a name="news-publisher-enhancements"></a>Mejoras de publicador de noticias

Con iOS 10, Apple se permite a ningún usuario de revistas principales y nuevas organizaciones blogs y editores independientes para registrarse y producto y distribuir el contenido a la aplicación de noticias de Apple. Para obtener más información, vea de Apple [noticias recursos](https://newsresources.apple.com/) documentación.

## <a name="providing-haptic-feedback"></a>Proporcionar comentarios hápticos

En el iPhone 7 y iPhone 7 +, Apple ha incluido nuevas respuestas haptics que proporcionan otras formas de ponerse en contacto físicamente el usuario. Usar las nuevas opciones de comentarios al tacto para obtener la atención del usuario y reforzar sus acciones.

Varios elementos de interfaz de usuario integrados ya proporcionen comentarios hápticos como selectores, los conmutadores y los controles deslizantes. iOS 10 ahora agrega la capacidad de desencadenar mediante programación haptics con una subclase concreta de la `UIFeedbackGenerator` clase.

Para obtener más información, visite nuestro [proporcionar comentarios hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md) guía.

## <a name="proactive-suggestions"></a>Sugerencias automático

iOS 10 presenta nuevas formas de contratación conducir a una aplicación al permitir que el sistema de forma proactiva presentar información útil automáticamente al usuario en los momentos adecuados. Al igual que iOS 9 proporcionan la capacidad de Agregar búsqueda en profundidad la aplicación con servicios, entrega y sugerencias de Siri, con iOS 10 que una aplicación puede exponer la funcionalidad que se puede presentar al usuario por el sistema desde dentro de las siguientes ubicaciones:

- El selector de la aplicación
- La pantalla de bloqueo
- CarPlay
- Asignaciones
- Interacciones de Siri
- Sugerencias de QuickType 

Una aplicación expone esta funcionalidad en el sistema mediante un conjunto de tecnologías como [NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/), marcado web, servicios de núcleo, MapKit, el Reproductor de Media y UIKit.

Para obtener más información, visite nuestro [Introducción a sugerencias automático](~/ios/platform/search/proactive-suggestions.md) guía.

## <a name="request-app-review"></a>Solicitar revisión de aplicación

Nuevo en iOS 10.3 la `RequestReview()` método permite que una aplicación iOS pedir al usuario que calificar o revisarlo. Aunque este método se puede llamar en cualquier punto donde tiene sentido en la experiencia del usuario, el proceso de revisión rige y controlado por una directiva de almacén de aplicación. Como resultado, este método puede o no puede mostrar una alerta y nunca se debería llamar en respuesta a una acción del usuario, como pulsar un botón.

Para obtener más información, visite nuestro [solicitar revisión de aplicación](~/ios/platform/request-app-review.md) guía.

## <a name="security-and-privacy-enhancements"></a>Mejoras de seguridad y privacidad

Apple ha realizado varias mejoras en la seguridad y privacidad en 10 de iOS que le ayudará a los desarrolladores mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final.

Como resultado, aplicaciones que se ejecutan en iOS 10 (o posterior) deben declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de una o varias claves específicas de privacidad de sus `Info.plist` archivos que explican al usuario ¿por qué la aplicación desea tener acceso.

Para obtener más información, visite nuestro [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) guía.

## <a name="sirikit"></a>SiriKit

Nuevo en 10 de iOS, SiriKit permite que una aplicación de Xamarin.iOS proporcionar servicios que son accesibles para el usuario mediante Siri en un dispositivo iOS. Esta funcionalidad se proporciona en uno o más extensión de la aplicación con el nuevo **intentos** y **calidades de interfaz de usuario** marcos de trabajo.

SiriKit es compatible con los dominios de servicio siguientes:

- Una llamada de vídeo o audio.
- Transporte de reserva.
- Administrar entrenamientos.
- Mensajería.
- Buscar fotografías.
- Enviar o recibir pagos.

Cuando el usuario realiza una solicitud de Siri que implican a uno de los servicios de la extensión de la aplicación, SiriKit envía la extensión de un **intención** objeto que describe la solicitud del usuario junto con cualquier dato de apoyo. La extensión de la aplicación, a continuación, genera la correspondiente **respuesta** de objeto para el determinado **intención**, que detalla cómo la extensión puede atender la solicitud.

Siri normalmente controla toda la interacción del usuario, la extensión de la aplicación puede utilizar el **intención de interfaz de usuario** framework para presentar un amplio, personalizado interfaz de usuario que incluye la aplicación de la personalización de marca y la información adicional.

Para obtener más información, visite nuestro [Introducción a SiriKit](~/ios/platform/sirikit/index.md) guía.

## <a name="speech-recognition"></a>Reconocimiento de voz

iOS 10 incluye una nueva API de voz que permite que la aplicación admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivos o grabados) en texto.

Dado que el reconocimiento de voz requiere la transmisión y el almacenamiento temporal de datos en servidores de Apple, la aplicación _debe_ solicitar el permiso del usuario para realizar el reconocimiento mediante la inclusión de la `NSSpeechRecognitionUsageDescription` clave en su `Info.plist` archivo y llamar a la `SFSpeechRecognizer.RequestAutorization` método.

Para obtener más información, visite nuestro [Introducción para el reconocimiento de voz](~/ios/platform/speech.md) guía.

## <a name="user-notifications"></a>Notificaciones de usuario

Nuevo en 10, la notificación al usuario framework permite la entrega y el manejo de notificaciones locales y remotas de iOS. Con este marco de trabajo, la aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

Además, la aplicación o la extensión puede recibir (y potencialmente modificar) las notificaciones locales y remotas que se entregan al dispositivo de iOS del usuario.

El nuevo marco de interfaz de usuario de notificación de usuario permite que la aplicación o extensión de la aplicación para personalizar la apariencia de las notificaciones locales y remotas cuando se presentan al usuario.

Para obtener más información, visite nuestro [Framework de notificaciones de usuario](~/ios/platform/user-notifications/index.md) guía.

## <a name="video-subscriber-account"></a>Cuenta del suscriptor de vídeo

Nueva para iOS 10, el marco de trabajo de la cuenta del suscriptor de vídeo permite aplicaciones esa compatibilidad autenticado con transmisión por secuencias o vídeo bajo demanda para autenticarse con su proveedor de TV por cable o satélite mediante una experiencia de inicio de sesión único para el usuario final.

## <a name="wide-color"></a>Color amplia

iOS 10 amplía la compatibilidad con formatos de píxel de rango extendido y los espacios de la amplia gama de colores en todo el sistema incluidos los marcos, como los gráficos esenciales, imagen Core, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas de color amplia adicional se ve facilitado por proporcionar este comportamiento en toda la pila completa de gráficos.

Además, [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) se ha modificado para que funcione en el nuevo extendidos **sRGB** espacio de color, lo que facilita la combinación de colores de gamas de color amplia sin pérdida de rendimiento significativas.

Apple ofrece las siguientes prácticas recomendadas al trabajar con colores ancho:

- [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/) ahora se utiliza un espacio de colores sRGB y se dejará sujete valores para la `0.0` a `1.0` intervalo. Si la aplicación se basa en el comportamiento de clamp anterior, deberá modificarse para iOS 10.
- El entorno de dibujo se configurará para el espacio de color sRGB al realizar personalizado `UIView` dibujar en un iPad Pro.
- Si la aplicación realiza la representación personalizada de `UIImages`, use la nueva [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) clase para especificar el uso de los formatos de rango extendido o intervalo estándar.
- Cuando se usa una API de bajo nivel, como gráficos de núcleo o sistema operativo para proporcionar procesamiento de imágenes, el desarrollador debe usan un formato color de rango extendido espacio y de píxeles que admite valores de punto flotante de 16 bits. En caso necesario, el desarrollador tenga que sujete manualmente los valores de componente de color.
- Para los gráficos esenciales, imagen Core y los sombreadores de rendimiento de sistema operativo proporcionan nuevos métodos para convertir entre los espacios de dos color.

Para obtener más información, visite nuestro [Introducción a Color amplia](~/ios/platform/wide-color.md) guía.

## <a name="widget-enhancements"></a>Mejoras de widgets

Apple ha introducido varias mejoras en el sistema de Widget para asegurarse de que los widgets tienen un aspecto excelentes en cualquier fondo que existe en el nuevo iOS 10 la pantalla de bloqueo. El [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) propiedad ha quedado desusada y se ha reemplazado por el nuevo [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) propiedades. Además, los widgets ahora contienen una [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propiedad que permite al desarrollador describir el contenido está disponible y permite al usuario expandir y contraer el contenido.

Para obtener más información, visite nuestro [búsqueda y mejoras de Widget de pantalla Inicio](~/ios/platform/search/widgets.md) guía.

## <a name="additional-framework-changes"></a>Cambios de Framework adicionales

Además de los cambios principales framework y adiciones enumeradas anteriormente, Apple ha realizado muchos cambios de marco secundarias adicionales en iOS 10.

Para obtener más información, visite nuestro [Framework modificándola](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) guía.

## <a name="deprecated-apis"></a>Interfaces API en desuso

Las API siguientes han quedado obsoletas en iOS 10:

- El `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` y `CKFetchRecordChangesOperation` clases han quedado obsoletas en CloudKit para iOS 10. Use la [CKDiscoverAllUserIdentitiesOperation](https://developer.xamarin.com/api/type/CloudKit.CKDiscoverUserIdentitiesOperation/), [CKUserIdentity](https://developer.xamarin.com/api/type/CloudKit.CKUserIdentity/) y [CKFetchRecordZoneChangesOperation](https://developer.xamarin.com/api/type/CloudKit.CKFetchRecordZoneChangesOperation/) clases (que admiten el uso compartido de registro) en su lugar.
- Varios [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) API (por ejemplo, las suscripciones basados en consultas y zona) han quedado en desuso. Use la [CKRecordZoneSubscription](https://developer.xamarin.com/api/type/CloudKit.CKRecordZoneSubscription/) y [CKQuerySubscription](https://developer.xamarin.com/api/type/CloudKit.CKQuerySubscription/) API en su lugar.
- [NSPersistentStoreCoordnator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) han quedado en desuso símbolos relacionados con el contenido.
- `ADBannerView`, `ADInterstitialAd` y relacionados con los símbolos en el [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/) clase han quedado en desuso.
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) símbolos relacionados con valores de punto flotante han quedado en desuso.
- El `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` y `UIUserNotificationSettings` clases de UIKit han quedado en desuso. Use la [las notificaciones de usuario](#User-Notifications) framework en su lugar.
- El `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` y `DidReceiveRemoteNotification` WatchKit métodos han quedado en desuso. Use la `HandleActionForNotification` y `DidReceiveNotification` métodos en su lugar.
- El `DidReceiveLocalNotification` y `DidReceiveRemoteNotification` métodos de la [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) han quedado en desuso. Cree una instancia de [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) que implementa los métodos adecuados y asígnelo a la `Delegate` propiedad de la [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) objeto.
- El **aplicación de centro de juego** en desuso y quitado de iOS. Si la aplicación usa GameKit, lo _debe_ presentar su propia interfaz para mostrar las características de GameKit como tablas de líderes, etcetera.

Vea de Apple [iOS 9.3 las diferencias de API de iOS 10.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) documentación para obtener una lista completa de elementos obsoletos.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Novedades de iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
