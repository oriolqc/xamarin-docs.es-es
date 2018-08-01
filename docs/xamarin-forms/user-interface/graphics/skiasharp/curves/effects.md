---
title: Efectos de ruta de acceso en SkiaSharp
description: En este artículo se explica varios efectos de ruta de acceso de SkiaSharp que permiten a las rutas de acceso que se usará para trazado y rellenar y se muestra cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: charlespetzold
ms.author: chape
ms.date: 07/29/2017
ms.openlocfilehash: 2071a2fb140d0e9c78d4c86d6aa70d3606dc1f98
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244115"
---
# <a name="path-effects-in-skiasharp"></a>Efectos de ruta de acceso en SkiaSharp

_Detectar varios efectos de ruta de acceso que permiten a las rutas de acceso que se usará para trazado y rellenar_

A *efecto de la ruta de acceso* es una instancia de la [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) clase que se crea con uno de los ocho estático `Create` métodos. El `SKPathEffect` , a continuación, se establece el objeto para el [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) propiedad de un `SKPaint` objeto para una amplia variedad de efectos interesantes, por ejemplo, trazado una línea con una ruta de acceso pequeña replicada:

![](effects-images/patheffectsample.png "El ejemplo de cadena vinculada")

Efectos de ruta de acceso le permiten:

- Trazar una línea con puntos y guiones
- Trazar una línea con cualquier ruta de acceso relleno
- Rellena un área con líneas de trama.
- Rellena un área con una ruta de acceso de mosaico
- Asegúrese de que las esquinas redondeadas vivas
- Agregar aleatorio "vibración" a las líneas y curvas

Además, puede combinar dos o más efectos de ruta de acceso.

Este artículo también muestra cómo utilizar el `GetFillPath` método `SKPaint` para convertir una ruta de acceso en otra ruta de acceso mediante la aplicación de propiedades de `SKPaint`, incluido `StrokeWidth` y `PathEffect`. Esto genera algunas técnicas interesantes, como obtener una ruta de acceso que es un esquema de otra ruta de acceso. `GetFillPath` También es útil en relación con los efectos de la ruta de acceso.

## <a name="dots-and-dashes"></a>Puntos y guiones

