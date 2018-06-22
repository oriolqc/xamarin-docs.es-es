---
title: Para los gráficos esenciales en Xamarin.iOS
description: En este artículo se describe los marcos de iOS para los gráficos esenciales. Muestra cómo se utiliza para los gráficos esenciales para dibujar la geometría, imágenes y archivos PDF.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 7d7124c7d09ca4e36ce22d60f578ea4a75d4a05b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786761"
---
# <a name="core-graphics-in-xamarinios"></a>Para los gráficos esenciales en Xamarin.iOS

_En este artículo se describe los marcos de iOS para los gráficos esenciales. Muestra cómo se utiliza para los gráficos esenciales para dibujar la geometría, imágenes y archivos PDF._

iOS incluye la [ *Core gráficos* ](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) framework para proporcionar compatibilidad con el dibujo bajo nivel. Estos marcos son lo que habilitar las grandes capacidades gráficas en UIKit. 

Gráficos de núcleo es un marco de trabajo de bajo nivel gráficos 2D que permite dibujar gráficos independientes del dispositivo. Todos los 2D dibujar en UIKit usa internamente para los gráficos esenciales.

Para los gráficos esenciales admite dibujar en una serie de escenarios incluidos:

-  [Dibujar en la pantalla a través de un `UIView` ](#Drawing_in_a_UIView_Subclass) .
-  [Dibujar imágenes en la memoria o en pantalla](#Drawing_Images_and_Text).
-  Crear y dibujar en un archivo PDF.
-  Lectura y el dibujo de un PDF existente.


## <a name="geometric-space"></a>Espacio geométrica

Independientemente del escenario, todo el dibujo realizado con gráficos de núcleo se realiza en geométrica espacio, lo que significa que funciona en puntos abstractos en lugar de píxeles. Se describe la acción que realizará dibujado en términos de geometría y el dibujo de estado, como colores, estilos de línea, etc. y para los gráficos esenciales controla todo traducir en píxeles. Dicho estado se agrega a un contexto de gráficos, que se puede considerar como lienzo de un pintor.

Hay algunas ventajas de este enfoque:

-  Código de dibujo se convierte en dinámico y posteriormente puede modificar el gráfico en tiempo de ejecución.
-  Lo que reduce la necesidad de imágenes estáticas en el paquete de aplicación puede reducir el tamaño de la aplicación.
-  Gráficos se convierten en más resistentes a los cambios en la resolución en todos los dispositivos.

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>Dibujar en una subclase de UIView

Cada `UIView` tiene un `Draw` método al que se llama por el sistema cuando sea necesario dibujar. Para agregar código de dibujo a una vista de la subclase `UIView` e invalide `Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Nunca se debería llamar directamente a Draw. Es llamado por el sistema durante el procesamiento de bucle de ejecución. La primera vez en el bucle de ejecución después de agrega una vista a la jerarquía de vista, su `Draw` se llama al método. Las llamadas subsiguientes a `Draw` se producen cuando la vista está marcada como que necesitan dibujar llamando `SetNeedsDisplay` o `SetNeedsDisplayInRect` en la vista.

### <a name="pattern-for-graphics-code"></a>Modelo de código de gráficos

El código en el `Draw` implementación debe describir lo que quiere dibujado. El código de dibujo sigue un patrón en el que se establece un estado de dibujo y llama a un método para solicitar que se dibuja. Este patrón se puede generalizar como sigue:

1. Obtiene un contexto de gráficos.

2. Configurar atributos de dibujo.

3. Crear algunos objeto geometry a partir de tipos primitivos de dibujo.

4. Llamar a un método Draw o trazo.

### <a name="basic-drawing-example"></a>Ejemplo de dibujo básico

Por ejemplo, considere el siguiente fragmento de código:

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {
            
    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100), 
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);       
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

Vamos a desglosar este código:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
Con esta línea, primero obtiene el contexto de gráficos actual que se usará para dibujar. Puede considerar un contexto gráfico como el lienzo que dibujo ocurre en, que contiene todos los Estados sobre los dibujos, como los colores de relleno y trazo, así como la geometría que se va a dibujar.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

Después de obtener un contexto de gráficos el código configura algunos atributos a usar al dibujar, se muestra anteriormente. En este caso se establecen los colores de línea de ancho, trazo y relleno. Todos los dibujos posteriores, a continuación, se utilizará estos atributos porque se mantienen en el estado del contexto de gráficos.

Para crear el código de objeto geometry usa un `CGPath`, lo que permite una ruta de acceso de gráficos se puede describir de líneas y curvas conectadas. En este caso, la ruta de acceso agrega las líneas que conectan una matriz de puntos que forman un triángulo. Tal como se muestra a continuación para los gráficos esenciales utiliza un sistema de coordenadas para la vista de dibujo, donde el origen está en la parte superior izquierda, con x-direct positivo hacia la derecha y la dirección del eje y positivo hacia abajo:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

Una vez que se crea la ruta de acceso, se agrega al contexto de gráficos para que una llamada a `AddPath` y `DrawPath` respectivamente puede dibujarlo.

La vista resultante se muestra a continuación:

 ![](core-graphics-images/00-bluetriangle.png "El triángulo de la salida de ejemplo")

## <a name="creating-gradient-fills"></a>Crear degradados

También existen formas más enriquecidas de dibujo. Por ejemplo, para los gráficos esenciales permite crear degradados y aplicar las rutas de acceso de recorte. Para dibujar un relleno de degradado dentro de la ruta de acceso del ejemplo anterior, primero la ruta de acceso debe establecerse como el trazado de recorte:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

Si se establece la ruta de acceso actual como el trazado de recorte restringe todos los dibujos posteriores a la geometría de la ruta de acceso, por ejemplo, el código siguiente, que dibuja un degradado lineal:

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient, 
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top), 
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom), 
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

Estos cambios producen un relleno de degradado tal y como se muestra a continuación:

 ![](core-graphics-images/01-gradient-fill.png "El ejemplo con un relleno de degradado")

## <a name="modifying-line-patterns"></a>Modificar patrones de línea

También es posible modificar los atributos de dibujo de líneas con gráficos de núcleo. Esto incluye cambiar el color de trazo y ancho de línea, así como el patrón de línea, tal como se muestra en el código siguiente:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

Agregar este código antes de los resultados de las operaciones de dibujo en unidades de trazos discontinuos 10 largo, con 4 unidades de espaciado entre los guiones, tal y como se muestra a continuación:

 ![](core-graphics-images/02-dashed-stroke.png "Agregar este código antes de los resultados de las operaciones de dibujo en trazos discontinuos")
 
Tenga en cuenta que al utilizar la API unificada en Xamarin.iOS, el tipo de matriz debe ser un `nfloat`y también debe convertirse explícitamente a Math.PI.

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>Texto e imágenes de dibujo

Además de dibujar trazados en contexto de gráficos de una vista, para los gráficos esenciales también admite dibujar imágenes y texto. Para dibujar una imagen, basta con crear una `CGImage` y lo pasa a un `DrawImage` llamar:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

Sin embargo, esto produce una imagen dibujada boca abajo, tal y como se muestra a continuación:

 ![](core-graphics-images/03-upside-down-monkey.png "Una imagen dibujada boca abajo")

La razón de ello es el origen de los gráficos de núcleo de dibujo de la imagen está en la parte inferior izquierda, mientras que la vista tiene su origen en la parte superior izquierda. Por lo tanto, para mostrar correctamente la imagen, el origen debe modificarse, que puede realizarse mediante la modificación de la *actual matriz de transformación* *(CTM)*. El CTM define donde resida puntos, también conocido como *espacio de usuario*. Invertir el CTM en la dirección del eje y y desplazamiento por el alto de los límites en la dirección del eje y negativo pueden girar la imagen.

El contexto de gráficos tiene métodos auxiliares para transformar la CTM. En este caso, `ScaleCTM` "voltea" el dibujo y `TranslateCTM` desplaza a la parte superior izquierda, tal y como se muestra a continuación:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
    
    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}   
```

A continuación, se muestra la imagen resultante en vertical:

 ![](core-graphics-images/04-upright-monkey.png "El verticales de la imagen que se muestra de ejemplo")

> [!IMPORTANT]
> Cambios en el contexto de gráficos se aplican a todas las operaciones de dibujo. Por lo tanto, cuando se transforma el CTM, afectará a todos los dibujos adicionales. Por ejemplo, si el triángulo que dibujó después de la transformación de CTM, podría aparecer boca abajo.

### <a name="adding-text-to-the-image"></a>Agregar texto a la imagen

Como con las rutas de acceso y las imágenes, dibujar texto con gráficos principales implica el mismo patrón básico de un estado de gráficos y llamar a un método para dibujar. En el caso de texto, el método para mostrar el texto es `ShowText`. Cuando se agrega a la imagen de ejemplo de dibujo, el código siguiente dibuja texto con gráficos esenciales:

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);
    
    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

Como puede ver, establecer el estado de los gráficos para dibujar el texto es similar a la geometría de dibujo. Para texto dibujar sin embargo, también se aplican el dibujo modo y la fuente del texto. En este caso, una sombra también se aplica, aunque aplicar sombras funciona la misma ruta de acceso de dibujo.

El texto resultante se muestra con la imagen tal y como se muestra a continuación:

 ![](core-graphics-images/05-text-on-image.png "El texto resultante se muestra con la imagen")

## <a name="memory-backed-images"></a>Imágenes basadas en memoria

Además de dibujo al contexto de gráficos de una vista, admite el núcleo gráficos dibujo memoria copia imágenes, también conocido como dibujar fuera de la pantalla. Si lo hace, se necesita:

-  Crear un contexto de gráficos que está respaldado por una memoria en mapa de bits
-  Establecer el estado del dibujo y emitir comandos de dibujo
-  Obtener la imagen desde el contexto
-  Quitar el contexto


A diferencia de la `Draw` (método), donde se proporciona el contexto de la vista, en este caso que crea el contexto de una de dos maneras:

1. Mediante una llamada a `UIGraphics.BeginImageContext` (o `BeginImageContextWithOptions`)

2. Al crear un nuevo `CGBitmapContextInstance`

 `CGBitmapContextInstance` es útil cuando se trabaja directamente con los bits de imagen, como en los casos donde se usa un algoritmo de manipulación de imagen personalizada. En todos los demás casos, debe usar `BeginImageContext` o `BeginImageContextWithOptions`.

Una vez que tenga un contexto de la imagen, agregar código de dibujo es igual que está en un `UIView` subclase. Por ejemplo, el ejemplo de código que se usó previamente para dibujar un triángulo puede utilizarse para dibujar en una imagen en memoria en lugar de en un `UIView`, tal y como se muestra a continuación:

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100), 
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }
    
    return triangleImage;
}
```

Un uso común de dibujo a un mapa de bits basada en memoria es capturar una imagen desde cualquier `UIView`. Por ejemplo, el siguiente código representa el nivel de la vista a un contexto de mapa de bits y crea un `UIImage` de él:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Archivos PDF de dibujo

Además de las imágenes, gráficos de núcleo admite el dibujo de PDF. Como imágenes, puede representar un archivo PDF en la memoria, así como leer un PDF para la representación en un `UIView`.

### <a name="pdf-in-a-uiview"></a>PDF en un UIView

Para los gráficos esenciales también admite leer un PDF de un archivo y la representación de una vista con la `CGPDFDocument` clase. La `CGPDFDocument` clase representa un archivo PDF en el código y puede usarse para leer y dibujar páginas.

Por ejemplo, el siguiente código en un `UIView` subclase lee un archivo PDF de un archivo en un `CGPDFDocument`:

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }
  
     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

El `Draw` , a continuación, puede utilizar el método la `CGPDFDocument` para leer una página en `CGPDFPage` y representar mediante una llamada a `DrawPDFPage`, tal y como se muestra a continuación:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
        
    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);
        
        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {
            
        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);
        
        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);
        
        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>PDF basado en memoria

Para un PDF en memoria, debe crear un contexto PDF mediante una llamada a `BeginPDFContext`. Dibujo a PDF es específico a las páginas. Cada página se inicia mediante una llamada a `BeginPDFPage` y se ha completado llamando `EndPDFContent`, los gráficos de código. Además, como con el dibujo de la imagen, memoria copiados PDF dibujo usa un origen en la parte inferior izquierda, que se tienen en cuenta modificando el CTM simplemente al igual que con imágenes.

El código siguiente muestra cómo dibujar texto en un archivo PDF:

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();
       
using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);      
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }
    
//complete a PDF page
UIGraphics.EndPDFContent ();
```

El texto resultante se dibuja en el archivo PDF, que, a continuación, se incluye en un `NSData` que puede guardarse, cargado, por correo electrónico, etcetera.


## <a name="summary"></a>Resumen

En este artículo analizamos las capacidades de gráficos que se proporcionan a través de la *Core gráficos* framework. Hemos visto cómo se utiliza para los gráficos esenciales para dibujar la geometría, imágenes y archivos PDF en el contexto de un `UIView,` así como a los contextos de gráficos basado en memoria.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de gráficos básica](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Gráficos y animación tutorial](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animación básica](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Recetas de animación de núcleo](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
