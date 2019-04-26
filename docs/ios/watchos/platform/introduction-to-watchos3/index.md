---
title: Introducción a watchOS 3
description: Este artículo presenta todas las características disponibles en watchOS 3 y las API nuevas y modificadas para desarrolladores de Xamarin.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2017
ms.openlocfilehash: 0428a0df157e359ab34a6a71dbba31bdeb6962fa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224067"
---
# <a name="introduction-to-watchos-3"></a>Introducción a watchOS 3

_Este artículo presenta todas las características disponibles en watchOS 3 y las API nuevas y modificadas para desarrolladores de Xamarin._

Este documento trata los temas siguientes:

- [Novedades en watchOS 3](#Whats-New-in-watchOS-3)
    - [Mejoras de Apple pagar](#Apple-Pay-Enhancements) agrega compatibilidad para los pagos en la aplicación en el Apple Watch.
    - [Tareas en segundo plano](#Background-Tasks) asigne a la aplicación la capacidad de actualizar su información en segundo plano para que esté listo cuando el usuario lo necesite.
    - [Mejoras de las complicaciones](#Complications-Enhancements) se han realizado para watchOS 3 que ofrecen nuevas características para las aplicaciones.
    - [Marcos de trabajo recién disponible](#Newly-Available-Frameworks) han expuesto para las aplicaciones para watchOS.
    - [Sugerencias proactivas](#Proactive-Suggestions) permite que la aplicación mostrar de forma proactiva información al usuario.
    * Varios [mejoras de seguridad y privacidad](#Security-and-Privacy-Enhancements) se han realizado en watchOS 3.
    - [Las instantáneas y acoplar](#Snapshots-and-Dock) ofrecer al usuario un acceso rápido a las aplicaciones de aplicación para watchOS.
    - [Las notificaciones de usuario](#User-Notifications) proporciona notificaciones locales y remotas para el usuario.
    * Varios [mejoras de marco de conectividad de inspección](#Watch-Connectivity-Framework-Enhancements) se han realizado en watchOS 3.
    * Varios [WatchKit Framework mejoras](#WatchKit-Framework-Enhancements) se han realizado en watchOS 3.
    - [Mejoras de la aplicación workout](#Workout-App-Enhancements) ofrece capacidades nuevas al ejercicio relacionados con las aplicaciones de Apple Watch.
- [Cambios adicionales de Framework](#Additional-Framework-Changes) se han realizado a lo largo de watchOS 3.
- [Las API en desuso](#Deprecated-APIs) en watchOS 3.

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>Novedades en watchOS 3

Apple ha agregado varias nuevas API y servicios en watchOS 3 junto con muchas mejoras en las características existentes, incluyendo:

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Mejoras de Apple Pay

En watchOS 3, el marco de PassKit se ha ampliado para permitir la compatibilidad con los pagos seguros, en la aplicación (de productos y servicios) para las aplicaciones que se ejecutan en el Apple Watch.

Use la nueva [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) y [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) clases para presentar y responder a una interfaz donde el usuario puede autorizar a las solicitudes de pago.

Para obtener más información, consulte nuestra [Apple pagar mejoras](~/ios/watchos/platform/apple-pay.md) guía.

<a name="Background-Tasks" />

## <a name="background-tasks"></a>Tareas en segundo plano

watchOS 3 presenta varias tareas en segundo plano que una aplicación puede usar para actualizar su información asegurarse de que tiene el contenido que el usuario necesita antes de abrirlo.

Están disponibles las siguientes tareas en segundo plano nuevo:

- **Actualización de la aplicación en segundo plano** : la [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) tareas permite que la aplicación actualizar su estado en segundo plano. Normalmente esto incluirá la otra tarea, como descargar nuevo contenido desde internet mediante un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Instantáneas de actualización en segundo plano** : la [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) tareas permite que la aplicación actualizar su contenido y la interfaz de usuario antes de que el sistema toma una instantánea que se usará para rellenar el muelle.
- **En segundo plano inspección conectividad** : la [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) se inicia una tarea de la aplicación cuando recibe datos en segundo plano desde el iPhone emparejado.
- **En segundo plano de la sesión de la dirección URL** : la [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) se inicia una tarea de la aplicación cuando una transferencia en segundo plano requiere autorización o completa (éxito o error).

Para obtener más información, consulte nuestra [tareas en segundo plano](~/ios/watchos/platform/background-tasks.md) guía.

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>Mejoras de complicaciones

Las complicaciones son pequeños elementos visuales que proporcionan información útil con un solo vistazo. Dependiendo de la pantalla del reloj seleccionada, el usuario tiene la capacidad para personalizar una esfera del reloj con uno o más complicación.

watchOS 3 proporciona la capacidad para crear uno o más complicación para la aplicación del reloj para que el usuario puede tener acceso a su información de un vistazo de una esfera del reloj de la aplicación.

Además, las complicaciones ofrecen las siguientes ventajas:

- El usuario puede iniciar rápidamente la aplicación pulsando en la complicación directamente desde una esfera del reloj.
- Tener una de las complicaciones de la aplicación en las causas de la cara de inspección del sistema para mantener la aplicación en un estado de listo para iniciar donde intenta iniciar la aplicación en segundo plano, mantenerlo en memoria y proporciona adicional es hora de actualizar.
- Complicaciones se garantiza que al menos 50 de inserción de las actualizaciones al día.
- Cuando la aplicación incluye complicaciones, se presentarán en la Galería de cara de Apple Watch (consulte Apple [agrega complicaciones a la galería](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery) documentación para obtener más información).

En watchOS 3, el marco de trabajo ClockKit ahora incluye varias plantillas nuevas de complicaciones extra grandes como [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) y [CLKComplicationTemplateExtraLargeRingImage ](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Además, para crear texto localizable, use los métodos nuevos de la [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) clase.

Para obtener más información, consulte nuestra [técnicas de interacción rápida para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guía.

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>Marcos de trabajo recién disponibles

watchOS 3 incluye varios marcos de Apple existentes que antes no estaban disponibles, como:

- **SceneKit** -uso de SceneKit para incluir 3D se modela en la interfaz de usuario de la aplicación del reloj, incluyendo la mayoría de las características disponibles en otras plataformas como física, iluminación, sombreado, animación y los sistemas de partículas. No se admiten audio espacial 3D, personalizados sombreados Metal u OpenGL, filtros de imágenes de núcleo y materiales basados en físicamente.
- **SpriteKit** -SpriteKit de uso para representar y animar los sprites de interfaz de usuario de la aplicación de inspección de aplicación incluyendo la mayoría de las características disponibles en otras plataformas, como los sistemas de acciones, physics, iluminación y partícula. No se admiten audio espacial 3D, la reproducción de vídeo y filtros de imágenes de núcleo.
- **AVFoundation** : para administrar y reproducir audio.
- **CloudKit** : para mover datos entre los contenedores de iCloud y de aplicación de inspección.
- **Core Audio** : para administrar los tipos de datos para representar secuencias de audio, búferes complejos y los valores de hora.
- **GameKit** : para crear juegos sociales.

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>Sugerencias proactivas

watchOS 3 permite a presentar proactivamente información al usuario dentro de la aplicación dada contextos. Para admitir esta característica, el [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) ahora incluye la `MapItem` propiedad que permite que la aplicación proporcione la información de ubicación para su uso posterior por otras aplicaciones.

Para obtener más información, consulte nuestra [Introducción a sugerencias proactivas](~/ios/watchos/platform/proactive-suggestions.md) guía.

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>Mejoras de seguridad y privacidad

Apple ha realizado varias mejoras en seguridad y privacidad en watchOS 3 que le ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final.

Como resultado, las aplicaciones que se ejecutan en watchOS 3 (o posterior) deben declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de uno o más claves específicas de privacidad de sus `Info.plist` archivos que explican al usuario por qué la aplicación quiere tener acceso.

Dado que watchOS 3 comparte estos cambios con iOS 10, consulte nuestra iOS 10 [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) guía para obtener más información.

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>Las instantáneas y acoplamiento

En watchOS 3, Apple ha agregado el muelle donde los usuarios pueden anclar sus aplicaciones favoritas y acceder rápidamente a ellos. Cuando el usuario presiona el botón en el Apple Watch, se mostrará una galería de instantáneas de la aplicación anclada. El usuario puede deslizar izquierda o derecha para buscar la aplicación deseada, a continuación, puntee en la aplicación para iniciarla reemplazando la instantánea con la interfaz de la aplicación en ejecución.

Periódicamente, el sistema toma instantáneas de la interfaz de usuario de la aplicación y las instantáneas utiliza para rellenar a los documentos. watchOS ofrece la oportunidad de actualizar su contenido y la interfaz de usuario antes de esta instantánea se toma de la aplicación.

Para obtener más información, consulte nuestra [tareas en segundo plano](~/ios/watchos/platform/background-tasks.md) guía y Apple [WKSnapshotRefreshBackgroundTask referencia](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) .

<a name="User-Notifications" />

## <a name="user-notifications"></a>Notificaciones de usuario

El marco de trabajo de notificación de usuario que introdujo en watchOS 3 admite la entrega de notificaciones locales y remotas para el Apple Watch. Usar este marco de trabajo para programar las notificaciones basadas en condiciones específicas, como la hora del día o la ubicación y para recibir y controlar las notificaciones.

Para obtener más información, consulte nuestra [técnicas de interacción rápida para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guía.

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>Ver las mejoras de marco de conectividad

El nuevo `HasContentPending` propiedad de la [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) clase indica que la sesión ha recibido datos en segundo plano que debe procesarse. Y el `RemainingComplicationUserInfoTransfers` propiedad devuelve el resto horas que la aplicación de iOS puede actualizar su watchOS complicación.

Para obtener más información, consulte nuestra [tareas en segundo plano](~/ios/watchos/platform/background-tasks.md) guía.

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>Mejoras de Framework de WatchKit

watchOS 3 incluye varias mejoras en el marco de WatchKit, incluidas las siguientes:

- La aplicación puede obtener el estado de la corona Digital con el nuevo [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) clase y recibir actualizaciones cuando el usuario gira la corona utilizando el [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) clase.
- El [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) clase ahora incluye la `ApplicationState` método y [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) constante que la aplicación puede usar para realizar un seguimiento del estado en tiempo de ejecución de la aplicación. `WKExtension` También proporciona dos nuevos métodos que pueden usarse para programar tareas en segundo plano.
- El [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) ahora incluye el nuevo `ApplicationWillEnterForeground`, `ApplicationDidEnterBackground` y `HandleBackgroundTasks` métodos para supervisar los cambios de estado de la aplicación y controlar las actualizaciones de tareas en segundo plano.
- Un nuevo [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) ha agregado la clase para proporcionar los siguientes tipos de reconocimiento de gestos para las aplicaciones de watch: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) y [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- El nuevo [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) clase proporciona una interfaz para cualquier HomeKit adjunta cámara IP.
- El nuevo [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) clase permite que la aplicación mostrar una película "este póster" que se reemplaza por la película de ejecución cuando el usuario lo pulsa.
- El nuevo [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) clase permite que la aplicación presentar un botón de Apple Pay en su interfaz de usuario que inicie una solicitud de pago al tocarlo.
- El nuevo [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) clase presenta una interfaz para mostrar una escena de SceneKit en el Apple Watch.
- El nuevo [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) clase presenta una interfaz para mostrar una escena de SpriteKit en el Apple Watch.

Para obtener más información, consulte nuestra [técnicas de interacción rápida para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guía.

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>Mejoras de la aplicación Workout

Nuevo para watchOS 3, entrenamiento relacionados con las aplicaciones tienen la capacidad de ejecutar en segundo plano en el Apple Watch. Para habilitar esta característica (y obtener acceso a datos HealthKit), la aplicación debe incluir el `WKBackgroundModes` clave en el `Info.plist` archivo con el valor `workout-processing`.

Además, el desarrollador tiene ahora la capacidad de iniciar la aplicación de watchOS entrenamiento de la versión de la aplicación de iOS en el iPhone emparejado.

Para obtener más información, consulte nuestra [mejoras de la aplicación Workout](~/ios/watchos/platform/workout-apps.md) guía.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Cambios de marco de trabajo adicionales

Además de los cambios en la plataforma principal y adiciones enumeradas anteriormente, Apple ha realizado muchos cambios de marco secundarias adicionales en watchOS 3.

Para obtener más información, consulte nuestra [cambios adicionales de Framework](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) guía.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>Interfaces API desusadas

Las API siguientes han quedado obsoletas en watchOS 3:

- La `UILocalNotification` clase de UIKit ha quedado desusado y se debe reemplazar con el marco de trabajo de notificación de usuario.

Consulte Apple [watchOS 2.2 a las diferencias de API de watchOS 3.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) documentación para obtener una lista completa de cambios y elementos obsoletos.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Novedades de watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
