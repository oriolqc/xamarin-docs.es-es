---
title: "Píxeles y unidades independientes del dispositivo"
description: Explorar las diferencias entre las coordenadas SkiaSharp e Xamarin.Forms
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: 5e07377584996694aa8597af79317957c51050ec
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="pixels-and-device-independent-units"></a>Píxeles y unidades independientes del dispositivo

_Explorar las diferencias entre las coordenadas SkiaSharp e Xamarin.Forms_

En este artículo se explora las diferencias en el sistema de coordenadas que se usan en SkiaSharp y Xamarin.Forms. Puede obtener información para convertir entre los dos sistemas de coordenadas y también dibujar gráficos que rellena un área determinada:

![](pixels-images/screenfillexample.png "Una elipse que ocupe toda la pantalla")

Si ha sido programando en Xamarin.Forms durante algún tiempo, podría tener una idea de Xamarin.Forms coordenadas y los tamaños. Los círculos dibujados en los dos artículos anteriores pueden parecer un poco más pequeños para usted.

Los círculos *son* pequeño en comparación con tamaños de Xamarin.Forms. De forma predeterminada, SkiaSharp dibuja en unidades de píxeles mientras Xamarin.Forms basa coordenadas y los tamaños de una unidad independiente del dispositivo establecida por la plataforma subyacente. (Puede encontrar más información en el sistema de coordenadas de Xamarin.Forms en [capítulo 5. Tratar con tamaños de](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) de la libreta de *crear aplicaciones móviles con Xamarin.Forms*.)

