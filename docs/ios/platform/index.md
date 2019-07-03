---
title: Introducción a las características de plataforma de iOS
description: Este documento incluye vínculos a diversas guías que describen las características introducidas en distintas versiones de iOS y otras características de la plataforma iOS.
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/25/2018
ms.openlocfilehash: 6703e922a628504e0afdcf56533d74741131581a
ms.sourcegitcommit: a6ba6ed086bcde4f52fb05f83c59c68e8aa5e436
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540159"
---
# <a name="ios-platform-features-overview"></a>Introducción a las características de plataforma de iOS

Esta página incluye versiones recientes iOS además de destacar algunos de los marcos de Apple se puede acceder con Xamarin.iOS.

## <a name="ios-releases"></a>versiones de iOS

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Introducción a iOS 13 obtener una vista previa](~/ios/platform/ios13/index.md) | Este documento describe la vista previa del 13 de Xamarin.iOS.|
| [Introducción a iOS 12](~/ios/platform/introduction-to-ios12/index.md) | Este documento describe iOS 12 características disponibles para su uso al compilar aplicaciones de Xamarin.iOS.|
| [Introducción a iOS 11](~/ios/platform/introduction-to-ios11/index.md) | Este documento describe las características nuevas y actualizadas en iOS 11 y Xcode 9, como ARKit, Core ML, Core NFC, arrastrar y colocar, MapKit, PDFKit, SiriKit y visión. Incluye vínculos a guías que describen cómo usar estas características con Xamarin.iOS. |
| [Introducción a iOS 10](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 incluye varias nuevas API y servicios que le permiten desarrollar aplicaciones con las nuevas características y funcionalidad. Con iOS 10, las aplicaciones tienen nuevas capacidades como la ampliación de mapas, los mensajes, teléfono y Siri. En esta sección se muestra cómo aprovechar estas características en una aplicación de Xamarin.iOS. |
| [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md)   | En esta sección define los cambios realizados en iOS 9, al actualizar desde iOS 8 y cómo usar estas características en una aplicación de Xamarin.iOS. |
| [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)         | iOS 8 realiza un gran número de cambios en el sistema operativo desde iOS 7. En este caso, se muestra qué son y cómo usarlas. |
| [Introducción a iOS 7](~/ios/platform/introduction-to-ios7/index.md)   | Acerca de las nuevas API principales introducidas en iOS 7, incluidos View Controller realiza la transición, mejoras en las animaciones UIView, UIKit Dynamics y el Kit de texto. |
| [Introducción a iOS 6](~/ios/platform/introduction-to-ios6/index.md)   | Explicaciones de las características introducidas en iOS 6, incluidas las vistas de colección, pasar Kit, Kit de evento y el marco de redes sociales. |

## <a name="apple-payiosplatformapple-paymd"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay se introdujo junto con iOS 8, permitiendo a los usuarios pueden pagar bienes físicos como alimentos, entretenimiento y las pertenencias a través de sus dispositivos iOS. Está disponible en iPhone 6 y iPhone 6 Plus y también se pueden emparejar con el Apple Watch para compras en la tienda. Cuando se utiliza en un iPhone, usa Touch ID como una manera de confirmar y autorizar a las transacciones para de un usuario tarjeta de crédito o débito.

## <a name="callkitiosplatformcallkitmd"></a>[CallKit](~/ios/platform/callkit.md)

La nueva API de CallKit en iOS 10 proporciona una manera para las aplicaciones VOIP integrar con la interfaz de usuario de iPhone y proporcionar una interfaz conocida y experiencia para el usuario final. Con esta API usuarios pueden ver e interactuar con llamadas VOIP desde la pantalla de bloqueo del dispositivo iOS y administrar contactos con la aplicación de teléfono **favoritos** y **recientes** vistas.

## <a name="contacts-and-contactsuiiosplatformcontactsmd"></a>[Contacts y ContactsUI](~/ios/platform/contacts.md)

Con la introducción de iOS 9, Apple ha lanzado dos nuevos marcos de trabajo, `Contacts` y `ContactsUI`, que reemplace la existente libreta de direcciones y marcos de interfaz de usuario de libreta de direcciones usan por iOS 8 y versiones anteriores.

## <a name="document-pickeriosplatformdocument-pickermd"></a>[Selector de documentos](~/ios/platform/document-picker.md)

El selector de documentos permite que los documentos deben compartirse entre aplicaciones. Estos documentos pueden almacenarse en iCloud o en el directorio de una aplicación diferente. Los documentos se comparten a través del conjunto de [extensiones de proveedor de documento](~/ios/platform/extensions.md) el usuario ha instalado en su dispositivo.

## <a name="eventkitiosplatformeventkitmd"></a>[EventKit](~/ios/platform/eventkit.md)

iOS tiene dos relacionados con el calendario aplicaciones integradas: la aplicación de calendario y la aplicación de recordatorios. Es fácil comprender cómo la aplicación de calendario administra datos de calendario, pero la aplicación de recordatorios es menos obvia. Recordatorios realmente pueden tener fechas asociadas con ellos en términos de cuando están debido, cuando se completen, etcetera. Por lo tanto, iOS almacena todos los datos de calendario, ya sea en los eventos de calendario o recordatorios en una ubicación, llamado el *base de datos de calendario*.

## <a name="ios-extensionsiosplatformextensionsmd"></a>[extensiones de iOS](~/ios/platform/extensions.md)

Las extensiones, como se mencionó en iOS 8, están especializadas en `UIViewControllers` que se presentan por iOS en contextos estándares tal como en el **centro de notificaciones**, como tipos de teclado personalizado solicitados por el usuario para realizar especializados entrada o en otros contextos, como modificar una foto donde la extensión puede proporcionar filtros de efectos especiales.

## <a name="graphics-and-animation-in-iosiosplatformgraphics-animation-iosindexmd"></a>[Gráficos y animación en iOS](~/ios/platform/graphics-animation-ios/index.md)

Gráficos y animación en iOS se tratan conceptos clave gráficos en iOS como CoreImage, Core gráficos y animación básica.

## <a name="handoffiosplatformhandoffmd"></a>[Handoff](~/ios/platform/handoff.md)

Apple introdujo Handoff en iOS 8 y OS X Yosemite (10.10) para proporcionar un mecanismo común para el usuario transferir las actividades iniciadas en uno de sus dispositivos, a otro dispositivo que ejecuta la misma aplicación u otra aplicación que admita la misma actividad.

## <a name="healthkitiosplatformhealthkitmd"></a>[HealthKit](~/ios/platform/healthkit.md)

Kit de mantenimiento proporciona un almacén de datos seguro para la información del usuario relacionada con el mantenimiento. Las aplicaciones del Kit de mantenimiento pueden, con el permiso del usuario explícita, leer y escribir en este almacén de datos y recibir notificaciones cuando se agregan datos pertinentes. Las aplicaciones pueden presentar los datos o usuario puede usar la aplicación de mantenimiento proporcionado de Apple para ver un panel de todos sus datos.

## <a name="homekitiosplatformhomekitmd"></a>[HomeKit](~/ios/platform/homekit.md)

Apple introdujo HomeKit en iOS 8 para proporcionar un marco común para detectar y comunicarse con dispositivos de automatización del hogar en Inicio de un usuario. HomeKit proporciona una plataforma común para los dispositivos y configuración de acciones para controlarlos.

## <a name="in-app-purchasingiosplatformin-app-purchasingindexmd"></a>[En la aplicación de compra](~/ios/platform/in-app-purchasing/index.md)

las aplicaciones de iOS pueden vender productos digitales o servicios que usan StoreKit: un conjunto de API proporcionados por iOS que se comunican con servidores de Apple para llevar a cabo transacciones financieras con un usuario a través de su identificador de Apple. Las APIs StoreKit son principalmente los relacionados con la recuperación de información de producto y llevar a cabo las transacciones: no hay ningún componente de interfaz de usuario. Las aplicaciones que implementan la compra de la aplicación deben crear su propia interfaz de usuario y realizar un seguimiento de los productos adquiridos con código personalizado para proporcionar servicios de los productos necesarios para el usuario.

## <a name="ios-gaming-apisiosplatformgamingindexmd"></a>[las API de juegos de iOS](~/ios/platform/gaming/index.md)

Apple ha realizado varias mejoras tecnológicas a la API de juegos en iOS 9 que resulte más fácil implementar gráficos de juegos y audio en una aplicación de Xamarin.iOS. Estos incluyen la facilidad de desarrollo a través de marcos de alto nivel y aprovechar la eficacia de GPU de su dispositivo iOS para mejorar la velocidad y las capacidades de gráficos.

## <a name="message-app-integrationiosplatformmessage-app-integrationindexmd"></a>[Integración de aplicaciones de mensaje](~/ios/platform/message-app-integration/index.md)

Nuevo en iOS 10, una extensión de mensaje de la aplicación se integra con la **mensajes** funcionalidad nueva aplicación y presenta al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos.

## <a name="multitasking-for-ipadiosplatformmultitaskingmd"></a>[Multitarea para iPad](~/ios/platform/multitasking.md)

iOS 9 agrega compatibilidad con multitarea ejecuta dos aplicaciones al mismo tiempo en un hardware específico iPad. Multitarea para iPad es compatible a través de las siguientes características: Diapositiva a través, la vista en dos paneles & imagen en imagen.

## <a name="passkitiosplatformpasskitmd"></a>[PassKit](~/ios/platform/passkit.md)

Passbook es una aplicación para iPhone y iPod toca con iOS 6. Almacena y muestra los códigos de barras y otra información para vincular las transacciones de clientes en su teléfono con el mundo real. Pasadas se generan por los comercios y se envían al cliente por correo electrónico, direcciones URL o desde una aplicación de iOS de comerciante. Libreta con dispositivo almacena y organiza todas las fases en un teléfono y muestra avisos de Pass en la pantalla de bloqueo según la fecha y hora o la ubicación del dispositivo.

Este documento presenta Passbook, mediante la API de Kit pasar con Xamarin.iOS y explica cómo implementar pasadas en el servidor.

## <a name="photokitiosplatformphotokitmd"></a>[PhotoKit](~/ios/platform/photokit.md)

Kit de foto es un nuevo marco que permite a las aplicaciones para la biblioteca de imágenes de sistema de consulta y crear interfaces de usuario personalizadas para ver y modificar su contenido. Incluye una serie de clases que representan la imagen y activos de vídeo, así como las colecciones de recursos, como carpetas y álbumes.

## <a name="request-app-reviewiosplatformrequest-app-reviewmd"></a>[Solicitar revisión de aplicación](~/ios/platform/request-app-review.md)

Nuevo a iOS 10.3, el `RequestReview()` método permite que una aplicación de iOS pedir al usuario que calificar o lo revise. Cuando se llama a este método en una aplicación de trasvase de registros que el usuario ha instalado desde la aplicación de Store, iOS 10 controlará la clasificación de toda y revisar el proceso para el desarrollador. Dado que este proceso se rige por la directiva de App Store, una alerta puede o no pueden mostrarse.

## <a name="search-apisiosplatformsearchindexmd"></a>[API de búsqueda](~/ios/platform/search/index.md)

Se ha ampliado la búsqueda en iOS 9 para proporcionar excelentes nuevas formas de acceder a información y características dentro de una aplicación de Xamarin.iOS. Mediante las nuevas API de búsqueda de la aplicación, se podrán buscar a través de Spotlight y Safari resultados de la búsqueda, entrega y avisos de Siri y sugerencias de contenido de la aplicación. Esto permite a los usuarios acceder rápidamente a las actividades y la información en profundidad dentro de la aplicación.

## <a name="sirikitiosplatformsirikitindexmd"></a>[SiriKit](~/ios/platform/sirikit/index.md)

Nuevo IOS 10, permite que una aplicación de iOS proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas de un dispositivo iOS mediante extensiones de aplicación y el nuevo SiriKit **intenciones** y **Intents UI** marcos de trabajo.

## <a name="social-frameworkiosplatformsocial-frameworkmd"></a>[Redes sociales](~/ios/platform/social-framework.md)

El marco de redes sociales proporciona una API unificada para interactuar con las redes sociales incluso _Twitter_ y _Facebook_, así como _SinaWeibo_ para los usuarios en China.

## <a name="speech-recognitioniosplatformspeechmd"></a>[Reconocimiento de voz](~/ios/platform/speech.md)

iOS 10 incluye una nueva API de voz que permite que la aplicación admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivo o grabados) en texto.

