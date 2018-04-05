---
title: Dibujar un círculo Simple
description: Obtenga información acerca de los conceptos básicos de dibujo SkiaSharp, incluidos lienzos y paint
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9e5b18bd6a565639e2ca22f4637a67c4f48717f6
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="drawing-a-simple-circle"></a>Dibujar un círculo Simple

_Obtenga información acerca de los conceptos básicos de dibujo SkiaSharp, incluidos lienzos y paint_

Este artículo presenta los conceptos de dibujar gráficos en Xamarin.Forms con SkiaSharp, incluida la creación de un `SKCanvasView` objeto que se va a hospedar los gráficos, control de la `PaintSurface` eventos y usar un `SKPaint` objeto para especificar el color y otros dibujos atributos.

El [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa contiene todo el código de ejemplo de esta serie de artículos de SkiaSharp. La primera página se titula **círculo Simple** e invoca la clase page [ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs). Este código muestra cómo dibujar un círculo en el centro de la página con un radio de 100 píxeles. El contorno del círculo es rojo y el interior del círculo es azul.

![](circle-images/circleexample.png "Un círculo azul destacado en rojo")

El [ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) página clase se deriva de `ContentPage` y contiene dos `using` directivas para los espacios de nombres SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Crea el siguiente constructor de la clase un [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/) de objetos, se adjunta un controlador para el [ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/) eventos y conjuntos de la `SKCanvasView` los objetos según el contenido de la página:

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

El `SKCanvasView` ocupa todo el área de contenido de la página. También puede combinar una `SKCanvasView` con otros Xamarin.Forms `View` derivados, como verá en otros ejemplos.

El `PaintSurface` controlador de eventos es donde no todos los el dibujo. Por lo general llama a este método varias veces mientras se ejecuta el programa, por lo que debe mantener toda la información necesaria para volver a mostrar los gráficos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

El [ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/) objeto que se incluye en el evento tiene dos propiedades:

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) de tipo [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) de tipo [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

El `SKImageInfo` estructura contiene información sobre la superficie de dibujo, lo más importante, es ancho y alto en píxeles. La `SKSurface` objeto representa la superficie de dibujo. En este programa, la superficie de dibujo es una pantalla de vídeo, pero en otros programas un `SKSurface` objeto también puede representar un mapa de bits que utilizan SkiaSharp para dibujar en.

La propiedad más importante de `SKSurface` es [ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/) de tipo [ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/). Esta clase es un contexto que se usa para realizar el dibujo real de dibujo de gráficos. La `SKCanvas` objeto encapsula un estado de los gráficos, que incluye las transformaciones de gráficos y el recorte.

Este es un principio típica de un `PaintSurface` controlador de eventos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

El [ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/) método borra el lienzo con un color transparente. Una sobrecarga permite especificar un color de fondo para el lienzo.

El objetivo es dibujar un círculo rojo rellenado en azul. Dado que esta imagen de gráfico determinada contiene dos colores diferentes, el trabajo debe hacerse en dos pasos. El primer paso es dibujar el contorno del círculo. Para especificar el color y otras características de la línea, debe crear e inicializar un [ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/) objeto:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

El [ `Style` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Style/) propiedad indica que desea *trazo* una línea (en este caso, el contorno del círculo) en lugar de *relleno* el interior. Los tres miembros de la [ `SKPaintStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaintStyle/) enumeración son los siguientes:

- [`Fill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Fill/)
- [`Stroke`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Stroke/)
- [`StrokeAndFill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.StrokeAndFill/)

De manera predeterminada, es `Fill`. Utilice la tercera opción para trazar la línea y rellenar el interior con el mismo color.

Establecer el [ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/) propiedad con un valor de tipo [ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/). Una manera de obtener un `SKColor` valor consiste en convertir un Xamarin.Forms `Color` valor a un `SKColor` valor mediante el método de extensión [ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/). El [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/) clase en el `SkiaSharp.Views.Forms` espacio de nombres incluye otros métodos que convierten entre valores de Xamarin.Forms y SkiaSharp.

El [ `StrokeWidth` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeWidth/) propiedad indica el grosor de la línea. Aquí se establece en 25 píxeles.

Use que `SKPaint` objeto que se va a dibujar el círculo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

Coordenadas están especificadas en relación con la esquina superior izquierda de la superficie de presentación. X coordina incrementar a la derecha y Y coordenadas bajan. A menudo en la documentación relativa a los gráficos, la notación matemática (x, y) se utiliza para denotar un punto. El punto (0, 0) es la esquina superior izquierda de la superficie de presentación y a menudo se denomina la *origen*.

Los dos primeros argumentos de `DrawCircle` indican las coordenadas X e Y del centro del círculo. Estos se asignan a la mitad del ancho y alto de la superficie de presentación para colocar el centro del círculo en el centro de la superficie de presentación. El tercer argumento especifica el radio del círculo y el último argumento es el `SKPaint` objeto.

Para rellenar el interior del círculo, puede modificar las dos propiedades de la `SKPaint` objeto y llame al método `DrawCircle` nuevo. Este código también muestra una manera alternativa de obtener un `SKColor` valor de uno de los muchos campos de la [ `SKColors` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColors/) estructura:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
En esta ocasión, el `DrawCircle` llamada rellena el círculo con las nuevas propiedades de la `SKPaint` objeto.

Este es el programa que se ejecuta en la plataforma Universal de Windows, iOS y Android:

[![](circle-images/simplecircle-small.png "Captura de pantalla triple de la página de círculo Simple")](circle-images/simplecircle-large.png#lightbox "Triple captura de pantalla de la página de círculo Simple")

Cuando se ejecuta el programa, puede activar el teléfono o el simulador lateralmente para ver cómo se vuelve a dibujar el gráfico. Cada vez que el gráfico necesario volver a dibujar, la `PaintSurface` se vuelve a llamar al controlador de eventos.

Un `SKPaint` objeto es algo más que una colección de propiedades de dibujo de gráficos. Estos objetos son muy ligeros. Puede volver a usar `SKPaint` objetos como no de este programa, o puede crear varios `SKPaint` objetos de diversas combinaciones de propiedades del dibujo. Puede crear e inicializar estos objetos fuera de la `PaintSurface` controlador de eventos y también puede guardarlas como campos en la clase de página.

Aunque se especifica el ancho del contorno del círculo como 25 píxeles &mdash; o un cuarto del radio del círculo &mdash; parece ser más estrecho y no hay una buena razón para ello: mitad del ancho de la línea se oculta el círculo azul. Los argumentos para el `DrawCircle` método definen las coordenadas geométricas abstractas de un círculo. El interior azul tiene un tamaño para esa dimensión al píxel más cercano, pero el esquema de 25 píxeles de ancho sobrepasa el círculo geométrico &mdash; mitad en el interior y la otra mitad en el exterior.

El ejemplo siguiente en el [integración con Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) artículo se muestra cómo hacerlo visualmente.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
