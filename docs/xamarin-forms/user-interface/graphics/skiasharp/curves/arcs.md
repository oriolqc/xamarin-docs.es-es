---
title: Tres maneras para dibujar un arco
description: En este artículo se explica cómo usar SkiaSharp para definir arcos de tres maneras diferentes y se muestra cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: charlespetzold
ms.author: chape
ms.date: 05/10/2017
ms.openlocfilehash: 9e0ed04543436ec7a83d13fa6a56637fc7916338
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244157"
---
# <a name="three-ways-to-draw-an-arc"></a>Tres maneras para dibujar un arco

_Obtenga información sobre cómo usar SkiaSharp para definir arcos de tres maneras diferentes_

Un arco es una curva en la circunferencia de una elipse, por ejemplo, las partes redondeadas de este inicio de sesión de infinito:

![](arcs-images/arcsample.png "Inicio de sesión de infinito")

A pesar de la sencillez de esa definición, no hay ninguna manera de definir una función de dibujo del arco que satisface todas las necesidades, y por lo tanto, no hay consenso entre los sistemas de gráficos de la mejor manera de dibujar un arco. Por este motivo, la `SKPath` clase no se restringe a solo un enfoque.

`SKPath` define un `AddArc` método, cinco diferentes `ArcTo` métodos y relativo de dos `RArcTo` métodos. Estos métodos se dividen en tres categorías, que representa tres enfoques muy diferentes para especificar un archivo. Que utilice depende de la información disponible para definir el arco y cómo se ajusta este arco con los otros gráficos que se va a dibujar.

## <a name="the-angle-arc"></a>El ángulo de arco

El enfoque de arco de ángulo para dibujar arcos requiere que se especifique un rectángulo que delimita una elipse. El arco de la circunferencia de la elipse se indica mediante ángulos desde el centro de la elipse que realiza el principio del arco y su longitud. Dos métodos diferentes dibujan arcos de ángulo. Estos son los [ `AddArc` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) método y la [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKRect/System.Single/System.Single/System.Boolean/) método:

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Estos métodos son idénticos a lo Android [ `AddArc` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.AddArc/p/Android.Graphics.RectF/System.Single/System.Single/) y [ `ArcTo` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.ArcTo/p/Android.Graphics.RectF/System.Single/System.Single/System.Boolean/) métodos. El archivo iOS [ `AddArc` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArc/p/System.Boolean/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/) método es similar pero está restringido a arcos en la circunferencia de un círculo en lugar de generalizar a una elipse.

Ambos métodos comienzan con un `SKRect` valor que define la ubicación y el tamaño de una elipse:

![](arcs-images/anglearcoval.png "La elipse que se inicia un arco de ángulo")

El arco es una parte de la circunferencia de la elipse.

El `startAngle` argumento es un ángulo en grados en relación con una línea horizontal que se dibuja desde el centro de la elipse a la derecha hacia la derecha. El `sweepAngle` argumento es relativa a la `startAngle`. Estos son `startAngle` y `sweepAngle` valores de grados de 60 y 100, respectivamente:

![](arcs-images/anglearcangles.png "Los ángulos que definen un arco de ángulo")

El arco empieza por el ángulo inicial. Su longitud se rige por el ángulo de barrido:

![](arcs-images/anglearchighlight.png "El arco ángulo resaltado")

La curva que se agrega a la ruta de acceso con el `AddArc` o `ArcTo` método es simplemente que parte de la circunferencia de la elipse, se muestra en rojo:

![](arcs-images/anglearc.png "El arco ángulo por sí mismo")

El `startAngle` o `sweepAngle` los argumentos pueden ser negativos: el arco es hacia la derecha para los valores positivos de `sweepAngle` y hacia la izquierda para los valores negativos.

Sin embargo, `AddArc` does *no* definir un contorno cerrado. Si se llama a `LineTo` después `AddArc`, se dibuja una línea desde el final del arco hasta el punto en el `LineTo` método y el mismo puede decirse de `ArcTo`.

`AddArc` inicia un nuevo contorno automáticamente y es funcionalmente equivalente a una llamada a `ArcTo` con un argumento final de `true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

Que se llama el último argumento `forceMoveTo`, y esto provoca que eficazmente un `MoveTo` llamar al principio del arco. Inicia un nuevo contorno. Que no es así con un último argumento del `false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Esta versión de `ArcTo` dibuja una línea desde la posición actual hasta el principio del arco. Esto significa que el arco puede estar en algún lugar en medio de un contorno más grande.

