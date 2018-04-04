---
title: Limitaciones
description: Algunas limitaciones de Xamarin Player en vivo
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2018
ms.openlocfilehash: 0068540ec385ab3be56865c7728eb3128154a2ea
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="limitations"></a>Limitaciones

![Característica de vista previa](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisitos de dispositivo
La aplicación de Xamarin Player en vivo es compatible con los siguientes dispositivos:

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 o posterior.
- ARM-v7a, ARM v8a, ARM64-v8a, x 86 o procesador x86_64.

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 o posterior.
- Procesador de ARM64.

-----

## <a name="limitations"></a>Limitaciones

Existen algunas limitaciones en lo que se puede ejecutar Xamarin Player en vivo, incluidos los siguientes elementos:

### <a name="xamarinforms"></a>Xamarin.Forms
- No se admiten los representadores personalizados.
- No se admiten efectos.
- No se admiten controles personalizados con propiedades enlazables personalizado.
- No se admiten los recursos incrustados (ie. incrustar imágenes u otros recursos en una PCL).
- Marcos de trabajo de otros fabricantes MVVM no son compatibles (en Internet Explorer. Prisma, Mvvm cruzada, Mvvm claro, etcetera.).
- No se admiten los catálogos de activos en iOS.

### <a name="other-project-types"></a>Otros tipos de proyecto
- Reproductor en vivo está destinado a Android nativo o proyectos de iOS (que usan XML Android o guiones gráficos para la interfaz de usuario).

### <a name="misc"></a>Varios
- Compatibilidad limitada para reflexión (actualmente afecta a algunos NuGets populares, como el código y Json.NET). Otros NuGets todavía puede ser compatible.
- No se puede invalidar algunas clases del sistema (por ejemplo, no se puede implementar una subclase).
- Algunas características de la plataforma que requieren el aprovisionamiento no pueden trabajar en la aplicación de Xamarin Player en vivo (sin embargo se ha configurado para operaciones comunes, como acceso de la Galería fotográfica).
- Se omiten los destinos personalizados y pasos de compilación. Por ejemplo, herramientas como Fody, Retit, AutoFac y no se pueden incorporar AutoMapper.
- Proyectos de F # no se admiten en Android y compatibilidad en iOS limitada
- Escenarios avanzados con clases genéricas personalizadas e interfaces podrían no admitirse.

Notifique los problemas adicionales en [bugzilla](https://aka.ms/live-player-report-issue).


## <a name="related-links"></a>Vínculos relacionados

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
- [Configuración](~/tools/live-player/install.md)