La página en el [ **SkewSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programa titulada " **tamaño de la superficie** SkiaSharp salida de texto se utiliza para mostrar el tamaño de la superficie de pantalla de tres orígenes diferentes:

- La normal Xamarin.Forms [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) y [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) propiedades de la `SKCanvasView` objeto.
- El [ `CanvasSize` ](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKCanvasView.CanvasSize/) propiedad de la `SKCanvasView` objeto.
- El [ `Size` ](https://developer.xamarin.com/api/property/SkiaSharp.SKImageInfo.Size/) propiedad de la `SKImageInfo` valor, que es coherente con la `Width` y `Height` propiedades utilizadas en las dos páginas anteriores.

El [ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) clase muestra cómo mostrar estos valores. El constructor guarda el `SKCanvasView` objeto como un campo, por lo que son accesibles en el `PaintSurface` controlador de eventos:

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas` incluye seis diferentes `DrawText` métodos, pero esto [ `DrawText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawText/p/System.String/System.Single/System.Single/SkiaSharp.SKPaint/) método es la más sencilla:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Especifica la cadena de texto, las coordenadas X e Y que el texto se va a comenzar y un `SKPaint` objeto. La coordenada X especifica dónde se sitúa el lado izquierdo del texto, pero tenga cuidado con: la coordenada Y especifica la posición de la *previsto* del texto. Si alguna vez ha escrito a mano en Papel pautado, la línea de base es la línea en qué sit de caracteres, y a continuación que descienden de los trazos descendentes (como los de las letras g, p, q e y).

La `SKPaint` objeto le permite especificar el color del texto, la familia de fuentes y el tamaño del texto. De forma predeterminada, el [ `TextSize` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.TextSize/) propiedad tiene un valor de 12, lo que resulta en texto muy pequeño en dispositivos de alta resolución como los teléfonos. En sólo las aplicaciones más sencillas, también necesitará cierta información sobre el tamaño del texto que se van a mostrar. El `SKPaint` clase define un [ `FontMetrics` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontMetrics/) propiedad y varios [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/) métodos, pero para menos necesidades sofisticadas, el [ `FontSpacing` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontSpacing/) propiedad proporciona un valor recomendado para las líneas sucesivas espaciado de texto.

El siguiente `PaintSurface` controlador crea una `SKPaint` de objeto para un `TextSize` de 40 píxeles, que es el alto deseado vertical del texto de la parte superior de trazos ascendentes a la parte inferior de los trazos descendentes. El `FontSpacing` valor que la `SKPaint` objeto devuelve un poco mayor que, acerca de 47 píxeles.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

El método comienza la primera línea de texto con una X de 20 (para un poco margen situado a la izquierda) una coordenada y de `fontSpacing`, que es un poco más que lo que es necesario para mostrar el alto total de la primera línea de texto en la parte superior de la superficie de presentación. Después de cada llamada a `DrawText`, la coordenada Y se incrementa en uno o dos incrementos de `fontSpacing`.

Este es el programa que se ejecuta en las tres plataformas:

[![](pixels-images/surfacesize-small.png "Captura de pantalla triple de la página de tamaño de la superficie")](pixels-images/surfacesize-large.png#lightbox "Triple captura de pantalla de la página de tamaño de la superficie")

Como puede ver, el `CanvasSize` propiedad de la `SKCanvasView` y `Size` propiedad de la `SKImageInfo` valor son coherentes en el informe de las dimensiones en píxeles. El `Height` y `Width` propiedades de la `SKCanvasView` son propiedades de Xamarin.Forms y el tamaño de la vista en las unidades independientes del dispositivo definidos por la plataforma de informes.

El simulador de iOS 7 de la izquierda tiene 2 píxeles por unidad independiente del dispositivo, el 5 de Nexus Android en el centro de tiene 3 píxeles por unidad y la 925 Lumia Nokia a la derecha tiene 2,25 píxeles por unidad. Que de por qué los más sencillos circle parece anterior se muestra sobre el mismo tamaño en el iPhone y Windows phone, pero es más pequeñas en el teléfono Android.

Si prefiere trabajar completamente en unidades independientes del dispositivo, puede hacerlo estableciendo la `IgnorePixelScaling` propiedad de la `SKCanvasView` a `true`. Sin embargo, no podría gustan los resultados. SkiaSharp representa los gráficos en una superficie de dispositivos más pequeño, con un tamaño de píxel igual que el tamaño de la vista en unidades independientes del dispositivo. (Por ejemplo, SkiaSharp utilizaría una superficie de pantalla de 360 x 512 píxeles en el 5 de Nexus.) A continuación, aumentar la escala esa imagen en tamaño, lo que genera imágenes de mapa de bits notable dentadas.

Para mantener la misma resolución de imagen, una mejor solución es escribir sus propias funciones simples para realizar conversiones entre los dos sistemas de coordenadas.

Además el `DrawCircle` método `SKCanvas` también define dos `DrawOval` métodos que dibuja una elipse. Una elipse se define mediante dos radii en lugar de un solo radio. Se conocen como el *radius principales* y *radio menor*. El `DrawOval` método dibuja una elipse con los dos radios parecida a los ejes X e Y. Esta restricción se puede superar con transformaciones o el uso de un trazado de gráficos (para cubrir más adelante), pero [esto `DrawOval` método](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/) nombres de argumento dos radii `rx` y `ry` para indicar que es parecidas a la los ejes X e Y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

¿Es posible dibujar una elipse rellena la superficie de presentación? El **elipse rellenar** muestra la página Cómo. El `PaintSurface` controlador de eventos en el [ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) clase resta la mitad del ancho del trazo de la `xRadius` y `yRadius` valores para ajustarse a la elipse completa y su se describen en la superficie de presentación:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

A continuación se ejecuta en las tres plataformas:

[![](pixels-images/ellipsefill-small.png "Captura de pantalla triple de la página de tamaño de la superficie")](pixels-images/ellipsefill-large.png#lightbox "Triple captura de pantalla de la página de tamaño de la superficie")

El [otros `DrawOval` método](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/) tiene un [ `SGRect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRect/) argumento, que es un rectángulo que se definen en función de las coordenadas X e Y de su esquina superior izquierda y la esquina inferior derecha. La elipse rellena dicho rectángulo, lo que sugiere que sería posible usarlo en el **elipse rellenar** página similar al siguiente:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

Sin embargo, trunca todas las aristas del contorno de la elipse en los cuatro lados. Debe ajustar todo el `SKRect` los argumentos de constructor basados en la `strokeWidth` para solucionar este problema derecho:

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
