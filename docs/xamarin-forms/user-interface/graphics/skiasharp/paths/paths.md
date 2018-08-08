---
title: Conceptos básicos de la ruta de acceso de SkiaSharp
description: En este artículo explora el objeto de SkiaSharp SKPath para la combinación de líneas y curvas conectadas y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 3c07614c12fb503638d3d5e63b24eb5367ba691a
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615540"
---
# <a name="path-basics-in-skiasharp"></a>Conceptos básicos de la ruta de acceso de SkiaSharp

_Explorar el objeto de SkiaSharp SKPath para la combinación de líneas y curvas conectadas_

Una de las características más importantes de la ruta de acceso de gráficos es la capacidad para definir cuándo deben estar conectadas varias líneas y al no deben estar conectados. La diferencia puede ser bastante visible, como se muestra en la parte superior de estos dos triángulos:

![](paths-images/connectedlinesexample.png "Dos triángulos que muestra la diferencia entre las líneas de conexión y sin conexión")

Una ruta de acceso de gráficos está encapsulado por el [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) objeto. Una ruta de acceso es una colección de uno o varios *contornos*. Cada contorno es una colección de *conectado* líneas rectas y curvas. Contornos no están conectados entre sí, pero pueden superponer visualmente. A veces un único perfil puede superponer.

Un contorno generalmente comienza con una llamada al método siguiente de `SKPath`:

- `MoveTo` Para iniciar un nuevo contorno

El argumento para ese método es un punto único, que se puede expresar como un `SKPoint` valor o como independiente X e Y coordenadas. El `MoveTo` llamada establece un punto al principio de contorno e inicial *punto actual*. Puede llamar a los métodos siguientes para continuar con el contorno con una línea o curva desde el punto actual a un punto especificado en el método, que, a continuación, se convierte en el nuevo punto activo:

- `LineTo` Para agregar una línea recta a la ruta de acceso
- `ArcTo` Para agregar un arco, que es una línea de la circunferencia de un círculo o elipse
- `CubicTo` Para agregar una curva spline de Bézier cúbica
- `QuadTo` Para agregar una curva spline de Bézier cuadrática
- `ConicTo` Para agregar una spline Bézier cuadrática racional, que puede representar con exactitud cónicos secciones (botón de puntos suspensivos, parábolas e hipérbolas)

Ninguno de estos cinco métodos contienen toda la información necesaria para describir la línea o curva. Cada uno de estos cinco métodos funciona junto con el punto actual establecido por la llamada de método que le precede inmediatamente. Por ejemplo, el `LineTo` método agrega una línea recta para el contorno basándose en el punto actual, por lo que el parámetro `LineTo` es solo un único punto.

El `SKPath` clase también define los métodos que tienen los mismos nombres que estos seis métodos, pero con un `R` al principio:

- `RMoveTo`
- `RLineTo`
- `RArcTo`
- `RCubicTo`
- `RQuadTo`
- `RConicTo`

El `R` significa *relativa*. Tienen la misma sintaxis que los métodos correspondientes sin el `R` pero están en relación con el punto actual. Estos son útiles para dibujar partes similares de una ruta de acceso en un método que se llama varias veces.

Un contorno termina con otra llamada a `MoveTo` o `RMoveTo`, que comienza un nuevo contorno o una llamada a `Close`, que cierra el contorno. El `Close` método automáticamente anexa una línea recta desde el punto actual hasta el primer punto del contorno y marca la ruta de acceso como cerrado, lo que significa que se representará sin los extremos de trazo.

Se ilustra la diferencia entre los contornos abiertas y cerradas en el **dos perfiles de triángulo** página, que usa un `SKPath` objeto con dos perfiles para representar dos triángulos. El primer contorno está abierto y se cierra el segundo. Este es el [ `TwoTriangleContours` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs) clase:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

