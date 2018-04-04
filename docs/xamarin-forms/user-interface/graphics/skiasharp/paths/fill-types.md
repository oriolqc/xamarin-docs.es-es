---
title: Los tipos de relleno de ruta de acceso
description: Detectar los distintos efectos posibles con tipos de relleno de ruta de acceso de SkiaSharp
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 88b9dacef7a77d5f18908bdcb696e5172ceaa8c7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="the-path-fill-types"></a>Los tipos de relleno de ruta de acceso

_Detectar los distintos efectos posibles con tipos de relleno de ruta de acceso de SkiaSharp_

Se pueden superponer dos perfiles en una ruta de acceso y las líneas que componen un contorno solo se pueden superponer. Cualquier área delimitada potencialmente puede rellenarse, pero no desea rellenar todas las áreas entre comillas. Por ejemplo:

![](fill-types-images/filltypeexample.png "Que señala el cinco estrellas parcialmente filles")

Tiene un poco control sobre esto. El algoritmo de llenado se rige por el [ `SKFillType` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.FillType/) propiedad de `SKPath`, que establece en un miembro de la [ `SKPathFillType` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathFillType/) enumeración:

- [`Winding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.Winding/), el valor predeterminado
- [`EvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.EvenOdd/)
- [`InverseWinding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseWinding/)
- [`InverseEvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseEvenOdd/)

Los algoritmos de generación y par-impar determinan si cualquier área delimitada está rellena o no rellena basándose en una hipotética línea dibujada en esa área hasta el infinito. Esa línea cruza con una o más líneas de límites que constituyen la ruta de acceso. Con el modo de generación, si el número de límites las líneas dibujadas en el equilibrio de una dirección espera el número de las líneas dibujadas en el otro sentido, a continuación, en el área no se rellena. En caso contrario, se rellena el área. El algoritmo par-impar rellena un área si el número de líneas de límite es impar.

Con muchas rutas de acceso de rutinas, el algoritmo de generación a menudo rellena todas las áreas adjuntas de una ruta de acceso. El algoritmo par-impar generalmente produce resultados más interesantes.

El ejemplo clásico es una estrella de cinco puntas, como se muestra en el **Five-Pointed estrella** página. El [FivePointedStarPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml) archivo crean instancias de dos `Picker` vistas para seleccionar la ruta de acceso de relleno tipo y si la ruta de acceso se traza rellena o ambos y en qué orden:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.FivePointedStarPage"
             Title="Five-Pointed Star">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="fillTypePicker"
                Title="Path Fill Type"
                Grid.Row="0"
                Grid.Column="0"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Winding</x:String>
                <x:String>EvenOdd</x:String>
                <x:String>InverseWinding</x:String>
                <x:String>InverseEvenOdd</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="drawingModePicker"
                Title="Drawing Mode"
                Grid.Row="0"
                Grid.Column="1"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Fill only</x:String>
                <x:String>Stroke only</x:String>
                <x:String>Stroke then Fill</x:String>
                <x:String>Fill then Stroke</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

El archivo de código subyacente utiliza ambos `Picker` valores para dibujar una estrella de cinco puntas:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPath path = new SKPath
    {
        FillType = (SKPathFillType)Enum.Parse(typeof(SKPathFillType),
                        fillTypePicker.Items[fillTypePicker.SelectedIndex])
    };
    path.MoveTo(info.Width / 2, info.Height / 2 - radius);

    for (int i = 1; i < 5; i++)
    {
        // angle from vertical
        double angle = i * 4 * Math.PI / 5;
        path.LineTo(center + new SKPoint(radius * (float)Math.Sin(angle),
                                        -radius * (float)Math.Cos(angle)));
    }
    path.Close();

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 50,
        StrokeJoin = SKStrokeJoin.Round
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    switch (drawingModePicker.SelectedIndex)
    {
        case 0:
            canvas.DrawPath(path, fillPaint);
            break;

        case 1:
            canvas.DrawPath(path, strokePaint);
            break;

        case 2:
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case 3:
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

Normalmente, el tipo de relleno de ruta de acceso debe afectan al solo rellenos y trazos no, pero los dos `Inverse` modos afectan a rellenos y trazos. Para los rellenos, las dos `Inverse` tipos rellenar áreas oppositely para que se rellena el área situada fuera de la estrella. Para los trazos, los dos `Inverse` tipos de color todo excepto el trazo. Uso de estos tipos de relleno inversa puede producir algunos efectos impares, como se muestra en la captura de pantalla de iOS:

[![](fill-types-images/fivepointedstar-small.png "Captura de pantalla triple de la página de estrella de Five-Pointed")](fill-types-images/fivepointedstar-large.png#lightbox "Triple captura de pantalla de la página de estrella de Five-Pointed")

Las capturas de pantalla de Android y Windows mobile muestran los efectos de generación y par-impar típicos, pero el orden de los trazos y relleno también afecta a los resultados.

El algoritmo de generación es dependiente de la dirección que se dibujan líneas. Normalmente cuando se va a crear una ruta de acceso, puede controlar esa dirección según sus especificaciones que se dibujan líneas desde un punto a otro. Sin embargo, el `SKPath` clase también define métodos como `AddRect` y `AddCircle` que dibujar contornos todos. Para controlar cómo se dibujan estos objetos, los métodos incluyen un parámetro de tipo [ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/), que tiene dos miembros:

- [`Clockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.Clockwise/)
- [`CounterClockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.CounterClockwise/)

Los métodos de `SKPath` que incluyen un `SKPathDirection` asignarle un valor predeterminado del parámetro `Clockwise`.

El **círculos superpuestos** página crea una ruta de acceso con cuatro círculos superpuestos con un tipo de relleno par-impar de la ruta de acceso:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(info.Width, info.Height) / 4;

    SKPath path = new SKPath
    {
        FillType = SKPathFillType.EvenOdd
    };

    path.AddCircle(center.X - radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X - radius / 2, center.Y + radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y + radius / 2, radius);

    SKPaint paint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    canvas.DrawPath(path, paint);

    paint.Style = SKPaintStyle.Stroke;
    paint.StrokeWidth = 10;
    paint.Color = SKColors.Magenta;

    canvas.DrawPath(path, paint);
}
```

Es una imagen interesante creada con un mínimo de código:

[![](fill-types-images/overlappingcircles-small.png "Captura de pantalla triple de la página de círculos superpuestos")](fill-types-images/overlappingcircles-large.png#lightbox "Triple captura de pantalla de la página de círculos superpuestos")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