El **ángulo arco** página le permite usar dos controles deslizantes para especificar el inicio y ángulos de barrido. El archivo XAML crea una instancia de dos `Slider` elementos y un `SKCanvasView`. El `PaintCanvas` controlador en el [ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) archivo dibuja el óvalo y el arco utilizando dos `SKPaint` objetos que se definan como campos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
    float startAngle = (float)startAngleSlider.Value;
    float sweepAngle = (float)sweepAngleSlider.Value;

    canvas.DrawOval(rect, outlinePaint);

    using (SKPath path = new SKPath())
    {
        path.AddArc(rect, startAngle, sweepAngle);
        canvas.DrawPath(path, arcPaint);
    }
}
```

Como puede ver, el ángulo inicial y el ángulo de barrido pueden tener valores negativos:

[![](arcs-images/anglearc-small.png "Captura de pantalla triple de la página de arco de ángulo")](arcs-images/anglearc-large.png#lightbox "Triple captura de pantalla de la página de arco de ángulo")

Este enfoque para generar un archivo es forma algorítmica la más sencilla y es fácil derivar las ecuaciones paramétricas que describen el arco. Conocer el tamaño y la ubicación de la elipse y los ángulos inicial y barrido, los puntos inicial y final del arco se puede calcular usar funciones trigonométricas simple:

x = oval. MidX + (oval. Ancho / 2) * cos(angle)

y = oval. MidY + (oval. Alto / 2) * sin(angle)

El `angle` valor sea `startAngle` o `startAngle + sweepAngle`.

El uso de dos ángulos para definir un arco es mejor para los casos donde se conoce la longitud del arco que va a dibujar, por ejemplo, para crear un gráfico circular angular. El **un gráfico circular seccionado** página se muestra cómo hacerlo. El [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) clase utiliza una clase interna para definir algunos datos fabricadas y colores:

```csharp
class ChartData
{
    public ChartData(int value, SKColor color)
    {
        Value = value;
        Color = color;
    }

    public int Value { private set; get; }

    public SKColor Color { private set; get; }
}

ChartData[] chartData =
{
    new ChartData(45, SKColors.Red),
    new ChartData(13, SKColors.Green),
    new ChartData(27, SKColors.Blue),
    new ChartData(19, SKColors.Magenta),
    new ChartData(40, SKColors.Cyan),
    new ChartData(22, SKColors.Brown),
    new ChartData(29, SKColors.Gray)
};

```

El `PaintSurface` controlador primero recorre en bucle los elementos que se va a calcular un `totalValues` número. Partir de ahí, puede determinar el tamaño de cada elemento como la fracción del total y convertir en un ángulo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int totalValues = 0;

    foreach (ChartData item in chartData)
    {
        totalValues += item.Value;
    }

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float explodeOffset = 50;
    float radius = Math.Min(info.Width / 2, info.Height / 2) - 2 * explodeOffset;
    SKRect rect = new SKRect(center.X - radius, center.Y - radius,
                             center.X + radius, center.Y + radius);

    float startAngle = 0;

    foreach (ChartData item in chartData)
    {
        float sweepAngle = 360f * item.Value / totalValues;

        using (SKPath path = new SKPath())
        using (SKPaint fillPaint = new SKPaint())
        using (SKPaint outlinePaint = new SKPaint())
        {
            path.MoveTo(center);
            path.ArcTo(rect, startAngle, sweepAngle, false);
            path.Close();

            fillPaint.Style = SKPaintStyle.Fill;
            fillPaint.Color = item.Color;

            outlinePaint.Style = SKPaintStyle.Stroke;
            outlinePaint.StrokeWidth = 5;
            outlinePaint.Color = SKColors.Black;

            // Calculate "explode" transform
            float angle = startAngle + 0.5f * sweepAngle;
            float x = explodeOffset * (float)Math.Cos(Math.PI * angle / 180);
            float y = explodeOffset * (float)Math.Sin(Math.PI * angle / 180);

            canvas.Save();
            canvas.Translate(x, y);

            // Fill and stroke the path
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, outlinePaint);
            canvas.Restore();
        }

        startAngle += sweepAngle;
    }
}
```

