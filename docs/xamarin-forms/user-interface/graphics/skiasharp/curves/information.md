---
title: Enumeración e información de ruta de acceso
description: Este artículo explica cómo obtener información acerca de las rutas de acceso de SkiaSharp y enumerar el contenido y se muestra cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/12/2017
ms.openlocfilehash: 53d1fce20a0e3bc75ba34ab84b2549211567e222
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243797"
---
# <a name="path-information-and-enumeration"></a>Enumeración e información de ruta de acceso

_Obtenga información sobre las rutas de acceso y enumerar el contenido_

El [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) clase define varias propiedades y métodos que permiten obtener información acerca de la ruta de acceso. El [ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/) y [ `TightBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.TightBounds/) propiedades (y métodos relacionados) obtienen las dimensiones de métrica de una ruta de acceso. El [ `Contains` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Contains/p/System.Single/System.Single/) método le permite determinar si un punto determinado está dentro de una ruta de acceso.

A veces resulta útil para determinar la longitud total de todas las líneas y curvas que forman una ruta de acceso. Esto no es una tarea de forma algorítmica simple, por lo que toda una clase denominada [ `PathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/) se dedica a él.

También a veces resulta útil obtener todas las operaciones y los puntos que forman una ruta de acceso del dibujo. En primer lugar, esta función puede parecer innecesaria: si el programa ha creado la ruta de acceso, el programa ya conoce el contenido. Sin embargo, ha visto que las rutas de acceso también se pueden crear por [efectos de ruta de acceso](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) y convirtiendo [cadenas de texto en trazados](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md). También puede obtener todas las operaciones y los puntos que componen estas rutas de acceso de dibujo. Una posibilidad es aplicar una transformación algorítmica a todos los puntos. Esto permite técnicas como el ajuste de texto alrededor de un hemisferio:

![](information-images/pathenumerationsample.png "Texto ajustado en un hemisferio")

## <a name="getting-the-path-length"></a>Obtener la longitud de ruta de acceso

En el artículo [ **las rutas de acceso y el texto** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) ha visto cómo utilizar la [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/) método para dibujar una cadena de texto cuyo previsto sigue el curso de una ruta de acceso. Pero ¿qué ocurre si desea cambiar el tamaño del texto para que se ajuste exactamente a la ruta de acceso? Para dibujar texto alrededor de un círculo, es fácil porque es fácil calcular la circunferencia de un círculo. Pero la circunferencia de una elipse o la longitud de una curva de Bézier no es tan sencilla.

