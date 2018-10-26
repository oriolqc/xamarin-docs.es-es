---
title: Plataformas de Objective-c.
description: Este documento describe las diversas plataformas de inserción de .NET puede tener como destino cuando se trabaja con código de Objective-C. Describe macOS, iOS, tvOS y watchOS.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 8091fb4e8328f61f1471d061b51b4735de3c089c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108891"
---
# <a name="objective-c-platforms"></a>Plataformas de Objective-c.

Inserción de .NET puede tener como destino varias plataformas al generar código de Objective-C:

* macOS
* iOS
* tvOS
* watchOS [aún no implementado]

La plataforma está seleccionada, pasando el `--platform=<platform>` argumento de línea de comandos a la inserción de. NET.

Al compilar para iOS, tvOS y watchOS plataformas, inserción de .NET siempre creará un marco que incrusta Xamarin.iOS, puesto que Xamarin.iOS contiene una gran cantidad de código de compatibilidad en tiempo de ejecución que se requiere en estas plataformas.

Sin embargo, al compilar para la plataforma macOS, es posible elegir si el marco de trabajo generado debe incrustar Xamarin.Mac o no. Es posible que no incruste Xamarin.Mac si el ensamblado enlazado no hace referencia a Xamarin.Mac.dll (directa o indirectamente), y esta opción está seleccionada, pasando `--platform=macOS` a la herramienta de inserción de .NET.

Si el ensamblado dependiente contiene una referencia a Xamarin.Mac.dll, es necesario incrustar Xamarin.Mac, y además el embeddinator debe saber qué plataforma de destino para usar.

Existen tres posibles plataformas de destino de Xamarin.Mac: `modern` (anteriormente denominados `mobile`), `full` y `system` (se describe la diferencia entre cada uno en Xamarin.Mac [.NET framework de destino] [ 1] documentación), y cada uno de ellos se selecciona pasando `--platform=macOS-modern`, `--platform=macOS-full` o `--platform=macOS-system` a la herramienta de inserción de .NET.

[1]: ~/mac/platform/target-framework.md
