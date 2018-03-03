---
title: "Características de la plataforma"
description: "Características específicas del de Apple Watch para incluir en las aplicaciones de watchOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 16e779761aa164ea9890547e3baca527a3592021
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="platform-features"></a>Características de la plataforma

_Características específicas del de Apple Watch para incluir en las aplicaciones de watchOS._

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Mejoras de pago de Apple](~/ios/watchos/platform/apple-pay.md)

En watchOS 3, el marco de trabajo PassKit se ha ampliado para permitir la compatibilidad con los pagos seguros, de la aplicación (de mercancías físicas y servicios) para las aplicaciones que se ejecutan en el de Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Tareas en segundo plano](~/ios/watchos/platform/background-tasks.md)

watchOS 3 presenta varias tareas en segundo plano que una aplicación puede usar para actualizar su información asegurándose de que tiene el contenido que el usuario necesita antes de abrirlo.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introducción a watchOS 4](introduction-to-watchos4.md)

Nuevas características de watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introducción a watchOS 3](introduction-to-watchos3/index.md)

Este artículo detallan todas las API nuevas y modificadas disponibles en watchOS 3 para los desarrolladores de Xamarin.

##  <a name="notificationsnotificationsmd"></a>[Notificaciones](notifications.md)

Obtenga información acerca de cómo proporcionar el control de la aplicación de inspección de notificación personalizada.

##  <a name="complicationscomplicationsmd"></a>[Complicaciones](complications.md)

Agregar compatibilidad de complicación para mostrar los datos actualizados en la pantalla del reloj.


## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Sugerencias automático](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 permite a proactivamente presentan información al usuario dentro de la aplicación dada contextos. Para admitir esta característica, el [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) incluye ahora el `MapItem` propiedad que permite a la aplicación proporcionar información de ubicación para su uso posterior por otras aplicaciones.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Técnicas de interacción rápida](~/ios/watchos/platform/quick-interaction-techniques.md)

Proporciona las interacciones del usuario rápido son esenciales para crear aplicaciones de Apple Watch atractivas y complicaciones. Nuevo para watchOS 3, Apple ha agregado compatibilidad para identificadores de gestos, acceso a la copa Digital y nuevas técnicas de notificación de usuario y la navegación. Esto, junto con la compatibilidad agregada para SceneKit y SpriteKit, permiten al desarrollador crear fácilmente enriquecidas y concisa interfaces que son sensibles y rápido.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Mejoras de la aplicación de entrenamiento](~/ios/watchos/platform/workout-apps.md)

Nuevo para watchOS 3, entrenamiento relacionados con las aplicaciones tienen la capacidad para ejecutarse en segundo plano en el Apple Watch. Para habilitar esta característica (y obtener acceso a datos HealthKit), la aplicación debe incluir el `WKBackgroundModes` clave en el `Info.plist` archivo con el valor `workout-processing`.
