---
title: Plataformas Objective-c.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: a783dc554f54922f4fa4f99a43d83c4c864ef681
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="objective-c-platforms"></a>Plataformas Objective-c.


Incrustación de .NET puede tener como destino varias plataformas al generar código Objective-C:

* macOS
* iOS
* tvOS
* watchOS [todavía no ha implementado]

Se selecciona la plataforma pasando el `--platform=<platform>` argumento de línea de comandos para el embeddinator.

Al compilar para las plataformas de iOS, tvOS y watchOS, la embeddinator siempre creará un marco que incrusta Xamarin.iOS, puesto que Xamarin.iOS contiene una gran cantidad de código de soporte en tiempo de ejecución que se requiere en estas plataformas.

Sin embargo, al compilar para la plataforma de macOS, es posible elegir si el marco de trabajo generado debe incrustar Xamarin.Mac o no. Es posible que no incruste Xamarin.Mac si el ensamblado dependiente no hace referencia a Xamarin.Mac.dll (directa o indirectamente), y esta opción está seleccionada, pasando `--platform=macOS` a la embeddinator.

Si el ensamblado dependiente contiene una referencia a Xamarin.Mac.dll, es necesario incrustar Xamarin.Mac y además el embeddinator debe conocer qué plataforma de destino para usar.

Hay tres posibles marcos de trabajo de destino Xamarin.Mac: `modern` (llamado anteriormente `mobile`), `full` y `system` (se describe la diferencia entre sí en del Xamarin.Mac [.NET framework de destino] [ 1] documentación), y cada uno de ellos se selecciona pasando `--platform=macOS-modern`, `--platform=macOS-full` o `--platform=macOS-system` a la embeddinator.

[1]: ~/mac/platform/target-framework.md
