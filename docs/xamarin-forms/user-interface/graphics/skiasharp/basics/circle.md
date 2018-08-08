---
title: Dibujar un círculo Simple en SkiaSharp
description: En este artículo se explica los conceptos básicos de dibujo de SkiaSharp, incluidos elementos Canvas y paint, en las aplicaciones de Xamarin.Forms y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: e06a1310fad01da11c8d8b115df504cc19426344
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615228"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>Dibujar un círculo Simple en SkiaSharp

_Conozca los aspectos básicos de dibujo de SkiaSharp, incluidos elementos Canvas y paint_

Este artículo presentan los conceptos de dibujar gráficos en Xamarin.Forms mediante SkiaSharp, incluida la creación de un `SKCanvasView` objeto para hospedar los gráficos, controlar el `PaintSurface` eventos y usar un `SKPaint` objeto para especificar el color y otros dibujos atributos.

El [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa contiene todo el código de ejemplo de esta serie de artículos de SkiaSharp. La primera página se titula **círculo Simple** e invoca la clase de página [ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs). Este código muestra cómo dibujar un círculo en el centro de la página con un radio de 100 píxeles. El contorno del círculo es rojo, y el interior del círculo azul.

![](circle-images/circleexample.png "Un círculo azul destacado en rojo")

El [ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) deriva de la clase de página `ContentPage` y contiene dos `using` directivas para los espacios de nombres de SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Crea el siguiente constructor de la clase un [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/) de objetos, adjunta un controlador para el [ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/) eventos y conjuntos de la `SKCanvasView` objeto como el contenido de la página:

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

El `SKCanvasView` ocupa toda el área de contenido de la página. Como alternativa, puede combinar una `SKCanvasView` con otros Xamarin.Forms `View` derivados, como verá en otros ejemplos.

El `PaintSurface` controlador de eventos es donde realiza todo el dibujo. Este método generalmente se llama varias veces mientras se ejecuta el programa, por lo que debe mantener toda la información necesaria para volver a mostrar los gráficos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

El [ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/) objeto que acompaña el evento tiene dos propiedades:

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) de tipo [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) de tipo [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

El `SKImageInfo` estructura contiene información sobre la superficie de dibujo, lo más importante, es ancho y alto en píxeles. La `SKSurface` objeto representa la superficie de dibujo. En este programa, la superficie de dibujo es una pantalla de vídeo, pero en otros programas un `SKSurface` objeto también puede representar un mapa de bits que usan SkiaSharp en la que para dibujar.

La propiedad más importante de `SKSurface` es [ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/) typu [ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/). Esta clase es un contexto que se usa para realizar el dibujo real de dibujo de gráficos. La `SKCanvas` objeto encapsula un estado de los gráficos, que incluye las transformaciones gráficas y el recorte.

Este es un inicio típico de un `PaintSurface` controlador de eventos:

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

El [ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/) método borra el lienzo con un color transparente. Una sobrecarga permite especificar un color de fondo del lienzo.

El objetivo aquí es dibujar un círculo rojo rellenado en azul. Dado que esta imagen de gráfico determinada contiene dos colores diferentes, el trabajo debe hacerse en dos pasos. El primer paso es dibujar el contorno del círculo. Para especificar el color y otras características de la línea, crear e inicializar un [ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/) objeto:

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

Establecer el [ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/) propiedad con un valor de tipo [ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/). Una manera de obtener un `SKColor` valor consiste en convertir un objeto Xamarin.Forms `Color` valor a un `SKColor` valor mediante el método de extensión [ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/). El [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/) clase en el `SkiaSharp.Views.Forms` espacio de nombres incluye otros métodos que convierten entre los valores de Xamarin.Forms y valores de SkiaSharp.

El [ `StrokeWidth` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeWidth/) propiedad indica el grosor de la línea. Aquí se establece en 25 píxeles.

Que usas `SKPaint` objeto que se va a dibujar el círculo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

Las coordenadas se especifican respecto a la esquina superior izquierda de la superficie de pantalla. Las coordenadas X aumento a la derecha y aumento de las coordenadas Y deja de funcionar. A menudo en discusión acerca de los gráficos, la notación matemática (x, y) se utiliza para denotar un punto. El punto (0, 0) es la esquina superior izquierda de la superficie de pantalla y a menudo se denomina el *origen*.

Los dos primeros argumentos de `DrawCircle` indican las coordenadas X e Y del centro del círculo. Éstos se asignan a mitad del ancho y alto de la superficie de visualización para colocar el centro del círculo en el centro de la superficie de pantalla. El tercer argumento especifica el radio del círculo y el último argumento es el `SKPaint` objeto.

Para rellenar el interior del círculo, puede modificar dos propiedades de la `SKPaint` objeto y llamar a `DrawCircle` nuevo. Este código también muestra una manera alternativa de obtener un `SKColor` valor desde uno de los numerosos campos de la [ `SKColors` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColors/) estructura:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
Esta vez, el `DrawCircle` llamada rellena el círculo con las nuevas propiedades de la `SKPaint` objeto.

Este es el programa que se ejecuta en la plataforma Universal de Windows, iOS y Android:

[![](circle-images/simplecircle-small.png "Captura de pantalla de la página de círculo Simple triple")](circle-images/simplecircle-large.png#lightbox "Triple captura de pantalla de la página de círculo Simple")

Al ejecutar el programa, puede activar el teléfono o el simulador lateralmente para ver cómo se vuelve a dibujar el gráfico. Cada vez que el gráfico debe volver a dibujar, la `PaintSurface` se llama al controlador de eventos nuevo.

Un `SKPaint` objeto es poco más que una colección de propiedades de dibujo de gráficos. Estos objetos son muy ligeros. Puede volver a usar `SKPaint` objetos como hace este programa, o puede crear varios `SKPaint` objetos para diferentes combinaciones de propiedades del dibujo. Puede crear e inicializar estos objetos fuera de la `PaintSurface` controlador de eventos y puede guardarlas como campos en la clase de página.

Aunque se especifica el ancho del contorno del círculo como 25 píxeles &mdash; o un cuarto del radio del círculo &mdash; parece ser más delgados y hay una buena razón para ello: la mitad del ancho de la línea está oculto por un círculo azul. Los argumentos para el `DrawCircle` método definen las coordenadas geométricas abstractas de un círculo. El interior azul tiene un tamaño para esa dimensión y el píxel más próximo, pero el esquema de 25 píxeles de ancho sobrepasa el círculo geométrico &mdash; mitad en el interior y la otra mitad en la parte exterior.

El ejemplo siguiente en el [integración con Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) artículo muestra visualmente.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
