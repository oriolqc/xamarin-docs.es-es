---
title: "La transformación de traducción"
description: "Obtenga información acerca de cómo usar la transformación de traslación de desplazamiento SkiaSharp gráficos"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: cac2479af2778af6043a85583f9d7b518748d7da
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="the-translate-transform"></a>La transformación de traducción

_Obtenga información acerca de cómo usar la transformación de traslación de desplazamiento SkiaSharp gráficos_

Es el tipo más sencillo de transformación en SkiaSharp el *traducir* o *traducción* transformar. Esta transformación desplaza a objetos gráficos en las direcciones horizontal y verticales. En un sentido, la traducción es la transformación más innecesaria porque normalmente puede conseguir el mismo efecto cambiando simplemente las coordenadas que esté usando en la función de dibujo. Al representar una ruta de acceso, sin embargo, todas las coordenadas se encapsulan en la ruta de acceso, por lo que es mucho más fácil aplicar una transformación de traslación para cambiar la ruta de acceso completa.

Traducción también es útil para ver la animación y los efectos de texto simple:

![](translate-images/translateexample.png "Sombra de texto, crea un grabado y relieve con la traducción")

El [ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/) método `SKCanvas` tiene dos parámetros que hará que se va a desplazar horizontalmente y verticalmente los objetos de gráficos dibujados a continuación:

```csharp
public void Translate (Single dx, Single dy)
```

Estos argumentos pueden ser negativos. Un segundo [ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/SkiaSharp.SKPoint/) método combina los valores de dos traducción en un único equipo `SKPoint` valor:

```csharp
public void Translate (SKPoint point)
```

