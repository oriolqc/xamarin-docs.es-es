---
title: Introducción a iOS 10
description: Este artículo presenta todas las características disponibles en iOS 10 y las API nuevas y modificadas para desarrolladores de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: b018fe343a7d46f1323119b03a22cc3831a02d9f
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870253"
---
# <a name="introduction-to-ios-10"></a>Introducción a iOS 10

_Este artículo presenta todas las características disponibles en iOS 10 y las API nuevas y modificadas para desarrolladores de Xamarin.iOS._

## <a name="introducing-ios-10"></a>Introducción a iOS 10

Con la nueva iOS 10 SDK, Apple ha incluido nuevas API y servicios que permiten a los desarrolladores crear nuevas categorías de aplicaciones y características. Una aplicación de iOS ahora puede ampliar las aplicaciones de mensajes, Siri, teléfono y mapas para proporcionar funcionalidades completas para el usuario final que no estaba disponible anteriormente.

Para obtener más información en iOS 10, consulte Apple [iOS + aplicaciones](https://developer.apple.com/ios/) documentación.

## <a name="whats-new-in-ios-10"></a>Novedades en iOS 10

Apple ha agregado varias nuevas API y servicios en iOS 10 junto con muchas mejoras en las características existentes, incluyendo:


## <a name="adapting-to-the-true-tone-display"></a>Para adaptarse a la presentación de tono True

Tecnología de tono True muestre Apple usa el sensor de luz ambiente en un dispositivo iOS ajustar dinámicamente el color y la intensidad de la pantalla para que coincida con las condiciones de iluminación. iOS 10 proporciona las nuevas [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) clave que se puede agregar a la aplicación `Info.plist` de archivos y controla cómo se aplica el cambio de color estándar el tono de True. 

Están disponibles los siguientes valores:

- `UIWhitePointAdaptivityStyleStandard` **Default** -usar la adaptabilidad de punto de blanco estándar.
- `UIWhitePointAdaptivityStyleReading` : Se usa para aplicaciones orientadas a leer.
- `UIWhitePointAdaptivityStyleGame` : Se usa para las aplicaciones centradas en juego.
- `UIWhitePointAdaptivityStyleVideo` : Se usa para las aplicaciones centradas en el vídeo.
- `UIWhitePointAdaptivityStylePhoto` : Se usa para las aplicaciones centradas en fotografía donde es más importante que los ajustes del entorno de punto blanco fidelidad del color.

<a name="app-extensions" />

## <a name="app-extensions"></a>Extensiones de aplicación

Apple proporciona varios puntos de extensión de aplicación nuevos en iOS 10:

- Directorio de llamada
- Las intenciones y calidades de interfaz de usuario
- Mensajes
- Contenido de la notificación
- Notification Services
- Módulo de etiqueta

Además, 3ª parte teclado aplicación extensiones tener las siguientes mejoras:

- El nuevo `DocumentInputMode` propiedad de la `UITextDocumentProxy` clase puede determinar el idioma de entrada de un documento y permitir la extensión de teclado para alinearse con ese idioma.
- El nuevo `HandleInputModeList` método permite la extensión de teclado mostrar el menú del selector de teclado del sistema en respuesta a la clave del mundo que se pulsa.

Para obtener más información, consulte nuestra [Introducción a las extensiones](~/ios/platform/extensions.md), [integración de aplicaciones de mensaje](~/ios/platform/message-app-integration/index.md), [Introducción a sugerencias proactivas](~/ios/platform/search/proactive-suggestions.md), [ Introducción a SiriKit](~/ios/platform/sirikit/index.md), [Introducción a las notificaciones de usuario](~/ios/platform/user-notifications/index.md) y Apple [Guía de programación de la extensión de la aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

## <a name="app-search-enhancements"></a>Mejoras en la búsqueda de la aplicación

Core Spotlight en iOS 10 proporciona varias mejoras para la búsqueda de la aplicación, como:

- **Popularidad de vínculo profundo de Micromecenazgo (con privacidad diferencial)** -proporciona una manera para promover el contenido de la aplicación con vínculo profundo en resultados de búsqueda.
- **Buscar en la aplicación** -usar el nuevo `CSSearchQuery` clase para proporcionar capacidad de búsqueda de Spotlight en aplicación similar a cómo funcionan las aplicaciones de correo electrónico, mensajes y notas.
- **Buscar continuación** : permite que un usuario iniciar una búsqueda de Spotlight o Safari, a continuación, abrir una aplicación y continuar esa búsqueda.
- **Visualización de resultados de validación** -Apple [herramienta de validación de API de búsqueda de aplicación](https://search.developer.apple.com/appsearch-validation-tool) ahora muestra una representación visual de marcado de un sitio Web y vínculos profundos cuando realice las pruebas.
- **Aplicación de uso compartido de imágenes del mensaje** -permite imágenes populares de aplicación proporcionadas para el uso compartido de mensajes (a través de una extensión de la aplicación de mensaje) para que aparezca en las búsquedas de Spotlight.

Para obtener más información, consulte nuestra [mejoras en la aplicación de búsqueda](~/ios/platform/search/app-search-enhancements.md) guía.

## <a name="apple-pay-enhancements"></a>Mejoras de Apple Pay

Apple ha realizado varias mejoras en Apple Pay en iOS 10 que permiten al usuario realizar pagos seguros desde sitios Web y mediante la interacción con Siri y mapas.

Con iOS 10, varias API nuevas se han agregado que funcionan con iOS y watchOS para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

Además, el marco de PassKit se ha ampliado para admitir Apple Pay fuera de `UIKit` y para permitir que los emisores de tarjeta presentar sus tarjetas desde dentro de sus aplicaciones.

Para obtener más información, consulte nuestra [Apple pagar mejoras](~/ios/platform/apple-pay.md) guía.

## <a name="alternate-app-icons"></a>Iconos de aplicación alternativos

Apple ha agregado varias mejoras a iOS 10.3 que permitan a una aplicación administrar su icono:

 - `ApplicationIconBadgeNumber` : Obtiene o establece el distintivo de icono de la aplicación en el Springboard.
 - `SupportsAlternateIcons` -If `true` la aplicación tiene un conjunto alternativo de iconos.
 - `AlternateIconName` -Devuelve el nombre del icono alternativo seleccionado actualmente o `null` si mediante el icono principal.
 - `SetAlternameIconName` : Use este método para cambiar el icono de la aplicación en el icono alternativo especificado.

Para obtener más información, consulte nuestra [iconos de aplicación alternativo](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) guía.


## <a name="introduction-to-callkit"></a>Introducción a CallKit

La nueva API de CallKit en iOS 10 proporciona una manera para las aplicaciones VOIP integrar con la interfaz de usuario de iPhone y proporcionar una interfaz conocida y experiencia para el usuario final. Con esta API, los usuarios pueden ver e interactuar con llamadas VOIP desde la pantalla de bloqueo del dispositivo iOS y administrar contactos con la aplicación de teléfono **favoritos** y **recientes** vistas.

Además, la API de CallKit proporciona la capacidad para crear extensiones de aplicación que se puede asociar un número de teléfono con un nombre (identificador de llamada) o indicar el sistema cuando un número debe ser bloqueados (llamar a bloqueo).

Para obtener más información, consulte nuestra [Introducción a Callkit](~/ios/platform/callkit.md) guía.

## <a name="message-app-integration"></a>Integración de aplicaciones de mensaje

iOS 10 permite la inclusión de una extensión de aplicación de mensaje en la solución Xamarin.iOS que se integra con la **mensajes** funcionalidad nueva aplicación y presenta al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos. Existen dos tipos de mensaje de extensión de la aplicación:

- **Módulos de adhesivo** -contiene una colección de etiquetas que el usuario puede agregar a un mensaje. Módulos de etiqueta se pueden crear sin escribir ningún código.
- **Aplicación iMessage** -puede presentar una interfaz de usuario personalizada en la aplicación mensajes para seleccionar adhesivos, escribir texto, incluidos los archivos de medios (con las conversiones de tipo opcional) y crear, editar y enviar mensajes de interacción.

Para obtener más información, consulte nuestra [integración de aplicaciones de mensaje](~/ios/platform/message-app-integration/index.md) guía.

## <a name="news-publisher-enhancements"></a>Mejoras del Editor de noticias

Con iOS 10, Apple permitirá que cualquier persona de revistas principales y nuevas organizaciones bloggers y editores independientes para registrarse y producto y entregar contenido a la aplicación Apple News. Para obtener más información, consulte Apple [noticias recursos](https://newsresources.apple.com/) documentación.

## <a name="providing-haptic-feedback"></a>Provisión de comentarios hápticos

En el iPhone 7 y iPhone 7 Plus, Apple ha incluido las respuestas haptics nuevas que proporcionan otras formas de captar físicamente el usuario. Usar las nuevas opciones de comentarios al tacto para obtener la atención del usuario y reforzar sus acciones.

Varios elementos de interfaz de usuario integrados ya proporcionan comentarios hápticos como controles deslizantes, conmutadores y selectores. iOS 10 ahora agrega la posibilidad de desencadenar mediante programación haptics con una subclase concreta de la `UIFeedbackGenerator` clase.

Para obtener más información, consulte nuestra [proporcionar comentarios hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md) guía.

## <a name="proactive-suggestions"></a>Sugerencias proactivas

iOS 10 presenta nuevas formas de conducción engagement para una aplicación, ya que permite el sistema de forma proactiva presentar información útil automáticamente al usuario en los momentos adecuados. Igual que iOS 9 proporciona la capacidad de agregar una búsqueda en profundidad a la aplicación mediante Spotlight, entrega y sugerencias de Siri, con iOS 10, que una aplicación puede exponer la funcionalidad que se puede presentar al usuario por el sistema desde dentro de las siguientes ubicaciones:

- El modificador de la aplicación
- La pantalla de bloqueo
- CarPlay
- Asignaciones
- Interacciones de Siri
- Sugerencias de QuickType 

Una aplicación expone esta funcionalidad al sistema mediante un conjunto de tecnologías como [NSUserActivity](xref:Foundation.NSUserActivity), marcado web, Core Spotlight, MapKit, Media Player y UIKit.

Para obtener más información, consulte nuestra [Introducción a sugerencias proactivas](~/ios/platform/search/proactive-suggestions.md) guía.

## <a name="request-app-review"></a>Solicitar revisión de aplicación

Nuevo a iOS 10.3, el `RequestReview()` método permite que una aplicación de iOS pedir al usuario que calificar o lo revise. Aunque este método puede llamarse en cualquier punto donde tenga sentido en la experiencia del usuario, el proceso de revisión se rige y controlado por la directiva de App Store. Como resultado, este método puede o no puede mostrar una alerta y nunca debe llamarse en respuesta a una acción del usuario, como pulsar un botón.

Para obtener más información, consulte nuestra [solicitar revisión de la aplicación](~/ios/platform/request-app-review.md) guía.

## <a name="security-and-privacy-enhancements"></a>Mejoras de seguridad y privacidad

Apple ha realizado varias mejoras en seguridad y privacidad en iOS 10 que le ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final.

Como resultado, las aplicaciones que se ejecutan en iOS 10 (o posterior) deben declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de uno o más claves específicas de privacidad de sus `Info.plist` archivos que explican al usuario por qué la aplicación quiere tener acceso.

Para obtener más información, consulte nuestra [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) guía.

## <a name="sirikit"></a>SiriKit

Nuevo iOS 10, SiriKit permite que una aplicación de Xamarin.iOS proporcionar servicios que son accesibles para el usuario mediante Siri en un dispositivo iOS. Esta funcionalidad se proporciona en la extensión de la aplicación de uno o más con el nuevo **intenciones** y **Intents UI** marcos de trabajo.

SiriKit admite los siguientes dominios de servicio:

- Una llamada de vídeo o audio.
- Transporte de reserva.
- Administración de sesiones de ejercicios.
- Mensajería.
- Buscar fotografías.
- Enviar o recibir los pagos.

Cuando el usuario realiza una solicitud de Siri que implican a uno de los servicios de la extensión de aplicación, SiriKit envía la extensión de un **intención** objeto que describe la solicitud del usuario junto con cualquier dato de apoyo. La extensión de la aplicación, a continuación, genera adecuado **respuesta** de objeto para el determinado **intención**, que detalla cómo la extensión puede atender la solicitud.

Mientras que Siri normalmente controla toda la interacción del usuario, puede usar la extensión de la aplicación el **intención UI** framework para presentar un personalizadas y enriquecidas interfaz de usuario que incluye la aplicación de la personalización de marca y la información adicional.

Para obtener más información, consulte nuestra [Introducción a SiriKit](~/ios/platform/sirikit/index.md) guía.

## <a name="speech-recognition"></a>Reconocimiento de voz

iOS 10 incluye una nueva API de voz que permite que la aplicación admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivo o grabados) en texto.

Dado que el reconocimiento de voz requiere la transmisión y almacenamiento temporal de datos en los servidores de Apple, la aplicación _debe_ solicitar permiso del usuario para realizar el reconocimiento mediante la inclusión de la `NSSpeechRecognitionUsageDescription` clave en su `Info.plist` archivo y llamar a la `SFSpeechRecognizer.RequestAutorization` método.

Para obtener más información, consulte nuestra [Introducción para el reconocimiento de voz](~/ios/platform/speech.md) guía.

## <a name="user-notifications"></a>Notificaciones de usuario

Nuevo en iOS 10, la notificación de usuario framework permite la entrega y el tratamiento de las notificaciones locales y remotas. Con este marco de trabajo, la aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

Además, la aplicación o extensión puede recibir (y posiblemente modificar) las notificaciones locales y remotas que se entregan a dispositivos iOS del usuario.

El nuevo marco de interfaz de usuario de notificación de usuario permite que la aplicación o extensión de la aplicación para personalizar la apariencia de las notificaciones locales y remotas cuando se presentan al usuario.

Para obtener más información, consulte nuestra [plataforma de notificaciones de usuario](~/ios/platform/user-notifications/index.md) guía.

## <a name="video-subscriber-account"></a>Cuenta de suscriptor de vídeo

Novedad de iOS 10, el marco de trabajo de la cuenta de suscriptor de vídeo permite aplicaciones esa compatibilidad autenticado con transmisión por secuencias o vídeo bajo demanda para autenticarse con su proveedor de TV por cable o satélite mediante una experiencia de inicio de sesión único para el usuario final.

## <a name="wide-color"></a>Color amplio

iOS 10 amplía la compatibilidad con formatos de píxel de rango ampliado y espacios de la amplia gama de colores en todo el sistema incluidos marcos como gráficos de Core, imagen básica, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas a color amplia es aún más disminuido proporcionando este comportamiento a lo largo de la pila de gráficos completa.

Además, [UIKit](xref:UIKit) se ha modificado para que funcione en el nuevo extendidos **sRGB** espacio de color, lo que facilita la combinación de colores en gamas de color amplia sin pérdida de rendimiento significativas.

Apple ofrece las siguientes prácticas recomendadas al trabajar con colores amplios:

- [UIColor](xref:UIKit.UIColor) ahora utiliza espacio de color sRGB y se dejará de fijar los valores para el `0.0` a `1.0` intervalo. Si la aplicación se basa en el comportamiento de bloqueo anterior, deberá modificarse para iOS 10.
- El entorno de dibujo se configurarán para el espacio de color sRGB al realizar personalizado `UIView` de dibujo en un iPad Pro.
- Si la aplicación realiza una representación personalizada de `UIImages`, use la nueva [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) clase para especificar el uso de los formatos de intervalo extendido o intervalo estándar.
- Cuando se usa una API de bajo nivel como gráficos básicos o sistema operativo para proporcionar procesamiento de imágenes, el desarrollador debe usar un rango extendido píxeles y el espacio de formato de color que es compatible con los valores de punto flotante de 16 bits. En caso necesario, el desarrollador deberá fijar manualmente los valores de componentes de color.
- Gráficos básicos, imagen Core y los sombreadores de rendimiento de sistema operativo proporcionan nuevos métodos para la conversión entre los espacios de dos colores.

Para obtener más información, consulte nuestra [Introducción a Color amplia](~/ios/platform/wide-color.md) guía.

## <a name="widget-enhancements"></a>Mejoras de widget

Apple ha introducido varias mejoras en el sistema de Widget para asegurarse de que los widgets tienen un aspecto excelentes en cualquier en segundo plano que existe en el nuevo iOS 10 pantalla de bloqueo. El [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) propiedad ha quedado desusada y se ha reemplazado por el nuevo [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) propiedades. Además, los widgets ahora contienen una [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propiedad que permite al desarrollador describir el contenido que está disponible y permite al usuario expandir y contraer el contenido.

Para obtener más información, consulte nuestra [mejoras el Widget de pantalla de inicio y de búsqueda](~/ios/platform/search/widgets.md) guía.

## <a name="additional-framework-changes"></a>Cambios de marco de trabajo adicionales

Además de los cambios en la plataforma principal y adiciones enumeradas anteriormente, Apple ha realizado muchos cambios de marco secundarias adicionales en iOS 10.

Para obtener más información, consulte nuestra [cambios adicionales de Framework](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) guía.

## <a name="deprecated-apis"></a>Interfaces API desusadas

Las API siguientes han quedado obsoletas en iOS 10:

- El `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` y `CKFetchRecordChangesOperation` clases han quedado obsoletas en directo de CloudKit para iOS 10. Use la [CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation), [CKUserIdentity](xref:CloudKit.CKUserIdentity) y [CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation) clases (que admiten el uso compartido de registro) en su lugar.
- Varios [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) API (por ejemplo, suscripciones basadas en la zona y basados en consultas) han quedado en desuso. Use la [CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription) y [CKQuerySubscription](xref:CloudKit.CKQuerySubscription) API en su lugar.
- [NSPersistentStoreCoordnator](xref:CoreData.NSPersistentStoreCoordinator) símbolos relacionados con contenido ubicuo han quedado en desuso.
- `ADBannerView`, `ADInterstitialAd` y relacionados con los símbolos en el [UIViewController](xref:UIKit.UIViewController) clase han quedado en desuso.
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) símbolos relacionados con valores de punto flotante han quedado en desuso.
- El `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` y `UIUserNotificationSettings` han quedado en desuso de las clases de UIKit. Use la [notificaciones de usuario](#user-notifications) framework en su lugar.
- El `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` y `DidReceiveRemoteNotification` WatchKit métodos han quedado en desuso. Use la `HandleActionForNotification` y `DidReceiveNotification` métodos en su lugar.
- El `DidReceiveLocalNotification` y `DidReceiveRemoteNotification` métodos de la [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) han quedado en desuso. Cree una instancia de [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) que implementa los métodos adecuados y asígnelo a la `Delegate` propiedad de la [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) objeto.
- El **Game Center aplicación** ha sido en desuso y quitados de iOS. Si la aplicación usa GameKit, lo _debe_ presentar su propia interfaz para mostrar las características de GameKit como marcadores, etcetera.

Consulte Apple [iOS 9.3 a las diferencias de API de iOS 10.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) documentación para obtener una lista completa de elementos obsoletos.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Novedades de iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
