---
title: características de la plataforma watchOS
description: Este documento incluye vínculos a diversas guías que describen las características de la plataforma watchOS, como Apple Pay, notificaciones, complicaciones, sugerencias proactivas, aplicaciones de entrenamiento y mucho más.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 09200ba5968838edf829b30a50a8ad0f4a3ab3aa
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "39030683"
---
# <a name="watchos-platform-features"></a>características de la plataforma watchOS

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[Introducción a watchOS 5](introduction-to-watchos5/index.md)

Este documento proporciona una descripción general de las características nuevas y actualizadas en watchOS 5 que están disponibles para su uso al compilar aplicaciones para watchOS con Xamarin.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introducción a watchOS 4](introduction-to-watchos4.md)

Este documento proporciona una descripción general de las características que se agregan y actualizan en watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introducción a watchOS 3](introduction-to-watchos3/index.md)

En este artículo se describe las API nuevas y actualizadas en watchOS 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Mejoras de Apple Pay](~/ios/watchos/platform/apple-pay.md)

En watchOS 3, el marco de PassKit se ha ampliado para permitir la compatibilidad con los pagos seguros, en la aplicación (de productos y servicios) para las aplicaciones que se ejecutan en el Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Tareas en segundo plano](~/ios/watchos/platform/background-tasks.md)

watchOS 3 presenta varias tareas en segundo plano que una aplicación puede usar para actualizar su información asegurarse de que tiene el contenido que el usuario necesita antes de abrirlo.

## <a name="notificationsnotificationsmd"></a>[Notificaciones](notifications.md)

Obtenga información sobre cómo proporcionar una notificación personalizada de control en la aplicación del reloj.

## <a name="complicationscomplicationsmd"></a>[Complicaciones](complications.md)

Agregar compatibilidad con la complicación para mostrar los datos actualizados en la pantalla del reloj.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Sugerencias proactivas](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 permite a presentar proactivamente información al usuario dentro de la aplicación dada contextos. Para admitir esta característica, el [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) ahora incluye la `MapItem` propiedad que permite que la aplicación proporcione la información de ubicación para su uso posterior por otras aplicaciones.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Técnicas de interacción rápida](~/ios/watchos/platform/quick-interaction-techniques.md)

Proporcionar las interacciones del usuario rápido son esenciales para crear atractivas aplicaciones de Apple Watch y complicaciones. Nuevo para watchOS 3, Apple ha agregado compatibilidad para los reconocedores de gestos, acceso a la corona Digital y nuevas técnicas de notificación de usuario y la navegación. Esto, junto con la compatibilidad agregada para SceneKit y SpriteKit, permiten al desarrollador crear fácilmente interfaces enriquecidas y ofrecer rápidos y dinámico.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Mejoras de la aplicación de entrenamiento](~/ios/watchos/platform/workout-apps.md)

Nuevo para watchOS 3, entrenamiento relacionados con las aplicaciones tienen la capacidad de ejecutar en segundo plano en el Apple Watch. Para habilitar esta característica (y obtener acceso a datos HealthKit), la aplicación debe incluir el `WKBackgroundModes` clave en el `Info.plist` archivo con el valor `workout-processing`.
