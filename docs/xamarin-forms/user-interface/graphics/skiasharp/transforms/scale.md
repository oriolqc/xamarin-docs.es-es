---
title: "La transformación de escala"
description: "Detectar la transformación de escala SkiaSharp para ajustar la escala para diversos tamaños de los objetos"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: 3ea498b3672c0b9ef4efeff7ec5981dca5a36912
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="the-scale-transform"></a>La transformación de escala

_Detectar la transformación de escala SkiaSharp para ajustar la escala para diversos tamaños de los objetos_

Como ha visto en [traducir la transformación](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md) artículo, la transformación de traducción puede mover un objeto gráfico de una ubicación a otra. En cambio, la transformación de escala cambia el tamaño del objeto gráfico:

![](scale-images/scaleexample.png "Una escala de tamaño de palabra alta")

La transformación de escala a menudo, también hace que las coordenadas de gráficos mover a medida que se efectúan mayor.

Anteriormente, vio dos fórmulas de transformación que describen los efectos de factores de traducción de `dx` y `dy`:

x' = x + dx

y' = y + dy

Escalar los factores de `sx` y `sy` son multiplicativas en lugar de suma:

x' = sx · x

y' = sy · y

Los valores predeterminados de los factores de traducción son 0; los valores predeterminados de los factores de escala son 1.

El `SKCanvas` clase define cuatro `Scale` métodos. La primera [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/) método es para el factor de casos si desea que la misma escala horizontal y vertical:

```csharp
public void Scale (Single s)
```

Esto se conoce como *isótropo* escalado & #x 2014; ajuste de escala que es el mismo en ambas direcciones. Escalado isótropo conserva la relación de aspecto del objeto.

El segundo [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/) método le permite especificar valores diferentes para el escalado horizontal y vertical:

```csharp
public void Scale (Single sx, Single sy)
```

Esto da como resultado *anisotrópico* ajuste de escala.
La tercera [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/SkiaSharp.SKPoint/) método combina los dos factores de escala en un único equipo `SKPoint` valor:

```csharp
public void Scale (SKPoint size)
```

El cuarto `Scale` método se describirá en breve.

El **escala básica** página muestra el `Scale` método. El [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) archivo XAML contiene dos `Slider` elementos que le permiten seleccionar los factores de escala horizontales y verticales entre 0 y 10. El [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) archivo de código subyacente usa estos valores para llamar a `Scale` antes de mostrar un rectángulo redondeado trazado con una línea discontinua y cambiará de tamaño para ajustarse a algún texto en la parte superior izquierda esquina del lienzo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Tal vez se pregunte: ¿cómo afecta los factores de escala el valor devuelto de la `MeasureText` método `SKPaint`? La respuesta es: no generarlos en absoluto. `Scale` es un método de `SKCanvas`. No no afecta a cualquier cosa que haga con un `SKPaint` objeto hasta que se utiliza dicho objeto para representar algo en el lienzo.

Como puede ver, todo lo que se dibuja después de la `Scale` llamar aumenta proporcionalmente:

[![](scale-images/basicscale-small.png "Triple captura de pantalla de la página de escala básica")](scale-images/basicscale-large.png "Triple captura de pantalla de la página de escala básica")

El texto, el ancho de la línea discontinua, la longitud de los guiones en esa línea, el redondeo de las esquinas y el margen de 10 píxeles entre los bordes superiores e izquierdos del lienzo y el rectángulo redondeado son todos los sujetos a los mismos factores de escala.

> [!IMPORTANT]
> La plataforma Universal de Windows no se representará correctamente texto anisotropicly escalado.

Anisotrópico escalado causas el ancho del trazo se convierta en diferentes para las líneas alineado con los ejes horizontales y vertical. (Esto también es evidente a partir de la primera imagen en esta página.) Si no desea que el ancho del trazo se ve afectado por los factores de escala, establézcalo en 0 y siempre será un píxel de ancho sin tener en cuenta el `Scale` configuración.

Ajuste de escala es relativo a la esquina superior izquierda del lienzo. Esto podría ser exactamente lo que desee, pero no es posible. Suponga que desea colocar el texto y el rectángulo en otro lugar en el lienzo y que desea escalar en relación con su centro. En ese caso puede usar la cuarta versión de la [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/System.Single/System.Single/) método, que incluye dos parámetros adicionales para especificar el centro de ajuste de escala:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