El uso de la [ `PathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) método descrito en el artículo [ **puntos y guiones**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). El primer argumento del método es una matriz que contiene un número par de dos o más valores, alternando entre longitudes de guiones y longitudes de los espacios entre los guiones:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Estos valores son *no* en relación con el ancho del trazo. Por ejemplo, si el ancho del trazo es 10, y desea que una línea formada por guiones cuadrados y cuadrados vacíos, establezca el `intervals` matriz {10, 10}. El `phase` argumento indica que en el modelo de guiones comienza la línea. En este ejemplo, si desea que comience con la diferencia de cuadrados la línea, establezca `phase` a 10.

Los extremos de los guiones se ven afectados por la `StrokeCap` propiedad de `SKPaint`. Para el ancho del trazo ancho, es muy común para establecer esta propiedad en `SKStrokeCap.Round` para redondear los extremos de los guiones. En este caso, los valores de la `intervals` realice matriz *no* incluir la longitud extra resultantes de la operación de redondeo, lo que significa que un punto circular requiere especificar un ancho de cero. Para un ancho de trazo de 10, para crear una línea con puntos circulares y espacios entre los puntos del diámetro del mismo, use un `intervals` matriz de {0, 20}.

El **animar texto decimal con punto** página es similar a la **texto que se describen** página descrita en el artículo [ **integrar texto y gráficos** ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) en que muestra que describe los caracteres de texto estableciendo el `Style` propiedad de la `SKPaint` el objeto a `SKPaintStyle.Stroke`. Además, **animar texto decimal con punto** utiliza `SKPathEffect.CreateDash` para conceder a este describir un aspecto con puntos y el programa también anima la `phase` argumento de la `SKPathEffect.CreateDash` método para crear los puntos parece que viaja alrededor del texto caracteres. Ésta es la página en modo horizontal:

[![](effects-images/animateddottedtext-small.png "Captura de pantalla triple de la página de texto animado de números separados por puntos")](effects-images/animateddottedtext-large.png#lightbox "Triple captura de pantalla de la página de texto animado de números separados por puntos")

El [ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) clase comienza por definir algunas constantes y también invalida la `OnAppearing` y `OnDisappearing` métodos para la animación:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
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

El `PaintSurface` controlador comienza creando un `SKPaint` objeto para mostrar el texto. El `TextSize` propiedad se ajusta según el ancho de la pantalla:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds = new SKRect();
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

Hacia el final del método, el `SKPathEffect.CreateDash` es llamar al método mediante el `dashArray` que se define como un campo y el animado `phase` valor. El `SKPathEffect` instancia se establece en el `PathEffect` propiedad de la `SKPaint` objeto para mostrar el texto.

Como alternativa, puede establecer la `SKPathEffect` el objeto a la `SKPaint` objeto antes de medir el texto y centrar en la página. Sin embargo, en ese caso, el animado puntos y guiones provocar alguna variación del tamaño del texto representado y el texto tiende a Vibrar un poco. (¡Pruébelo!)

Observará que como el círculo puntos animado alrededor de los caracteres de texto, hay un punto determinado en cada curva cerrada donde suelen aparecer dentro y fuera de la existencia de los puntos. Esto es donde la ruta de acceso que define el contorno de carácter empieza y termina. Si la longitud de ruta de acceso no es un múltiplo entero de la longitud del modelo de guiones (en este caso 20 píxeles) puede satisfacer sólo parte de ese modelo al final de la ruta de acceso.

Es posible ajustar la longitud del modelo de guiones para ajustarse a la longitud de la ruta de acceso, pero que necesita determinar la longitud de la ruta de acceso, una técnica que se tratan en un artículo futuro.

El **punto / guión Morph** programa anima el mismo patrón de guiones para que parecen guiones dividir en puntos, que se combinan para guiones formulario nuevo:

[![](effects-images/dotdashmorph-small.png "Captura de pantalla triple de la página punto guión Morph")](effects-images/dotdashmorph-large.png#lightbox "Triple captura de pantalla de la página Morph de guión punto")

El [ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) clase invalidaciones el `OnAppearing` y `OnDisappearing` métodos al igual que el programa anterior, pero debe define la clase la `SKPaint` objeto como un campo:

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

El `PaintSurface` controlador crea una ruta de acceso elíptico en función del tamaño de la página y ejecuta una sección larga de código que establece la `dashArray` y `phase` variables. Como la variable animada `t` varía entre 0 y 1, el `if` bloques dividen ese tiempo en cuatro trimestres y en cada uno de los trimestres, `tsub` también comprendido entre 0 y 1. Al final, el programa crea la `SKPathEffect` y lo establece en el `SKPaint` objeto para dibujar.

## <a name="from-path-to-path"></a>Desde la ruta de acceso a la ruta de acceso

El [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) método `SKPaint` convierte una ruta de acceso en otra basándose en los valores de la `SKPaint` objeto. Para ver cómo funciona esto, reemplace el `canvas.DrawPath` llamar en el programa anterior con el código siguiente:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

En este código nuevo, el `GetFillPath` llamar convierte el `ellipsePath` (que es simplemente una elipse) en `newPath`, que, a continuación, se muestra con `newPaint`. El `newPaint` objeto se crea con todos los valores de propiedad predeterminados salvo que la `Style` propiedad se establece en función con el valor booleano devuelve el valor de `GetFillPath`.

Los objetos visuales son idénticos salvo por el color, que se establece en `ellipsePaint` pero no `newPaint`. En lugar de la elipse simple definida en `ellipsePath`, `newPath` contiene numerosos contornos de ruta de acceso que definen la serie de puntos y guiones. Esto es el resultado de aplicar distintas propiedades de `ellipsePaint` : `StrokeWidth`, `StrokeCap`, y `PathEffect` : a `ellipsePath` y contar con la ruta de acceso resultante de `newPath`. El `GetFillPath` método devuelve un valor booleano que indica si la ruta de acceso de destino es que se debe satisfacer o no; en este ejemplo, el valor devuelto es `true` para rellenar la ruta de acceso.

Intente cambiar el `Style` en `newPaint` a `SKPaintStyle.Stroke` y verá los contornos individuales de la ruta de acceso que se describen con una ancho de píxel de una línea.

## <a name="stroking-with-a-path"></a>Trazado con una ruta de acceso

El [ `SKPathEffect.Create1DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create1DPath/p/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPath1DPathEffectStyle/) método es conceptualmente similar a `SKPathEffect.CreateDash` salvo que especifique una ruta de acceso en lugar de un patrón de guiones y espacios. Esta ruta de acceso se replica varias veces en el trazo de la línea o curva.

La sintaxis es la siguiente:

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

> [!IMPORTANT]
> Esté atento a: hay una sobrecarga de `Create1DPath` que se define con un argumento de enumeración de tipo `SkPath1DPathEffect` con una minúscula "k". Este nombre es un error y, por consiguiente que enumeración y método están en desuso, pero es muy fácil para el método en desuso se convierta en parte del código y es difícil ver exactamente qué es incorrecto.

En general, la ruta de acceso que se pasa a `Create1DPath` será pequeño y centrado alrededor del punto (0, 0). El `advance` parámetro indica la distancia entre los centros de ruta de acceso como la ruta de acceso se replica en la línea. Normalmente, se establece este argumento en el ancho aproximado de la ruta de acceso. El `phase` desempeña argumento el mismo rol aquí tal como se hace en el `CreateDash` método.