El **acumulado traducir** página de la [ **SkiaSharpForms** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programa de ejemplo muestra varias llamadas de la `Translate` método son acumulativos. El [ `AccumulatedTranslate` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) clase muestra 20 versiones del mismo rectángulo, cada uno de ellos desplaza desde el rectángulo anterior lo suficiente para que se ajusta a lo largo de la diagonal. Este es el `PaintSurface` controlador de eventos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

Los rectángulos sucesivas gradual hacia abajo en la página:

[![](translate-images/accumulatedtranslate-small.png "Captura de pantalla triple de la página acumulados traducir")](translate-images/accumulatedtranslate-large.png#lightbox "Triple captura de pantalla de la página acumulados traducir")

Si son los factores de traducción acumulado `dx` y `dy`, y es el punto especificado en una función de dibujo (`x`, `y`), a continuación, el objeto gráfico se presenta en el punto (`x'`, `y'`), donde:

x' = x + dx

y' = y + dy

Se conocen como el *transformar fórmulas* para la traducción. Los valores predeterminados de `dx` y `dy` un nuevo `SKCanvas` son 0.

Es habitual usar la transformación de traducir para efectos de sombra y técnicas similares, como el **traducir los efectos de texto** muestra la página. Aquí es la parte pertinente de la `PaintSurface` controlador en el [ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) clase:

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

En cada uno de los tres ejemplos `Translate` se llama para mostrar el texto para el desplazamiento desde la ubicación proporcionada por el `x` y `y` variables. A continuación, el texto se muestra de nuevo en otro color con ningún efecto de traducción:

[![](translate-images/translatetexteffects-small.png "Captura de pantalla triple de la página de traducir los efectos de texto")](translate-images/translatetexteffects-large.png#lightbox "Triple captura de pantalla de la página de traducir los efectos de texto")

Cada uno de los tres ejemplos muestra una forma diferente de que se niegue el `Translate` llamar:

El primer ejemplo simplemente llama `Translate` nuevo pero con valores negativos. Dado que el `Translate` llamadas son acumulativas, esta secuencia de llamadas simplemente restaura la traducción total a los valores predeterminados de cero.

El segundo ejemplo se llama [ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix()/). Esto hace que todas las transformaciones que se deben volver a su estado predeterminado.

El tercer ejemplo guarda el estado de la de la `SKCanvas` objeto con una llamada a [ `Save` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Save()/) y, a continuación, se restaura el estado con una llamada a [ `Restore` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Restore/). Esta es la manera más versátil para manipular las transformaciones para una serie de las operaciones de dibujo. Estos `Save` y `Restore` llama a la función como una pila: puede llamar a `Save` varios tiempo y, después, llame `Restore` en invertir secuencia para volver al estado anterior. El `Save` método devuelve un entero, y puede pasar ese entero al [ `RestoreToCount` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RestoreToCount/) eficazmente llamar a `Restore` varias veces. El [ `SaveCount` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.SaveCount/) propiedad devuelve el número de Estados que se hayan guardado en la pila.

Sin embargo, no tiene que preocuparse acerca de las transformaciones que se lleva a través de una llamada de la `PaintSurface` controlador a la siguiente. Cada nueva llamada a `PaintSurface` ofrece un nuevo `SKCanvas` objeto con transformaciones predeterminadas.

Otro uso común de la `Translate` transformación es para representar un objeto visual que se ha originalmente creado mediante coordenadas que son convenientes para el dibujo. Por ejemplo, puede especificar las coordenadas que definen un reloj analógico con un centro en el punto (0, 0). A continuación, puede utilizar transformaciones para mostrarla donde desee. Esto se muestra en el [**Hendecagram matriz**] página. El [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs) clase comienza creando un `SKPath` objeto para una estrella que señala el 11. La `HendecagramPath` objeto se define como público, estático y de solo lectura, por lo que se puede tener acceso desde otros programas de demostración. Se crea en un constructor estático:

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

Si el centro de la estrella es el punto (0, 0), son todos los puntos de la estrella en un círculo que rodea a ese punto. Cada punto es una combinación de valores de seno y el coseno del ángulo que se incremente en 5/11ths de 360 grados. (También es posible crear una estrella que señala el 11 aumentando el ángulo 2/11, 11ths/3 o 4/11 del círculo.) Se establece el radio del círculo como 100.

Si esta ruta de acceso se representa sin las transformaciones, el centro se colocará en la esquina superior izquierda de la `SKCanvas`y solo un trimestre de ella estarán visibles. El `PaintSurface` controlador de `HendecagramPage` en su lugar utiliza `Translate` para colocar en mosaico varias copias de la estrella en el lienzo, cada uno de ellos al azar de color:

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

Este es el resultado:

[![](translate-images/hendecagramarray-small.png "Captura de pantalla triple de la página de la matriz de Hendecagram")](translate-images/hendecagramarray-large.png#lightbox "Triple captura de pantalla de la página de matriz Hendecagram")

Las animaciones suelen implican transformaciones. El **Hendecagram animación** página mueve la estrella que señala el 11 de un círculo. El [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) clase comienza con algunos campos e invalidaciones de la `OnAppearing` y `OnDisappearing` métodos para iniciar y detener un temporizador de Xamarin.Forms:

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

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
            angle = (float)(360 * t);
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

El `angle` campo se anima de 0 a 360 grados cada 5 segundos. El `PaintSurface` controlador utiliza la `angle` propiedad de dos maneras: para especificar el matiz del color en el `SKColor.FromHsl` método y como un argumento a la `Math.Sin` y `Math.Cos` métodos para controlar la ubicación de la estrella:

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

El `PaintSurface` llamadas del controlador de la `Translate` método dos veces, primero se traduzcan en el centro del lienzo y, a continuación, se convierten en la circunferencia de un círculo que se centra en (0, 0). Se establece el radio del círculo que ser tan grande como sea posible mientras mantiene la estrella dentro de los límites de la página:

[![](translate-images/hendecagramanimation-small.png "Captura de pantalla triple de la página de animación de Hendecagram")](translate-images/hendecagramanimation-large.png#lightbox "Triple captura de pantalla de la página de animación Hendecagram")

Tenga en cuenta que la estrella mantiene la misma orientación a medida que se centra en el centro de la página. No gira en absoluto. Es un trabajo para una transformación de giro.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
