---
title: Limitaciones de Xamarin Live Player
description: Este documento describe las limitaciones de Xamarin Live Player. Describe los requisitos de dispositivos, las características funciona con tipos de proyecto y otros temas variados.
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: aff6990df1b710190f11c2d7fa09c8399e94f8af
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "40251233"
---
# <a name="limitations-of-xamarin-live-player"></a>Limitaciones de Xamarin Live Player

![Característica de vista previa](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisitos del dispositivo

La aplicación Xamarin Live Player admite los siguientes dispositivos Android:

- Android 4.2 o posterior.
- ARM-v7a, v8a de ARM, ARM64-v8a, x 86 o procesador x86_64.

## <a name="limitations"></a>Limitaciones

Hay algunas limitaciones en lo que se puede ejecutar en Xamarin Live Player, incluidos los siguientes elementos:

### <a name="ios"></a>iOS

Live Player no está disponible para iOS.

### <a name="xamarinforms"></a>Xamarin.Forms

- No se admiten los representadores personalizados.
- No se admiten los efectos.
- No se admiten los controles personalizados con propiedades enlazables personalizadas.
- No se admiten los recursos incrustados (p. ej., insertar imágenes u otros recursos en una PCL).
- No se admiten los marcos MVVM de otros fabricantes (p. ej., Prism, Mvvm Cross, Mvvm Light, etc.).

### <a name="other-project-types"></a>Otros tipos de proyecto

- Live Player no está pensado para proyectos nativos de Android (que usan XML de Android para la interfaz de usuario).

### <a name="misc"></a>Varios

- Compatibilidad limitada con la reflexión (actualmente, afecta a algunos NuGets populares, como SQLite y Json.NET). Puede que otros NuGets sigan siendo compatibles. Todavía pueden admitir otros paquetes de NuGet.
- No se pueden reemplazar algunas clases del sistema (por ejemplo, no puede implementar una subclase).
- Algunas características de la plataforma que requieren aprovisionamiento no funcionan en la aplicación Xamarin Live Player (en cambio, se ha configurado para operaciones comunes, como el acceso a la Galería fotográfica).
- Se omiten los destinos personalizados y los pasos de compilación. Por ejemplo, no se pueden incorporar herramientas como Fody, Refit, AutoFac y AutoMapper.
- No se admiten los proyectos de F #
- Escenarios avanzados con clases genéricas personalizadas e interfaces podrían no admitirse.

Use **notificar un problema** en [Visual Studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio-2017) o [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem) para informar cualquier problema con Xamarin Live Player.

## <a name="related-links"></a>Vínculos relacionados

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
- [Configuración](~/tools/live-player/install.md)
