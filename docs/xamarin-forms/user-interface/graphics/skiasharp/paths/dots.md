---
title: Puntos y guiones
description: Supere las complejidades de dibujar líneas de puntos y guiones en SkiaSharp
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 274c8e9a79fa3fadff14f1174d86aad04d902b05
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="dots-and-dashes"></a>Puntos y guiones

_Supere las complejidades de dibujar líneas de puntos y guiones en SkiaSharp_

SkiaSharp le permiten dibujar líneas que no estén firmes sino que se compone de puntos y guiones:

![](dots-images/dottedlinesample.png "Línea de puntos")

Esto se hace un *efecto de la ruta de acceso*, que es una instancia de la [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) clase que establece en el [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) propiedad de `SKPaint`. Puede crear una ruta de acceso efecto (o los efectos de ruta de acceso de combinación) mediante el método estático `Create` métodos definidos por `SKPathEffect`.

Para dibujar líneas de puntos o guiones, se utiliza el [ `SKPathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) método estático. Hay dos argumentos: en primer lugar se trata de una matriz de `float` valores que indican la longitud de los puntos y guiones y la longitud de los espacios entre ellas. Esta matriz debe tener un número par de elementos, y debe haber al menos dos elementos. (Puede haber cero elementos en la matriz, pero que da lugar a una línea sólida.) Si hay dos elementos, la primera es la longitud de un punto o un guión y el segundo es la longitud de la separación antes del siguiente punto o guión. Si hay más de dos elementos, a continuación, se encuentran en este orden: guión longitud, longitud del espacio, longitud de guión, longitud del espacio y así sucesivamente.

Por lo general, deberá realizar las longitudes de guión y separación un múltiplo del ancho del trazo. Si el ancho del trazo es de 10 píxeles, por ejemplo, a continuación, la matriz {10, 10} dibujará una línea de puntos donde las brechas y los puntos son la misma longitud que el grosor del trazo.

Sin embargo, el `StrokeCap` configuración de la `SKPaint` también afecta al objeto estos puntos y guiones. Como verá en breve, tiene un impacto en los elementos de esta matriz.

Decimal con punto y se muestran las líneas discontinuas en la **puntos y guiones** página. El [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml) archivo crean instancias de dos `Picker` vistas, uno para que le permite seleccionar un extremo del trazo y la segunda para seleccionar una matriz de guión:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
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

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>10, 10</x:String>
                <x:String>30, 10</x:String>
                <x:String>10, 10, 30, 10</x:String>
                <x:String>0, 20</x:String>
                <x:String>20, 20</x:String>
                <x:String>0, 20, 20, 20</x:String>
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

Los tres primeros elementos en el `dashArrayPicker` se supone que el ancho del trazo es de 10 píxeles. El {10, 10} matriz es para una línea de puntos, {30, 10} es para una línea discontinua y {10, 10, 30, 10} es para una línea de puntos y guiones. (Los otros tres se explicará en breve.)

El [ `DotsAndDashesPage` archivo de código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs) contiene el `PaintSurface` controlador de eventos y un par de rutinas auxiliares para tener acceso a la `Picker` vistas:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)Enum.Parse(typeof(SKStrokeCap),
                        strokeCapPicker.Items[strokeCapPicker.SelectedIndex]),

        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }

    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string[] strs = picker.Items[picker.SelectedIndex].Split(new char[] { ' ', ',' },
                                                             StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

En las siguientes capturas de pantalla, la pantalla de iOS en el extremo izquierdo muestra una línea de puntos:

[![](dots-images/dotsanddashes-small.png "Captura de pantalla triple de la página de puntos y guiones")](dots-images/dotsanddashes-large.png#lightbox "Triple captura de pantalla de la página de puntos y guiones")

Sin embargo, la pantalla Android también se supone que para mostrar una línea de puntos con la matriz {10, 10}, pero en su lugar, la línea es sólida. ¿Qué ocurre? El problema es que la pantalla Android también tiene una configuración de CAP de trazo de `Square`. Este comando extiende todos los guiones por la mitad del ancho de trazo, haciendo que los espacios que se llene.

Para evitar este problema cuando se usa un extremo del trazo de `Square` o `Round`, debe reducir las longitudes de guión en la matriz por la longitud de trazo (a veces, lo que produce una longitud de guión de 0) y aumentar las longitudes de separación por la longitud del trazo. Se trata de cómo las tres últimas guión matrices en la `Picker` se calcularon en el archivo XAML:

- {10, 10} está {0, 20} para una línea de puntos
- {30, 10} está {20, 20} para una línea discontinua
- {10, 10, 30, 10} está {0, 20, 20, 20} por una línea de puntos y guiones

Limitar la muestra de pantalla de Windows que números separados por puntos y guiones línea para un trazo de `Round`. El `Round` cap trazo a menudo proporciona la mejor apariencia de puntos y guiones en líneas gruesas.

Hasta ahora no se ha realizado ninguna mención del segundo parámetro a la `SKPathEffect.CreateDash` método. Este parámetro se denomina `phase` y hace referencia a un desplazamiento dentro del modelo de puntos y guiones para el comienzo de la línea. Por ejemplo, si la matriz de guión es {10, 10} y el `phase` es 10, a continuación, la línea comienza con un espacio en lugar de un punto.

Una aplicación interesante de las `phase` parámetro está en una animación. El **animando espiral** página es similar a la **Archimedean espiral** página, salvo que la [ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/AnimatedSpiralPage.cs) clase anima la `phase` parámetro. La página también muestra otra alternativa a la animación. El ejemplo anterior de la [ `PulsatingEllipsePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml.cs) usa el `Task.Delay` método para controlar la animación. Este ejemplo utiliza en su lugar el Xamarin.Forms `Device.Timer` método:


```csharp
const double cycleTime = 250;       // in milliseconds

SKCanvasView canvasView;
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float dashPhase;

public AnimatedSpiralPage()
{
    Title = "Animated Spiral";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    stopwatch.Start();

    Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
    {
        double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
        dashPhase = (float)(10 * t);
        canvasView.InvalidateSurface();

        if (!pageIsActive)
        {
            stopwatch.Stop();
        }

        return pageIsActive;
    });
}
```

Por supuesto, tendrá que ejecutan realmente el programa para ver la animación:

[![](dots-images/animatedspiral-small.png "Triple captura de pantalla de la página animado espiral")](dots-images/animatedspiral-large.png#lightbox "Triple captura de pantalla de la página de espiral animado")

Ahora ha visto cómo dibujar líneas y definir curvas con ecuaciones paramétricas. Una sección para publicarlos posteriormente describirá los distintos tipos de curvas que `SKPath` admite.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
