---
title: Gráficos esenciales en Xamarin.iOS
description: En este artículo se analiza los marcos de iOS de gráficos básicos. Muestra cómo usar Core Graphics para dibujar geometría, imágenes y archivos PDF.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f3fe22e56a2c45524923a316ef28e54e5a3cc3f8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102976"
---
# <a name="core-graphics-in-xamarinios"></a>Gráficos esenciales en Xamarin.iOS

_En este artículo se analiza los marcos de iOS de gráficos básicos. Muestra cómo usar Core Graphics para dibujar geometría, imágenes y archivos PDF._

iOS incluye el [ *Core Graphics* ](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) framework para proporcionar soporte técnico de dibujo de bajo nivel. Estos marcos son lo que habilitan las capacidades gráficas enriquecidas dentro de UIKit. 

Gráficos de Core es un marco de gráficos 2D de bajo nivel que permite dibujar gráficos independientes del dispositivo. Dibujo de UIKit 2D que todos los utiliza internamente Core Graphics.

Core gráficos admite el dibujo en un número de escenarios, como:

-  [Dibuja en la pantalla a través de un `UIView` ](#Drawing_in_a_UIView_Subclass) .
-  [Dibujar imágenes en la memoria o en pantalla](#Drawing_Images_and_Text).
-  Crear y dibujar en un archivo PDF.
-  Lectura y dibuje un PDF existente.


## <a name="geometric-space"></a>Espacio geométrica

Independientemente del escenario, todos los dibujos realiza con gráficos básicos se realiza en espacio geométrica, lo que significa que funciona en puntos abstractos en lugar de píxeles. Describa qué desea dibujado en términos de geometría y estado como colores, estilos de línea, etc. de dibujo y Core Graphics controla la traducción de todo, en píxeles. Dicho estado se agrega a un contexto de gráficos, que se puede considerar como lienzo de un pintor.

Hay algunas ventajas de este enfoque:

-  Código de dibujo se convierte en dinámico y posteriormente puede modificar los gráficos en tiempo de ejecución.
-  Reduce la necesidad de imágenes estáticas en el paquete de aplicación puede reducir el tamaño de la aplicación.
-  Gráficos se convierten en sea más resistentes a los cambios de resolución en todos los dispositivos.

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>Dibujar en una subclase UIView

Cada `UIView` tiene un `Draw` método llamado por el sistema cuando sea necesario dibujar. Para agregar código de dibujo a una vista de la subclase `UIView` e invalidar `Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Nunca se debería llamar directamente a Draw. Es llamado por el sistema durante el procesamiento de bucle de ejecución. La primera vez a través del bucle de ejecución después de agrega una vista a la jerarquía de vistas, su `Draw` se llama al método. Las llamadas subsiguientes a `Draw` se producen cuando la vista está marcada como que necesitan dibujar llamando `SetNeedsDisplay` o `SetNeedsDisplayInRect` en la vista.

### <a name="pattern-for-graphics-code"></a>Patrón para el código de gráficos

El código en el `Draw` implementación debe describir lo que desea dibujar. El código de dibujo sigue un patrón en el que establece algún estado de dibujo y llama a un método para solicitar que se dibuja. Este patrón se puede generalizar como sigue:

1. Obtiene un contexto de gráficos.

2. Configurar los atributos de dibujo.

3. Crear algunos geometría de dibujo primitivos.

4. Llame al método un trazo o dibujo.

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
Con esta línea, primero obtiene el contexto de gráficos actual para utilizar para dibujar. Puede pensar un contexto de gráficos como el lienzo que dibujo se produce en, que contiene todos los Estados sobre el dibujo, como los colores de relleno y trazo, así como la geometría que se va a dibujar.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

Después de obtener un contexto de gráficos el código configura algunos atributos que se usará al dibujar, se muestra anteriormente. En este caso se establecen los colores de línea de ancho, trazo y relleno. Los dibujos posteriores, a continuación, usará estos atributos porque se mantienen en estado del contexto de gráficos.

Para crear geometría en el código usa un `CGPath`, lo que permite una ruta de acceso de gráficos que se describirá en líneas y curvas. En este caso, la ruta de acceso agrega las líneas que conectan una matriz de puntos que componen un triángulo. Tal como se muestra debajo de Graphics Core usa un sistema de coordenadas para la vista de dibujo, donde el origen es la esquina superior izquierda, con x-direct positivo a la derecha y la dirección positiva y hacia abajo:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

Una vez creada la ruta de acceso, se agrega al contexto de gráficos para que una llamada a `AddPath` y `DrawPath` respectivamente puede dibujarlo.

La vista resultante se muestra a continuación:

 ![](core-graphics-images/00-bluetriangle.png "El triángulo de la salida de ejemplo")

## <a name="creating-gradient-fills"></a>Crear rellenos de degradado

También están disponibles los formularios más completas de dibujo. Por ejemplo, gráficos de Core permite crear rellenos de degradado y aplicar los trazados de recorte. Para dibujar un relleno de degradado dentro de la ruta de acceso del ejemplo anterior, primero la ruta de acceso debe establecerse como el trazado de recorte:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

Si se establece la ruta de acceso actual como el trazado de recorte restringe todos los dibujos subsiguientes dentro de la geometría de la ruta de acceso, por ejemplo, el código siguiente, que dibuja un degradado lineal:

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

Estos cambios producen un relleno de degradado, tal como se muestra a continuación:

 ![](core-graphics-images/01-gradient-fill.png "El ejemplo con un relleno de degradado")

## <a name="modifying-line-patterns"></a>Modificar modelos de línea

También pueden modificarse los atributos de dibujo de líneas con gráficos esenciales. Esto incluye cambiar el color de trazo y el ancho de línea, así como el patrón de línea, tal como se muestra en el código siguiente:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

Agregar este código antes de los resultados de las operaciones de dibujo en unidades de trazos discontinuos 10 largo, con 4 unidades de espaciado entre guiones, tal como se muestra a continuación:

 ![](core-graphics-images/02-dashed-stroke.png "Agregar este código antes de los resultados de las operaciones de dibujo en trazos discontinuos")
 
Tenga en cuenta que al usar la API unificada en Xamarin.iOS, el tipo de matriz debe ser un `nfloat`y también debe convertirse explícitamente al Math.PI.

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>Texto e imágenes de dibujo

Dibujar trazados en contexto de gráficos de una vista, además de Graphics Core también admite dibujar imágenes y texto. Para dibujar una imagen, basta con crear un `CGImage` y pasarlo a un `DrawImage` llamar:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

Sin embargo, esto genera una imagen dibujada al revés, tal como se muestra a continuación:

 ![](core-graphics-images/03-upside-down-monkey.png "Una imagen dibujada boca abajo")

La razón de esto es origen de gráficos básicos de dibujo de imagen es en la parte inferior izquierda, mientras que la vista tiene su origen en la esquina superior izquierda. Por lo tanto, para mostrar la imagen correctamente, el origen debe modificarse, que puede realizarse mediante la modificación de la *actual matriz de transformación* *(CTM)*. El CTM define que residen puntos, también conocido como *espacio de usuario*. Invertir el CTM en la dirección del eje y y desplazar por la altura de los límites en la dirección y negativo pueden voltear la imagen.

El contexto de gráficos tiene métodos auxiliares para transformar el CTM. En este caso, `ScaleCTM` "voltea" el dibujo y `TranslateCTM` lo desplaza a la parte superior izquierda, como se muestra a continuación:

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

A continuación, se muestra la imagen resultante vertical:

 ![](core-graphics-images/04-upright-monkey.png "La posición vertical de imagen que se muestra de ejemplo")

> [!IMPORTANT]
> Los cambios realizados en el contexto de gráficos se aplican a todas las operaciones de dibujos subsiguientes. Por lo tanto, cuando se transforma el CTM, afectará a todos los dibujos. Por ejemplo, si ha dibujado el triángulo después de la transformación CTM, aparecería boca abajo.

### <a name="adding-text-to-the-image"></a>Agregar texto a la imagen

Como con las rutas de acceso e imágenes, dibujar texto con gráficos de Core implica el mismo patrón básico de configuración de estado de los gráficos y llamar a un método para dibujar. En el caso de texto, el método para mostrar texto es `ShowText`. Cuando se agrega a la imagen de ejemplo de dibujo, el siguiente código dibuja texto con gráficos básicos:

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

Como puede ver, establecer el estado de los gráficos para dibujar el texto es similar a dibujar la geometría. Texto de dibujo sin embargo, el texto de la fuente y el modo de dibujo se aplican también. En este caso, una sombra también se aplica, aunque aplicar sombras funciona de la misma ruta de acceso de dibujo.

El texto resultante se muestra con la imagen como se muestra a continuación:

 ![](core-graphics-images/05-text-on-image.png "El texto resultante se muestra con la imagen")

## <a name="memory-backed-images"></a>Imágenes de seguridad de memoria

Además de dibujo al contexto de gráficos de una vista, admite el Core Graphics dibujo memoria habían respaldada imágenes, también conocido como dibujar fuera de la pantalla. Si lo hace, se requiere:

-  Creación de un contexto de gráficos que está respaldado por una memoria de mapa de bits
-  Establecer el estado del dibujo y emitir comandos de dibujo
-  Introducción a la imagen desde el contexto
-  Quitando el contexto


A diferencia de la `Draw` método, donde el contexto proporcionado por la vista, en este caso que crea el contexto de dos maneras:

1. Mediante una llamada a `UIGraphics.BeginImageContext` (o `BeginImageContextWithOptions`)

2. Creando un nuevo `CGBitmapContextInstance`

 `CGBitmapContextInstance` es útil cuando se trabaja directamente con los bits de imagen, como para los casos donde se usa un algoritmo de manipulación de la imagen personalizada. En todos los demás casos, debe usar `BeginImageContext` o `BeginImageContextWithOptions`.

Una vez que un contexto de la imagen, agregar código de dibujo es tal como es en un `UIView` subclase. Por ejemplo, el ejemplo de código que se usó anteriormente para dibujar un triángulo puede utilizarse para dibujar una imagen en la memoria en lugar de en un `UIView`, tal y como se muestra a continuación:

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

Un uso común de dibujar un mapa de bits basada en memoria es capturar una imagen desde cualquier `UIView`. Por ejemplo, el código siguiente representa la capa de la vista al contexto de un mapa de bits y crea un `UIImage` de él:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Archivos PDF de dibujo

Además de las imágenes, gráficos de Core admite el dibujo de PDF. Como imágenes, puede representar un PDF en la memoria, así como leer un archivo PDF para la representación en un `UIView`.

### <a name="pdf-in-a-uiview"></a>PDF en un UIView

Gráficos de Core también admite leer un archivo PDF de un archivo y representarlo en una vista mediante el `CGPDFDocument` clase. La `CGPDFDocument` clase representa un archivo PDF en el código y puede usarse para leer y dibujar las páginas.

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

El `Draw` , a continuación, puede usar el método la `CGPDFDocument` leer una página en `CGPDFPage` y para representarlo mediante una llamada a `DrawPDFPage`, tal y como se muestra a continuación:

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

Para un archivo PDF en memoria, deberá crear un contexto PDF mediante una llamada a `BeginPDFContext`. Dibujar en PDF es granular a las páginas. Cada página se inicia mediante una llamada a `BeginPDFPage` y se ha completado llamando `EndPDFContent`, los gráficos de código. Además, como con el dibujo de imagen, memoria copiados PDF dibujo usa un origen en la parte inferior izquierda, que puede tenerse modificando el CTM simplemente como con imágenes.

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

El texto resultante se dibuja en el archivo PDF, que, a continuación, se incluye en un `NSData` que pueden guardarse, cargado, por correo electrónico, etcetera.


## <a name="summary"></a>Resumen

En este artículo explicamos las capacidades gráficas que proporciona a través de la *Core Graphics* framework. Hemos visto cómo usar Core Graphics para dibujar geometría, imágenes y archivos PDF en el contexto de un `UIView,` así como a los contextos de seguridad de memoria de gráficos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de gráficos de Core](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Gráficos y animación tutorial](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animación básica](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Recetas de animación de Core](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
