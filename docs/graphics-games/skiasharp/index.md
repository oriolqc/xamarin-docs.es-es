---
title: Dibujo 2D
description: Cross Platform dibujo 2D con SkiaSharp
ms.topic: article
ms.prod: xamarin
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 09/14/2017
ms.openlocfilehash: ee0625f22062fef3c27a697ce33488274abc24d9
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="2d-drawing"></a>Dibujo 2D

SkiaSharp proporciona una API de C# eficaz para realizar gráficos 2D. Funciona con [biblioteca de Skia de Google](http://skia.org), la misma biblioteca que alimenta pilas gráficas Google Chrome, Firefox y de Android.

[![](images/ide-sml.png "SkiaSharp proporciona una API de C# eficaz para realizar gráficos 2D")](images/ide.png#lightbox)

Es una biblioteca Portable SkiaSharp y cómodamente se distribuye como un [paquete de NuGet multiplataforma](https://www.nuget.org/packages/SkiaSharp)y admite las siguientes plataformas de fábrica: macOS, el escritorio de Windows, Xamarin.iOS y Xamarin.Android.

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[Introducción a SkiaSharp](~/graphics-games/skiasharp/introduction.md)

Información general sobre los conceptos básicos de ejemplo y SkiaSharp código para representar gráficos, texto, mapas de bits y utilizar filtros de imágenes.

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[Tutoriales de SkiaSharp de Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

Obtenga información acerca de cómo trabajar con entre gráficos de plataforma que se procesan en Xamarin.Forms:

- [Conceptos básicos de dibujo](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [Dibujar un círculo simple](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [Integración con Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [Píxeles y unidades independientes del dispositivo](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [Animación básica](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [Integración de texto y gráficos](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [Conceptos básicos de mapa de bits](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [Las líneas y las rutas de acceso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [Líneas y extremos de trazo](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [Conceptos básicos de la ruta de acceso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [Los tipos de relleno de ruta de acceso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [Polilíneas y ecuaciones paramétricas](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [Puntos y guiones](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [Pintura con los dedos](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [Transformaciones](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [La transformación de traducción](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [La transformación de escala](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [La transformación de giro](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
  * [La transformación sesga](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/skew.md)
  * [Transforma la matriz](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)
  * [Manipulaciones táctil](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)
  * [Transformaciones afines no](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)
  * [Giro 3D](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)
- [Las rutas de acceso y curvas](~/xamarin-forms/user-interface/graphics/skiasharp/curves/index.md)
  * [Tres maneras de dibujar un arco](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)
  * [Tres tipos de curvas de Bézier](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)
  * [Datos de trazado de SVG](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)
  * [Recorte con trazados y regiones](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)
  * [Efectos del trazado](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)
  * [Trazados y texto](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)
  * [Enumeración e información de trazado](~/xamarin-forms/user-interface/graphics/skiasharp/curves/information.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[Notas específicas de plataforma](~/graphics-games/skiasharp/platform.md)

Esta página describe las instrucciones de instalación para SkiaSharp en distintas plataformas como iOS, Android, Mac OS y Windows.

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[Documentación de la API](https://developer.xamarin.com/api/namespace/SkiaSharp/)

Puede examinar el [documentación de la API](https://developer.xamarin.com/api/namespace/SkiaSharp/) para SkiaSharp en nuestro sitio web.

## <a name="work-in-progress"></a>Trabajo en curso

SkiaSharp es un trabajo en curso que se está compartiendo con nuestra comunidad. Aunque nos hemos enlaza partes importantes de la API Skia, cantidad de trabajo permanece para realizarse. Estamos utilizando la API de C estable obtenidas por Skia y nuestro plan es continuar que contribuyen a nuestro trabajo a los enlaces de C de Skia para ofrecer una explicación exhaustiva a las API.

Para ayudarnos a guide nuestros esfuerzos de enlace, deje los comentarios y sugerencias como problemas en el repositorio de GitHub [http://github.com/mono/SkiaSharp](http://github.com/mono/SkiaSharp).