## <a name="textkitiosplatformtextkitmd"></a>[TextKit](~/ios/platform/textkit.md)

Kit de texto es una nueva API que ofrece características de diseño y la representación de texto eficaz. Se basa en el marco de texto de núcleo de bajo nivel, pero es mucho más fácil de usar que el texto principal.

## <a name="3d-touchiosplatform3d-touchmd"></a>[Entrada táctil 3D](~/ios/platform/3d-touch.md)

Este artículo se proporciona e Introducción al uso de las nuevas API 3D Touch para agregar gestos confidenciales presión a las aplicaciones de Xamarin.iOS que se ejecutan en el nuevo iPhone 6s y iPhone 6s Plus dispositivos.

## <a name="touch-idiosplatformtouchidmd"></a>[Touch ID](~/ios/platform/touchid.md)

Id. de toque se introdujo en iOS 7 como un medio para autenticar al usuario - similar a un código de acceso. Sin embargo, estaba limitado a desbloquear el dispositivo, utilizando el Store de la aplicación, mediante iTunes y autenticar sólo la cadena de claves de iCloud.

## <a name="user-notificationsiosplatformuser-notificationsindexmd"></a>[Notificaciones de usuario](~/ios/platform/user-notifications/index.md)

Nuevo en iOS 10, la notificación de usuario framework permite la entrega y el tratamiento de las notificaciones locales y remotas. Con este marco de trabajo, la aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

