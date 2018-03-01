---
title: Integraciones de ejemplo
ms.topic: article
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 3b07e457bd54f16826f44a3b913434fea6ebe20b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="sample-integrations"></a>Integraciones de ejemplo

## <a name="sample-integrations"></a>Integraciones de ejemplo

Consulte la [cocina receptor] [ KitchenSink] ejemplo para obtener un ejemplo de una integración. Simplemente compilar `KitchenSink.sln` en Visual Studio para Mac o Visual Studio y a continuación, abra `KitchenSink.workbook`.

[![Captura de pantalla de integración de receptor de cocina](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png)

El ejemplo de receptor de cocina muestra ambos conjuntos de conceptos:

* Las partes de representación muestran cómo usar `RepresentationManager` para mejorar la presentación utilizando las representaciones integradas.
* El `Person` objeto y el representador de JavaScript asociado muestran cómo utilizar `ISerializableObject` sin tener que pasar a través de un proveedor de representación.

Consulte también [SkiaSharp] [ skiasharp] para obtener un ejemplo del mundo real de integración de base de datos que usa las existentes [representaciones](~/tools/workbooks/sdk/representations.md) proporcionada por los libros de Xamarin para representar sus tipos.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks