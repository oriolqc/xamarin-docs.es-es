---
title: "Integración con Xamarin.Forms"
description: "Crear gráficos de SkiaSharp que respondan a entrada táctil y elementos de Xamarin.Forms"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: aa6011c783228848212c633c1c8059c402fdaa3f
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="integrating-with-xamarinforms"></a>Integración con Xamarin.Forms

_Crear gráficos de SkiaSharp que respondan a entrada táctil y elementos de Xamarin.Forms_

Gráficos de SkiaSharp pueden integrarse con el resto de Xamarin.Forms de varias maneras. Puede combinar un lienzo SkiaSharp y Xamarin.Forms elementos en la misma página y los elementos de Xamarin.Forms incluso posición encima de un lienzo de SkiaSharp:

![](integration-images/integrationexample.png "Al seleccionar un color con controles deslizantes")

Otro enfoque para crear gráficos interactivos de SkiaSharp de Xamarin.Forms es a través de la entrada táctil.
La segunda página en la [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programa se titula **pulse rellenar alternar**. Dibuja un círculo simple de dos maneras de &mdash; sin relleno y con un relleno de &mdash; activar o desactivar para una derivación. El [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) clase muestra cómo se pueden modificar SkiaSharp gráficos en respuesta a la entrada del usuario.

Para esta página, el `SKCanvasView` se crea una instancia de clase en el [TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) archivo, lo que también establece una Xamarin.Forms [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) en la vista:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

Observe el `skia` declaración de espacio de nombres XML.

El `Tapped` controlador para la `TapGestureRecognizer` objeto simplemente alterna el valor de un campo booleano y llamadas a la [ `InvalidateSurface` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface()/) método `SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

La llamada a `InvalidateSurface` eficazmente genera una llamada a la `PaintSurface` controlador, que usa el `showFill` campo para rellenar o no de relleno del círculo:

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
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

El `StrokeWidth` propiedad se ha establecido en 50 para resaltar la diferencia. También puede ver el ancho de línea entera dibujando el interior en primer lugar y, a continuación, en el esquema. De forma predeterminada, los gráficos cifras dibujado más adelante en el `PaintSurface` los dibuja anteriormente en el controlador interferir con controlador de eventos.

El **Color explorar** página se muestra cómo se pueden integrar también SkiaSharp gráficos con otros elementos de Xamarin.Forms y también se muestra la diferencia entre los dos métodos alternativos para definir colores en SkiaSharp. El método estático [ `SKColor.FromHsl` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsl/p/System.Single/System.Single/System.Single/System.Byte/) método crea un `SKColor` valor basado en el modelo de saturación de matiz de luminosidad:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

El método estático [ `SKColor.FromHsv` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsv/p/System.Single/System.Single/System.Single/System.Byte/) método crea un `SKColor` valor basado en el modelo de valor de saturación de matiz similar:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

En ambos casos, el `h` intervalos de argumentos entre 0 y 360. El `s`, `l`, y `v` argumentos comprendidos entre 0 y 100. El `a` (alfa o la opacidad) argumento puede oscilar entre 0 y 255.

El [ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) archivo crea dos `SKCanvasView` objetos en un `StackLayout` en paralelo con `Slider` y `Label` vistas que permiten al usuario seleccionar HSL y Valores de color HSV:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Los dos `SKCanvasView` elementos se encuentran en una sola celda `Grid` con un `Label` sentado en la parte superior para mostrar el valor de color RGB resultante.

El [ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) archivo de código subyacente es relativamente sencilla. El recurso compartido `ValueChanged` controlador para las tres `Slider` elementos simplemente invalida ambos `SKCanvasView` elementos. El `PaintSurface` controladores desactive el lienzo con el color indicado por la `Slider` elementos y también establece la `Label` sentado en la parte superior de la `SKCanvasView` elementos:

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

En el HSL y HSV los modelos de color, el valor de matiz comprendido entre 0 y 360 e indica el matiz del color dominante. Estos son los colores del arco iris tradicionales: rojo, naranja, amarillo, verde, azul, indigo, violeta y copia en un círculo rojo.

En el modelo HSL, un valor 0 para la luminosidad siempre es negro y un valor de 100 siempre es blanco. Cuando el valor de saturación es 0, los valores de luminosidad entre 0 y 100 son tonalidades de gris. Al aumentar la saturación, agrega más de color. Colores puros (que son los valores RGB con un componente es igual a 255, otro igual a 0 y el tercer comprendido entre 0 y 255) que se producen cuando la saturación es 100 y la luminosidad es 50.

En el modelo HSV, colores puros como resultado una vez la saturación y el valor 100. Cuando el valor es 0, independientemente de cualquier otro valor, el color es negro. Escala de grises se produce cuando la saturación es 0 y el valor varía entre 0 y 100.

Pero es la mejor manera de tener una idea de los dos modelos experimentar con ellos:

[![](integration-images/colorexplore-large.png "Captura de pantalla triple de la página de Color explorar")](integration-images/colorexplore-small.png#lightbox "Triple captura de pantalla de la página explorar de Color")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