## <a name="wide-coloriosplatformwide-colormd"></a>[Color amplio](~/ios/platform/wide-color.md)

10 de iOS y macOS Sierra mejora la compatibilidad con formatos de píxel de rango ampliado y espacios de la amplia gama de colores en todo el sistema incluidos marcos como gráficos de Core, imagen básica, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas a color amplia es aún más disminuido proporcionando este comportamiento a lo largo de la pila de gráficos completa.

## <a name="binding-objective-cbinding-objective-cindexmd"></a>[Enlace de Objective-C](binding-objective-c/index.md)

Cuando se trabaja en iOS, pueden producirse los casos donde desee utilizar una biblioteca de Objective-C de terceros. En estos casos, puede usar proyectos de enlace de MonoTouch para crear un enlace a las bibliotecas nativas de Objective-C de C#. El proyecto usa las mismas herramientas que utilizamos para traer las API de iOS a C#. Este documento describe cómo enlazar Objective-C API.

## <a name="referencing-native-librariesnative-interopmd"></a>[Hacer referencia a bibliotecas nativas](native-interop.md)

Xamarin.iOS admite la vinculación con las bibliotecas nativas de C y las bibliotecas de Objective-C. Este documento describe cómo vincular las bibliotecas de C nativas con el proyecto de Xamarin.iOS.

## <a name="embedded-frameworksembedded-frameworksmd"></a>[Plataformas admitidas](embedded-frameworks.md)

Se explica cómo insertar los marcos de trabajo de usuario de Objective-C en aplicaciones de Xamarin.iOS.
