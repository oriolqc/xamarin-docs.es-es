---
title: Las rutas de acceso y las líneas de SkiaSharp
description: Utilice SkiaSharp para dibujar líneas y los gráficos de rutas de acceso
ms.topic: article
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 897e3bbe0375a425709ec63edf25088ac35106e5
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="skiasharp-lines-and-paths"></a>Las rutas de acceso y las líneas de SkiaSharp

_Utilice SkiaSharp para dibujar líneas y los gráficos de rutas de acceso_

El [sección anterior](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) muestra que el SkiaSharp `SKCanvas` clase incluye varios métodos para dibujar rectángulos, elipses y rectángulos redondeados. Esta sección y en las secciones posteriores tratan las diferentes clases conectadas con la creación y la representación *trazados de gráficos*.

La ruta de acceso de gráficos es el enfoque más generalizado para dibujar líneas y curvas en SkiaSharp. Esta sección incluye el uso de un `SKPath` objeto que se va a dibujar líneas rectas como utilizar una colección de líneas rectas pequeños (denominada una *polilínea*) para dibujar curvas que se pueden definir matemáticamente. Una sección posterior se describe los diversos tipos de curvas compatibles con `SKPath`.

Todos los programas de ejemplo en esta sección aparecen bajo el encabezado **líneas y las rutas de acceso** en la página principal de la [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa y en el [ **Las rutas de acceso** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Paths) carpeta de la solución.

## <a name="lines-and-stroke-capslinesmd"></a>[Líneas y extremos de trazo](lines.md)

Obtenga información acerca de cómo usar SkiaSharp para dibujar líneas con extremos de trazo diferente.

## <a name="path-basicspathsmd"></a>[Conceptos básicos del trazado](paths.md)

Explorar el objeto SkiaSharp SKPath para combinar las líneas y curvas conectadas.

## <a name="the-path-fill-typesfill-typesmd"></a>[Tipos de relleno del trazado](fill-types.md)

Descubra los distintos efectos posibles con tipos de relleno de ruta de acceso de SkiaSharp.

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[Polilíneas y ecuaciones paramétricas](polylines.md)

Utilice SkiaSharp para representar cualquier línea que puede definir con ecuaciones paramétricas.

## <a name="dots-and-dashesdotsmd"></a>[Puntos y guiones](dots.md)

Supere las complejidades de dibujar líneas de puntos y guiones en SkiaSharp.

## <a name="finger-paintingfinger-paintmd"></a>[Pintura con los dedos](finger-paint.md)

Use los dedos para dibujar en el lienzo.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
