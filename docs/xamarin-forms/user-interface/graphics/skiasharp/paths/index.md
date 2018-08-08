---
title: Las rutas de acceso y las líneas de SkiaSharp
description: En este artículo se explica cómo usar SkiaSharp para dibujar líneas y trazados de gráficos en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9febfabb7b44b1ec09abda4b352691b37565cb48
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615140"
---
# <a name="skiasharp-lines-and-paths"></a>Las rutas de acceso y las líneas de SkiaSharp

_Use SkiaSharp para dibujar líneas y los gráficos de rutas de acceso_

El [sección anterior](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) demostró que el SkiaSharp `SKCanvas` clase incluye varios métodos para dibujar rectángulos, elipses y rectángulos redondeados. Esta sección y en las secciones posteriores tratan las distintas clases conectadas con la creación y la representación *trazados de gráficos*.

La ruta de acceso de gráficos es el enfoque más generalizado para dibujar líneas y curvas de SkiaSharp. En esta sección se describe el uso un `SKPath` objeto para dibujar líneas rectas y usar una colección de líneas rectas diminutas (llamado un *polyline*) para dibujar curvas que se pueden definir matemáticamente. Una sección posterior se describe los distintos tipos de curvas compatibles con `SKPath`.

Todos los programas de ejemplo en esta sección aparecen bajo el encabezado **líneas y las rutas de acceso** en la página principal de la [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa y en el [ **Rutas** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) carpeta de la solución.

## <a name="lines-and-stroke-capslinesmd"></a>[Líneas y extremos de trazo](lines.md)

Obtenga información sobre cómo usar SkiaSharp para dibujar líneas con extremos de trazo diferente.

## <a name="path-basicspathsmd"></a>[Conceptos básicos del trazado](paths.md)

Explorar el objeto de SkiaSharp SKPath para combinar varias líneas y curvas.

## <a name="the-path-fill-typesfill-typesmd"></a>[Tipos de relleno del trazado](fill-types.md)

Descubra los distintos efectos posibles con tipos de relleno de ruta de acceso de SkiaSharp.

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[Polilíneas y ecuaciones paramétricas](polylines.md)

Utilice SkiaSharp para representar cualquier línea que se puede definir con ecuaciones paramétricas.

## <a name="dots-and-dashesdotsmd"></a>[Puntos y guiones](dots.md)

Supere las complejidades de dibujo de líneas de puntos y guiones de SkiaSharp.

## <a name="finger-paintingfinger-paintmd"></a>[Pintura con los dedos](finger-paint.md)

Utilizar los dedos para pintar en el lienzo.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
