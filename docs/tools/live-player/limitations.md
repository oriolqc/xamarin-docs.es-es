---
title: Limitaciones
description: Algunas limitaciones de Xamarin Player en vivo
ms.topic: article
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 08/04/2017
ms.openlocfilehash: d551c0a82fb8f970ce5a00ec9e64ac7f49c81a44
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="limitations"></a>Limitaciones

![Característica de vista previa](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisitos de dispositivo
La aplicación de Xamarin Player en vivo es compatible con los siguientes dispositivos:

### <a name="ios"></a>iOS
- iOS 9.0 o posterior.
- Procesador de ARM64.
- Compruebe el [App Store](https://itunes.apple.com/us/app/xamarin-live-player/id1228841832?mt=8) para obtener una lista de dispositivos compatibles.

### <a name="android"></a>Android
- Android 4.2 o posterior.
- ARM-v7a, ARM v8a, ARM64-v8a, x 86 o procesador x86_64.


## <a name="limitations"></a>Limitaciones

Existen algunas limitaciones en lo que se puede ejecutar Xamarin Player en vivo, incluidos los siguientes elementos:

### <a name="android"></a>Android
- Interfaces de usuario de Android diseñadas con archivos AXML no son compatibles actualmente.

### <a name="ios"></a>iOS
- No se admiten algunas características de guión gráfico de iOS.
- no se admiten archivos XIB de iOS.

### <a name="xamarinforms"></a>Xamarin.Forms
- No se admiten los representadores personalizados.
- No se admiten efectos.
- No se admiten controles personalizados con propiedades enlazables personalizado.
- No se admiten los recursos incrustados (ie. incrustar imágenes u otros recursos en una PCL).

### <a name="misc"></a>Varios
- Compatibilidad limitada para reflexión (actualmente afecta a algunos NuGets populares, como el código y Json.NET). Otros NuGets siguen siendo compatibles.
- No se puede invalidar algunas clases del sistema (por ejemplo, no se puede implementar una subclase).
- Algunas características de la plataforma que requieren el aprovisionamiento no pueden trabajar en la aplicación de Xamarin Player en vivo (sin embargo se ha configurado para operaciones comunes, como acceso de la Galería fotográfica).
- Se omiten los destinos personalizados y pasos de compilación. Por ejemplo, no se pueden incorporar herramientas como Fody.

> [!WARNING]
> **Nota:** Xamarin Player Live no funciona con Xamarin Studio

Notifique los problemas adicionales en [bugzilla](https://aka.ms/live-player-report-issue).


## <a name="related-links"></a>Vínculos relacionados

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
- [Configuración](~/tools/live-player/install.md)
