---
title: "Introducción a watchOS 3"
description: "Este artículo detallan todas las características disponibles en watchOS 3 y las API nuevas y modificadas para desarrolladores de Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/07/2017
ms.openlocfilehash: f11db7496d132b742cb57b86ddea240712b09e34
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-watchos-3"></a>Introducción a watchOS 3

_Este artículo detallan todas las características disponibles en watchOS 3 y las API nuevas y modificadas para desarrolladores de Xamarin._

Este documento tratará los siguientes temas:

- [What's New en watchOS 3](#Whats-New-in-watchOS-3)
    - [Mejoras de Apple pagar](#Apple-Pay-Enhancements) agrega compatibilidad para los pagos de la aplicación en el de Apple Watch.
    - [Tareas en segundo plano](#Background-Tasks) dar a la aplicación la posibilidad de actualizar su información en segundo plano para que esté listo cuando el usuario lo necesite.
    - [Mejoras de las complicaciones](#Complications-Enhancements) se han realizado para watchOS 3 que proporciona características nuevas para las aplicaciones.
    - [Marcos de trabajo recién disponible](#Newly-Available-Frameworks) ha expuesto para las aplicaciones de watchOS.
    - [Sugerencias automático](#Proactive-Suggestions) permite que la aplicación mostrar de forma proactiva información al usuario.
    * Varios [mejoras de seguridad y privacidad](#Security-and-Privacy-Enhancements) se han realizado en watchOS 3.
    - [Las instantáneas y acoplar](#Snapshots-and-Dock) proporcionar al usuario acceso rápido a las aplicaciones de watchOS de aplicación.
    - [Las notificaciones de usuario](#User-Notifications) proporciona notificaciones locales y remotas para el usuario.
    * Varios [Framework mejoras de conectividad de inspección](#Watch-Connectivity-Framework-Enhancements) se han realizado en watchOS 3.
    * Varios [WatchKit Framework mejoras](#WatchKit-Framework-Enhancements) se han realizado en watchOS 3.
    - [Mejoras de la aplicación de entrenamiento](#Workout-App-Enhancements) proporciona capacidades nuevas para el entrenamiento relacionadas con aplicaciones de Apple Watch.
- [Cambios de Framework adicionales](#Additional-Framework-Changes) se han realizado a lo largo de watchOS 3.
- [Interfaces API en desuso](#Deprecated-APIs) en watchOS 3.

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>What's New en watchOS 3

Apple ha agregado varias nuevas API y servicios en watchOS 3 junto con muchas mejoras en las características existentes, incluyendo:

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Mejoras de pago de Apple

En watchOS 3, el marco de trabajo PassKit se ha ampliado para permitir la compatibilidad con los pagos seguros, de la aplicación (de mercancías físicas y servicios) para las aplicaciones que se ejecutan en el de Apple Watch.

Use la nueva [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) y [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) clases para presentar y responder a una interfaz donde el usuario puede autorizar las solicitudes de pago.

Para obtener más información, visite nuestro [Apple pagar mejoras](~/ios/watchos/platform/apple-pay.md) guía.

<a name="Background-Tasks" />

## <a name="background-tasks"></a>Tareas en segundo plano

watchOS 3 presenta varias tareas en segundo plano que una aplicación puede usar para actualizar su información asegurándose de que tiene el contenido que el usuario necesita antes de abrirlo.

Están disponibles las siguientes tareas en segundo plano nuevo:

- **Actualización de la aplicación en segundo plano** - la [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) tarea permite que la aplicación actualizar su estado en segundo plano. Normalmente esto incluirá otra tarea, como la descarga de nuevo contenido desde internet mediante un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Instantáneas de actualización en segundo plano** - la [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) tarea permite que la aplicación actualizar su contenido y la interfaz de usuario antes de que el sistema toma una instantánea que se usará para rellenar el área.
- **Conectividad de inspección en segundo plano** - la [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) tarea se inicia por la aplicación cuando recibe datos de fondo desde el iPhone emparejado.
- **Sesión de dirección URL en segundo plano** - la [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) tarea se inicia por la aplicación cuando una transferencia en segundo plano requiere autorización o completa (con éxito o error).

Para obtener más información, visite nuestro [tareas en segundo plano](~/ios/watchos/platform/background-tasks.md) guía.

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>Mejoras de las complicaciones

Las complicaciones son pequeños elementos visuales que proporcionan información útil de un vistazo. Dependiendo de la pantalla del reloj seleccionada, el usuario tiene la capacidad para personalizar una pantalla watch con uno o más complicación.

watchOS 3 proporciona la capacidad para crear uno o más complicación de la aplicación de inspección para que el usuario puede tener acceso a su información de una instantánea de una cara de inspección de la aplicación.

Además, las complicaciones ofrecen las siguientes ventajas:

- El usuario puede iniciar rápidamente la aplicación mediante el aprovechamiento de la complicación directamente desde una pantalla del reloj.
- Tener una de las complicaciones de la aplicación en las causas de la cara de inspección del sistema para mantener la aplicación en un estado de listo para iniciar donde intenta iniciar la aplicación en segundo plano, mantenerlo en memoria y proporciona extra de tiempo para actualizar.
- Complicaciones se garantiza que al menos 50 actualizaciones de inserción al día.
- Cuando la aplicación incluye complicaciones, estar incluido en la Galería de cara de Apple Watch (vea de Apple [complicaciones agregar a la Galería de](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery) documentación para obtener más información).

En watchOS 3, el marco de trabajo ClockKit ahora incluye varias plantillas nuevo para complicaciones extra grandes como [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) y [CLKComplicationTemplateExtraLargeRingImage ](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Además, para crear texto traducible, use métodos nuevos de la [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) clase.

Para obtener más información, visite nuestro [rápido técnicas de interacción para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guía.

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>Marcos de trabajo recién disponibles

watchOS 3 incluye varios marcos de Apple existentes que antes no estaban disponibles como:

- **SceneKit** -SceneKit de uso para incluir 3D se modela en la interfaz de usuario de la aplicación de inspección incluyendo la mayoría de las características disponibles en otras plataformas como física, iluminación, sombreado, animación y los sistemas de partículas. No se admiten audio espacial 3D, sombreadores de sistema operativo u OpenGL personalizados, filtros de imágenes de núcleo y materiales basados en físicamente.
- **SpriteKit** -SpriteKit de uso para representar y animar sprites en incluyendo la mayoría de las características disponibles en otras plataformas como sistemas de acciones, física, iluminación y partícula de interfaz de usuario de la aplicación de inspección de aplicación. No se admiten los filtros de imágenes de núcleo, reproducción de vídeo y audio espacial 3D.
- **AVFoundation** : para administrar y reproduzca el audio.
- **CloudKit** : para mover datos entre los contenedores de aplicación e iCloud de inspección.
- **Principales del Audio** : para administrar tipos de datos para representar secuencias de audio, búferes complejos y valores de hora.
- **GameKit** : para crear juegos sociales.

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>Sugerencias automático

watchOS 3 permite a proactivamente presentan información al usuario dentro de la aplicación dada contextos. Para admitir esta característica, el [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) incluye ahora el `MapItem` propiedad que permite a la aplicación proporcionar información de ubicación para su uso posterior por otras aplicaciones.

Para obtener más información, visite nuestro [Introducción a sugerencias automático](~/ios/watchos/platform/proactive-suggestions.md) guía.

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>Mejoras de seguridad y privacidad

Apple ha realizado varias mejoras en la seguridad y privacidad en watchOS 3 que le ayudará a los desarrolladores mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final.

Como resultado, aplicaciones que se ejecutan en watchOS 3 (o posterior) deben declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de una o varias claves específicas de privacidad de sus `Info.plist` archivos que explican al usuario ¿por qué la aplicación desea tener acceso.

Dado que watchOS 3 comparte estos cambios con iOS 10, visite nuestro iOS 10 [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) guía para obtener más información.

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>Las instantáneas y acoplar

En watchOS 3, Apple ha agregado el muelle donde los usuarios pueden anclar sus aplicaciones favoritas y acceder rápidamente a ellos. Cuando el usuario presiona el botón de lado en el de Apple Watch, se mostrará una galería de instantáneas de la aplicación anclados. El usuario puede deslizar rápidamente izquierda o derecha para buscar la aplicación deseada y después puntee en la aplicación para iniciarla, reemplazando la instantánea con la interfaz de la aplicación en ejecución.

Periódicamente, el sistema toma instantáneas de la interfaz de usuario de la aplicación y esas instantáneas utiliza para rellenar a los documentos. watchOS ofrece la oportunidad para actualizar su contenido y la interfaz de usuario antes de esta instantánea se toma de la aplicación.

Para obtener más información, vea nuestra [tareas en segundo plano](~/ios/watchos/platform/background-tasks.md) guía y de Apple [WKSnapshotRefreshBackgroundTask referencia](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) .

<a name="User-Notifications" />

## <a name="user-notifications"></a>Notificaciones de usuario

El marco de trabajo de notificación al usuario que se introdujo en watchOS 3 admite la entrega de notificaciones locales y remotas a la de Apple Watch. Utilice este marco de trabajo para programar las notificaciones basadas en condiciones específicas, como la hora del día o ubicación y para recibir y controlar las notificaciones.

Para obtener más información, visite nuestro [rápido técnicas de interacción para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guía.

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>Ver mejoras de conectividad de Framework

El nuevo `HasContentPending` propiedad de la [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) clase indica que la sesión ha recibido datos en segundo plano que debe procesarse. Y el `RemainingComplicationUserInfoTransfers` propiedad devuelve el resto horas a las que la aplicación de iOS puede actualizar su watchOS complicación.

Para obtener más información, visite nuestro [tareas en segundo plano](~/ios/watchos/platform/background-tasks.md) guía.

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>Mejoras de WatchKit Framework

watchOS 3 incluye varias mejoras en el marco de trabajo de WatchKit incluyen los siguientes:

- La aplicación puede obtener el estado de la la copa Digital con el nuevo [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) clase y recibir actualizaciones cuando el usuario gira la copa utilizando la [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) clase.
- El [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) clase ahora incluye la `ApplicationState` método y [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) constante que la aplicación puede usar para realizar un seguimiento del estado en tiempo de ejecución de la aplicación. `WKExtension` También proporciona dos nuevos métodos que pueden utilizarse para programar tareas en segundo plano.
- El [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) ahora incluye la nueva `ApplicationWillEnterForeground`, `ApplicationDidEnterBackground` y `HandleBackgroundTasks` métodos para supervisar los cambios en el estado de la aplicación y controlar las actualizaciones de tareas de fondo.
- Un nuevo [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) se ha agregado la clase para proporcionar los siguientes tipos de reconocimiento de gestos para las aplicaciones de inspección: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer ](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) y [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- El nuevo [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) clase proporciona una interfaz para cualquier HomeKit adjunta cámara IP.
- El nuevo [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) clase permite que la aplicación mostrar una película "póster" que se sustituye por la película de ejecución cuando el usuario puntee en él.
- El nuevo [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) clase permite que la aplicación presentar un botón de Apple Pay en su interfaz de usuario que inicie una solicitud de pago cuando puntea.
- El nuevo [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) clase presenta una interfaz para mostrar una escena SceneKit en el de Apple Watch.
- El nuevo [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) clase presenta una interfaz para mostrar una escena SpriteKit en el de Apple Watch.

Para obtener más información, visite nuestro [rápido técnicas de interacción para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guía.

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>Mejoras de la aplicación de entrenamiento

Nuevo para watchOS 3, entrenamiento relacionados con las aplicaciones tienen la capacidad para ejecutarse en segundo plano en el Apple Watch. Para habilitar esta característica (y obtener acceso a datos HealthKit), la aplicación debe incluir el `WKBackgroundModes` clave en el `Info.plist` archivo con el valor `workout-processing`.

Además, el desarrollador ahora tiene la capacidad para iniciar la aplicación de entrenamiento watchOS desde la versión de la aplicación de iOS en iPhone emparejado.

Para obtener más información, visite nuestro [mejoras de la aplicación de entrenamiento](~/ios/watchos/platform/workout-apps.md) guía.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Cambios de Framework adicionales

Además de los cambios principales framework y adiciones enumeradas anteriormente, Apple ha realizado muchos cambios de marco secundarias adicionales en watchOS 3.

Para obtener más información, visite nuestro [Framework modificándola](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) guía.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>Interfaces API en desuso

Las API siguientes han quedado obsoletas en watchOS 3:

- La `UILocalNotification` clase de UIKit ha quedado desusado y debe reemplazarse por el marco de trabajo de notificación al usuario.

Vea de Apple [watchOS 2.2 a diferencias en las API watchOS 3.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) documentación para obtener una lista completa de elementos obsoletos y los cambios.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Novedades de watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
