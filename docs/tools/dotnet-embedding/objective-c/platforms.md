---
title: Plataformas Objective-c.
description: Este documento describe las diferentes plataformas que puede usarse para incrustar .NET cuando se trabaja con código Objective-C. Se trata de Mac OS, iOS, tvOS y watchOS.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 6eeb776959d1a2a37d67bfae6603971d0e5a22b7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793896"
---
# <a name="objective-c-platforms"></a>Plataformas Objective-c.

Incrustación de .NET puede tener como destino varias plataformas al generar código Objective-C:

* macOS
* iOS
* tvOS
* watchOS [todavía no ha implementado]

Se selecciona la plataforma pasando el `--platform=<platform>` argumento de línea de comandos a la inserción de. NET.

Al compilar para iOS, tvOS y watchOS plataformas, .NET incrustar siempre creará un marco que incrusta Xamarin.iOS, puesto que Xamarin.iOS contiene una gran cantidad de código de soporte en tiempo de ejecución que se requiere en estas plataformas.

Sin embargo, al compilar para la plataforma de macOS, es posible elegir si el marco de trabajo generado debe incrustar Xamarin.Mac o no. Es posible que no incruste Xamarin.Mac si el ensamblado dependiente no hace referencia a Xamarin.Mac.dll (directa o indirectamente), y esta opción está seleccionada, pasando `--platform=macOS` a la herramienta de incrustación. NET.

Si el ensamblado dependiente contiene una referencia a Xamarin.Mac.dll, es necesario incrustar Xamarin.Mac y además el embeddinator debe conocer qué plataforma de destino para usar.

Hay tres posibles marcos de trabajo de destino Xamarin.Mac: `modern` (llamado anteriormente `mobile`), `full` y `system` (se describe la diferencia entre sí en del Xamarin.Mac [.NET framework de destino] [ 1] documentación), y cada uno de ellos se selecciona pasando `--platform=macOS-modern`, `--platform=macOS-full` o `--platform=macOS-system` a la herramienta de incrustación. NET.

[1]: ~/mac/platform/target-framework.md
