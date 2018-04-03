---
title: Las rutas de acceso y el texto
description: Explorar la intersección de las rutas de acceso y el texto
ms.topic: article
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 08/01/2017
ms.openlocfilehash: 25e76b7e02aa5bf6d5e83ea8188456115c527ef3
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="paths-and-text"></a>Las rutas de acceso y el texto

_Explorar la intersección de las rutas de acceso y el texto_

En los sistemas de gráfico moderno, fuentes de texto son colecciones de esquemas de carácter, normalmente definidas curvas de Bézier cuadráticas. Por lo tanto, muchos sistemas de gráfico moderno incluyen una función para convertir caracteres de texto en una ruta de acceso de gráficos. 

Ya ha visto que puede trazar los contornos de caracteres de texto, así como rellenarlo. Esto le permite mostrar estos contornos de caracteres con un ancho del trazo determinado e incluso un efecto de la ruta de acceso, como se describe en el [ **efectos de ruta de acceso** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) artículo. Pero también es posible convertir una cadena de caracteres en un `SKPath` objeto. Esto significa que los contornos de texto pueden utilizarse para efectuar el recorte con técnicas que se describen en la [ **con rutas de acceso y las regiones de recorte** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md) artículo. 

Además de utilizar un efecto de la ruta de acceso para trazar un esquema de caracteres, también puede crear efectos que se basan en una rutas de acceso se derivan de las cadenas de caracteres de la ruta de acceso y puede incluso combinar los dos efectos:

![](text-paths-images/pathsandtextsample.png "Efecto de la ruta de acceso de texto")

En el [artículo anterior](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) ha visto cómo [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/) método `SKPaint` puede obtener una descripción de un trazado. También puede utilizar este método con las rutas de acceso que se deriva de los contornos de caracteres.

Por último, este artículo muestra otro intersección de las rutas de acceso y el texto: el [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/) método `SKCanvas` permite mostrar una cadena de texto para que la línea de base del texto sigue un trazado curvo.

## <a name="text-to-path-conversion"></a>Texto a la conversión de la ruta de acceso

