---
title: Pintar con los dedos
description: Use los dedos para dibujar en el lienzo.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: charlespetzold
ms.author: chape
ms.date: 04/05/2017
ms.openlocfilehash: 95c023d702d165b7a8a0ba392b2f87af58bfae07
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="finger-painting"></a>Pintar con los dedos

_Use los dedos para dibujar en el lienzo._

Un `SKPath` objeto puede actualizar y mostrar continuamente. Esta característica permite una ruta de acceso que se usará para dibujo interactivo, como en un programa finger-painting.

![](finger-paint-images/fingerpaintsample.png "Un ejercicio de pintar con los dedos")

La compatibilidad táctil en Xamarin.Forms no permite el seguimiento individuales dedos en la pantalla, por lo que se ha desarrollado un efecto de seguimiento de entrada táctil de Xamarin.Forms para proporcionar compatibilidad táctil adicionales. Este efecto se describe en el artículo [ **invocar eventos de efectos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). El programa de ejemplo [ **demostraciones de efecto de seguimiento de entrada táctil** ](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/) incluye dos páginas que usan SkiaSharp, incluido un programa finger-painting.

El [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) solución incluye este evento de seguimiento de entrada táctil. El proyecto de biblioteca de clases Portable incluye la `TouchEffect` (clase), el `TouchActionType` enumeración, el `TouchActionEventHandler` delegar y la `TouchActionEventArgs` clase. Cada uno de los proyectos de plataforma incluye un `TouchEffect` clase para esa plataforma; el proyecto de iOS también contiene un `TouchRecognizer` clase.

El **pintura con los dedos** página **SkiaSharpFormsDemos** es una implementación simplificada de pintar con los dedos. No admiten la selección de color o ancho de trazo, no tiene ninguna manera para borrar el lienzo y por supuesto no puede guardar la ilustración.

El [ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml) archivo coloca el `SKCanvasView` en una sola celda `Grid` y adjunta el `TouchEffect` a la `Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

Adjuntar el `TouchEffect` directamente a la `SKCanvasView` no funciona en todas las plataformas.

El [ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml.cs) archivo de código subyacente define dos colecciones para almacenar el `SKPath` objetos, así como un `SKPaint` objeto para representar estas rutas de acceso:

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

Como el nombre sugiere, la `inProgressPaths` diccionario almacena las rutas de acceso que se va a dibujar actualmente por uno o más dedos. La clave del diccionario es el identificador de entrada táctil que acompaña a los eventos de toque. El `completedPaths` campo es una colección de rutas de acceso que se han finalizado cuando un dedo dibujo solucionada la ruta de acceso de la pantalla.

El `TouchAction` controlador administra estas dos colecciones. Cuando un dedo toca la pantalla por primera vez un nuevo `SKPath` se agrega a `inProgressPaths`. Cuando se desplace ese dedo, puntos adicionales se agregan a la ruta de acceso. Cuando se suelta el dedo, la ruta de acceso se transfiere a la `completedPaths` colección. Puede pintar con los dedos varias al mismo tiempo. Después de cada cambio realizado en una de las rutas de acceso o las colecciones, el `SKCanvasView` se invalida:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

Los puntos que acompaña a los eventos de seguimiento de entrada táctil son las coordenadas de Xamarin.Forms; estos se deben convertir a coordenadas de SkiaSharp, que son píxeles. Que es el propósito de la `ConvertToPixel` método.

El `PaintSurface` controlador, a continuación, simplemente representa ambas colecciones de rutas de acceso. Las rutas de acceso anteriores completadas aparecen debajo de las rutas de acceso en curso:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ,,,
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

Los dibujos dedo sólo están limitados por su talent:

[![](finger-paint-images/fingerpaint-small.png "Captura de pantalla triple de la página de pintura con los dedos")](finger-paint-images/fingerpaint-large.png#lightbox "Triple captura de pantalla de la página de pintura con los dedos")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Demostraciones de efecto de seguimiento de entrada táctil (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)
- [Invocación de eventos de efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
