---
title: "Integración de texto y gráficos"
description: "Consulte cómo determinar el tamaño de la cadena de texto representado integrar texto con gráficos de SkiaSharp"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 1cb6b6fcd8a9d02910842eb3eba966fce281d977
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="integrating-text-and-graphics"></a>Integración de texto y gráficos

_Consulte cómo determinar el tamaño de la cadena de texto representado integrar texto con gráficos de SkiaSharp_

En este artículo se muestra cómo medir el texto, posiblemente escalar el texto de un tamaño determinado y el texto se integren con otros gráficos:

![](text-images/textandgraphicsexample.png "Texto rodeado por rectángulos")

El SkiaSharp `Canvas` clase también incluye métodos para dibujar un rectángulo ([`DrawRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRect/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)) y un rectángulo con esquinas redondeadas ([`DrawRoundRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRoundRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPaint/)). Estos métodos requieren el rectángulo se definan como un `SKRect` valor.

El **texto enmarcado** página centros de una cadena de texto corta en la página y bordes con un marco formada por un par de rectángulos redondeados. El [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) clase muestra cómo se hizo.

En SkiaSharp se utiliza la `SKPaint` clase para establecer los atributos de texto y la fuente, pero también puede utilizar para obtener el tamaño del texto representado. El principio de los siguientes `PaintSurface` controlador de eventos llama dos diferente `MeasureText` métodos. La primera [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/) llamada tiene un sencillo `string` argumento y devuelve el ancho en píxeles del texto basándose en los atributos de fuente actual. El programa, a continuación, calcula un nuevo `TextSize` propiedad de la `SKPaint` objeto basado en ese ancho representado, actual `TextSize` propiedad y el ancho del área de presentación. Está diseñado para establecer `TextSize` para que el texto de cadena para representar al 90% del ancho de la pantalla:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds;
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

El segundo [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/SkiaSharp.SKRect@/) llamada tiene un `SKRect` argumento, por lo que obtiene un ancho y el alto del texto representado. El `Height` propiedad de este `SKRect` valor depende de la presencia de letras mayúsculas, trazos ascendentes y descendentes de la cadena de texto. Diferentes `Height` valores se notifican para las cadenas de texto "mom", "cat" y "dog", por ejemplo.

El `Left` y `Top` propiedades de la `SKRect` estructura indican las coordenadas de la esquina superior izquierda del texto representado si se muestra el texto por una `DrawText` llama con posiciones X e Y de 0. Por ejemplo, cuando se ejecuta este programa en un emulador de iPhone 7, `TextSize` se asigna el valor 90.6254 como resultado del cálculo después de la primera llamada a `MeasureText`. El `SKRect` valor obtenido de la segunda llamada a `MeasureText` tiene los siguientes valores de propiedad:

- `Left` = 6
- `Top` = &#x2013;68
- `Width` = 664.8214
- `Height` = 88;

Tenga en cuenta que las coordenadas X e Y se pasa a la `DrawText` método especifica el lado izquierdo del texto en la línea de base. El `Top` valor indica que el texto extiende 68 píxeles por encima de esta línea de base y (restar TI 68 desde 88) 20 píxeles por debajo de la línea de base. El `Left` el valor 6 indica que el texto comienza 6 píxeles a la derecha del valor X en la `DrawText` llamar. Esto permite el espaciado entre caracteres normales. Si desea mostrar el texto perfectamente en la esquina superior izquierda de la pantalla, pase los negativos de estas `Left` y `Top` valores como las coordenadas X e Y de `DrawText`, en este ejemplo & #x 2013; 6 y 68.

El `SKRect` estructura define varias propiedades útiles y métodos, algunos de los cuales se utilizan en el resto de la `PaintSurface` controlador. El `MidX` y `MidY` valores indican las coordenadas del centro del rectángulo. (En el ejemplo iPhone 7, dichos valores son 338.4107 y & #x 2013; 24.) El código siguiente utiliza estos valores para el cálculo más fácil de coordenadas para centrar el texto de la pantalla:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

El `PaintSurface` controlador concluye con dos llamadas a `DrawRoundRect`, lo cual requieren argumentos de `SKRect`. Esto `SKRect` valor es ciertamente similar a la `SKRect` valor obtenido de la `MeasureText` (método), pero no puede ser el mismo. En primer lugar, debe ser un poco más grande para que no se dibuja el rectángulo redondeado a través de los bordes del texto y, en segundo lugar, necesita que se va a desplazar en un espacio para que la `Left` y `Top` valores corresponden a la esquina superior izquierda donde el rectángulo es como coloca. Estos dos trabajos se llevan a cabo por `Offset` y `Inflate` métodos definidos por `SKRect`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

A continuación, el resto del método es sencilla. Crea otro `SKPaint` objeto para los bordes y las llamadas `DrawRoundRect` dos veces. La segunda llamada usa un rectángulo exagerar otro 10 píxeles. La primera llamada especifica un radio de redondeo de 20 píxeles. el segundo tiene un radio de redondeo de 30 píxeles, por lo que parecen ser paralelo:

 [![](text-images/framedtext-small.png "Captura de pantalla triple de la página de texto enmarcado")](text-images/framedtext-large.png#lightbox "Triple captura de pantalla de la página de texto enmarcado")

Puede activar el teléfono o el simulador lateralmente para ver el texto y el marco aumentan de tamaño.

Si solo necesita centrar el texto en la pantalla, puede hacerlo aproximadamente sin necesidad de medir el texto estableciendo la `TextAlign` propiedad de `SKPaint` a `SKTextAlign.Center`. La coordenada X que especifique en el `DrawText` método indica a continuación, donde está ubicado el centro horizontal del texto. Si se pasa el punto medio de la pantalla para la `DrawText` método, el texto estará centrado horizontalmente y *casi* centrada verticalmente porque estará centrada verticalmente la línea de base.

Texto en sí se puede tratar mucho como una opción gráfica. Es una opción simple mostrar el contorno de los caracteres de texto en lugar de la presentación rellena normal:

[![](text-images/outlinedtext-small.png "Captura de pantalla de la página de texto que se describen el triple")](text-images/outlinedtext-large.png#lightbox "Triple captura de pantalla de la página de texto que se describen")

Esto se realiza cambiando la normal `Style` propiedad de la `SKPaint` objeto de su valor predeterminado de `SKPaintStyle.Fill` a `SKPaintStyle.Stroke` y especificando el ancho del trazo. El `PaintSurface` controlador de la **texto que se describen** página muestra cómo hacerlo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds;
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

 En los últimos varios artículos, que tiene ahora visto cómo usar SkiaSharp para dibujar texto, círculos, elipses y rectángulos redondeados. El siguiente paso es [SkiaSharp líneas y las rutas de acceso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md) en que se obtendrá información sobre cómo dibujar líneas conectadas en una ruta de acceso de gráficos.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
