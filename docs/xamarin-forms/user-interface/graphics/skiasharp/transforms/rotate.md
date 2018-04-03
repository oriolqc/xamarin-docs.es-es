---
title: La transformación de giro
description: Explorar los efectos y animaciones posibles con la transformación de giro SkiaSharp
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: e3e9c42fafc9a3d139a90bda6289260c5f3a667b
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="the-rotate-transform"></a>La transformación de giro

_Explorar los efectos y animaciones posibles con la transformación de giro SkiaSharp_

Con la transformación de giro, objetos de gráficos SkiaSharp interrupción libres de la restricción de alineación con los ejes horizontales y verticales:

![](rotate-images/rotateexample.png "Texto girado alrededor de un centro")

Para girar un objeto gráfico alrededor del punto (0, 0), SkiaSharp admite tanto una [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/) método y un [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/) método:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Un círculo de 360 grados es el mismo que 2π radianes, por lo que resulta fácil convertir entre las dos unidades. Usar el que sea conveniente. Todas las funciones trigonométricas en el método estático [ `Math` ](https://developer.xamarin.com/api/type/System.Math/) clase usar unidades de radianes.

Rotación se lleva a la derecha para aumentar la ángulos. (Aunque la rotación en el sistema de coordenadas cartesiano es hacia la izquierda por convención, giro hacia la derecha es coherente con coordenadas Y así aumentar va hacia abajo). Negativo ángulos y ángulos mayores que se permiten 360 grados.

Las fórmulas de transformación de giro son más complejas que los de traducir y escalado. Para un ángulo de α, las fórmulas de transformación son:

x' = x•cos(α) – y•sin(α)   

y` = x•sin(α) + y•cos(α)

El **girar básica** página muestra el `RotateDegrees` método. El [ `BasicRotate.xaml.cs` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) archivo muestra parte del texto con su línea de base que se centra en la página y gira según un `Slider` con un intervalo de -360 a 360. Aquí es la parte pertinente de la `PaintSurface` controlador:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Dado que rotación se centra en la esquina superior izquierda del lienzo, para la mayoría de ángulos en este programa, se gira el texto fuera de la pantalla:

[![](rotate-images/basicrotate-small.png "Captura de pantalla triple de la página girar básica")](rotate-images/basicrotate-large.png#lightbox "Triple captura de pantalla de la página girar básica")

Muy a menudo querrá girar algo se centra en torno a un punto de pivote especificada mediante estas versiones de la [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/System.Single/System.Single/) y [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/System.Single/System.Single/) métodos:

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

El **centrado girar** página es exactamente igual que el **girar básica** salvo que la versión expandida de la `RotateDegrees` se usa para establecer el centro de rotación en el mismo punto que se usa para colocar el texto:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Ahora el texto gira alrededor del punto que se usa para colocar el texto, que es el centro horizontal de la línea de base del texto:

[![](rotate-images/centeredrotate-small.png "Captura de pantalla triple de la página centrado girar")](rotate-images/centeredrotate-large.png#lightbox "Triple captura de pantalla de la página centrado girar")

Al igual que con la versión centradas en aspectos de la `Scale` /método siguiente, la versión centradas en aspectos de la `RotateDegrees` llamada es un método abreviado:

```csharp
RotateDegrees (degrees, px, py);
```

Es equivalente a la siguiente:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Descubrirá que a veces puede combinar `Translate` llama con `Rotate` llamadas. Por ejemplo, estos son los `RotateDegrees` y `DrawText` llama el **centrado girar** página;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

El `RotateDegrees` llamada es equivalente a dos `Translate` llamadas y no centrado `RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

El `DrawText` llamada para mostrar texto en una ubicación concreta es equivalente a un `Translate` llamar para esa ubicación seguida `DrawText` en el punto (0, 0):

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

Los dos consecutivos `Translate` llamadas cancelan entre sí:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

Conceptualmente, las dos transformaciones se aplican en el orden opuesto a cómo aparecen en el código. El `DrawText` llamada muestra el texto en la esquina superior izquierda del lienzo. El `RotateDegrees` llamada gira ese texto con respecto a la esquina superior izquierda. La `Translate` llamada mueve el texto en el centro del lienzo.

Normalmente, hay varias maneras para combinar la rotación y la traslación. El **texto girado** página crea la siguiente pantalla:

[![](rotate-images/rotatedtext-small.png "Captura de pantalla triple de la página de texto girado")](rotate-images/rotatedtext-large.png#lightbox "Triple captura de pantalla de la página de texto girado")

Este es el `PaintSurface` controlador de la [ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) clase:

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

El `xCenter` y `yCenter` valores indican el centro del lienzo. El `yText` valor es un poco de desplazamiento de la. Esto indica la coordenada Y que es necesaria colocar el texto para que quede centrado realmente verticalmente en la página. El `for` bucles, a continuación, establece un giro centrado en el centro del lienzo. La rotación es en incrementos de 30 grados. El texto se dibuja utilizando la `yText` valor. El número de espacios en blanco delante de la palabra "gira" en la `text` valor determinó empíricamente para realizar la conexión entre estas cadenas de 12 texto parece ser un dodecagon.

Una manera de simplificar este código es incrementar el ángulo de giro de 30 grados cada vez que el bucle, después de la `DrawText` llamar. Esto elimina la necesidad de llamadas a `Save` y `Restore`. Tenga en cuenta que la `degrees` ya no se utiliza la variable dentro del cuerpo de la `for` bloque:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

También es posible utilizar la forma simple de `RotateDegrees` anteponiendo el bucle con una llamada a `Translate` para mover todo el contenido en el centro del lienzo:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

Modificados `yText` cálculo ya no incorpora `yCenter`. Ahora el `DrawText` llamada centra el texto verticalmente en la parte superior del lienzo.

Dado que las transformaciones se aplican conceptualmente opuesta a cómo aparecen en el código, a menudo resulta posible transformaciones para comenzar con índole más globales, seguidas de más transformaciones locales. Por lo general, suele ser la manera más fácil de combinar la rotación y la traslación.

Por ejemplo, imagine que desea dibujar un objeto gráfico que gira alrededor de su centro de forma muy similar a un planeta rotar sobre su eje. Pero también desea que se giran en torno al centro de la pantalla de forma muy similar a un planeta girar alrededor del sol este objeto.

Para ello, puede colocar el objeto en la esquina superior izquierda del lienzo y, a continuación, usar una animación para girar alrededor esa esquina. A continuación, convertir el objeto horizontal como un radio orbital. Aplicar ahora una segunda rotación animada, también alrededor del origen. Esto hace que el objeto giran en torno a la esquina. Ahora se convierten en el centro del lienzo.

Este es el `PaintSurface` controlador que contiene esta transformación llamadas en orden inverso:

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

El `revolveDegrees` y `rotateDegrees` son animados campos. Este programa utiliza una técnica de animación diferentes en función de la Xamarin.Forms `Animation` clase. (Esta clase se describe en [Chapter 22 de *crear aplicaciones móviles con Xamarin.Forms*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) la `OnAppearing` reemplazada crea dos `Animation` objetos que tienen métodos de devolución de llamada y, a continuación, llama a `Commit` en estas plataformas para una duración de la animación:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

La primera `Animation` objeto anima `revolveDegrees` de 0 a 360 grados más de 10 segundos. La segunda se anima `rotateDegrees` de 0 a 360 grados cada 1 segundo y también invalida la superficie para generar otra llamada a la `PaintSurface` controlador. El `OnDisappearing` invalidación cancela estos dos animaciones:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

El **reloj analógico desagradable** programa (denominado así porque un reloj analógico sea más atractivo se describirá en un artículo de una versión posterior) usa rotación que se va a dibujar las marcas de minuto y la hora del reloj como girar las manos. El programa dibuja el reloj con un sistema de coordenadas arbitrario en función de un círculo que se centra en el punto (0, 0) con un radio de 100. Realiza traducción y ajuste de escala para expandir y ese círculo en la página del centro.

El `Translate` y `Scale` llamadas se aplican globalmente al reloj, por lo que las que son los primeros que se llamarán tras la inicialización de la `SKPaint` objetos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}

```csharp
There are 60 marks of two different sizes that must be drawn in a circle around the clock. The `DrawCircle` call draws that circle at the point (0, –90), which relative to the center of the clock corresponds to 12:00. The `RotateDegrees` call increments the rotation angle by 6 degrees after every tick mark. The `angle` variable is used solely to determine if a large circle or a small circle is drawn:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

Por último, el `PaintSurface` controlador obtiene la hora actual y calcula los grados de rotación durante la hora, minuto y segundo manos. Cada parte se dibuja en la posición de 12:00 para que sea el ángulo de giro con respecto a:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

El reloj es ciertamente funcional aunque las manecillas son tosco en su lugar:

[![](rotate-images/uglyanalogclock-small.png "Captura de pantalla de la página de texto de reloj analógico desagradable de triple")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