Un nuevo `SKPath` objeto se crea para cada sector del gráfico circular. La ruta de acceso se compone de una línea desde el centro, un `ArcTo` para dibujar el arco y otra línea volver a los resultados del centro de la `Close` llamar. Este programa muestra sectores del gráfico circular "seccionado" moviéndolas todo desde el centro por 50 píxeles. Dicha tarea requiere un vector en la dirección del punto central del ángulo de barrido para cada segmento:

[![](arcs-images/explodedpiechart-small.png "Captura de pantalla triple de la página de un gráfico circular seccionado")](arcs-images/explodedpiechart-large.png#lightbox "Triple captura de pantalla de la página de un gráfico circular seccionado")

Para ver su aspecto sin "expansión", basta con convertir en comentario la `Translate` llamar:

[![](arcs-images/explodedpiechartunexploded-small.png "Captura de pantalla triple de la página de un gráfico circular seccionado sin la expansión")](arcs-images/explodedpiechartunexploded-large.png#lightbox "Triple captura de pantalla de la página de un gráfico circular seccionado sin la expansión")

## <a name="the-tangent-arc"></a>El arco tangente

El segundo tipo de arco compatible con `SKPath` es el *arco tangente*, que se denomina así porque el arco es la circunferencia de un círculo que es tangente a dos líneas conectadas.

Un arco tangente se agrega a una ruta de acceso con una llamada a la [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/System.Single/) método con dos `SKPoint` parámetros, o la [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/) sobrecarga con independiente `Single` parámetros para el puntos:

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Esto `ArcTo` método es similar a la PostScript [ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) (función) (página 532 en el documento PDF) y el archivo iOS [ `AddArcToPoint` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArcToPoint/p/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/) método.

El `ArcTo` método implica tres puntos:

- Punto actual de la curva o el punto (0, 0) si `MoveTo` no se ha llamado
- El primer argumento de punto para la `ArcTo` método, denominado el *punto las esquinas*
- El segundo argumento de punto `ArcTo`, llamado el *punto de destino*:

![](arcs-images/tangentarcthreepoints.png "Tres puntos que comienzan un arco tangente")

Estos tres puntos definen dos líneas conectadas:

![](arcs-images/tangentarcconnectinglines.png "Líneas que conectan los tres puntos de un arco tangente")

Si los tres puntos son colineales &mdash; es decir, si encuentran en la misma línea recta &mdash; no se dibuja ningún arco.

El `ArcTo` método también incluye un `radius` parámetro. Esto define el radio de un círculo:

![](arcs-images/tangentarccircle.png "El círculo de un arco tangente")

No se ha generalizado el arco tangente de una elipse.

Si las dos líneas se encuentran en cualquier ángulo, ese círculo puede insertarse entre las líneas para que resulte tangente en ambas líneas:

![](arcs-images/tangentarctangentcircle.png "El círculo de arco tangente entre las dos líneas")

No toque la curva que se agrega a la curva de cualquiera de los puntos especificados en la `ArcTo` método. Está formada por una línea recta desde el punto actual hasta el primer punto tangente y un arco que termina en el segundo punto tangente:

![](arcs-images/tangentarchighlight.png "El arco tangente resaltado entre las dos líneas")

Aquí es la última línea recta y un arco que se agrega a la curva de:

![](arcs-images/tangentarc.png "El arco tangente resaltado entre las dos líneas")

Puede continuar el contorno del segundo punto tangente.

El **arco tangente** página le permite experimentar con el arco tangente. Ésta es la primera de varias páginas que se derivan de [ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs), que define algunas prácticas `SKPaint` objetos y realiza `TouchPoint` procesamiento:

```csharp
public class InteractivePage : ContentPage
{
    protected SKCanvasView baseCanvasView;
    protected TouchPoint[] touchPoints;

    protected SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3
    };

    protected SKPaint redStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 15
    };

    protected SKPaint dottedStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    };

    protected void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = baseCanvasView.CanvasSize.Width / (float)baseCanvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            baseCanvasView.InvalidateSurface();
        }
    }
}
```

La clase `TangentArcPage` se deriva de `InteractivePage`. El constructor en el [ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) archivo es responsable de crear instancias e inicializar el `touchPoints` matriz y configuración `baseCanvasView` (en `InteractivePage`) a la `SKCanvasView` crea una instancia de objeto en el [ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) archivo:

```csharp
public partial class TangentArcPage : InteractivePage
{
    public TangentArcPage()
    {
        touchPoints = new TouchPoint[3];

        for (int i = 0; i < 3; i++)
        {
            TouchPoint touchPoint = new TouchPoint
            {
                Center = new SKPoint(i == 0 ? 100 : 500,
                                     i != 2 ? 100 : 500)
            };
            touchPoints[i] = touchPoint;
        }

        InitializeComponent();

        baseCanvasView = canvasView;
        radiusSlider.Value = 100;
    }

    void sliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

El `PaintSurface` controlador utiliza la `ArcTo` método para dibujar el arco en función de los puntos táctiles y un `Slider`, pero también forma algorítmica calcula el círculo que se basa el ángulo:

```csharp
public partial class TangentArcPage : InteractivePage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw the two lines that meet at an angle
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.LineTo(touchPoints[1].Center);
            path.LineTo(touchPoints[2].Center);
            canvas.DrawPath(path, dottedStrokePaint);
        }

        // Draw the circle that the arc wraps around
        float radius = (float)radiusSlider.Value;

        SKPoint v1 = Normalize(touchPoints[0].Center - touchPoints[1].Center);
        SKPoint v2 = Normalize(touchPoints[2].Center - touchPoints[1].Center);

        double dotProduct = v1.X * v2.X + v1.Y * v2.Y;
        double angleBetween = Math.Acos(dotProduct);
        float hypotenuse = radius / (float)Math.Sin(angleBetween / 2);
        SKPoint vMid = Normalize(new SKPoint((v1.X + v2.X) / 2, (v1.Y + v2.Y) / 2));
        SKPoint center = new SKPoint(touchPoints[1].Center.X + vMid.X * hypotenuse,
                                     touchPoints[1].Center.Y + vMid.Y * hypotenuse);

        canvas.DrawCircle(center.X, center.Y, radius, this.strokePaint);

        // Draw the tangent arc
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.ArcTo(touchPoints[1].Center, touchPoints[2].Center, radius);
            canvas.DrawPath(path, redStrokePaint);
        }

        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }

    // Vector methods
    SKPoint Normalize(SKPoint v)
    {
        float magnitude = Magnitude(v);
        return new SKPoint(v.X / magnitude, v.Y / magnitude);
    }

    float Magnitude(SKPoint v)
    {
        return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
    }
}
```

Este es el **arco tangente** página que se ejecuta en las tres plataformas:

[![](arcs-images/tangentarc-small.png "Captura de pantalla triple de la página de arco tangente")](arcs-images/tangentarc-large.png#lightbox "Triple captura de pantalla de la página de arco tangente")

El arco tangente es perfecto para crear esquinas redondeadas, como un rectángulo redondeado. Dado que `SKPath` ya incluye un `AddRoundedRect` método, el **redondea Heptágono** página muestra cómo utilizar `ArcTo` para redondear los vértices de un polígono siete lados. (El código se ha generalizado para cualquier polígono regular.)

El `PaintSurface` controlador de la [ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) clase contiene uno `for` bucle para calcular las coordenadas de los siete vértices el Heptágono y un segundo para calcular los puntos intermedios de los siete lados de estos vértices. Estos puntos medios se usan para construir la ruta de acceso:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float cornerRadius = 100;
    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPoint[] vertices = new SKPoint[numVertices];
    SKPoint[] midPoints = new SKPoint[numVertices];

    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    // Coordinates of the midpoints of the sides connecting the vertices
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        int prevVertex = (vertex + numVertices - 1) % numVertices;
        midPoints[vertex] = new SKPoint((vertices[prevVertex].X + vertices[vertex].X) / 2,
                                        (vertices[prevVertex].Y + vertices[vertex].Y) / 2);
    }

    // Create the path
    using (SKPath path = new SKPath())
    {
        // Begin at the first midpoint
        path.MoveTo(midPoints[0]);

        for (int vertex = 0; vertex < numVertices; vertex++)
        {
            SKPoint nextMidPoint = midPoints[(vertex + 1) % numVertices];

            // Draws a line from the current point, and then the arc
            path.ArcTo(vertices[vertex], nextMidPoint, cornerRadius);

            // Connect the arc with the next midpoint
            path.LineTo(nextMidPoint);
        }
        path.Close();

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);
        }
    }
}

```