El `px` y `py` parámetros definen un punto que a veces se denomina el *escalado center* pero en el SkiaSharp documentación se conoce como un *dinamizar punto*. Se trata de un punto con respecto a la esquina superior izquierda del lienzo que no se ve afectado por el ajuste de escala. Los ajustes de escala se produce en relación con ese centro.

El [ **centrado escala** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) página muestra el funcionamiento. El `PaintSurface` controlador es similar a la **escala básica** programa salvo que la `margin` valor se calcula para centrar el texto horizontalmente, lo cual implica que el programa funciona mejor en modo vertical:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Se coloca la esquina superior izquierda del rectángulo redondeado `margin` píxeles desde la izquierda del lienzo y `margin` píxeles desde la parte superior. Los últimos dos argumentos para el `Scale` método se establecen en los valores más el ancho y alto del texto, que también es el ancho y alto del rectángulo redondeado. Esto significa que los ajustes de escala es relativa al centro del rectángulo:

[![](scale-images/centeredscale-small.png "Captura de pantalla triple de la página de escala centrado")](scale-images/centeredscale-large.png "Triple captura de pantalla de la página de escala centrado")

El `Slider` elementos de este programa tienen un intervalo de & #x 2013; 10 a 10. Como puede ver, los valores negativos de vertical escalado (por ejemplo, como en el Android pantalla en el centro) hará que los objetos girar alrededor del eje horizontal que pasa a través del centro de ajuste de escala. Los valores negativos de escala (como se muestra en la pantalla de Windows a la derecha) horizontal hacen objetos que se va a girar alrededor del eje vertical que pasa a través del centro de ajuste de escala.

Esta cuarta versión de la `Scale` método es realmente un acceso directo. Desea ver cómo funciona esto si se reemplaza el `Scale` método en este código con lo siguiente:

```csharp
canvas.Translate(-px, -py);
```

Estos son los negativos de las coordenadas del punto de pivote.

Ejecute el programa otra vez. Verá que el rectángulo y el texto se desplazan para que el centro está en la esquina superior izquierda del lienzo. Apenas puede verlo. Los controles deslizantes no funcionan por supuesto porque ahora no se escala en todo el programa.

A continuación, agregue las opciones básicas `Scale` llama (sin un centro de la escala) *antes de* que `Translate` llamar:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Si está familiarizado con este ejercicio en otros sistemas de programación de gráficos, puede que piense que es incorrecto, pero no lo es. Skia controla las llamadas sucesivas transformación un poco diferente de lo que es posible que esté familiarizado con.

Con las sucesivas `Scale` y `Translate` llamadas, el centro del rectángulo redondeado aún está en la esquina superior izquierda, pero ahora se puede escalar con respecto a la esquina superior izquierda del lienzo, que también es el centro del rectángulo redondeado.

Ahora, antes de que `Scale` llamada agregue otro `Translate` llama con los valores centrados:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Esto lo escalado se vuelve a desplazar a la posición original. Son equivalentes a las tres llamadas:

```csharp
canvas.Scale(sx, sy, px, py);
```

Las transformaciones individuales se combinan para que la fórmula de transformación total es:

 x' = sx · (x – px) + px

 y' = sy · (y – copiar) + copiar

Tenga en cuenta que los valores predeterminados de `sx` y `sy` son 1. Es fácil convencer a sí mismo que el punto de pivote (px, copiar) no se transforma por estas fórmulas. Permanece en la misma ubicación en relación con el lienzo.

Cuando se combinan `Translate` y `Scale` llamadas, el orden es importante. Si el `Translate` viene después de la `Scale`, los factores de traducción se escalan eficazmente por los factores de escala. Si el `Translate` precede el `Scale`, los factores de traducción no se escalan. Este proceso se convierte en algo más claro (aunque más matemáticos) cuando se introduce el asunto de matrices de transformación.

El `SKPath` clase define solo lectura [ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/) propiedad que devuelve una `SKRect` definir el alcance de las coordenadas de la ruta de acceso. Por ejemplo, cuando la `Bounds` propiedad se obtiene de la ruta de acceso de hendecagram que creó anteriormente, el `Left` y `Top` propiedades del rectángulo son aproximadamente -100, el `Right` y `Bottom` son propiedades aproximadamente 100 y el `Width` y `Height` propiedades son aproximadamente 200. (La mayoría de los valores reales es un poco menor porque los puntos de las estrellas se definen mediante un círculo con un radio de 100, pero solo el punto superior es paralelo con los ejes horizontales o verticales).

