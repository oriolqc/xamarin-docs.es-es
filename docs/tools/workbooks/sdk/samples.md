---
title: Integraciones de ejemplo
description: Vínculos de este documento a ejemplos que muestran las integraciones de libros de Xamarin. Ejemplos vinculados trabajan con representación de representación y SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 75d88af4c294a35d45f05724eb96ce822cddf126
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793978"
---
# <a name="sample-integrations"></a>Integraciones de ejemplo

Consulte la [cocina receptor] [ KitchenSink] ejemplo para obtener un ejemplo de una integración. Simplemente compilar `KitchenSink.sln` en Visual Studio para Mac o Visual Studio y a continuación, abra `KitchenSink.workbook`.

[![Captura de pantalla de integración de receptor de cocina](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

El ejemplo de receptor de cocina muestra ambos conjuntos de conceptos:

* Las partes de representación muestran cómo usar `RepresentationManager` para mejorar la presentación utilizando las representaciones integradas.
* El `Person` objeto y el representador de JavaScript asociado muestran cómo utilizar `ISerializableObject` sin tener que pasar a través de un proveedor de representación.

Consulte también [SkiaSharp] [ skiasharp] para obtener un ejemplo del mundo real de integración de base de datos que usa las existentes [representaciones](~/tools/workbooks/sdk/representations.md) proporcionada por los libros de Xamarin para representar sus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
