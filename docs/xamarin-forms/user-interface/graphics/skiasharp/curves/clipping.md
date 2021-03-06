---
title: Recorte con trazados y regiones
description: En este artículo se explica cómo usar rutas de acceso de SkiaSharp a gráficos de clip a áreas específicas y para crear regiones y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 4f8b6b7ea0db8d46886c3391f1aef3ba20a5be44
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086055"
---
# <a name="clipping-with-paths-and-regions"></a>Recorte con trazados y regiones

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Usar rutas de acceso a los gráficos de clip a áreas específicas y para crear regiones_

A veces es necesario restringir la representación de gráficos a un área determinada. Esto se conoce como *recorte*. Puede utilizar el recorte de efectos especiales, como esta imagen de un objeto monkey ven a través de un principal:

![](clipping-images/clippingsample.png "Monkey a través de un principal")

El *área recorte* es el área de la pantalla en la que se representan los gráficos. No se representa todo lo que se muestra fuera del área de recorte. El área de recorte normalmente se define mediante un rectángulo o un [ `SKPath` ](xref:SkiaSharp.SKPath) objeto, pero también puede definir un área de recorte mediante un [ `SKRegion` ](xref:SkiaSharp.SKRegion) objeto. Estos dos tipos de objetos en primer lugar parecen estar relacionadas con porque se puede crear una región de una ruta de acceso. Sin embargo, no se puede crear una ruta de acceso de una región, y son muy diferentes de internamente: Una ruta de acceso consta de una serie de líneas y curvas, mientras que una región se define mediante una serie de líneas de exploración horizontal.

La imagen anterior se creó mediante la **Monkey a través de ojo de cerradura** página. El [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) clase define una ruta de acceso utilizando los datos SVG y utiliza el constructor para cargar un mapa de bits de recursos del programa:

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

Aunque la `keyholePath` objeto describe el contorno de un principal, las coordenadas son completamente arbitrarias y reflejan lo que era útil cuando los datos de ruta de acceso fue creados. Por este motivo, el `PaintSurface` controlador obtiene los límites de esta ruta de acceso y las llamadas `Translate` y `Scale` para mover la ruta de acceso al centro de la pantalla y convertirlo en casi tan altos como la pantalla:


