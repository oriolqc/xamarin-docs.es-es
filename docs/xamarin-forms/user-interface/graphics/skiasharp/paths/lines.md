---
title: Líneas y extremos de trazo
description: En este artículo se explica cómo usar SkiaSharp para dibujar líneas con extremos de trazo diferentes en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 000bf24c1b06baab892f0b165c8b9eeebebce49d
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615267"
---
# <a name="lines-and-stroke-caps"></a>Líneas y extremos de trazo

_Obtenga información sobre cómo utilizar SkiaSharp para dibujar líneas con extremos de trazo diferente_

En SkiaSharp, representar una sola línea es muy diferente de la representación de una serie de líneas rectas conectadas. Incluso al dibujar líneas individuales, sin embargo, a menudo resulta necesaria para proporcionar a las líneas de un ancho de trazo determinado y más ancha la línea, más importante, se convierte en la apariencia del final de las líneas, llamado el *extremo trazo*:

![](lines-images/strokecapsexample.png "Las opciones de extremos de tres trazo")

Para dibujar líneas individuales, `SKCanvas` define una sencilla [ `DrawLine` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawLine/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/) método cuyos argumentos indican iniciales y finales de coordenadas de la línea con un `SKPaint` objeto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

De forma predeterminada, el `StrokeWidth` propiedad de una instancia recién creada `SKPaint` objeto es 0, que tiene el mismo efecto que un valor de 1 en la representación de una línea de un píxel en el grosor. Esto parece muy ligero en dispositivos de alta resolución, como teléfonos, por lo que probablemente desee establecer el `StrokeWidth` en un valor mayor. ¿Pero cuando empiece a dibujar las líneas de un grosor de tamaño ajustable, que provoca otro problema: cómo empieza y finaliza de estas líneas gruesas la se debe representar?

Se llama a la apariencia del final de las líneas y se inicia un *extremo de línea* o, en Skia, un *extremo trazo*. La palabra "cap" en este contexto hace referencia a un tipo de hat &mdash; algo que se encuentra en el extremo de la línea. Establece el [ `StrokeCap` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeCap/) propiedad de la `SKPaint` objeto en uno de los siguientes miembros de la [ `SKStrokeCap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeCap/) enumeración:

- [`Butt`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Butt/) (el valor predeterminado)
- [`Square`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)

Se ilustran mejor con un programa de ejemplo. La segunda sección de la página principal de la [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa comienza con una página titulada **extremos de trazo** según la [ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs) clase. Esta página define una `PaintSurface` controlador de eventos que se repite en los tres miembros de la `SKStrokeCap` enumeración, mostrar el nombre del miembro de enumeración y dibujar una línea con ese extremo trazo:

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

Para cada miembro de la `SKStrokeCap` enumeración, el controlador dibuja dos líneas, uno con un grosor de trazo de 50 píxeles y otra línea situado en la parte superior con un grosor de trazo de 2 píxeles. Esta segunda línea pretende ilustrar geométrica comienzo y al final de la línea independiente del grosor de la línea y un extremo del trazo:

[![](lines-images/strokecaps-small.png "Captura de pantalla de la página extremos de trazo triple")](lines-images/strokecaps-large.png#lightbox "Triple captura de pantalla de la página extremos de trazo")

Como puede ver, el `Square` y `Round` extremos de trazo amplían la longitud de la línea de forma eficaz por la mitad del ancho del trazo al principio de la línea y de nuevo al final. Esta extensión se vuelve importante cuando es necesario determinar las dimensiones de un objeto graphics representado.

La `SKCanvas` clase incluye también otro método para dibujar varias líneas que resulta bastante peculiar:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

El `points` parámetro es una matriz de `SKPoint` valores y `mode` es un miembro de la [ `SKPointMode` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPointMode/) enumeración, que tiene tres miembros:

- [`Points`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Points/) para representar los puntos individuales
- [`Lines`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Lines/) para conectarse a cada par de puntos
- [`Polygon`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Polygon/) para conectar todos los puntos consecutivos

El **varias líneas** página muestra este método. El [ `MultipleLinesPage` archivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml) crean instancias de dos `Picker` vistas que le permiten seleccionar un miembro de la `SKPointMode` enumeración y un miembro de la `SKStrokeCap` enumeración:

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

Debe comprobar la existencia de este controlador de la `SKCanvasView` objeto porque el controlador de eventos es el primero se le llama cuando el `SelectedIndex` propiedad de la `Picker` se establece en 0 en el archivo XAML, y que se produce antes de la `SKCanvasView` se ha creado una instancia.

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

La captura de pantalla muestra una variedad de `Picker` las selecciones realizadas en las tres plataformas:

[![](lines-images/multiplelines-small.png "Captura de pantalla triple de la página de varias líneas")](lines-images/multiplelines-large.png#lightbox "Triple captura de pantalla de la página de varias líneas")

El iPhone a la izquierda se muestra cómo el `SKPointMode.Points` hace que el miembro de enumeración `DrawPoints` para representar cada uno de los puntos de la `SKPoint` si el extremo de línea es de matriz como un cuadrado `Butt` o `Square`. Se representan los círculos si el extremo de línea es `Round`.

Cuando se usa en su lugar `SKPointMode.Lines`, como se muestra en la pantalla de Android en el centro, el `DrawPoints` método dibuja una línea entre cada par de `SKPoint` valores, utilizando el extremo de línea especificado, en este caso `Round`.

La captura de pantalla UWP muestra el resultado de la `SKPointMode.Polygon` valor. Se dibuja una línea entre los puntos sucesivas de la matriz, pero si observa muy de cerca, verá que estas líneas no están conectadas. Cada una de estas líneas independientes comienza y termina con el extremo de línea especificado. Si selecciona el `Round` CAP, pueden aparecer las líneas se conecten, pero realmente no estén conectados.

Si las líneas están conectadas o no conectadas son un aspecto fundamental de trabajar con trazados de gráficos.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