El [ `SKPath1DPathEffectStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath1DPathEffectStyle/) tiene tres miembros:

- [`Translate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Translate/)
- [`Rotate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Rotate/)
- [`Morph`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Morph/)

El `Translate` miembro hace que la ruta de acceso que se mantiene en la misma orientación tal y como se ha replicado a lo largo de una línea o una curva. Para `Rotate`, la ruta de acceso se gira en función de una tangente de la curva. La ruta de acceso tiene su orientación normal para las líneas horizontales. `Morph` es similar a `Rotate` salvo que la ruta de acceso también se curva para que coincida con la curvatura de la línea que se traza.

El **efecto de ruta de acceso de D 1** página muestra estas tres opciones. El [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) archivo define un selector que contiene tres elementos que corresponden a los tres miembros de la enumeración:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Translate</x:String>
                <x:String>Rotate</x:String>
                <x:String>Morph</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

El [ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) archivo de código subyacente define tres `SKPathEffect` objetos como campos. Estos se crean usando `SKPathEffect.Create1DPath` con `SKPath` objetos creados mediante `SKPath.ParseSvgPathData`. El primero es un sencillo cuadro, el segundo es una forma de rombo y el tercero es un rectángulo. Se utilizan para mostrar los estilos de tres efecto:

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch (effectStylePicker.Items[effectStylePicker.SelectedIndex])
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

El `PaintSurface` controlador crea una curva de Bézier que itera sobre sí mismo y tiene acceso el selector para determinar qué `PathEffect` debe utilizarse para trazarlo. Las tres opciones: `Translate`, `Rotate`, y `Morph` , se muestran de izquierda a derecha:

[![](effects-images/1dpatheffect-small.png "Captura de pantalla triple de la página de efecto de la ruta de acceso de 1D")](effects-images/1dpatheffect-large.png#lightbox "Triple captura de pantalla de la página de efecto de la ruta de acceso de 1D")

La ruta de acceso especificada en el `SKPathEffect.Create1DPath` método siempre se rellena. La ruta de acceso especificada en el `DrawPath` método siempre se traza si el `SKPaint` objeto tiene sus `PathEffect` propiedad establecida en un efecto de la ruta de acceso de 1D. Tenga en cuenta que la `pathPaint` objeto no tiene ningún `Style` configuración, que normalmente tiene como valor predeterminado `Fill`, pero la ruta de acceso se traza independientemente.

El cuadro que se utiliza en el `Translate` ejemplo es 20 píxeles cuadrados y la `advance` argumento se establece en 24. Esta diferencia hace que un espacio entre los cuadros cuando la línea es aproximadamente horizontal o vertical, pero los cuadros se superponen un poco cuando la línea se diagonal porque la diagonal del cuadro es 28,3 píxeles.

La forma de rombo en el `Rotate` ejemplo también es 20 píxeles de ancho. El `advance` se establece en 20 para que los puntos seguirán touch como el rombo se gira junto con la curvatura de la línea.

La forma de rectángulo en el `Morph` ejemplo es 50 píxeles de anchos, con un `advance` configuración de 55 para realizar un pequeño espacio entre los rectángulos como que están doblados alrededor de la curva de Bézier.

Si el `advance` argumento es menor que el tamaño de la ruta de acceso, a continuación, se pueden superponer las rutas de acceso duplicadas. Esto puede dar lugar a que algunos efectos interesantes. El **cadena vinculado** página muestra una serie de círculos que parezcan similares a una cadena vinculada, que se bloquea en la forma distintivo de un catenaria superpuestos:

[![](effects-images/linkedchain-small.png "Captura de pantalla triple de la página de cadena vinculado")](effects-images/linkedchain-large.png#lightbox "Triple captura de pantalla de la página de cadena vinculada")

Buscar muy próxima y verá que estos no son realmente círculos. Cada vínculo de la cadena es dos arcos, tamaño y se colocan por lo que parece a conectarse con vínculos adyacente.

Una cadena o un cable de distribución de pesos uniforme se bloquea en forma de un catenaria. Una arquitectura integrada en el formulario de un catenaria invertido se beneficia de una distribución equitativa de presión desde el peso de un arco. La catenaria tiene una descripción matemática aparentemente simple:

y = un · COSH(x / a)

El *cosh* es la función de coseno hiperbólico. Para *x* igual a 0, *cosh* es cero y *y* es igual a *un*. Representa el centro de la catenaria. Al igual que el *coseno* función, *cosh* se dice *incluso*, lo que significa que *cosh(–x)* es igual a *cosh(x)*, y valores se incrementan para aumentar argumentos positivos o negativos. Estos valores describen las curvas que forman los lados de la catenaria.

Buscar el valor adecuado de *un* para ajustarse a la catenaria a las dimensiones de la página del teléfono no es un cálculo directo. Si *w* y *h* son el ancho y alto de un rectángulo, el valor óptimo de *un* satisface la siguiente ecuación:

COSH (w/2/a) = 1 + h / a

El método siguiente en la [ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) clase incorpora ese igualdad haciendo referencia a las dos expresiones de la izquierda y derecha del signo igual como `left` y `right`. Para valores pequeños de *una*, `left` es mayor que `right`; para valores grandes de *una*, `left` es menor que `right`. El `while` bucle se reduce en un valor óptimo de *un*:

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

El `SKPath` los vínculos que se cree en el constructor de clase y los resultantes del objeto `SKPathEffect` , a continuación, se establece el objeto para el `PathEffect` propiedad de la `SKPaint` objeto que se almacena como un campo:

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

El trabajo principal de la `PaintSurface` controlador consiste en crear una ruta de acceso para la catenaria propio. Después de determinar la óptima *una* y almacenarla en la `optA` variable, que también necesite calcule un desplazamiento de la parte superior de la ventana. A continuación, puede acumular una colección de `SKPoint` los valores de la catenaria, que convertir en una ruta de acceso y dibujar la ruta de acceso con el que se han creado previamente `SKPaint` objeto:

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

Este programa define la ruta de acceso utilizada en `Create1DPath` tener sus (0, 0) punto en el centro. Esto parece razonable porque el (0, 0) punto de la ruta de acceso está alineado con la línea o curva que lo es etiquetar. Sin embargo, puede usar un no centrado en (0, 0) punto para algunos efectos especiales.

El **cinta transportadora** página crea una ruta de acceso similar a una banda transportadora rectangulares con una curva superior e inferior que se este tamaño se ajusta a las dimensiones de la ventana. Dicha ruta de acceso se traza con un sencillo `SKPaint` objeto 20 píxeles de ancho y color gris y, a continuación, se traza nuevo con otro `SKPaint` objeto con un `SKPathEffect` objetos que hacen referencia a una ruta de acceso similar a un depósito poco:

[![](effects-images/conveyorbelt-small.png "Captura de pantalla triple de la página de la cinta transportadora")](effects-images/conveyorbelt-large.png#lightbox "Triple captura de pantalla de la página de la cinta transportadora")

El (0, 0) punto de la ruta de acceso de depósitos es el identificador, por lo que cuando el `phase` argumento está animado, parecen que los depósitos giran en torno a la cinta transportadora, quizás scooping de agua en la parte inferior y el volcado horizontal en la parte superior.

El [ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) clase implementa animación con las invalidaciones de la `OnAppearing` y `OnDisappearing` métodos. La ruta de acceso para el cubo se define en el constructor de la página:

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

El código de creación de depósitos completa con dos transformaciones que hacen que el cubo un poco más grande y activarlo hacia un lado. Aplicar estas transformaciones era más fácil que ajustar todas las coordenadas en el código anterior.

El `PaintSurface` controlador comienza con la definición de una ruta de acceso de la cinta transportadora propio. Esto es simplemente un par de líneas y un par de comas círculos que se dibujan con una línea gris oscuro de 20 píxeles de ancho:

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect =
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase,
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

La lógica para dibujar la cinta transportadora no funciona en modo horizontal.

Los cubos se deben uniformemente sobre separadas en la cinta transportadora de 200 píxeles. Sin embargo, la cinta transportadora probablemente no es un múltiplo de 200 píxeles largos, lo que significa que, como el `phase` argumento de `SKPathEffect.Create1DPath` está animando, depósitos aparecerá dentro y fuera de existencia.

Por este motivo, el programa en primer lugar calcula un valor denominado `length` que es la longitud de la cinta transportadora. Dado que la cinta transportadora consta de líneas rectas y círculos punto y, esto es un cálculo simple. A continuación, el número de depósitos se calcula dividiendo `length` por 200. Esto se redondea al entero más próximo, y que número es, a continuación, dividen en `length`. El resultado es un espacio para un número entero de depósitos. El `phase` argumento es simplemente una fracción de ese.

## <a name="from-path-to-path-again"></a>Desde la ruta de acceso a la ruta de acceso del nuevo

En la parte inferior de la `DrawSurface` controlador en **cinta transportadora**, comente la `canvas.DrawPath` llamar y reemplácelo por el código siguiente:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Al igual que con el ejemplo anterior de `GetFillPath`, verá que los resultados son iguales, salvo el color. Después de ejecutar `GetFillPath`, la `newPath` objeto contiene varias copias de la ruta de acceso de depósitos, cada uno de ellos situado en el mismo lugar que la animación los coloca en el momento de la llamada.

## <a name="hatching-an-area"></a>Un área de sombreado

El [ `SKPathEffect.Create2DLines` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DLine/p/System.Single/SkiaSharp.SKMatrix/) método rellena un área con líneas paralelas, a menudo denominado *líneas de sombreado*. El método tiene la siguiente sintaxis:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

El `width` argumento especifica el ancho del trazo de las líneas de trama. El `matrix` parámetro es una combinación de rotación de escala y opcional. El factor de escala indica el incremento de píxeles que Skia se usa para las líneas de trama de espacio. La separación entre las líneas es el factor de escala menos el `width` argumento. Si el factor de escala es menor o igual que el `width` valor, no habrá ningún espacio entre las líneas de trama y aparece el área que se rellenará. Especifique el mismo valor para el escalado horizontal y vertical.

De forma predeterminada, las líneas de trama son horizontales. Si el `matrix` parámetro contiene rotación, las líneas de trama rotan hacia la derecha.

El **relleno de trama** página muestra este efecto de la ruta de acceso. El [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) clase define tres efectos de ruta de acceso como campos, la primera para las líneas de trama horizontal con un ancho de 3 píxeles con un factor de escala que indica que están espaciados 6 píxeles. Por lo tanto, la separación entre las líneas es 3 píxeles. El segundo efecto de ruta de acceso es para las líneas de trama vertical con un ancho de 6 píxeles con un espaciado 24 píxeles (por lo que la separación es 18 píxeles), y el tercero es Sombreado diagonal líneas 12 espaciados anchos 36 píxeles separados.

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6,
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12,
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Tenga en cuenta la matriz `Multiply` método. Dado que los factores de escala horizontales y verticales son los mismos, no importa el orden en el que se multiplican las matrices de ajuste de escala y la rotación.

El `PaintSurface` controlador utiliza estos efectos de ruta de tres acceso con tres colores diferentes en combinación con `fillPaint` para rellenar un rectángulo redondeado cambiará de tamaño para ajustarse a la página. El `Style` propiedad establecida en `fillPaint` se omite; cuando el `SKPaint` objeto incluye un efecto de la ruta de acceso creado a partir de `SKPathEffect.Create2DLine`, el área se rellena a pesar de todo:

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

Si observa detenidamente los resultados, verá que las líneas de trama rojo y azul no están delimitadas con precisión al rectángulo redondeado. (Esto aparentemente es una característica del código subyacente Skia.) Si esto no es satisfactoria, se muestra un enfoque alternativo para las líneas de sombreado diagonal en verde: el rectángulo redondeado se utiliza como un trazado de recorte y las líneas de trama se dibujan en la página completa.

El `PaintSurface` controlador finaliza con una llamada a trazar simplemente el rectángulo redondeado, para que pueda ver la discrepancia con las líneas de trama rojo y azul:

[![](effects-images/hatchfill-small.png "Captura de pantalla triple de la página de relleno de trama")](effects-images/hatchfill-large.png#lightbox "Triple captura de pantalla de la página de relleno de trama")

La pantalla Android realmente no ser como esta: el escalado de la captura de pantalla ha provocado la líneas rojas finas y finos espacios se consolidan en líneas rojas aparentemente más ancho y espacios más amplio.

## <a name="filling-with-a-path"></a>Relleno con una ruta de acceso

El [ `SKPathEffect.Create2DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DPath/p/SkiaSharp.SKMatrix/SkiaSharp.SKPath/) en vigor permite rellenar un área con una ruta de acceso que se replica horizontal y verticalmente, disponer en mosaico el área:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

