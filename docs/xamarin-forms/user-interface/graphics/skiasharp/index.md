---
title: Uso de SkiaSharp en Xamarin.Forms
description: "Use SkiaSharp para gráficos 2D en las aplicaciones de Xamarin.Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/11/2017
ms.openlocfilehash: eaebd8cbae996e9a5792d0a4898fafb72bdded47
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="using-skiasharp-in-xamarinforms"></a>Uso de SkiaSharp en Xamarin.Forms

_Use SkiaSharp para gráficos 2D en las aplicaciones de Xamarin.Forms_

SkiaSharp es un sistema de gráficos 2D para .NET y C# gracias al motor de gráficos de Skia de código abierto que se utiliza habitualmente en productos de Google. Puede usar SkiaSharp en las aplicaciones de Xamarin.Forms para dibujar texto, mapas de bits y gráficos vectoriales 2D. Consulte la [dibujo 2D](~/graphics-games/skiasharp/index.md) guía para obtener más información sobre la biblioteca de SkiaSharp y algunos otros tutoriales.

Esta guía se da por supuesto que está familiarizado con la programación de Xamarin.Forms.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Seminario Web: SkiaSharp para Xamarin.Forms**

## <a name="skiasharp-preliminaries"></a>Pasos preliminares SkiaSharp

SkiaSharp para Xamarin.Forms se empaqueta como un paquete de NuGet. Después de crear una solución Xamarin.Forms en Visual Studio o Visual Studio para Mac, puede usar el Administrador de paquetes de NuGet para buscar el **SkiaSharp.Views.Forms** del paquete y lo agrega a la solución. Si activa la **referencias** sección de cada proyecto después de agregar SkiaSharp, puede ver que varios **SkiaSharp** bibliotecas se han agregado a cada uno de los proyectos de la solución.

Si la aplicación de Xamarin.Forms está destinada a iOS, use la página de propiedades de proyecto para cambiar el destino de implementación mínima a iOS 8.0.

En cualquier página de C# que utiliza SkiaSharp desea incluir un `using` la directiva para la [ `SkiaSharp` ](https://developer.xamarin.com/api/namespace/SkiaSharp/) espacio de nombres, que abarca todos los SkiaSharp clases, estructuras y enumeraciones que va a utilizar en los gráficos la programación. También debe tener un `using` la directiva para la [ `SkiaSharp.Views.Forms` ](https://developer.xamarin.com/api/namespace/SkiaSharp.Views.Forms/) espacio de nombres para las clases específicas de Xamarin.Forms. Esto es una cantidad menor espacio de nombres, con los más importantes clase [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/). Esta clase se deriva de la Xamarin.Forms `View` clase y hospedan la salida de gráficos de SkiaSharp.

> [!IMPORTANT]
> El `SkiaSharp.Views.Forms` espacio de nombres también contiene un `SKGLView` clase que deriva de `View` pero usa OpenGL para representar los gráficos. Por motivos de simplicidad, esta guía restringe a sí mismo a `SKCanvasView`, pero con `SKGLView` en su lugar, es muy similar.

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[Conceptos básicos de dibujo de SkiaSharp](basics/index.md)

Algunas de las cifras más sencillas de gráficos que se pueden dibujar con SkiaSharp son círculos, elipses y rectángulos. Mostrar estas cifras, obtendrá información sobre coordenadas SkiaSharp, tamaños y colores.

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[Trazados y líneas de SkiaSharp](paths/index.md)

Una ruta de acceso de gráficos es una serie de líneas rectas conectadas y curvas conectadas. Las rutas de acceso pueden ser traza, rellenado, o ambos. Este tema abarca muchos aspectos del dibujo de líneas, incluidos los extremos del trazo y uniones y discontinuas y líneas de puntos, pero deja poco geometrías de la curva.

## <a name="skiasharp-transformstransformsindexmd"></a>[Transformaciones de SkiaSharp](transforms/index.md)

Las transformaciones permiten que objetos de gráficos uniformemente traducir, escalar, girar, o sesgado. Este artículo también muestra cómo puede utilizar una matriz de transformación de 3 por 3 estándar para crear transformaciones no afines y aplicar transformaciones a las rutas de acceso.

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[Trazados y curvas de SkiaSharp](curves/index.md)

La exploración de las rutas de acceso continúa con la adición de curvas para los objetos de una ruta de acceso y aprovecharse de otras características eficaces de la ruta de acceso. Verá cómo se puede especificar una ruta de acceso completa en una cadena de texto concisa, cómo utilizar los efectos de la ruta de acceso y cómo profundizar en el funcionamiento interno de la ruta de acceso.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
- [SkiaSharp con Xamarin.Forms seminario Web (vídeo)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
