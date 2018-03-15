---
title: "Animación básica"
description: "Descubra cómo animar los gráficos SkiaSharp"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 347ecf72356915714200835263e38519c1ad8a13
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="basic-animation"></a>Animación básica

_Descubra cómo animar los gráficos SkiaSharp_

Puede animar gráficos SkiaSharp de Xamarin.Forms provocando el `PaintSurface` método al que llamar con mucha frecuencia, cada vez que dibujar los gráficos de forma ligeramente diferente. Esta es una animación que se muestra más adelante en este artículo con círculos concéntricos que aparentemente expanda desde el centro de:

![](animation-images/animationexample.png "Varios círculos concéntricos aparentemente expandir desde el centro")

El **Pulsating elipse** página en el [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programa anima los dos ejes de una elipse para que parezca estar pulsating y puede incluso controlar el tasa de esta pulsaciones:


El [ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) archivo crea una instancia de un Xamarin.Forms `Slider` y `Label` para mostrar el valor actual del control deslizante. Se trata de una manera común de integrar un `SKCanvasView` con otras vistas de Xamarin.Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.PulsatingEllipsePage"
             Title="Pulsating Ellipse">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Slider x:Name="slider"
                Grid.Row="0"
                Maximum="10"
                Minimum="0.1"
                Value="5"
                Margin="20, 0" />

        <Label Grid.Row="1"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Cycle time = {0:F1} seconds'}"
               HorizontalTextAlignment="Center" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

El archivo de código subyacente se crea un `Stopwatch` objeto para que actúe como un reloj de alta precisión. El `OnAppearing` conjuntos de invalidar el `pageIsActive` campo `true` y llama a un método denominado `AnimationLoop`. El `OnDisappearing` invalidación establece que `pageIsActive` campo `false`:

```csharp
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float scale;            // ranges from 0 to 1 to 0

public PulsatingEllipsePage()
{
    InitializeComponent();
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    AnimationLoop();
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    pageIsActive = false;
}
```

El `AnimationLoop` método inicia el `Stopwatch` y, a continuación, los bucles al `pageIsActive` es `true`. Esto es básicamente un "bucle infinito" mientras la página está activa, pero no hacer que el programa que deja de responder porque el bucle finaliza con una llamada a `Task.Delay` con el `await` operador, que permite a otras partes de la función del programa. El argumento `Task.Delay` hace que se completará después de 30/1 segundo. Esto define la velocidad de fotogramas de la animación.

```csharp
async Task AnimationLoop()
{
    stopwatch.Start();

    while (pageIsActive)
    {
        double cycleTime = slider.Value;
        double t = stopwatch.Elapsed.TotalSeconds % cycleTime / cycleTime;
        scale = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;
        canvasView.InvalidateSurface();
        await Task.Delay(TimeSpan.FromSeconds(1.0 / 30));
    }

    stopwatch.Stop();
}

```

El `while` bucle comienza obteniendo un tiempo de ciclo de la `Slider`. Se trata de un tiempo en segundos, por ejemplo, 5. La segunda instrucción calcula un valor de `t` para *tiempo*. Para una `cycleTime` de 5, `t` aumenta de 0 a 1 cada 5 segundos. El argumento para el `Math.Sin` función en el segundo varía de instrucción entre 0 y 2π cada 5 segundos. El `Math.Sin` función devuelve un valor comprendido entre 0 y 1 back en 0 y, a continuación, en &ndash;1 y 0 cada 5 segundos, pero con valores que cambian más lentamente cuando el valor es cerca de 1 o -1. Se agrega el valor 1 para que los valores siempre son positivos y, a continuación, se divide entre 2, por lo que los valores comprendido entre ½ y 1 por ½ en 0 por ½, pero más lento cuando el valor es de aproximadamente 1 y 0. Esto se almacena en la `scale` campo y el `SKCanvasView` pierde su validez.

El `PaintSurface` método usa esto `scale` valor para calcular los dos ejes de la elipse:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float maxRadius = 0.75f * Math.Min(info.Width, info.Height) / 2;
    float minRadius = 0.25f * maxRadius;

    float xRadius = minRadius * scale + maxRadius * (1 - scale);
    float yRadius = maxRadius * scale + minRadius * (1 - scale);

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.Color = SKColors.Blue;
        paint.StrokeWidth = 50;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);

        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.SkyBlue;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
    }
}
```

El método calcula un radio máximo en función del tamaño del área de presentación y un radio mínimo tomando como base el radio máximo. El `scale` se anima valor entre 0 y 1 y de vuelta a 0, por lo que el método que usa para calcular un `xRadius` y `yRadius` que oscila entre `minRadius` y `maxRadius`. Estos valores se utilizan para dibujar y rellenar una elipse:

[![](animation-images/pulsatingellipse-small.png "Captura de pantalla triple de la página de elipse parpadeante")](animation-images/pulsatingellipse-large.png#lightbox "Triple captura de pantalla de la página de elipse parpadeante")

Tenga en cuenta que la `SKPaint` objeto se crea en un `using` bloque. Al igual que muchas clases de SkiaSharp `SKPaint` deriva `SKObject`, que deriva de `SKNativeObject`, que implementa el [ `IDisposable` ](https://developer.xamarin.com/api/type/System.IDisposable/) interfaz. `SKPaint` invalida el `Dispose` método para liberar recursos no administrados.

 Colocar `SKPaint` en un `using` bloque garantiza que `Dispose` se llama al final del bloque para liberar estos recursos no administrados. Esto ocurre igualmente cuando la memoria utilizada por el `SKPaint` se libera el objeto por el recolector de elementos no utilizados. NET, pero en el código de animación, es mejor ser proactivo algo al liberar memoria de forma más ordenada.

 Sería una mejor solución en este caso concreto crear dos `SKPaint` objetos una vez y guárdelos como campos.

Precisamente eso es el **círculos expandiendo** does de animación. El [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) clase comienza con la definición de varios campos, incluidos un `SKPaint` objeto:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    const double cycleTime = 1000;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float t;
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke
    };

    public ExpandingCirclesPage()
    {
        Title = "Expanding Circles";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ...
}
```