```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

Pero no se representa la ruta de acceso. En su lugar, las siguientes las transformaciones, la ruta de acceso se usa para establecer un área de recorte con esta instrucción:

```csharp
canvas.ClipPath(keyholePath);
```

El `PaintSurface` controlador, a continuación, restablece las transformaciones con una llamada a `ResetMatrix` y dibuja el mapa de bits para ampliar el alto de la pantalla completa. Este código supone que el mapa de bits es cuadrada, que es este mapa de bits determinado. El mapa de bits se representa solo dentro del área definido por el trazado de recorte:

[![](clipping-images/monkeythroughkeyhole-small.png "Captura de pantalla triple del objeto Monkey a través de la página principal")](clipping-images/monkeythroughkeyhole-large.png#lightbox "Triple captura de pantalla de objeto Monkey a través de la página principal")

El trazado de recorte cuando está sujeto a las transformaciones en vigor el `ClipPath` se llama al método, y no a las transformaciones en vigor cuando un objeto gráfico (por ejemplo, un mapa de bits) se muestra. El trazado de recorte es parte del estado del lienzo que se guarda con el `Save` método y restaurada con el `Restore` método.

## <a name="combining-clipping-paths"></a>Combinar trazados de recorte

En realidad, el área de recorte no es "set" el `ClipPath` método. En su lugar, se combina con el trazado de recorte existente, que comienza como un tamaño igual al lienzo del rectángulo. Puede obtener los límites rectangulares del área recorte mediante la [ `ClipBounds` ](xref:SkiaSharp.SKCanvas.ClipBounds) propiedad o el [ `ClipDeviceBounds` ](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) propiedad. El `ClipBounds` propiedad devuelve un `SKRect` valor que refleja cualquier transforma que pueda estar en vigor. El `ClipDeviceBounds` propiedad devuelve un `RectI` valor. Esto es un rectángulo con las dimensiones de entero y describe el área de recorte en las dimensiones de píxeles reales.

Todas las llamadas a `ClipPath` reduce el área de recorte combinando el área de recorte con una nueva área. La sintaxis completa de la [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) método es:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

También hay un [ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) método que combina el área de recorte con un rectángulo:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

De forma predeterminada, el área de recorte resultante es una intersección del área recorte existente y la `SKPath` o `SKRect` que se especifica en el `ClipPath` o `ClipRect` método. Esto se muestra en el **Clip forman una intersección de cuatro círculos** página. El `PaintSurface` controlador en el [ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) clase reutiliza el mismo `SKPath` objeto para crear cuatro círculos superpuestos, cada uno de los cuales reduce el área de recorte mediante llamadas sucesivas a `ClipPath`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

Lo que queda es la intersección de estos cuatro círculos:

[![](clipping-images//fourcircleintersectclip-small.png "Captura de pantalla de la página cuatro Clip de círculo forman una intersección con triple")](clipping-images/fourcircleintersectclip-large.png#lightbox "Triple captura de pantalla de la página cuatro Clip de círculo forman una intersección")

El [ `SKClipOperation` ](xref:SkiaSharp.SKClipOperation) enumeración tiene solo dos miembros:

- `Difference` Quita la ruta de acceso especificada o un rectángulo de área de recorte existente

- `Intersect` forma una intersección con la ruta de acceso especificada o un rectángulo con área de recorte existente

Si reemplaza los cuatro `SKClipOperation.Intersect` argumentos en el `FourCircleIntersectClipPage` clase con `SKClipOperation.Difference`, verá lo siguiente:

[![](clipping-images//fourcircledifferenceclip-small.png "Captura de pantalla de la página cuatro Clip de círculo forman una intersección con la operación de diferencia triple")](clipping-images/fourcircledifferenceclip-large.png#lightbox "Triple captura de pantalla de la página cuatro Clip de círculo forman una intersección con la operación de diferencia")

Se quitaron cuatro círculos superpuestos en el área de recorte.

El **Clip operaciones** página muestra la diferencia entre estas dos operaciones con sólo un par de círculos. El primer círculo de la izquierda se agrega al área de recorte con la operación de ajuste predeterminado de `Intersect`, mientras que el segundo círculo de la derecha se agrega al área de recorte con la operación de recorte indicada por la etiqueta de texto:

[![](clipping-images//clipoperations-small.png "Captura de pantalla triple de la página operaciones de Clip")](clipping-images/clipoperations-large.png#lightbox "Triple captura de pantalla de la página operaciones de Clip")

El [ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) clase define dos `SKPaint` objetos como campos y, a continuación, divide la pantalla en dos áreas rectangulares. Estas áreas son diferentes dependiendo de si el teléfono está en modo vertical u horizontal. El `DisplayClipOp` clase, a continuación, muestra el texto y llamadas `ClipPath` con las rutas de acceso de dos círculo para ilustrar cada operación de recorte:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

Una llamada a `DrawPaint` normalmente hace que todo el lienzo para rellenarse con los que `SKPaint` objeto, pero en este caso, el método simplemente pinta dentro del área de recorte.

## <a name="exploring-regions"></a>Exploración de las regiones

También puede definir un área de recorte en términos de un [ `SKRegion` ](xref:SkiaSharp.SKRegion) objeto.

Una recién creada `SKRegion` objeto que describe un área vacía. Normalmente es la primera llamada en el objeto [ `SetRect` ](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) para que la región describe un área rectangular. El parámetro `SetRect` es un `SKRectI` valor &mdash; un rectángulo entero coordina porque especifica el rectángulo de píxeles. A continuación, puede llamar a [ `SetPath` ](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) con un `SKPath` objeto. Esto crea una región que es el mismo que el interior de la ruta de acceso, pero se recorta según la región rectangular inicial.

La región también se puede modificar mediante una llamada a uno de los [ `Op` ](xref:SkiaSharp.SKRegion.Op*) sobrecargas del método, como esta:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

El [ `SKRegionOperation` ](xref:SkiaSharp.SKRegionOperation) es similar a la enumeración `SKClipOperation` pero tiene más miembros:

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

La región que se va a realizar la `Op` llamada en se combina con la región especificada como un parámetro en función de la `SKRegionOperation` miembro. Cuando finalmente se adecuado para el recorte de una región, que puede establecer como el área de recorte del lienzo mediante la [ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) método `SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

