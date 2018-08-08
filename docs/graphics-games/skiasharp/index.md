---
title: Dibujo en 2D con SkiaSharp
description: Este documento proporciona información general sobre 2D multiplataforma de dibujo de SkiaSharp. Incluye vínculos a diversas guías que describen SkiaSharp y sus diversas API.
ms.prod: xamarin
ms.techonology: xamarin-skiasharp
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 7207f33e56f566a5528d93f9957e2ff780a22a65
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615527"
---
# <a name="2d-drawing-with-skiasharp"></a>Dibujo en 2D con SkiaSharp

SkiaSharp proporciona una API de C# eficaz para hacer los gráficos 2D. Funciona con [biblioteca de Google Skia](http://skia.org), la misma biblioteca que se utiliza en las pilas de gráficos de Google Chrome, Firefox y de Android.

[![](images/ide-sml.png "SkiaSharp proporciona una API de C# eficaz para hacer los gráficos 2D")](images/ide.png#lightbox)

SkiaSharp es una biblioteca portátil y se envía cómodamente como un [paquete de NuGet multiplataforma](https://www.nuget.org/packages/SkiaSharp)y admite las siguientes plataformas de fábrica: macOS, el escritorio de Windows, Xamarin.iOS y Xamarin.Android.

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[Introducción a SkiaSharp](~/graphics-games/skiasharp/introduction.md)

Información general sobre los conceptos básicos de SkiaSharp y ejemplo de código para representar gráficos, texto, mapas de bits y usar filtros de imágenes.

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[Tutoriales de SkiaSharp para Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

Obtenga información sobre cómo trabajar con entre gráficos de plataforma que se procesan en Xamarin.Forms:

- [Conceptos básicos de dibujo](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [Dibujar un círculo simple](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [Integración con Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [Píxeles y unidades independientes del dispositivo](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [Animación básica](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [La integración de texto y gráficos](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [Conceptos básicos de mapa de bits](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [Líneas y las rutas de acceso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [Líneas y extremos de trazo](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [Conceptos básicos de la ruta de acceso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [Los tipos de relleno de la ruta de acceso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [Polilíneas y ecuaciones paramétricas](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [Puntos y guiones](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [Pintura con los dedos](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [Transformaciones](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [La transformación de traslación](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [La transformación de escala](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [La transformación de giro](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
  * [Transformación de sesgo](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/skew.md)
  * [Transformaciones de matriz](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)
  * [Manipulaciones táctiles](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)
  * [Transformaciones no afines](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)
  * [Giro 3D](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)
- [Trazados y curvas](~/xamarin-forms/user-interface/graphics/skiasharp/curves/index.md)
  * [Tres maneras de dibujar un arco](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)
  * [Tres tipos de curvas de Bézier](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)
  * [Datos de trazado de SVG](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)
  * [Recorte con trazados y regiones](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)
  * [Efectos del trazado](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)
  * [Trazados y texto](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)
  * [Enumeración e información de trazado](~/xamarin-forms/user-interface/graphics/skiasharp/curves/information.md)
- [Mapas de bits](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/index.md)
  * [Mostrar mapas de bits](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/displaying.md)
  * [Crear y dibujar en mapas de bits](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/drawing.md)
  * [Recortar mapas de bits](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/cropping.md)
  * [Visualización segmentada de mapas de bits](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/segmented.md)
  * [Guardar mapas de bits en archivos](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/saving.md)
  * [Acceso a píxeles de mapas de bits](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/pixel-bits.md)
  * [Animar mapas de bits](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/animating.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[Notas específicas de plataforma](~/graphics-games/skiasharp/platform.md)

Esta página describen las instrucciones de configuración de SkiaSharp en distintas plataformas, incluidos iOS, Android, macOS y Windows.

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[Documentación de API](https://developer.xamarin.com/api/namespace/SkiaSharp/)

Puede examinar el [documentación de API](https://developer.xamarin.com/api/namespace/SkiaSharp/) de SkiaSharp en nuestro sitio web.

## <a name="work-in-progress"></a>Trabajo en curso

SkiaSharp es un trabajo en curso que nos estamos compartiendo con nuestra comunidad. Aunque nos hemos enlaza las partes importantes de la API Skia, cuánto trabajo queda para que se realicen. Estamos usando la API de C estable obtenidas por Skia y nuestro plan es continuar nuestro trabajo a los enlaces de C de Skia para proporcionar una cobertura completa para las API de contribución.

Para ayudarnos a guiar nuestros esfuerzos de enlace, deje comentarios o sugerencias como problemas en el repositorio de GitHub [ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp).
