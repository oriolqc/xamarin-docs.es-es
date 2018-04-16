---
title: Limitaciones
description: Algunas limitaciones de Xamarin Live Player
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2018
ms.openlocfilehash: 43699e77bc8b7365c6d5f7fbf27e19a945e69e22
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="limitations"></a>Limitaciones

![Característica de vista previa](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisitos del dispositivo
La aplicación de Xamarin Live Player es compatible con los siguientes dispositivos:

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 o posterior.
- ARM-v7a, ARM v8a, ARM64-v8a, x 86 o procesador x86_64.

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 o posterior.
- Procesador ARM64.

-----

## <a name="limitations"></a>Limitaciones

Existen algunas limitaciones en lo que se puede ejecutar en Xamarin Live Player, incluidos los siguientes puntos:

### <a name="xamarinforms"></a>Xamarin.Forms
- No se admiten Custom Renderers.
- No se admiten efectos.
- No se admiten controles personalizados con propiedades enlazables.
- No se admiten recursos embebidos (ej. imágenes u otros recursos embebidos en una PCL).
- Frameworks MVVM de otros fabricantes no son compatibles (ej. Prism, Mvvm Cross, Mvvm Light, etc.).
- No se admiten Asset Catalogs en iOS.

### <a name="other-project-types"></a>Otros tipos de proyecto
- Xamarin Live Player no está destinado a proyectos nativos de Android o iOS (que usan archivos XML en Android o Storyboards para la interfaz de usuario de iOS).

### <a name="misc"></a>Otros
- Compatibilidad limitada para APIs de reflexión (actualmente afecta a algunos paquetes NuGets populares, como SQLite y Json.NET). Otros paquetes NuGets todavía puede ser compatible.
- Algunas clases system no pueden sobreescribirse (por ejemplo, no puede implementar una subclase).
- Algunas características de la plataforma que requieren aprovisionamiento no funcionan en la aplicación de Xamarin Live Player (sin embargo se ha configurado para operaciones comunes, como acceso a la Galería fotográfica).
- Se omiten los destinos personalizados y pasos de compilación. Por ejemplo, no se pueden incorporar herramientas como Fody, Refit, AutoFac y AutoMapper.
- Proyectos de F# no se admiten en Android y la compatibilidad con iOS es limitada
- Escenarios avanzados con clases genéricas personalizadas e interfaces podrían no admitirse.

Notifique los problemas adicionales en [bugzilla](https://aka.ms/live-player-report-issue).


## <a name="related-links"></a>Enlaces relacionados

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
- [Configuración](~/tools/live-player/install.md)
