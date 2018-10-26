---
title: Integraciones de ejemplo
description: Este documento incluye vínculos a ejemplos que muestran las integraciones de Xamarin Workbooks. Ejemplos vinculados funcionan con representación de representación y SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: e35577b116180d2745e2f6afb792547f63873214
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117426"
---
# <a name="sample-integrations"></a>Integraciones de ejemplo

Consulte la [cocina receptor] [ KitchenSink] ejemplo para obtener un ejemplo de integración. Basta con generar `KitchenSink.sln` en Visual Studio para Mac o Visual Studio y, después, abra `KitchenSink.workbook`.

[![Captura de pantalla de integración de receptor de cocina](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

El ejemplo de receptor de cocina muestra ambos conjuntos de conceptos:

* Los fragmentos de representación muestran cómo usar `RepresentationManager` para mejorar la representación mediante el uso de las representaciones integradas.
* El `Person` objeto y su representador JavaScript asociado se muestra cómo usar `ISerializableObject` sin pasar por un proveedor de representación.

Consulte también [SkiaSharp] [ skiasharp] para un ejemplo del mundo real de una integración que usa existente [representaciones](~/tools/workbooks/sdk/representations.md) proporcionados por Xamarin Workbooks para representar sus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