El primer contorno consta de una llamada a [ `MoveTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.MoveTo/p/System.Single/System.Single/) utilizando las coordenadas X e Y en lugar de un `SKPoint` valor, seguido por tres llamadas a [ `LineTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.LineTo/p/System.Single/System.Single/) para dibujar los tres lados de la triángulo. El segundo perfil tiene sólo dos llamadas a `LineTo` , pero finaliza el contorno con una llamada a [ `Close` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Close()/), que cierra el contorno. La diferencia es importante:

[![](paths-images/twotrianglecontours-small.png "Captura de pantalla de la página de dos perfiles de triángulo triple")](paths-images/twotrianglecontours-large.png#lightbox "Triple captura de pantalla de la página de dos perfiles de triángulo")

Como puede ver, el contorno de la primera es obviamente una serie de tres líneas conectadas, pero no se conecta con el principio final. Las dos líneas se superponen en la parte superior. El segundo contorno obviamente se cierra y se realizaba con uno menos `LineTo` llama porque el `Close` método agrega automáticamente una línea final para cerrar el contorno.

`SKCanvas` solo define una [ `DrawPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawPath/p/SkiaSharp.SKPath/SkiaSharp.SKPaint/) método, que en esta demostración se llama dos veces para rellenar y trazar la ruta de acceso. Se rellenan todos los contornos, incluso aquellos que no están cerrados. Para fines de rellenar trazados no cerrados, una línea recta se supone que existe entre los puntos inicial y final de los perfiles. Si quita la última `LineTo` desde el primer perfil o quitar el `Close` llamada desde el contorno de segundo, cada contorno tendrán solo dos lados, pero que se rellenará como si fuese un triángulo.

`SKPath` define muchos otros métodos y propiedades. Los métodos siguientes agregan contornos todas la ruta de acceso, lo que podría cerrarse o no se cierra en función del método:

- `AddRect`
- [`AddRoundedRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddRoundedRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddCircle`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddCircle/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddOval`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddOval/p/SkiaSharp.SKRect/SkiaSharp.SKPathDirection/)
- [`AddArc`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) Para agregar una curva de la circunferencia de una elipse
- `AddPath` Para agregar otra ruta de acceso a la ruta de acceso actual
- [`AddPathReverse`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddPathReverse/p/SkiaSharp.SKPath/) Para agregar otra ruta de acceso en orden inverso

Tenga en cuenta que un `SKPath` objeto define sólo un objeto geometry &mdash; una serie de puntos y conexiones. Solo cuando un `SKPath` se combina con un `SKPaint` objeto es la ruta de acceso que se representa con un determinado color, ancho del trazo y así sucesivamente. Además, tenga en cuenta que el `SKPaint` objeto pasa a la `DrawPath` método define las características de la ruta de acceso completa. Si desea que se va a dibujar algo que requiere varios colores, debe usar una ruta de acceso independiente para cada color.

Como se define la apariencia del principio y al final de una línea por un extremo del trazo, el aspecto de la conexión entre dos líneas se define mediante un *combinación trazo*. Puede especificar esto estableciendo la [ `StrokeJoin` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeJoin/) propiedad de `SKPaint` a un miembro de la [ `SKStrokeJoin` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeJoin/) enumeración:

- [`Miter`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Miter/) para una combinación puntiaguda
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Round/) para una unión redondeada
- [`Bevel`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Bevel/) para una combinación corten desactivado

El **trazo combinaciones** página muestra estos tres combinaciones con código similar de trazar la **extremos de trazo** página. Se trata de la `PaintSurface` controlador de eventos en el [ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs) clase:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

Este es el programa que se ejecutan en las tres plataformas:

[![](paths-images/strokejoins-small.png "Triple captura de pantalla de la página de trazo une")](paths-images/strokejoins-large.png#lightbox "Triple captura de pantalla de la página se une a trazo")

La unión angular consta de un vértice agudo donde las líneas de conexión. Cuando se unen dos líneas en un ángulo pequeño, la unión angular puede ser bastante larga. Para evitar uniones excesivamente largos, la longitud de la unión angular está limitada por el valor de la [ `StrokeMiter` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeMiter/) propiedad de `SKPaint`. Una unión angular que supera esta longitud se cortan para convertirse en una unión biselada.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
