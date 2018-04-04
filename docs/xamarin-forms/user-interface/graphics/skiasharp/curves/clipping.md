---
title: Con las rutas de acceso y las regiones de recorte
description: Use rutas de acceso a los gráficos de clip a áreas específicas y para crear áreas de
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: charlespetzold
ms.author: chape
ms.date: 06/16/2017
ms.openlocfilehash: 0451653b4ee5c85b9bcf884b6b5609a251cf577c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="clipping-with-paths-and-regions"></a>Con las rutas de acceso y las regiones de recorte

_Use rutas de acceso a los gráficos de clip a áreas específicas y para crear áreas de_

A veces es necesario restringir la representación de gráficos a un área determinada. Esto se conoce como *recorte*. Puede utilizar el recorte para efectos especiales, como esta imagen de una vista a través de un ojo de cerradura de mono:

![](clipping-images/clippingsample.png "Mono a través de un ojo de cerradura")

El *área de recorte* es el área de la pantalla en el que se representan los gráficos. No se representa todo lo que se muestra fuera del área de recorte. El área de recorte normalmente se define mediante una [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) objeto, pero también puede definir un área de recorte usando un [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) objeto. Estos dos tipos de objetos en parecen relacionados en primer lugar porque se puede crear una región de una ruta de acceso. Sin embargo, no se puede crear una ruta de acceso de una región, y son muy diferentes internamente: una ruta de acceso consta de una serie de líneas y curvas, mientras que una región se define mediante una serie de líneas de exploración horizontal.

La imagen anterior se creó por la **mono a través de ojo de cerradura** página. El [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) clase define una ruta de acceso con datos SVG y utiliza el constructor para cargar un mapa de bits de recursos del programa:

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

Aunque la `keyholePath` objeto describe el contorno de un principal, las coordenadas son completamente arbitrarias y reflejen lo que era útil cuando los datos de ruta de acceso se diseñó. Por este motivo, el `PaintSurface` controlador obtiene los límites de esta ruta de acceso y las llamadas `Translate` y `Scale` para mover la ruta de acceso al centro de la pantalla y hágala casi tan grande como la pantalla:


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

Pero no se representa la ruta de acceso. En su lugar, después de las transformaciones, la ruta de acceso se utiliza para establecer un área de recorte con la siguiente instrucción:

```csharp
canvas.ClipPath(keyholePath);
```

El `PaintSurface` controlador, a continuación, restablece las transformaciones con una llamada a `ResetMatrix` y dibuja el mapa de bits para ampliar el alto de la pantalla completa. Este código supone que el mapa de bits es cuadrada, que es de este mapa de bits determinada. El mapa de bits se representa solo dentro del área definida por el trazado de recorte:

