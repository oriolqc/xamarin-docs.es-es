---
title: "Líneas y extremos de trazo"
description: "Aprenda cómo usar SkiaSharp para dibujar líneas con extremos de trazo diferente"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9a3090873569db2466db9ab25cc105ea59401df3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="lines-and-stroke-caps"></a>Líneas y extremos de trazo

_Aprenda cómo usar SkiaSharp para dibujar líneas con extremos de trazo diferente_

En SkiaSharp, una sola línea de representación es muy diferente de la representación de una serie de líneas rectas conectadas. Incluso al dibujar líneas individuales, sin embargo, a menudo resulta necesaria para proporcionar a las líneas de un ancho del trazo determinado y cuanto mayor sea la línea, el más importante, se convierte en la apariencia del final de las líneas, llama a la *cap trazo*:

![](lines-images/strokecapsexample.png "Las opciones de CAP de tres trazo")

Para dibujar líneas individuales, `SKCanvas` define un sencillo [ `DrawLine` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawLine/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/) método cuyos argumentos indican iniciales y finales de coordenadas de la línea con un `SKPaint` objeto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

De forma predeterminada, el `StrokeWidth` propiedad de una instancia recién creada `SKPaint` objeto es 0, que tiene el mismo efecto que un valor de 1 en la representación de una línea de un píxel en grosor. Esto parece muy delgado en dispositivos de alta resolución como teléfonos, por lo que probablemente deseará establecer el `StrokeWidth` en un valor mayor. ¿Pero cuando se empieza a dibujar líneas de un grosor considerable, que genera otro problema: cómo debe el se inicia y se termina de estas líneas gruesas representará?

Se llama a la apariencia de la comienza y termina de líneas un *extremo de línea* o, en Skia, un *cap trazo*. La palabra "cap" en este contexto hace referencia a un tipo de hat & #x 2014; algo que se encuentra en el extremo de la línea. Establece el [ `StrokeCap` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeCap/) propiedad de la `SKPaint` objeto en uno de los siguientes miembros de la [ `SKStrokeCap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeCap/) enumeración:

- [`Butt`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Butt/) (el valor predeterminado)
- [`Square`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)

Se ilustran mejor con un programa de ejemplo. La segunda sección de la página principal de la [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programa comienza con una página titulada **extremos de trazos** tomando como base la [ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs) clase. Esta página define una `PaintSurface` controlador de eventos que se repite en los tres miembros de la `SKStrokeCap` enumeración, mostrar el nombre del miembro de enumeración y dibujar una línea con ese extremo del trazo:

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
        TextAlign = SKTextAlign.Center
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

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

Para cada miembro de la `SKStrokeCap` enumeración, el controlador dibuja dos líneas, uno con un grosor del trazo de 50 píxeles y otra línea situado en la parte superior con un grosor del trazo de 2 píxeles. Esta segunda línea se pretende ilustrar geométrica inicial y final de la línea independiente del grosor de la línea y un extremo del trazo:

[![](lines-images/strokecaps-small.png "Captura de pantalla triple de la página extremos de trazos")](lines-images/strokecaps-large.png "Triple captura de pantalla de la página extremos de trazo")

Como puede ver, el `Square` y `Round` extremos de trazos amplían la longitud de la línea de forma eficaz por la mitad del ancho del trazo en el principio de la línea y de nuevo al final. Esta extensión se vuelve importante cuando es necesario determinar las dimensiones de un objeto graphics representado.

La `SKCanvas` clase incluye también otro método para dibujar varias líneas que resulta algo peculiar:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

El `points` parámetro es una matriz de `SKPoint` valores y `mode` es un miembro de la [ `SKPointMode` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPointMode/) enumeración, que tiene tres miembros:

- [`Points`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Points/) para representar los puntos individuales
- [`Lines`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Lines/) para conectar cada par de puntos
- [`Polygon`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Polygon/) para conectar todos los puntos consecutivos

El **varias líneas** página ilustra este método. El [ `MultipleLinesPage` archivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml) crean instancias de dos `Picker` vistas que permiten seleccionar un miembro de la `SKPointMode` enumeración y un miembro de la `SKStrokeCap` enumeración:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Points</x:String>
                <x:String>Lines</x:String>
                <x:String>Polygon</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Butt</x:String>
                <x:String>Round</x:String>
                <x:String>Square</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

El `SelectedIndexChanged` controlador para ambos `Picker` vistas simplemente invalida la `SKCanvasView` objeto:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Este controlador necesita comprobar la existencia de la `SKCanvasView` objeto porque el controlador de eventos es el primero llama cuando el `SelectedIndex` propiedad de la `Picker` se establece en 0 en el archivo XAML, y que se produce antes de la `SKCanvasView` se ha creado una instancia.

El `PaintSurface` controlador tiene acceso a un método genérico para la obtención de los dos elementos seleccionados de la `Picker` vistas y convertirlos en valores de enumeración:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = GetPickerItem<SKStrokeCap>(strokeCapPicker)
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = GetPickerItem<SKPointMode>(pointModePicker);
    canvas.DrawPoints(pointMode, points, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }
    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}
```

La captura de pantalla muestra una variedad de `Picker` selecciones en las tres plataformas:

[![](lines-images/multiplelines-small.png "Captura de pantalla triple de la página de varias líneas")](lines-images/multiplelines-large.png "Triple captura de pantalla de la página de varias líneas")

El iPhone a la izquierda se muestra cómo el `SKPointMode.Points` hace que el miembro de enumeración `DrawPoints` para representar cada uno de los puntos en el `SKPoint` matriz como un cuadrado si el extremo de línea es `Butt` o `Square`. Se representan círculos si el extremo de línea es `Round`.

Cuando se usa en su lugar `SKPointMode.Lines`, tal y como se muestra en la pantalla de Android en el centro, el `DrawPoints` método dibuja una línea entre cada par de `SKPoint` valores, utilizando el extremo de línea especificado, en este caso `Round`.

El dispositivo móvil de Windows muestra el resultado de la `SKPointMode.Polygon` valor. Se dibuja una línea entre los puntos sucesivas de la matriz, pero si observa muy atentamente, verá que estas líneas no están conectadas. Cada una de estas líneas independientes comienza y termina con el extremo de línea especificado. Si selecciona el `Round` CAP, las líneas que parece estar conectado, pero realmente no está conectados.

Si las líneas se conectado o no es un aspecto fundamental de trabajar con rutas de acceso de gráficos.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