Este es el programa que se ejecuta en las tres plataformas:

[![](arcs-images/roundedheptagon-small.png "Captura de pantalla triple de la página Heptágono redondea")](arcs-images/roundedheptagon-large.png#lightbox "Triple captura de pantalla de la página Heptágono redondeado")

## <a name="the-elliptical-arc"></a>Arco elíptico

Arco elíptico se agrega a una ruta de acceso con una llamada a la [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/SkiaSharp.SKPoint/) método que tiene dos `SKPoint` parámetros, o la [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/System.Single/System.Single/) se puede sobrecargar con independiente X e Y coordenadas:

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

Arco elíptico es coherente con la [arco elíptico](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) de Scalable Vector Graphics (SVG) y la plataforma Universal de Windows [ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) clase.

Estos `ArcTo` métodos dibujan un arco entre dos puntos, que es el punto actual del contorno, y el último parámetro para el `ArcTo` método (el `xy` parámetro o el separar `x` y `y` parámetros):

![](arcs-images/ellipticalarcpoints.png "Los dos puntos que definen un arco elíptico")

El primer parámetro de punto para la `ArcTo` método (`r`, o `rx` y `ry`) no es un punto, pero en su lugar, especifica los radios horizontales y verticales de la elipse;

![](arcs-images/ellipticalarcellipse.png "Los puntos suspensivos que definen un arco elíptico")

El `xAxisRotate` parámetro es el número de grados a la derecha para girar esta elipse:

![](arcs-images/ellipticalarctiltedellipse.png "La elipse superpuesta que definen un arco elíptico")

Si, a continuación, se coloca este elipse superpuesta para que entra en contacto con los dos puntos, los puntos se conectan mediante dos arcos diferentes:

![](arcs-images/ellipticalarcellipse1.png "El primer conjunto de arcos elípticos")

Estos dos arcos pueden distinguirse de dos maneras: el arco superior es mayor que el arco de la parte inferior y, como el arco se dibuja de izquierda a derecha, se dibuja el arco superior en una dirección de las agujas del reloj mientras se dibuja el arco inferior en una dirección hacia la izquierda.

También es posible ajustar la elipse entre los dos puntos de otro modo:

![](arcs-images/ellipticalarcellipse2.png "El segundo conjunto de arcos elípticos")

Ahora hay un arco más pequeño en la parte superior que se dibuja a la derecha y un arco más grande en la parte inferior que se dibuja a la izquierda.

Estos dos puntos, por tanto, se pueden conectar mediante un arco definido por la elipse superpuesta en un total de cuatro maneras:

![](arcs-images/ellipticalarccolors.png "Todos los arcos elípticos cuatro")

Estos cuatro arcos se distinguen por las cuatro combinaciones de la [ `SKPathArcSize` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathArcSize/) y [ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/) argumentos de tipo de enumeración para la `ArcTo` método:

- rojo: SKPathArcSize.Large y SKPathDirection.Clockwise
- verde: SKPathArcSize.Small y SKPathDirection.Clockwise
- azul: SKPathArcSize.Small y SKPathDirection.CounterClockwise
- magenta: SKPathArcSize.Large y SKPathDirection.CounterClockwise

Si la elipse superpuesta no es lo suficientemente grande como para caber entre los dos puntos, a continuación, lo uniformemente se escala hasta que sea lo suficientemente grande. Solo dos arcos únicos conectan los dos puntos en ese caso. Estos se pueden distinguir con el `SKPathDirection` parámetro.

Aunque este método para definir un arco sonidos complejo en la primera aparición, es el único método que permite definir un archivo con una elipse gira y, a menudo es el enfoque más sencillo cuando necesite integrar arcos con otras partes del contorno.

El **arco elíptico** página permite establecer de forma interactiva los dos puntos y el tamaño y la rotación de la elipse. El `EllipticalArcPage` clase se deriva de `InteractivePage`y el `PaintSurface` controlador en el [ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) archivo de código subyacente dibuja los cuatro arcos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        int colorIndex = 0;
        SKPoint ellipseSize = new SKPoint((float)xRadiusSlider.Value,
                                          (float)yRadiusSlider.Value);
        float rotation = (float)rotationSlider.Value;

        foreach (SKPathArcSize arcSize in Enum.GetValues(typeof(SKPathArcSize)))
            foreach (SKPathDirection direction in Enum.GetValues(typeof(SKPathDirection)))
            {
                path.MoveTo(touchPoints[0].Center);
                path.ArcTo(ellipseSize, rotation,
                           arcSize, direction,
                           touchPoints[1].Center);

                strokePaint.Color = colors[colorIndex++];
                canvas.DrawPath(path, strokePaint);
                path.Reset();
            }
    }

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}