[![](clipping-images/monkeythroughkeyhole-small.png "Captura de pantalla triple de la mono a través de la página principal")](clipping-images/monkeythroughkeyhole-large.png#lightbox "Triple captura de pantalla de la mono a través de la página principal")

El trazado de recorte cuando está sujeto a las transformaciones en vigor el `ClipPath` se llama al método, y no a las transformaciones en vigor cuando un objeto de gráfico (por ejemplo, un mapa de bits) se muestra. El trazado de recorte es parte del estado del lienzo que se guarda con la `Save` método y restaurada con el `Restore` método.

## <a name="combining-clipping-paths"></a>Combinar trazados de recorte

En sentido estricto, el área de recorte no es "set" el `ClipPath` método. En su lugar, se combina con el trazado de recorte existente, que se inicia como un rectángulo igual en tamaño a la pantalla. Puede obtener los límites rectangulares del área de recorte mediante el [ `ClipBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipBounds/) propiedad o el [ `ClipDeviceBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipDeviceBounds/) propiedad. El `ClipBounds` propiedad devuelve un `SKRect` valor que refleje cualquier transforma que pueda estar en vigor. El `ClipDeviceBounds` propiedad devuelve un `RectI` valor. Esto es un rectángulo con dimensiones de entero y describe el área de recorte en las dimensiones reales, en píxeles.

Todas las llamadas a `ClipPath` reduce el área de recorte combinando el área de recorte a una nueva área. La sintaxis completa de la [ `ClipPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipPath/p/SkiaSharp.SKPath/SkiaSharp.SKClipOperation/System.Boolean/) método es:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

También hay un [ `ClipRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRect/p/SkiaSharp.SKRect/SkiaSharp.SKClipOperation/System.Boolean/) método que combina el área de recorte en un rectángulo:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

De forma predeterminada, el área de recorte resultante es una intersección del área de recorte existente y la `SKPath` o `SKRect` que se especifica en el `ClipPath` o `ClipRect` método. Esto se muestra en el **Clip forman una intersección de cuatro círculos** página. El `PaintSurface` controlador en el [ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) clase reutiliza el mismo `SKPath` objeto para crear cuatro círculos superpuestos, cada uno de los cuales reduce el área de recorte a través de las llamadas sucesivas a `ClipPath`:

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

[![](clipping-images//fourcircleintersectclip-small.png "Captura de pantalla triple de la página de Clip de la intersección de círculo cuatro")](clipping-images/fourcircleintersectclip-large.png#lightbox "Triple captura de pantalla de la página cuatro Clip de círculo forman una intersección")

El [ `SKClipOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKClipOperation/) enumeración tiene solo dos miembros:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Difference/) Quita la ruta de acceso especificada o un rectángulo de área de recorte existente

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Intersect/) forma una intersección con la ruta de acceso especificada o un rectángulo con el área de recorte existente

Si reemplaza los cuatro `SKClipOperation.Intersect` argumentos en la `FourCircleIntersectClipPage` clase con `SKClipOperation.Difference`, verá lo siguiente:

[![](clipping-images//fourcircledifferenceclip-small.png "Captura de pantalla triple de la página cuatro Clip de círculo forman una intersección con la operación de diferencia")](clipping-images/fourcircledifferenceclip-large.png#lightbox "Triple captura de pantalla de la página cuatro Clip de círculo forman una intersección con la operación de diferencia")

Cuatro círculos superpuestos se eliminaron desde el área de recorte.

El **Clip operaciones** página muestra la diferencia entre estas dos operaciones con solo un par de círculos. El primer círculo de la izquierda se agrega al área de recorte con la operación de ajuste predeterminado de `Intersect`, mientras que el segundo círculo de la derecha se agrega al área de recorte con la operación de recorte indicada por la etiqueta de texto:

[![](clipping-images//clipoperations-small.png "Captura de pantalla triple de la página operaciones de Clip")](clipping-images/clipoperations-large.png#lightbox "Triple captura de pantalla de la página operaciones de Clip")

El [ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) clase define dos `SKPaint` objetos como campos y, a continuación, divide la pantalla de en dos áreas rectangulares. Estas áreas son diferentes dependiendo de si el teléfono está en modo vertical u horizontal. El `DisplayClipOp` clase, a continuación, muestra el texto y llamadas `ClipPath` con las rutas de acceso de dos círculo para ilustrar cada operación de recorte:

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

Al llamar a `DrawPaint` normalmente hace que todo el lienzo que se rellenará con el que `SKPaint` objeto, pero en este caso, el método simplemente pinta dentro del área de recorte.

## <a name="exploring-regions"></a>Explorar las regiones

Si ha explorado la documentación de API de `SKCanvas`, es posible que haya observado las sobrecargas de la `ClipPath` y `ClipRect` métodos que son similares a los métodos descritos anteriormente, pero en su lugar, tienen un parámetro denominado [ `SKRegionOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegionOperation/) en lugar de `SKClipOperation`. `SKRegionOperation` tiene seis miembros, lo que ofrece un poco más flexibilidad en la combinación de rutas de acceso a áreas de recorte de forma:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Difference/)

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Intersect/)

- [`Union`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Union/)

- [`XOR`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.XOR/)

- [`ReverseDifference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.ReverseDifference/)

- [`Replace`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Replace/)

Sin embargo, las sobrecargas de `ClipPath` y `ClipRect` con `SKRegionOperation` parámetros están obsoletos y no se pueden utilizar.

Puede seguir usando el `SKRegionOperation` enumeración pero requiere que se defina un área de recorte en términos de un [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) objeto.

Una recién creada `SKRegion` objeto describe un área vacía. Suele ser la primera llamada en el objeto [ `SetRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetRect/p/SkiaSharp.SKRectI/) para que la región de describir un área rectangular. El parámetro `SetRect` es un una `SKRectI` valor &mdash; el valor de rectángulo con propiedades de entero. A continuación, puede llamar a [ `SetPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetPath/p/SkiaSharp.SKPath/SkiaSharp.SKRegion/) con un `SKPath` objeto. Esto crea una región que es el mismo que el interior de la ruta de acceso, pero se recorta para la región rectangular inicial.

El `SKRegionOperation` enumeración solo entra en juego cuando se llama a uno de los [ `Op` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.Op/p/SkiaSharp.SKRegion/SkiaSharp.SKRegionOperation/) sobrecargas del método, como éste:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

La región que está realizando la `Op` llamada en se combina con la región especificada como un parámetro basado en la `SKRegionOperation` miembro. Cuando finalmente obtiene adecuado para el recorte de una región, puede establecerlo como el área de recorte del lienzo con el [ `ClipRegion` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRegion/p/SkiaSharp.SKRegion/SkiaSharp.SKClipOperation/) método `SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

Captura de pantalla siguiente muestra las áreas de recorte en función de las operaciones de seis región. El círculo izquierda es la región que la `Op` método se llama en, y el círculo derecha es la región que se pasa a la `Op` método:

[![](clipping-images//regionoperations-small.png "Captura de pantalla triple de la página de operaciones de región")](clipping-images/regionoperations-large.png#lightbox "Triple captura de pantalla de la página de operaciones de región")

¿Son estos todas las posibilidades de combinar estos dos círculos? Tenga en cuenta la imagen resultante como una combinación de tres componentes, que por sí solas o se ve en la `Difference`, `Intersect`, y `ReverseDifference` las operaciones. El número total de combinaciones es dos a la tercera potencia u ocho. Los dos que faltan son la región original (que da como resultado de no llamar a `Op` en absoluto) y una región completamente vacía.

Es más difícil para utilizar regiones para recortar porque debe crear primero una ruta de acceso y, a continuación, en una región de dicha ruta de acceso y, a continuación, combinar varias regiones. La estructura general de la **operaciones de región** es muy similar a la página **Clip Operations** pero la [ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) clase divide la pantalla en seis áreas de y Muestra el trabajo adicional necesario para usar regiones para este trabajo:

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

Para comprender el motivo de esta diferencia, resulta útil comprender qué una región es. Si ha pensado en cómo las operaciones de recorte u operaciones de región se puede implementar internamente, probablemente parece muy complicada. Se combinan varias rutas de acceso potencialmente muy complejas y el esquema de la ruta de acceso resultante es probablemente una pesadilla algorítmica.

Pero este trabajo se simplifica considerablemente si cada ruta de acceso se reduce a una serie de líneas de exploración horizontal, como las de la antigua vacío tube televisores. Cada línea de barrido es simplemente una línea horizontal con un punto inicial y un punto final. Por ejemplo, se puede descomponer un círculo con un radio de 10 a 20 líneas de exploración horizontal, cada uno de los cuales se inicia en la parte izquierda del círculo y termina en la parte derecha. Combinar dos círculos con cualquier operación de región pasa a ser muy simple porque es simplemente cuestión de examen de las coordenadas de inicio y finalización de cada par de líneas de exploración correspondiente.

Esto es lo que es una región: una serie de líneas de exploración horizontal que define un área.

Sin embargo, cuando se reduce un área a una serie de examen líneas, estas líneas se basan en una dimensión de píxel determinado de examen. En sentido estricto, la región no es un objeto de gráficos vectoriales. Es más cercano de naturaleza a un mapa de bits monocromático comprimido a una ruta de acceso. Por lo tanto, las regiones se no se pueden escalar o girar sin perder fidelidad y, por esta razón no se transforman cuando se utiliza para las áreas de recorte.

Sin embargo, puede aplicar transformaciones a regiones para fines de dibujo. El **región Paint** programa muestra claramente la naturaleza interna de las regiones. El [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) clase crea un `SKRegion` objeto basado en un `SKPath` de un círculo de radio de 10 unidades. Una transformación, a continuación, expande ese círculo para rellenar la página:

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

El `DrawRegion` llamada rellena la región en color naranja, mientras el `DrawPath` llamada trazos la ruta de acceso original en azul para la comparación:

[![](clipping-images//regionpaint-small.png "Captura de pantalla triple de la página de dibujo de región")](clipping-images/regionpaint-large.png#lightbox "Triple captura de pantalla de la página de dibujo de región")

La región es claramente una serie de coordenadas discretos.

Si no necesita utilizar las transformaciones en relación con las áreas de recorte, puede utilizar regiones para recortar, como el **cuatro – hoja trébol** muestra la página. El [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) clase construye una región compuesta de cuatro regiones circulares, establece esa región compuesta como el área de recorte y, a continuación, se dibuja una serie de 360 líneas rectas comunicados desde el centro de la página:

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

Realmente no parece un trébol de cuatro – hoja, pero es una imagen que en caso contrario, podría ser difícil representar sin recortes:

[![](clipping-images//fourleafclover-small.png "Captura de pantalla triple de la página cuatro – hoja trébol")](clipping-images/fourleafclover-large.png#lightbox "Triple captura de pantalla de la página cuatro – hoja trébol")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