El [ `GetTextPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetTextPath/p/System.String/System.Single/System.Single/) método `SKPaint` convierte una cadena de caracteres en un `SKPath` objeto:

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

El `x` y `y` argumentos indican el punto inicial de la línea base del lado izquierdo del texto. Desempeñan la misma función aquí como en la `DrawText` método `SKCanvas`. Dentro de la ruta de acceso, la línea de base del lado izquierdo del texto tendrán las coordenadas (x, y). 

El `GetTextPath` método es excesiva si simplemente desea rellenar o trazar la ruta de acceso resultante. Vector normal `DrawText` método le permite hacerlo. El `GetTextPath` método es más útil para otras tareas relacionadas con las rutas de acceso.

Una de estas tareas se recorte. El **texto de recorte** página crea un trazado de recorte en función de los contornos de caracteres de la palabra "CODE". Esta ruta de acceso se ajusta al tamaño de la página para recortar el mapa de bits que contiene una imagen de la **ajustar texto** código fuente:

[![](text-paths-images/clippingtext-small.png "Captura de pantalla triple de la página de recorte de texto")](text-paths-images/clippingtext-large.png#lightbox "Triple captura de pantalla de la página de texto de recorte")

El [ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) constructor de clase carga el mapa de bits que se almacena como un recurso incrustado en el **Media** carpeta de la solución:

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

El `PaintSurface` controlador comienza creando un `SKPaint` adecuado para el texto del objeto. El `Typeface` propiedad está establecida, así como la `TextSize`, aunque, en esta aplicación concreta el `TextSize` propiedad es puramente arbirtrary. También observe que no hay ningún `Style` configuración.

El `TextSize` y `Style` valores de las propiedades no son necesarios porque esto `SKPaint` objeto se usa únicamente para el `GetTextPath` llama mediante la cadena de texto "CODE". El controlador, a continuación, mide el resultante `SKPath` objeto y aplica tres transformaciones para centrar y escalar para el tamaño de la página. A continuación, se puede establecer la ruta de acceso como el trazado de recorte:

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, 
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

Una vez que se establece la ruta de acceso de recorte, se puede mostrar el mapa de bits y se ajustarán a los contornos de caracteres. Observe el uso de la [ `AspectFill` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRect.AspectFill/p/SkiaSharp.SKSize/) método `SKRect` que calcula un rectángulo con el que rellenar la página conservando la relación de aspecto.

El **efecto de la ruta de acceso de texto** página convierte un carácter único "y" comercial en una ruta de acceso para crear un efecto de la ruta de acceso de 1D. Un objeto de pintura con este efecto de la ruta de acceso, a continuación, se usa para trazar el contorno de una versión más grande de ese mismo carácter:

[![](text-paths-images/textpatheffect-small.png "Captura de pantalla triple de la página de efecto de la ruta de acceso de texto")](text-paths-images/textpatheffect-large.png#lightbox "Triple captura de pantalla de la página de efecto de la ruta de acceso de texto")

Cantidad de trabajo en el [ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) clase se produce en los campos y el constructor. Los dos `SKPaint` objetos definidos como campos sirven para dos propósitos diferentes: la primera (denominada `textPathPaint`) se utiliza para convertir la y comercial con un `TextSize` de 50 en una ruta de acceso para el efecto de la ruta de acceso de 1D. El segundo (`textPaint`) se usa para mostrar una versión ampliada de la "y" comercial con un efecto de esa ruta de acceso. Por esta razón, el `Style` de este segundo paint objeto se establece en `Stroke`, pero la `StrokeWidth` no se establece la propiedad porque dicha propiedad no es necesaria cuando se usa un efecto de la ruta de acceso de 1D:

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint 
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds;
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character, 
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

El constructor usa primero el `textPathPaint` objeto para medir la y comercial con un `TextSize` de 50. Los negativos de las coordenadas del centro del rectángulo, a continuación, se pasan a la `GetTextPath` método para convertir el texto en una ruta de acceso. La ruta de acceso resultante tiene el (0, 0) en el centro del carácter, que es ideal para un efecto de la ruta de acceso de 1D de punto.

Puede pensar que el `SKPathEffect` objeto creado al final del constructor se puede establecer la `PathEffect` propiedad de `textPaint` en lugar de guardar como un campo. Pero esta no girada out a funcionar muy bien porque distorsione los resultados de la `MeasureText` llamar a en el `PaintSurface` controlador:

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds;
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

Que `MeasureText` llamada se usa para centrar el carácter en la página. Para evitar problemas, el `PathEffect` propiedad está establecida en el objeto de paint después de que se ha medido el texto, pero antes de que se muestre.

## <a name="outlines-of-character-outlines"></a>Contornos de contornos de caracteres

Normalmente el [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/) método `SKPaint` convierte una ruta de acceso a otro mediante la aplicación de propiedades de paint, sobre todo el ancho y la ruta de acceso del efecto trazo. Cuando se utiliza sin efectos de la ruta de acceso, `GetFillPath` crea eficazmente una ruta de acceso que describe otra ruta de acceso. Esto se muestra en el **pulse al esquema de la ruta de acceso** página en el [ **efectos de ruta de acceso** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) artículo.

También puede llamar a `GetFillPath` en la ruta de acceso devuelto desde `GetTextPath` pero al principio puede que no esté completamente seguro de lo que aspecto que tendrá.

El **contornos de contorno de carácter** página muestra la técnica. Todo el código relevante está en el `PaintSurface` controlador de la [ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) clase.

El constructor comienza creando un `SKPaint` objeto denominado `textPaint` con un `TextSize` propiedad en función del tamaño de la página. Esto se convierte en una ruta de acceso mediante el `GetTextPath` método. Los argumentos de coordenadas `GetTextPath` eficazmente del centro de la ruta de acceso en la pantalla:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds;
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

El `PaintSurface` controlador, a continuación, crea una nueva ruta de acceso con el nombre `outlinePath`. Esto se convierte en la ruta de acceso de destino en la llamada a `GetFillPath`. El `StrokeWidth` propiedad de 25 causas `outlinePath` para describir el contorno de una ruta de acceso de 25 píxeles de ancho trazado los caracteres de texto. Esta ruta de acceso, a continuación, se muestra en rojo con un ancho de trazo de 5:

[![](text-paths-images/characteroutlineoutlines-small.png "Captura de pantalla de la página de carácter esquema contornos triple")](text-paths-images/characteroutlineoutlines-large.png#lightbox "Triple captura de pantalla de la página de contornos de contorno de carácter")

Examine con atención y verá que se superpone en el esquema de la ruta de acceso hace un vértice agudo. Se trata de artefactos normales de este proceso.

## <a name="text-along-a-path"></a>Texto a lo largo de una ruta de acceso

Normalmente se muestra el texto en una línea de base horizontal. Se puede girar texto para ejecutar vertical o diagonalmente, pero la línea de base es una línea recta.

Hay ocasiones, sin embargo, cuando desee que el texto que se va a ejecutar a lo largo de una curva. Este es el propósito de la [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/) método `SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

El texto especificado en el primer argumento se intentó ejecutar a lo largo de la ruta de acceso especificada como segundo argumento. Puede comenzar el texto en un desplazamiento desde el principio de la ruta de acceso con el `hOffset` argumento. Normalmente, la ruta de acceso constituye la línea base del texto: trazos ascendentes de texto se encuentran en un lado de la ruta de acceso y trazos descendentes de texto, en el otro. Pero sólo puede desplazar la línea base del texto de la ruta de acceso con el `vOffset` argumento.

Este método no tiene ninguna facilidad para ofrecer orientación sobre la configuración de la `TextSize` propiedad de `SKPaint` para hacer que el texto cambiará de tamaño perfectamente para ejecutarse desde el principio de la ruta de acceso al final. A veces puede averiguar ese tamaño de texto por su cuenta. En otras ocasiones que necesitará usar funciones de medición de ruta de acceso para describir en un artículo futuro.

El **texto Circular** programa de texto ajusta alrededor de un círculo. Es fácil determinar la circunferencia de un círculo, por lo que es fácil cambiar el tamaño del texto para ajustarse exactamente. El `PaintSurface` controlador de la [ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) clase calcula un radio de un círculo en función del tamaño de la página. Se convierte en ese círculo `circularPath`:

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

El `TextSize` propiedad de `textPaint` , a continuación, se ajusta para que el ancho del texto coincida con la longitud de la circunferencia:

[![](text-paths-images/circulartext-small.png "Captura de pantalla triple de la página de texto Circular")](text-paths-images/circulartext-large.png#lightbox "Triple captura de pantalla de la página de texto Circular")

Se ha elegido el texto en Sí para ser circulares algo así: la palabra "círculo" es tanto el asunto de la oración y el objeto de una frase preposicionales. 

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
