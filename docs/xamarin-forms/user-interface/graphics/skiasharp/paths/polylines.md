---
title: Polilíneas y ecuaciones paramétricas
description: Este artículo explica cómo para uso SkiaSharp para representar cualquier línea, puede definir con ecuaciones paramétricas y se muestra cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9539a21b7dbc91da63795639610886233ed705be
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245314"
---
# <a name="polylines-and-parametric-equations"></a>Polilíneas y ecuaciones paramétricas

_Usar SkiaSharp para representar cualquier línea que puede definir con ecuaciones paramétricas_

En la parte posterior de esta guía, podrá ver los distintos métodos que `SKPath` define para representar determinados tipos de curvas. Sin embargo, a veces es necesario dibujar un tipo de curva que no es compatible directamente con `SKPath`. En tal caso, puede utilizar una polilínea (una colección de líneas conectadas) para dibujar una curva que se puede definir matemáticamente. Si realiza lo suficientemente pequeño como las líneas y numerosos suficiente, el resultado será similar una curva. Este espiral es realmente 3.600 pequeñas líneas:

![](polylines-images/spiralexample.png "Una espiral")

Generalmente es mejor definir una curva en términos de un par de ecuaciones paramétricas. Estas son las ecuaciones de coordenadas X e Y que dependen de una tercera variable, a veces denominada `t` por vez. Por ejemplo, las siguientes ecuaciones paramétricas definen un círculo con un radio de 1 centrado en el punto (0, 0) para *t* de 0 a 1:

 x = cos(2πt) y = sin(2πt)

 Si desea un radio mayor que 1, simplemente puede multiplicar los valores de seno y coseno por ese radius y si tiene que mover el centro a otra ubicación, agregue esos valores:

 x = xCenter + radius·cos(2πt) y = yCenter + radius·sin(2πt)

Para una elipse con el paralelo ejes horizontal y vertical, intervienen dos radios:

x = xCenter + xRadius·cos(2πt) y = yCenter + yRadius·sin(2πt)

A continuación, puede colocar el código equivalente en SkiaSharp en un bucle que calcula los diversos puntos y agrega los utilizados para una ruta de acceso. El siguiente código SkiaSharp crea un `SKPath` objeto de una elipse rellena la superficie de presentación. El bucle recorre los 360 grados directamente. El centro de es la mitad del ancho y alto de la superficie de presentación y, por lo tanto, son los dos radios:

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

Esto da como resultado una elipse definida por 360 pequeñas líneas. Cuando se represente, aparece suave.

Por supuesto, no es necesario crear una elipse con una polilínea porque `SKPath` incluye un `AddOval` método que lo hace automáticamente. Pero desea dibujar un objeto visual que no sean suministrado por `SKPath`.

El **Archimedean espiral** página tiene código que es similar al código de elipse pero con una diferencia fundamental. Recorre los 360 grados del círculo 10 veces, ajustar continuamente el radio:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

El resultado también se denomina una *espiral aritmético* porque el desplazamiento entre cada bucle es constante:

[![](polylines-images/archimedeanspiral-small.png "Captura de pantalla triple de la página de espiral Archimedean")](polylines-images/archimedeanspiral-large.png#lightbox "Triple captura de pantalla de la página Archimedean espiral")

Tenga en cuenta que la `SKPath` se crea en un `using` bloque. Esto `SKPath` consume más memoria que la `SKPath` objetos en los programas anteriores, lo que sugiere que un `using` bloque es más adecuado para desechar recursos no administrados.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