```

A continuación se ejecuta en las tres plataformas:

[![](arcs-images/ellipticalarc-small.png "Captura de pantalla triple de la página de arco elíptico")](arcs-images/ellipticalarc-large.png#lightbox "Triple captura de pantalla de la página de arco elíptico")

El **arco infinito** página utiliza arco elíptico para dibujar un signo de infinito. El inicio de sesión de infinito se basa en dos círculos con radios de 100 unidades separadas por 100 unidades:

![](arcs-images/infinitycircles.png "Dos círculos")

Dos líneas por las fronteras entre sí son tangente a dos círculos:

![](arcs-images/infinitycircleslines.png "Dos círculos con líneas tangentes")

El inicio de sesión de infinito es una combinación de partes de estos círculos y las dos líneas. Para usar el arco elíptico para dibujar el inicio de sesión de infinito, se deben determinar las coordenadas donde las dos líneas son tangentes a los círculos.

Construir un rectángulo de la derecha en uno de los círculos:

![](arcs-images/infinitytriangle.png "Dos círculos con líneas de tangente y círculo incrustado")

El radio del círculo es 100 unidades y la hipotenusa del triángulo es 150 unidades, por lo que el ángulo α es el arco seno (seno inverso) de 100 dividido por 150 o 41.8 grados. La longitud del otro lado del triángulo es 150 el coseno de grados 41.8 o 112, que también se puede calcular mediante el teorema de Pitagórica.

A continuación, se pueden calcular las coordenadas del punto tangente con esta información:

x = 112·cos(41.8) = 83

y = 112·sin(41.8) = 75

Los cuatro puntos de tangencia son todo lo que es necesario dibujar un inicio de sesión de infinito centrado en el punto (0, 0) con los radios de círculo de 100:

![](arcs-images/infinitycoordinates.png "Dos círculos con líneas de tangente y coordenadas")

El `PaintSurface` controlador en el [ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) clase coloca el inicio de sesión de infinito para que el (0, 0) punto se coloca en el centro de la página y escala de la ruta de acceso para el tamaño de pantalla:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.LineTo(83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.CounterClockwise, 83, -75);
        path.LineTo(-83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.Clockwise, -83, -75);
        path.Close();

        // Use path.TightBounds for coordinates without control points
        SKRect pathBounds = path.Bounds;

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / pathBounds.Width,
                              info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

El código usa el `Bounds` propiedad de `SKPath` para determinar las dimensiones del seno infinito escalar al tamaño del lienzo:

[![](arcs-images/arcinfinity-small.png "Captura de pantalla triple de la página de arco infinito")](arcs-images/arcinfinity-large.png#lightbox "Triple captura de pantalla de la página de infinito de arco")

El resultado parece un poco pequeño, lo que sugiere que el `Bounds` propiedad de `SKPath` está informando de un tamaño mayor que la ruta de acceso.

Internamente, Skia aproxima el arco mediante varias curvas de Bézier cuadráticas. Estas curvas (tal y como se verá en la siguiente sección) contienen puntos de control que rigen cómo se dibuja la curva pero no forman parte de la curva representada. El `Bounds` esta propiedad incluye los puntos de control.

Para obtener un ajuste más estricto, use la `TightBounds` propiedad, que excluye los puntos de control. Este es el programa que se ejecuta en modo horizontal y usa el `TightBounds` propiedad que se va a obtener los límites de la ruta de acceso:

[![](arcs-images/arcinfinitytightbounds-small.png "Captura de pantalla triple de la página de arco infinito con límites estrictos")](arcs-images/arcinfinitytightbounds-large.png#lightbox "Triple captura de pantalla de la página de arco infinito con límites estrictos")

Aunque las conexiones entre las líneas rectas y arcos son matemáticamente suaves, el cambio de arco en línea recta puede parecer un poco abrupto. Un inicio de sesión de infinito mejor se presenta en la página siguiente.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