El `SKMatrix` factores de escala indican el espaciado horizontal y vertical de la ruta de acceso duplicada. Pero no se pueden girar la ruta de acceso con este `matrix` argumento; si desea que la ruta de acceso que se vayan a rotar, girar la ruta de acceso mediante el `Transform` método definido por `SKPath`.

La ruta de acceso replicada normalmente está alineado con los bordes superiores e izquierdos de la pantalla en lugar de en el área que se va a rellenar. Puede invalidar este comportamiento proporcionando factores de traducción entre 0 y los factores de escala para especificar el desplazamiento horizontal y vertical de los lados izquierdos y superiores.

El **relleno de ruta de acceso del icono** página muestra este efecto de la ruta de acceso. La ruta de acceso que se utiliza para colocar en mosaico del área se define como un campo en el [ `PathFileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) clase. La coordenadas horizontales y verticales comprendidos entre -40 a 40, lo que significa que esta ruta de acceso es 80 píxeles cuadrados:

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " +
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " +
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50),
                    100, 100, paint);
            }
        }
    }
}
```

En el `PaintSurface` controlador, el `SKPathEffect.Create2DPath` llamadas establece el espaciado horizontal y vertical en 64 para hacer que los iconos cuadrados 80 píxeles se superpongan. Afortunadamente, la ruta de acceso es similar a una pieza del rompecabezas, meshing perfectamente con contigua iconos:

[![](effects-images/pathtilefill-small.png "Captura de pantalla triple de la página relleno de mosaico de ruta de acceso")](effects-images/pathtilefill-large.png#lightbox "Triple captura de pantalla de la página relleno de mosaico de ruta de acceso")

El escalado de la captura de pantalla original hace algo de distorsión, especialmente en la pantalla de Android.

Observe que estos iconos siempre aparecen todo y nunca se truncarán. En las primeras dos capturas de pantalla, no es incluso evidente que el área que se está rellenando es un rectángulo redondeado. Si desea truncar estos iconos a un área determinada, utilice un trazado de recorte.

Pruebe a establecer el `Style` propiedad de la `SKPaint` el objeto a `Stroke`, y verá los iconos individuales que se describen en lugar de rellenado.

## <a name="rounding-sharp-corners"></a>Redondear esquinas

El **redondea Heptágono** programa se presenta en el [ **tres maneras para dibujar un arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) artículo permite que un arco tangente curva los puntos de una figura siete lados. El **Heptágono otra redondea** página muestra un método mucho más sencillo que usa un efecto de la ruta de acceso creado a partir de la [ `SKPathEffect.CreateCorner` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCorner/p/System.Single/) método:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Aunque el argumento único se denomina `radius` debe establecer en la mitad del radio de redondeo deseado. (Esto es una característica del código Skia subyacente).

Este es el `PaintSurface` controlador en el [ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) clase:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

Puede utilizar este efecto con el trazado o llene tomando como base la `Style` propiedad de la `SKPaint` objeto. Aquí está en las tres plataformas:

[![](effects-images/anotherroundedheptagon-small.png "Captura de pantalla triple de la página de otro Heptágono redondea")](effects-images/anotherroundedheptagon-large.png#lightbox "Triple captura de pantalla de la página de otro Heptágono redondeado")

Verá que este Heptágono redondeado es idéntico al programa anterior. Si necesita más convincente que el radio de redondeo realmente es 100, en lugar de los 50 especificado en el `SKPathEffect.CreateCorner` llamada, puede quite la instrucción final en el programa y vea un círculo de radio de 100 superpuesto a la esquina.

## <a name="random-jitter"></a>Vibración aleatoria

A veces las líneas rectas perfecta de gráficos para PC no son bastante la acción que realizará y se desea aleatoriedad un poco. En ese caso, debe tener probar el [ `SKPathEffect.CreateDiscrete` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDiscrete/p/System.Single/System.Single/System.UInt32/) método:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Puede usar este efecto de la ruta de acceso de trazado o rellenar. Las líneas se dividen en segmentos conectados: la longitud aproximada de los cuales se especifica por `segLength` y ampliar en direcciones diferentes. Especifica la extensión de la desviación de la línea original `deviation`.

El argumento final es un valor de inicialización usado para generar la secuencia pseudoaleatoria usada para el efecto. El efecto de vibración tendrá un aspecto un poco diferente para distintos valores de inicialización. El argumento tiene un valor predeterminado de cero, lo que significa que el efecto es el mismo cada vez que ejecuta el programa. Si desea vibración diferente cada vez que se actualiza la pantalla, puede establecer el valor de inicialización la `Millisecond` propiedad de un `DataTime.Now` valor (por ejemplo).


El **vibración experimentar** página le permite experimentar con distintos valores de trazado de un rectángulo:

[![](effects-images/jitterexperiment-small.png "Captura de pantalla de la página de vibración experimento de triple")](effects-images/jitterexperiment-large.png#lightbox "Triple screenshot of the JitterExperiment page")

El programa es usase. El [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) archivo crean instancias de dos `Slider` elementos y un `SKCanvasView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

El `PaintSurface` controlador en el [ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) se denomina archivo de código subyacente siempre que sea un `Slider` cambia el valor. Llama `SKPathEffect.CreateDiscrete` utilizando los dos `Slider` valores y lo usa para trazar un rectángulo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Puede utilizar este efecto con el que rellenar, en cuyo caso el contorno del área de relleno está sujeto a estas desviaciones aleatorias. El **texto vibración** página muestra cómo utilizar este efecto de la ruta de acceso para mostrar el texto. La mayoría del código de la `PaintSurface` controlador de la [ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) clase se dedica a ajustar el tamaño y centrar el texto:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

A continuación se ejecuta en modo horizontal en las tres plataformas:

[![](effects-images/jittertext-small.png "Captura de pantalla de la página de texto vibración de triple")](effects-images/jittertext-large.png#lightbox "Triple screenshot of the JitterText page")

## <a name="path-outlining"></a>Esquematización de la ruta de acceso

Ya ha visto dos ejemplos poco de la [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) método `SKPaint`, que también existe en un [sobrecarga](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/):

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale)
```

Solo se necesitan los dos primeros argumentos. El método tiene acceso a la ruta de acceso al que hace referencia el `src` argumento, modifica los datos de ruta de acceso en función de las propiedades de trazo en el `SKPaint` objeto (incluido el `PathEffect` propiedad) y, a continuación, escribe los resultados en la `dst` ruta de acceso. El `resScale` parámetro permite reducir la precisión para crear una ruta de acceso de destino más pequeño y el `cullRect` argumento puede eliminar perfiles fuera de un rectángulo.

Un uso básico de este método no implica en todos los efectos de la ruta de acceso. Si el `SKPaint` objeto tiene sus `Style` propiedad establecida en `SKPaintStyle.Stroke`y *no* tiene su `PathEffect` establecido, la acción `GetFillPath` crea una ruta de acceso que representa un *esquema*de la ruta de acceso de origen como si hubiera sido trazado por las propiedades de paint.

Por ejemplo, si la `src` ruta de acceso es un círculo simple del radio de 500 y el `SKPaint` objeto especifica un ancho del trazo de 100, el `dst` ruta de acceso se convierte en dos círculos concéntricos, uno con un radio de 450 y otra con un radio de 550. Se llama al método `GetFillPath` porque rellenar este `dst` ruta de acceso es el mismo que el trazado el `src` ruta de acceso. Pero también puede trazar el `dst` ruta de acceso para ver los contornos de ruta de acceso.

El **pulse al esquema de la ruta de acceso** se muestra cómo hacerlo. El `SKCanvasView` y `TapGestureRecognizer` se crean instancias en el [ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) archivo. El [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) archivo de código subyacente define tres `SKPaint` objetos como campos, dos para trazado con trazar anchos de 100 y 20 y el tercero para el llenado:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

Si no se ha pulsado la pantalla, la `PaintSurface` controlador utiliza la `blueFill` y `redThickStroke` pintar objetos para representar una ruta de acceso circular:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) -
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

El círculo se llena y se traza tal como se esperaría:

[![](effects-images/taptooutlinethepathnormal-small.png "Captura de pantalla triple de la página de puntear en el trazado del contorno normal")](effects-images/taptooutlinethepathnormal-large.png#lightbox "Triple captura de pantalla de la página de puntear en el trazado del contorno normal")

Al puntear en la pantalla, `outlineThePath` está establecido en `true`y el `PaintSurface` controlador crea un nuevo `SKPath` objeto y lo usa como la ruta de acceso de destino en una llamada a `GetFillPath` en la `redThickStroke` objeto de paint. A continuación, se llena y se traza con esa ruta de acceso de destino `redThinStroke`, da lugar a lo siguiente:

[![](effects-images/taptooutlinethepathoutlined-small.png "Captura de pantalla triple de la página de puntear en el trazado del contorno contorno")](effects-images/taptooutlinethepathoutlined-large.png#lightbox "Triple captura de pantalla de la página de puntear en el trazado del contorno contorno")

Los dos círculos rojos indican claramente que la ruta de acceso circular original se ha convertido en dos contornos circulares.

Este método puede ser muy útil para el desarrollo de las rutas de acceso que se utilizará para el `SKPathEffect.Create1DPath` método. Las rutas de acceso que especifique en estos métodos siempre se rellenan cuando se replican las rutas de acceso. Si no desea que la ruta de acceso completa que se debe rellenar, debe definir con cuidado los contornos.

Por ejemplo, en la **cadena vinculado** ejemplo, los vínculos se definieron con una serie de cuatro arcos, cada par de los cuales se basaban en dos radii describir el área de la ruta de acceso que se debe rellenar. Es posible reemplazar el código en el `LinkedChainPage` clase para realizar de forma ligeramente diferente.

En primer lugar, querrá volver a definir la `linkRadius` constante:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

El `linkPath` ahora solo dos arcos en función de ese único radius, con lo que se desea iniciar ángulos y ángulos de barrido es:

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

El `outlinePath` objeto es, a continuación, el destinatario del contorno de `linkPath` cuando se traza con las propiedades especificadas en `strokePaint`.

Otro ejemplo de cómo utilizar esta técnica viene a continuación para la ruta de acceso utilizada en un `SKPathEffect.Create2DPath` métodos.

## <a name="combining-path-effects"></a>Combinando los efectos de ruta de acceso

Los dos métodos de creación estáticos final de `SKPathEffect` son [ `SKPathEffect.CreateSum` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateSum/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/) y [ `SKPathEffect.CreateCompose` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCompose/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Estos métodos de combinan dos efectos de ruta de acceso para crear un efecto de trazado compuesto. El `CreateSum` método crea un efecto de ruta de acceso que es similar a los efectos de dos de la ruta de acceso que se aplica por separado, mientras que `CreateCompose` se aplica un efecto de la ruta de acceso (la `inner`) y, a continuación, se aplica el `outer` a la.

Ya ha visto cómo la `GetFillPath` método `SKPaint` puede convertir una ruta de acceso a otra ruta de acceso en función de `SKPaint` propiedades (incluidos `PathEffect`) por lo que no debe ser *demasiado* misteriosas cómo un `SKPaint`objeto puede realizar esa operación dos veces con los efectos de dos de la ruta de acceso especificados en el `CreateSum` o `CreateCompose` métodos.

Un uso obvio de `CreateSum` consiste en definir un `SKPaint` objeto que rellena una ruta de acceso con efecto de una ruta de acceso y trazos de la ruta de acceso con otro efecto de la ruta de acceso. Esto se muestra en el **Cats en marco** ejemplo, que muestra una matriz de gatos dentro de un marco con bordes festoneados:

[![](effects-images/catsinframe-small.png "Captura de pantalla triple de la página de gatos en marco")](effects-images/catsinframe-large.png#lightbox "Triple captura de pantalla de la página de gatos de marco")

El [ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) clase comienza con la definición de varios campos. Puede reconocer el primer campo de la [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) clase desde el [ **datos de ruta de acceso de SVG** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) artículo. Segunda ruta de acceso se basa en una línea y un arco para el patrón de Festonear del marco de:

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath =
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

El `catPath` podría usarse en el `SKPathEffect.Create2DPath` método si la `SKPaint` objeto `Style` propiedad está establecida en `Stroke`. Sin embargo, si la `catPath` se utiliza directamente en este programa, a continuación, el encabezado completo de la cat se completará y las patillas de un incluso no estará visibles. (¡Pruébelo!) Es necesario obtener el esquema de dicha ruta de acceso y utilizar dicho esquema en el `SKPathEffect.Create2DPath` método.

El constructor realiza este trabajo. Aplican primero dos transformaciones que se deben `catPath` para mover el (0, 0) señala el centro y reducir la escala de tamaño. `GetFillPath` Obtiene todos los contornos de los contornos de `outlinedCatPath`, y ese objeto se utiliza en el `SKPathEffect.Create2DPath` llamar. El ajuste de escala en factores el `SKMatrix` valor están ligeramente mayor que la horizontal y el tamaño vertical de la cat para proporcionar un búfer pequeño entre los iconos, aunque los factores de traducción estaban deriva un poco empíricamente para que un gato completo está visible en el esquina superior izquierda del marco de:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect =
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

A continuación, llama al constructor `SKPathEffect.Create1DPath` para el marco festoneado. Observe que el ancho de la ruta de acceso es 100 píxeles, pero el avance es 75 píxeles para que se superpone sobre la ruta de acceso replicada alrededor del marco. La instrucción final de las llamadas de constructor `SKPathEffect.CreateSum` para combinar los efectos de dos rutas y establezca el resultado en la `SKPaint` objeto.

Todo este trabajo permite la `PaintSurface` controlador sea muy sencillo. Solo es necesario definir un rectángulo y dibujar mediante `framePaint`:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

Los algoritmos detrás de los efectos de la ruta de acceso siempre hacen que la ruta de acceso completa usa para que rellenar o trazado para mostrarse, que puede hacer que algunos elementos visuales que aparezca fuera del rectángulo. El `ClipRect` llamadas anteriores a la `DrawRect` llamada permite que los objetos visuales a resultar mucho más limpio. (Pruebe sin recorte).

Es habitual usar `SKPathEffect.CreateCompose` para agregar cierta vibración a otro efecto de la ruta de acceso. Por supuesto, puede experimentar por su cuenta, pero este es un ejemplo ligeramente distinto:

El **líneas discontinuas de trama** rellena una elipse con líneas de trama que son discontinuas. La mayoría del trabajo de la [ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) clase se lleva a cabo directamente en las definiciones de campo. Estos campos definen un efecto de guión y un efecto de trama. Se definen como `static` ya que, a continuación, se hace referencia en un `SKPathEffect.CreateCompose` llamar a en el `SKPaint` definición:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect =
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60),
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

El `PaintSurface` controlador necesario para que contenga sólo la sobrecarga estándar además de una llamada a `DrawOval`:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2,
                        0.45f * info.Width, 0.45f * info.Height,
                        paint);
    }
    ...
}
```

Como ya habrá encontrado, no están restringidas con precisión al interior del área de las líneas de trama y en este ejemplo, siempre empiezan a la izquierda con un guión en todo:

[![](effects-images/dashedhatchlines-small.png "Captura de pantalla triple de la página de líneas discontinuas de trama")](effects-images/dashedhatchlines-large.png#lightbox "Triple captura de pantalla de la página de líneas discontinuas de trama")

Ahora que ha visto los efectos de ruta de acceso que van desde simples puntos y guiones para combinaciones extraños, use su imaginación y vea lo que puede crear.



## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