Este programa utiliza un enfoque diferente para animación tomando como base el Xamarin.Forms `Device.StartTimer`. El `t` campo se anima desde 0 hasta 1 cada `cycleTime` milisegundos:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            t = (float)(stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

El `PaintSurface` controlador dibuja 5 círculos concéntricos con radios animados. Si el `baseRadius` variable se calcula como 100, a continuación, como `t` se anima de 0 a 1, los radios del aumento de cinco círculos de 0 a 100, 100 a 200, 200 a 300, 300 y 400 y 400 y 500. Para la mayoría de los círculos el `strokeWidth` es 50, pero para el primer círculo, la `strokeWidth` anima desde 0 hasta 50. Para la mayoría de los círculos, el color es azul, pero para el círculo último, el color se anima desde azul transparente:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
        float baseRadius = Math.Min(info.Width, info.Height) / 12;

        for (int circle = 0; circle < 5; circle++)
        {
            float radius = baseRadius * (circle + t);

            paint.StrokeWidth = baseRadius / 2 * (circle == 0 ? t : 1);
            paint.Color = new SKColor(0, 0, 255,
                (byte)(255 * (circle == 4 ? (1 - t) : 1)));

            canvas.DrawCircle(center.X, center.Y, radius, paint);
        }
    }
}
```

El resultado es que la imagen tiene la misma cuando `t` es igual a 0 como cuando `t` es igual a 1, y los círculos parecen que siga expandiendo indefinidamente:

[![](animation-images/expandingcircles-small.png "Captura de pantalla triple de la página de expansión círculos")](animation-images/expandingcircles-large.png#lightbox "Triple captura de pantalla de la página de expansión círculos")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