La disponibilidad de esta información implica que debe ser posible derivar escala y traducir factores adecuados para el escalado de una ruta de acceso para el tamaño del lienzo. El [ **escalado anisotrópico** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) página se muestra cómo hacerlo con la estrella que señala el 11. Un *anisotrópico* escala significa que es diferente en las direcciones horizontal y verticales, lo que significa que la estrella no conserva su relación de aspecto original. Este es el código relevante el `PaintSurface` controlador:

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

El `pathBounds` rectángulo es obtenido en la parte superior de este código y, a continuación, se utiliza más adelante con el ancho y alto del lienzo en el `Scale` llamar. Llamada por sí sola escalará las coordenadas de la ruta de acceso cuando se representa por la `DrawPath` la estrella pero la llamada se centrará en la esquina superior derecha del lienzo. Debe se desplace hacia abajo y a la izquierda. Este es el trabajo de la `Translate` llamar. Esas dos propiedades de `pathBounds` son aproximadamente -100, por lo que los factores de traducción son aproximadamente 100. Dado que el `Translate` llamada es después de la `Scale` llamar a, esos valores se escalan eficazmente por los factores de escala, por lo que se mueve el centro de la estrella en el centro del lienzo del:

[![](scale-images/anisotropicscaling-small.png "Captura de pantalla triple de la página de ajuste de escala anisotrópico")](scale-images/anisotropicscaling-large.png "Triple captura de pantalla de la página de ajuste de escala anisotrópico")

Otra manera puede pensar en el `Scale` y `Translate` llamadas consiste en determinar el efecto en orden inverso: la `Translate` llamada cambia la ruta de acceso, por lo que pasa a ser totalmente visible pero orientan en la esquina superior izquierda del lienzo. El `Scale` método, a continuación, hace que ese estrella mayor con respecto a la esquina superior izquierda.

En realidad, parece que la estrella es un poco mayor que el lienzo. El problema es el ancho del trazo. El `Bounds` propiedad de `SKPath` indica las dimensiones de las coordenadas de codificación en la ruta de acceso y que es lo que el programa usa para ampliarlo. Cuando la ruta de acceso se representa con un ancho del trazo determinado, la ruta de acceso representada es mayor que el lienzo.

Para solucionar este problema debe compensar. Un enfoque sencillo en este programa es agregar el derecho de instrucción siguientes antes de la `Scale` llamar:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Esto aumenta la `pathBounds` rectángulo por 1,5 unidades en los cuatro lados. Esto es una solución razonable solo cuando se redondea a la combinación de trazo. Una unión en ángulo puede ser más larga y es difícil de calcular.

También puede usar una técnica similar con texto, como el **texto anisotrópico** muestra la página. Aquí es la parte pertinente de la `PaintSurface` controlador desde la [ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) clase:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

Es una lógica similar y el texto se expande hasta el tamaño de la página basándose en el rectángulo de límites de texto devuelto desde `MeasureText` (que es un poco mayor que el texto real):

[![](scale-images/anisotropictext-small.png "Captura de pantalla triple de la página de prueba anisotrópico")](scale-images/anisotropictext-large.png "Triple captura de pantalla de la página de prueba anisotrópico")

Si tiene que conservar la relación de aspecto de los objetos gráficos, desea utilizar isótropo de ajuste de escala. El **escalado isótropo** página se muestra cómo hacerlo para la estrella que señala el 11. Conceptualmente, los pasos para mostrar un objeto gráfico en el centro de la página con la escala isótropo son:

- Traducir el centro del objeto gráfico en la esquina superior izquierda.
- Escale el objeto basándose en el mínimo de las dimensiones de página horizontales y verticales dividido entre las dimensiones del objeto gráfico.
- Traducir el centro del objeto escalado para el centro de la página.

El [ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) realiza estos pasos en orden inverso antes de mostrar la estrella:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

El código también muestra la estrella diez veces más, separe cada vez que se reduce el ajuste de escala en 10% y progresivamente cambiando el color de rojo a azul:

[![](scale-images/isotropicscaling-small.png "Captura de pantalla triple de la página de ajuste de escala isótropo")](scale-images/isotropicscaling-large.png "Triple captura de pantalla de la página de ajuste de escala isótropo")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
