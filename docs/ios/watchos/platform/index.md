---
title: características de la plataforma watchOS
description: Vínculos de este documento diversas guías disponibles que describe las características de la plataforma de watchOS como Apple Pay, notificaciones, las complicaciones, sugerencias automático, aplicaciones de entrenamiento y mucho más.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 16d10dd69223f404aac7c933302992a1544461e9
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066619"
---
# <a name="watchos-platform-features"></a>características de la plataforma watchOS

Vínculos de este documento diversas guías disponibles que describe las características de la plataforma de watchOS como Apple Pay, notificaciones, las complicaciones, sugerencias automático, aplicaciones de entrenamiento y mucho más.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introducción a watchOS 4](introduction-to-watchos4.md)

Este documento proporciona una descripción general de funciones de agregado y actualizan en watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introducción a watchOS 3](introduction-to-watchos3/index.md)

Este artículo describen las API nuevas y actualizadas en watchOS 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Apple Pay mejoras](~/ios/watchos/platform/apple-pay.md)

En watchOS 3, el marco de trabajo PassKit se ha ampliado para permitir la compatibilidad con los pagos seguros, de la aplicación (de mercancías físicas y servicios) para las aplicaciones que se ejecutan en el de Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Tareas en segundo plano](~/ios/watchos/platform/background-tasks.md)

watchOS 3 presenta varias tareas en segundo plano que una aplicación puede usar para actualizar su información asegurándose de que tiene el contenido que el usuario necesita antes de abrirlo.

## <a name="notificationsnotificationsmd"></a>[Notificaciones](notifications.md)

Obtenga información acerca de cómo proporcionar el control de la aplicación de inspección de notificación personalizada.

## <a name="complicationscomplicationsmd"></a>[Complicaciones](complications.md)

Agregar compatibilidad de complicación para mostrar los datos actualizados en la pantalla del reloj.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Sugerencias automático](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 permite a proactivamente presentan información al usuario dentro de la aplicación dada contextos. Para admitir esta característica, el [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) incluye ahora el `MapItem` propiedad que permite a la aplicación proporcionar información de ubicación para su uso posterior por otras aplicaciones.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Técnicas de interacción rápida](~/ios/watchos/platform/quick-interaction-techniques.md)

Proporciona las interacciones del usuario rápido son esenciales para crear aplicaciones de Apple Watch atractivas y complicaciones. Nuevo para watchOS 3, Apple ha agregado compatibilidad para identificadores de gestos, acceso a la copa Digital y nuevas técnicas de notificación de usuario y la navegación. Esto, junto con la compatibilidad agregada para SceneKit y SpriteKit, permiten al desarrollador crear fácilmente enriquecidas y concisa interfaces que son sensibles y rápido.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Mejoras de la aplicación de entrenamiento](~/ios/watchos/platform/workout-apps.md)

Nuevo para watchOS 3, entrenamiento relacionados con las aplicaciones tienen la capacidad para ejecutarse en segundo plano en el Apple Watch. Para habilitar esta característica (y obtener acceso a datos HealthKit), la aplicación debe incluir el `WKBackgroundModes` clave en el `Info.plist` archivo con el valor `workout-processing`.
