---
title: La transformación sesga
description: Vea cómo la transformación sesga puede crear objetos gráficos superpuestos en SkiaSharp
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: charlespetzold
ms.author: chape
ms.date: 03/20/2017
ms.openlocfilehash: c9f5f9f20296b1c2443a8addeebd4d12ccaa1ab4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="the-skew-transform"></a>La transformación sesga

_Vea cómo la transformación sesga puede crear objetos gráficos superpuestos en SkiaSharp_

En SkiaSharp, la transformación sesga inclina objetos gráficos, como la sombra en esta imagen:

![](skew-images/skewexample.png "Un ejemplo de sesgado del programa sesgar texto de instantáneas")

El sesgo transforma rectángulos en paralelogramos, pero una elipse sesgada sigue siendo una elipse.

Aunque Xamarin.Forms define las propiedades para la traducción, ampliación y rotaciones, no hay ninguna propiedad correspondiente de Xamarin.Forms para sesgo.

El [ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/System.Single/System.Single/) método `SKCanvas` acepta dos argumentos de sesgo horizontal y vertical de sesgo:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Un segundo [ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/SkiaSharp.SKPoint/) método combina estos argumentos en una sola `SKPoint` valor:

```csharp
public void Skew (SKPoint skew)
```

Sin embargo, no es probable que va a usar cualquiera de estos dos métodos de forma aislada.

El **sesgar experimentar** página permite experimentar con sesgo valores comprendidos entre -10 y 10. Una cadena de texto está situada en la esquina superior izquierda de la página, con valores de sesgo obtenidos de dos `Slider` elementos. Este es el `PaintSurface` controlador en el [ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) clase:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

Valores de la `xSkew` argumento desplazar la parte inferior del texto adecuado para los valores positivos o a la izquierda para los valores negativos. Valores de `ySkew` desplazar a la derecha del texto hacia abajo para los valores positivos o hacia arriba para los valores negativos:

[![](skew-images/skewexperiment-small.png "Captura de pantalla triple de la página de experimento sesgar")](skew-images/skewexperiment-large.png#lightbox "Triple captura de pantalla de la página de experimento sesgar")

Si `xSkew` es el negativo de `ySkew`, el resultado es la rotación, pero también escalar un poco como las ventanas de esta pantalla se indica.

Las fórmulas de transformación son los siguientes:

x' = x + xSkew · y

y' = ySkew · x + y

Por ejemplo, si es un positivo `xSkew` valor, el transformado `x'` valor aumenta a medida que `y` aumenta. Es lo que ocasiona la inclinación.

Si un ancho de píxeles de triángulo 200 y 100 píxeles de alto se sitúa con la esquina superior izquierda en el punto (0, 0) y se representa con un `xSkew` valo 1.5, los resultados de paralelogramo siguientes:

![](skew-images/skeweffect.png "El efecto de la transformación de sesgo en un rectángulo")

Las coordenadas del borde inferior tienen `y` valores de 100, por lo que es desplazan 150 píxeles a la derecha.

Para los valores distintos de cero de `xSkew` o `ySkew`, solo el punto (0, 0) sigue siendo el mismo. Ese punto se puede considerar el centro de sesgado. Si necesita el centro del sesgo para que sea algo más (que suele ser el caso), no hay ningún `Skew` método que proporciona que. Debe combinar explícitamente `Translate` llama con el `Skew` llamar. Para centrar el sesgo en `px` y `py`, realizar las llamadas siguientes:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

Las fórmulas de transformación compuestas son:

x' = x + xSkew · (copiar y:)

y' = ySkew · (x – px) + y

Si `ySkew` es cero, y solo se especifica un valor distinto de cero de `xSkew`, a continuación, `px` valor no se utiliza. El valor es irrelevante y de forma similar para `ySkew` y `py`.

Se podría sienta más cómodo especificar sesgo como un ángulo de inclinación, como el ángulo α en este diagrama:

![](skew-images/skewangleeffect.png "El efecto de la transformación de sesgo en un rectángulo con un ángulo sesgar indicado")

La relación entre el turno de 150 píxeles a 100 píxeles vertical es la tangente de ese ángulo, en este ejemplo 56.3 grados.

El archivo XAML de la **sesgar experimento de ángulo** página es similar a la **ángulo sesgar** página salvo que la `Slider` elementos estar comprendido entre -90 y 90 grados. El [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) archivo de código subyacente se centra el texto en la página y se utiliza `Translate` para establecer un centro de sesgo en el centro de la página. Un valor bajo `SkewDegrees` método en la parte inferior del código convierte ángulos para sesgar valores:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

Tal y como acerca a un ángulo positivos o negativos de 90 grados, acerca de la tangente infinito, pero ángulos hasta aproximadamente 80 grados o lo están para su uso:

[![](skew-images/skewangleexperiment-small.png "Captura de pantalla triple de la página de experimento de ángulo de sesgo")](skew-images/skewangleexperiment-large.png#lightbox "Triple captura de pantalla de la página de sesgo experimento de ángulo")

Un sesgo horizontal negativo pequeño puede imitar oblicuo o cursiva el texto, como el **texto oblicua** muestra la página. El [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) clase muestra cómo hacerlo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

El `TextAlign` propiedad de `SKPaint` está establecido en `Center`. Sin las transformaciones, la `DrawText` llamar a coordenadas de (0, 0) colocaría el texto con el centro horizontal de la línea base en la esquina superior izquierda. El `SkewDegrees` sesga el texto horizontalmente 20 grados con respecto a la línea de base. El `Translate` llamada mueve el centro horizontal de la línea de base del texto en el centro del lienzo:

[![](skew-images/obliquetext-small.png "Captura de pantalla de la página de texto oblicua triple")](skew-images/obliquetext-large.png#lightbox "Triple captura de pantalla de la página de texto oblicua")

El **sesgar sombra texto** página muestra cómo utilizar una combinación de una escala sesga y vertical de 45 grados para realizar una sombra de texto que se inclina fuera el texto. Aquí es la parte pertinente de la `PaintSurface` controlador:

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

La sombra está mostrado primero y, a continuación, el texto:

[![](skew-images/skewshadowtext1-small.png "Captura de pantalla triple de la página sesgar sombra texto")](skew-images/skewshadowtext1-large.png#lightbox "Triple captura de pantalla de la página sesgar texto de instantáneas")

Coordenada vertical se pasa a la `DrawText` método indica la posición del texto con respecto a la línea de base. Que es la misma coordenada vertical utilizada para el centro de sesgado. Esta técnica no funcionará si la cadena de texto contiene caracteres descendentes. Por ejemplo, sustituya la palabra "extraña" para "Sombra" y aquí de resultado:

[![](skew-images/skewshadowtext2-small.png "Captura de pantalla triple de la página sesgar sombra texto con una palabra alternativa con trazos descendentes")](skew-images/skewshadowtext2-large.png#lightbox "Triple captura de pantalla de la página sesgar sombra texto con una palabra alternativa con trazos descendentes")

Todavía se alinean las instantáneas y el texto en la línea de base, pero el efecto solo no es correcto. Para corregir esto necesita obtener los límites de texto:

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

El `Translate` llamadas deben ser ajustado por el alto de los trazos descendentes:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Ahora la sombra se extiende desde la parte inferior de los trazos descendentes:

[![](skew-images/skewshadowtext3-small.png "Captura de pantalla triple de la página de texto de sombra sesgar con ajustes para los trazos descendentes")](skew-images/skewshadowtext3-large.png#lightbox "Triple captura de pantalla de la página de texto de sombra sesgar con ajustes para los trazos descendentes")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
