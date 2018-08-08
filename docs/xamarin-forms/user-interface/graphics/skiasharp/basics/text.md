---
title: La integración de texto y gráficos
description: En este artículo se explica cómo determinar el tamaño de la cadena de texto representada para integrar el texto con gráficos de SkiaSharp en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: be1524029ada79896f83517c3b439f2ad0e2c6d9
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615166"
---
# <a name="integrating-text-and-graphics"></a>La integración de texto y gráficos

_Vea cómo determinar el tamaño de la cadena de texto representada para integrar el texto con gráficos de SkiaSharp_

En este artículo se muestra cómo medir el texto, posiblemente escalar el texto a un tamaño determinado e integrar el texto con otros gráficos:

![](text-images/textandgraphicsexample.png "Texto rodeado de rectángulos")

El SkiaSharp `Canvas` clase también incluye métodos para dibujar un rectángulo ([`DrawRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRect/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)) y un rectángulo con esquinas redondeadas ([`DrawRoundRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRoundRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPaint/)). Estos métodos requieren que el rectángulo que se define como un `SKRect` valor.

El **texto enmarcado** página centros de una cadena de texto corta en la página y los bordes con un marco consta de un par de rectángulos redondeados. El [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) clase muestra cómo hacerlo.

En SkiaSharp usará el `SKPaint` clase para establecer los atributos de fuente y texto, pero también puede usar para obtener el tamaño del texto representado. El principio del siguiente `PaintSurface` controlador de eventos llama a dos diferentes `MeasureText` métodos. La primera [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/) llamada tiene un sencillo `string` argumento y devuelve el ancho en píxeles del texto según los atributos de fuente actual. A continuación, el sistema calcula un nuevo `TextSize` propiedad de la `SKPaint` objeto basado en ese ancho representado, actual `TextSize` propiedad y el ancho del área de visualización. Esto se pretende establecer `TextSize` para que el texto de cadena que se representará en el 90% del ancho de la pantalla:

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
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

El segundo [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/SkiaSharp.SKRect@/) llamada tiene un `SKRect` argumento, por lo que obtienen un ancho y alto del texto representado. El `Height` propiedad de este `SKRect` valor depende de la presencia de letras mayúsculas, los ascendentes y descendentes de la cadena de texto. Diferentes `Height` valores se notifican para las cadenas de texto "mom", "cat" y "dog", por ejemplo.

El `Left` y `Top` propiedades de la `SKRect` estructura indican las coordenadas de la esquina superior izquierda del texto representado si el texto se muestra un `DrawText` llamar con las posiciones X e Y de 0. Por ejemplo, cuando se ejecuta este programa en un simulador de iPhone 7 `TextSize` se asigna el valor 90.6254 como resultado del cálculo siguiente la primera llamada a `MeasureText`. El `SKRect` valor obtenido de la segunda llamada a `MeasureText` tiene los siguientes valores de propiedad:

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

Tenga en cuenta que las coordenadas X e Y se pasa a la `DrawText` método especifica el lado izquierdo del texto en la línea base. El `Top` valor indica que el texto extiende 68 píxeles por encima de esa línea de base y (si se resta 68 TI desde 88) 20 píxeles por debajo de la línea base. El `Left` valor 6 indica que el texto comienza 6 píxeles a la derecha del valor X de la `DrawText` llamar. Esto permite que el espaciado entre caracteres normales. Si desea mostrar el texto perfectamente en la esquina superior izquierda de la pantalla, pasar los valores negativos de estos `Left` y `Top` los valores de coordenadas X e Y de `DrawText`, en este ejemplo, &ndash;6 y 68.

El `SKRect` estructura define varias propiedades útiles y métodos, algunos de los cuales se usan en el resto de la `PaintSurface` controlador. El `MidX` y `MidY` valores indican las coordenadas del centro del rectángulo. (En el ejemplo de iPhone 7, estos valores son 338.4107 y &ndash;24.) El código siguiente usa estos valores para el cálculo más sencillo de coordenadas para centrar el texto de la pantalla:

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

El `PaintSurface` controlador concluye con dos llamadas a `DrawRoundRect`, ambos de los cuales requieren argumentos de `SKRect`. Esto `SKRect` valor es ciertamente es similar a la `SKRect` valor obtenido el `MeasureText` método, pero no puede ser el mismo. En primer lugar, debe ser un poco más grande para que no se dibuja el rectángulo redondeado a través de los bordes del texto y en segundo lugar, necesita que se desplazarán en el espacio para que la `Left` y `Top` valores corresponden a la esquina superior izquierda, donde es el rectángulo sea coloca. Estos dos trabajos se llevan a cabo por `Offset` y `Inflate` métodos definidos por `SKRect`:

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

A continuación, el resto del método es sencillo. Crea otro `SKPaint` objeto para los bordes y las llamadas `DrawRoundRect` dos veces. La segunda llamada usa un rectángulo inflado otro 10 píxeles. La primera llamada especifica un radio de redondeo de 20 píxeles. el segundo tiene un radio de 30 píxeles, por lo que parecen ser paralelo:

 [![](text-images/framedtext-small.png "Captura de pantalla de la página de texto enmarcado triple")](text-images/framedtext-large.png#lightbox "Triple captura de pantalla de la página de texto enmarcado")

Puede activar su teléfono o el simulador lateralmente para ver el texto y el marco aumentan de tamaño.

Si solo necesita algún texto en la pantalla del centro, puede hacerlo sin aproximadamente de medir el texto estableciendo el `TextAlign` propiedad de `SKPaint` a `SKTextAlign.Center`. La coordenada X que especifique en el `DrawText` método indica a continuación, donde se coloca el centro horizontal del texto. Si se pasa el punto medio de la pantalla para el `DrawText` método, el texto estará centrado horizontalmente y *casi* centrado verticalmente porque la línea de base se centrarán verticalmente.

Texto en sí se puede tratar mucho, como una opción gráfica. Es una opción sencilla mostrar el esquema de los caracteres del texto en lugar de la presentación rellenada normal:

[![](text-images/outlinedtext-small.png "Captura de pantalla de la página de texto que se describen el triple")](text-images/outlinedtext-large.png#lightbox "Triple captura de pantalla de la página de texto con contorno")

Esto se realiza cambiando el valor normal a `Style` propiedad de la `SKPaint` objeto de su valor predeterminado de `SKPaintStyle.Fill` a `SKPaintStyle.Stroke` y especificando un ancho de trazo. El `PaintSurface` controlador de la **texto con contorno** página muestra cómo hacerlo:

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
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

 En los últimos artículos de varias, que tiene ahora sabe cómo utilizar SkiaSharp para dibujar texto, círculos, elipses y rectángulos redondeados. El siguiente paso es [líneas de SkiaSharp y rutas de acceso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md) en que se obtendrá información sobre cómo dibujar líneas conectadas en una ruta de acceso de gráficos.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