El [ `SKPathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/) puede ayudar la clase. El [constructor](https://developer.xamarin.com/api/constructor/SkiaSharp.SKPathMeasure.SKPathMeasure/p/SkiaSharp.SKPath/System.Boolean/System.Single/) acepta un `SKPath` argumento y el [ `Length` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPathMeasure.Length/) propiedad revela su longitud.

Esto se muestra en el **longitud de ruta de acceso** ejemplo, que se basa en el **curva de Bézier** página. El [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) archivo deriva de `InteractivePage` e incluye una interfaz táctil:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

El [ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) archivo de código subyacente permite mover cuatro puntos táctiles para definir los dos puntos finales y puntos de control de una curva Bézier cúbica. Tres campos definen una cadena de texto, un `SKPaint` objeto y un ancho calculado del texto:

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

El `baseTextWidth` campo es el ancho del texto en función de un `TextSize` de 10.

El `PaintSurface` controlador dibuja la curva de Bézier y, a continuación, cambia el tamaño del texto para ajustarse a lo largo de su longitud total:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

El `Length` propiedad creado recientemente `SKPathMeasure` objeto obtiene la longitud de la ruta de acceso. Esto se divide por el `baseTextWidth` valor (que es el ancho del texto en función de un tamaño de texto de 10) y, a continuación, se multiplica por el tamaño del texto base de 10. El resultado es un nuevo tamaño del texto para mostrar el texto a lo largo de esa ruta de acceso:

[![](information-images/pathlength-small.png "Captura de pantalla triple de la página de longitud de ruta de acceso")](information-images/pathlength-large.png#lightbox "Triple captura de pantalla de la página de longitud de ruta de acceso")

Cuando la curva de Bézier obtiene mayor o menor, puede ver que el tamaño del texto cambia.

## <a name="traversing-the-path"></a>Atravesar la ruta de acceso

`SKPathMeasure` puede hacer algo más que simplemente medida la longitud de la ruta de acceso. Para cualquier valor entre cero y la longitud de la ruta de acceso, un `SKPathMeasure` objeto puede obtener la posición en la ruta de acceso y la tangente a la curva de ruta de acceso en ese momento. La tangente está disponible como un vector en forma de un `SKPoint` objeto o como un giro encapsulada en un `SKMatrix` objeto. Estos son los métodos de `SKPathMeasure` que obtener esta información de maneras distintas y flexible:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

El [ `SKPathMeasureMatrixFlags` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasureMatrixFlags/) son:

- [`GetPosition`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPosition/)
- [`GetTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)
- [`GetPositionAndTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)

El **monociclo mitad canalización** página anima un dibujo en un monociclo que parezca invalidar y hacia atrás a lo largo de una curva Bézier cúbica:

[![](information-images/unicyclehalfpipe-small.png "Captura de pantalla triple de la página de canalización mitad monociclo")](information-images/unicyclehalfpipe-large.png#lightbox "Triple captura de pantalla de la página de canalización monociclo mitad")

El `SKPaint` objeto usado para trazado la canalización de medio y el monociclo se define como un campo en el [ `UnicycleHalfPipePage` ]() clase. También define es el `SKPath` objeto para el monociclo:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

La clase contiene las invalidaciones estándares de la `OnAppearing` y `OnDisappearing` métodos para ver la animación. El `PaintSurface` controlador crea la ruta de acceso de la canalización de medio y, a continuación, lo muestra. Un `SKPathMeasure` objeto, a continuación, se crea basándose en esta ruta de acceso:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

El `PaintSurface` controlador calcula un valor de `t` que va de 0 a 1 cada cinco segundos. A continuación, utiliza el `Math.Cos` función para convertir todo esto en un valor de `t` que va de 0 a 1 y volver a 0, donde 0 corresponde a la monociclo al principio de la parte superior izquierda, mientras que 1 se corresponde con el monociclo en la esquina superior derecha. La función de coseno hace que la velocidad sea más lenta en la parte superior de la canalización y más rápido en la parte inferior.

Tenga en cuenta que el valor de `t` debe multiplicado por la longitud de ruta de acceso para el primer argumento para `GetMatrix`. La matriz, a continuación, se aplica a la `SKCanvas` objeto para dibujar la ruta de acceso de monociclo.

## <a name="enumerating-the-path"></a>Enumerar la ruta de acceso

Dos incrustados clases de `SKPath` permiten enumerar el contenido de la ruta de acceso. Estas clases son [ `SKPath.Iterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+Iterator/) y [ `SKPath.RawIterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+RawIterator/). Las dos clases son muy similares, pero `SKPath.Iterator` pueden eliminar elementos en la ruta de acceso con una longitud de cero, o cerca de su longitud es nula. El `RawIterator` se utiliza en el ejemplo siguiente.

Puede obtener un objeto de tipo `SKPath.RawIterator` mediante una llamada a la [ `CreateRawIterator` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CreateRawIterator()/) método `SKPath`. Enumerar la ruta de acceso se logra llamando repetidamente la [ `Next` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.Next/p/SkiaSharp.SKPoint[]/) método. Pasa una matriz de cuatro `SKPoint` valores:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

El `Next` método devuelve un miembro de la [ `SKPathVerb` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathVerb/) enumeración. Estos valores indican el comando de dibujo particular en la ruta de acceso. El número de puntos válidos que se insertan en la matriz depende de este verbo:

- [`Move`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Move/) con un solo punto
- [`Line`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Line/) con dos puntos
- [`Cubic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Cubic/) con cuatro puntos
- [`Quad`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Quad/) con tres puntos
- [`Conic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Conic/) con tres puntos (y también llama a la [ `ConicWeight` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.ConicWeight/) método para el peso)
- [`Close`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Close/) con un punto
- [`Done`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Done/)

El `Done` verbo indica que la enumeración ha finalizado.

Tenga en cuenta que no hay ningún `Arc` verbos. Esto indica que todos los arcos se convierten en curvas de Bézier cuando se agrega a la ruta de acceso.

Parte de la información en el `SKPoint` matriz es redundante. Por ejemplo, si un `Move` verbo es seguido por un `Line` verbo y, a continuación, el primero de los dos puntos que acompañan a la `Line` es el mismo que el `Move` punto. En la práctica, esta redundancia es muy útil. Al obtener una `Cubic` verbo, viene acompañado por todos los cuatro puntos que definen la curva de Bézier cúbica. No es necesario conservar la posición actual establecida por el verbo anterior.

Sin embargo, es el verbo problemático, `Close`. Este comando dibuja una línea recta desde la posición actual hasta el principio del contorno establecido anteriormente la `Move` comando. Idealmente, el `Close` verbo debe proporcionar esos dos puntos en lugar de un solo punto. ¿Qué es lo que es peor que es el punto que acompaña la `Close` verbo siempre es (0, 0). Esto significa que al enumerar a través de una ruta de acceso, probablemente necesitará conservar la `Move` punto y la posición actual.

El método estático [ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) clase contiene varios métodos que convierten los tres tipos de curvas de Bézier en una serie de líneas rectas pequeños que se aproxima a la curva. (Las fórmulas paramétricas se presentaron en el artículo [ **tipos tres de curvas de Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md).) El `Interpolate` método desglosa una línea recta en varias líneas cortas que son solo una unidad de longitud:

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

Todos estos métodos se hace referencia desde el método de extensión `CloneWithTransform` se muestra a continuación. Este método clona una ruta de acceso enumerando los comandos de la ruta de acceso y crear una nueva ruta de acceso en función de los datos. Sin embargo, la nueva ruta de acceso consta solo de `MoveTo` y `LineTo` llamadas. Todas las líneas rectas y curvas se reducen a una serie de líneas pequeños.

Al llamar a `CloneWithTransform`, se pasa al método un `Func<SKPoint, SKPoint>`, que es una función con un `SKPaint` parámetro que devuelve un `SKPoint` valor. Esta función se invoca para que cada punto de aplicar una transformación personalizada de algoritmo:

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

Dado que la ruta de acceso clonado se reduce a líneas rectas pequeños, la función de transformación tiene la capacidad de convertir líneas rectas en curvas.

Observe que el método conserva el primer punto de cada contorno en la variable denominada `firstPoint` y el dibujo de la posición actual después de cada comando en la variable `lastPoint`. Esto es necesario construir el cierre final de línea cuando un `Close` se encuentra el verbo.

El **GlobularText** ejemplo usa este método de extensión para ajustar el texto alrededor de un hemisferio aparentemente un efecto 3D:

[![](information-images/globulartext-small.png "Captura de pantalla triple de la página de texto Globular")](information-images/globulartext-large.png#lightbox "Triple captura de pantalla de la página de texto Globular")

El [ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) constructor de clase realiza esta transformación. Crea un `SKPaint` objeto para el texto y, a continuación, obtiene un `SKPath` objeto desde el `GetTextPath` método. Se trata de la ruta de acceso que se pasan a la `CloneWithTransform` método de extensión junto con una función de transformación:

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

La función de transformación calcula primero dos valores denominados `longitude` y `latitude` comprendidas entre – π/2 en la parte superior e izquierda del texto, o a π/2 a la derecha e inferior del texto. El intervalo de estos valores no es satisfactorio visualmente, por lo que se reducen al multiplicar por 0,75. (Pruebe el código sin los ajustes. El texto se convierte en demasiado oscuros en el norte y sur polos y demasiado fino en los lados.) Estas coordenadas esféricas tridimensionales se convierten a bidimensional `x` y `y` coordenadas mediante fórmulas estándares.

La nueva ruta de acceso se almacena como un campo. El `PaintSurface` controlador, a continuación, simplemente necesita para centrarse y escalar la ruta de acceso para mostrarla en la pantalla:

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

Se trata de una técnica muy versátil. Si la matriz de los efectos de la ruta de acceso se describe en el [ **efectos de ruta de acceso** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) artículo bastante no incluir algo que creemos que debe incluirse, se trata de una manera de rellenar los huecos.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