Captura de pantalla siguiente muestra las áreas de recorte basadas en las operaciones de seis región. El círculo de la izquierda es la región que el `Op` se llama al método en, y el círculo de la derecha es la región que se pasa a la `Op` método:

[![](clipping-images//regionoperations-small.png "Captura de pantalla de la página de operaciones de región triple")](clipping-images/regionoperations-large.png#lightbox "Triple captura de pantalla de la página de operaciones de región")

¿Son estos todas las posibilidades de combinar estas dos círculos? Observe la imagen resultante como una combinación de tres componentes, que por sí solos se ve en el `Difference`, `Intersect`, y `ReverseDifference` operaciones. El número total de combinaciones es dos a la tercera potencia, o también ocho. Los dos que faltan son la región original (que resulta de llamar no `Op` en absoluto) y una región completamente vacía.

Es más difícil para usar regiones de recorte, debe crear primero una ruta de acceso y, a continuación, en una región de dicha ruta de acceso y, a continuación, combinar varias regiones. La estructura general de la **operaciones de región** es muy similar a la página **Clip operaciones** pero la [ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) clase divide la pantalla de en seis áreas y Muestra el trabajo adicional necesario para usar las regiones para este trabajo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

Esta es una gran diferencia entre el `ClipPath` método y el `ClipRegion` método:

> [!IMPORTANT]
> A diferencia de la `ClipPath` método, el `ClipRegion` método no se ve afectado por las transformaciones.

Para comprender el motivo de esta diferencia, es útil comprender qué una región está. Si ha pensado en cómo las operaciones de clip o las operaciones de región podrían implementarse internamente, probablemente parece muy complicado. Se combinan varias rutas de acceso potencialmente muy complejos y el contorno de la ruta de acceso resultante es probablemente una pesadilla algorítmica.

Este trabajo se ha simplificado considerablemente si cada ruta de acceso se reduce a una serie de líneas de exploración horizontal, como las de tubos de vacío anticuado televisores. Cada línea de barrido es simplemente una línea horizontal con un punto inicial y un punto de conexión. Por ejemplo, un círculo con un radio de 10 píxeles se puede descomponer en 20 líneas de exploración horizontal, cada uno de los cuales comienza en la parte izquierda del círculo y termina en la parte derecha. Combinar dos círculos con cualquier operación de región pasa a ser muy sencillo porque es simplemente una cuestión de examen de las coordenadas de inicio y finalización de cada par de líneas de exploración correspondiente.

Esto es lo que es una región: Una serie de líneas horizontales que definen un área.

Sin embargo, cuando un área se reduce a una serie de análisis de las líneas, estas líneas se basan en una dimensión de píxel concreto de examen. En realidad, la región no es un objeto de gráficos vectoriales. Está más cerca de naturaleza un mapa de bits monocromático comprimido a una ruta de acceso. Por lo tanto, las regiones no se pueden escalar o girar sin perder fidelidad y por ese motivo no se transforman cuando se usa para las áreas de recorte.

Sin embargo, puede aplicar transformaciones a regiones para fines de dibujo. El **región Paint** programa demuestra claramente la naturaleza interna de regiones. El [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) clase crea un `SKRegion` objeto según un `SKPath` de un círculo de radio de 10 unidades. Una transformación, a continuación, expande ese círculo para rellenar la página:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

El `DrawRegion` llamada rellena la región en color naranja, mientras que el `DrawPath` llamada trazos de la ruta de acceso original en azul para la comparación:

[![](clipping-images//regionpaint-small.png "Captura de pantalla triple de la página de dibujo de la región")](clipping-images/regionpaint-large.png#lightbox "Triple captura de pantalla de la página de dibujo de región")

La región es claramente una serie de coordenadas discretas.

Si no tiene que usar transformaciones en relación con las áreas de recorte, puede usar las regiones de recorte, como el **trébol de cuatro –** muestra la página. El [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) clase construye una región compuesta de cuatro regiones circulares, establece esa región compuesta como el área de recorte y, a continuación, dibuja una serie de líneas rectas 360 procedentes del centro de la página:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

Realmente no parece un trébol de cuatro – hoja, pero es una imagen que serían difícil de representación sin recortes:

[![](clipping-images//fourleafclover-small.png "Captura de pantalla triple de la página trébol de cuatro –")](clipping-images/fourleafclover-large.png#lightbox "Triple captura de pantalla de la página trébol de cuatro –")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
