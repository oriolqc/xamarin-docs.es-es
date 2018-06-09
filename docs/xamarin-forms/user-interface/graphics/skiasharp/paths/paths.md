---
title: Conceptos básicos de la ruta de acceso en SkiaSharp
description: Este artículo explora el objeto SkiaSharp SKPath para combinar las líneas y curvas conectadas y se muestra cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 2fc492f723a7a009da506d241ddf2e8459908633
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244050"
---
# <a name="path-basics-in-skiasharp"></a>Conceptos básicos de la ruta de acceso en SkiaSharp

_Explorar el objeto SkiaSharp SKPath para combinar las líneas y curvas conectadas_

Una de las características más importantes de la ruta de acceso de gráficos es la capacidad para definir cuándo se deben conectar las varias líneas y al no deben estar conectados. La diferencia puede ser bastante visible, como se muestra en la parte superior de estos dos triángulos:

![](paths-images/connectedlinesexample.png "Dos triángulos que muestra la diferencia entre las líneas conectadas y desconectadas")

Una ruta de acceso de gráficos es encapsulada por el [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) objeto. Una ruta de acceso es una colección de uno o varios *perfiles*. Cada contorno es una colección de *conectado* líneas rectas y curvas conectadas. Contornos no están conectados entre sí, pero pueden superponer visualmente. En ocasiones, un perfil único puede superponer.

Un contorno generalmente se empieza con una llamada al método siguiente de `SKPath`:

- `MoveTo` Para iniciar un nuevo contorno

El argumento para ese método es un único punto, que se puede expresar como un `SKPoint` valor o como independiente X e Y coordina. El `MoveTo` llamada establece un punto al principio de la curva e inicial *punto actual*. Puede llamar a los métodos siguientes para continuar el contorno con una línea o una curva desde el punto actual a un punto especificado en el método, que, a continuación, se convierte en el nuevo punto actual:

- `LineTo` Para agregar una línea recta a la ruta de acceso
- `ArcTo` Para agregar un archivo, que es una línea en la circunferencia de un círculo o elipse
- `CubicTo` Para agregar una curva spline de Bézier cúbica
- `QuadTo` Para agregar una curva spline de Bézier cuadrática
- `ConicTo` Para agregar un spline Bézier cuadrática racional, que puede representar con precisión cónicos secciones (puntos suspensivos, parábolas e hipérbolas)

Ninguna de estas cinco métodos contiene toda la información necesaria para describir la línea o curva. Cada uno de estos cinco métodos funciona junto con el punto actual establecido por la llamada al método inmediatamente anterior. Por ejemplo, el `LineTo` método agrega una línea recta para el contorno basada en el punto actual, por lo que el parámetro `LineTo` es un solo punto.

El `SKPath` clase también define métodos que tienen los mismos nombres que estos seis métodos, pero con un `R` al principio:

- `RMoveTo`
- `RLineTo`
- `RArcTo`
- `RCubicTo`
- `RQuadTo`
- `RConicTo`

El `R` es el acrónimo *relativa*. Tienen la misma sintaxis que los métodos correspondientes sin el `R` pero están en relación con el punto actual. Estos son útiles para dibujar partes similares de una ruta de acceso en un método que se llama varias veces.

Un contorno termina con otra llamada a `MoveTo` o `RMoveTo`, que inicia un nuevo contorno o una llamada a `Close`, que cierra el contorno. El `Close` método automáticamente anexa una línea recta desde el punto actual hasta el primer punto del contorno y marca la ruta de acceso como cerrado, lo que significa que se representarán sin los extremos de trazo.

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

El primer contorno consta de una llamada a [ `MoveTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.MoveTo/p/System.Single/System.Single/) mediante las coordenadas X e Y en lugar de un `SKPoint` valor, seguido de tres llamadas a [ `LineTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.LineTo/p/System.Single/System.Single/) para dibujar los tres lados de la triángulo. El segundo perfil tiene solo dos llamadas a `LineTo` pero que termine el contorno con una llamada a [ `Close` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Close()/), que cierra el contorno. La diferencia es importante:

[![](paths-images/twotrianglecontours-small.png "Captura de pantalla triple de la página de dos perfiles de triángulo")](paths-images/twotrianglecontours-large.png#lightbox "Triple captura de pantalla de la página de dos perfiles de triángulo")

Como puede ver, obviamente, el primer contorno es una serie de tres líneas conectadas, pero el extremo no se conecta con el principio. Las dos líneas se superponen en la parte superior. El segundo contorno obviamente se cierra y se logró con uno menos `LineTo` llama porque el `Close` método agrega automáticamente una línea final para cerrar el contorno.

`SKCanvas` solo se define una [ `DrawPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawPath/p/SkiaSharp.SKPath/SkiaSharp.SKPaint/) método, que en esta demostración se llama dos veces para rellenar y trazar la ruta de acceso. Se rellenan todos los contornos, incluso aquellos que no están cerrados. Para fines de rellenar las rutas de acceso sin cerrar, una línea recta se supone que existe entre los puntos inicial y final de los perfiles. Si quita la última `LineTo` desde el primer contorno o quite el `Close` llamada desde el contorno de segundo, cada perfil tendrán solo dos lados pero que se rellenará como si fuera un triángulo.

`SKPath` define muchos otros métodos y propiedades. Los métodos siguientes agregan contornos todos a la ruta de acceso, que podría ser cerrado o no se cerró en función del método:

- `AddRect`
- [`AddRoundedRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddRoundedRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddCircle`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddCircle/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddOval`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddOval/p/SkiaSharp.SKRect/SkiaSharp.SKPathDirection/)
- [`AddArc`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) Para agregar una curva en la circunferencia de una elipse
- `AddPath` Para agregar otra ruta de acceso a la ruta de acceso actual
- [`AddPathReverse`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddPathReverse/p/SkiaSharp.SKPath/) Para agregar otra ruta de acceso en orden inverso

Tenga en cuenta que un `SKPath` objeto define solo un objeto geometry &mdash; una serie de puntos y conexiones. Solo cuando un `SKPath` se combina con un `SKPaint` objeto es la ruta de acceso que se representan con un color determinado, el ancho del trazo y así sucesivamente. Además, tenga en cuenta que la `SKPaint` objeto pasa a la `DrawPath` método define las características de la ruta de acceso completa. Si desea dibujar algo que requieren varios colores, debe utilizar una ruta de acceso independiente para cada color.

Igual que un extremo del trazo se define la apariencia de la inicial y final de una línea, la apariencia de la conexión entre dos líneas se define mediante una *combinación trazo*. Puede especificar esto estableciendo la [ `StrokeJoin` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeJoin/) propiedad de `SKPaint` a un miembro de la [ `SKStrokeJoin` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeJoin/) enumeración:

- [`Miter`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Miter/) para una combinación puntiaguda
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Round/) para una unión redondeada
- [`Bevel`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Bevel/) para una combinación reducido desactivado

El **combinaciones de trazo** de página muestra estos tres trazar las combinaciones con código similar del **extremos de trazos** página. Se trata de la `PaintSurface` controlador de eventos en el [ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs) clase:

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

Este es el programa que se ejecuta en las tres plataformas:

[![](paths-images/strokejoins-small.png "Captura de pantalla triple de la página se une a trazo")](paths-images/strokejoins-large.png#lightbox "Triple captura de pantalla de la página se une a trazo")

La combinación de ángulo consta de un vértice agudo que conectan las líneas. Cuando dos líneas se unen en un ángulo pequeño, la combinación de ángulo puede ser bastante larga. Para evitar uniones angulares excesivamente largos, la longitud de la combinación de ángulo está limitada por el valor de la [ `StrokeMiter` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeMiter/) propiedad de `SKPaint`. Una unión en ángulo que supera esta longitud se cortan se convierta en una combinación del bisel.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
