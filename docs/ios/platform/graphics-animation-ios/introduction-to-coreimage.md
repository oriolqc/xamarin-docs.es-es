---
title: Imagen del núcleo de Xamarin.iOS
description: Imagen de Core es un nuevo marco introducido con iOS 5 para proporcionar procesamiento de imágenes y funcionalidad de vídeo de mejora de live. Este artículo presenta estas características con ejemplos de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: d71f14c26865b71eca991910df4a68f2540f9715
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085571"
---
# <a name="core-image-in-xamarinios"></a>Imagen del núcleo de Xamarin.iOS

_Imagen de Core es un nuevo marco introducido con iOS 5 para proporcionar procesamiento de imágenes y funcionalidad de vídeo de mejora de live. Este artículo presenta estas características con ejemplos de Xamarin.iOS._

Imagen de Core es un nuevo marco introducido en iOS 5 que proporciona una serie de filtros integrados y los efectos para aplicarse a las imágenes y vídeos, incluida la detección de caras.

Este documento contiene ejemplos sencillos de:

-  Detección de caras.
-  Aplicar filtros a una imagen
-  Enumerar los filtros disponibles.


Estos ejemplos ayudarán a comenzar la incorporación de características de la imagen principal en las aplicaciones de Xamarin.iOS.

## <a name="requirements"></a>Requisitos

Debe usar la versión más reciente de Xcode.

## <a name="face-detection"></a>Detección de caras

La característica de detección de cara de imagen Core hace justo lo que dice: que intenta identificar caras de una foto y devuelve las coordenadas de las caras que reconoce. Esta información puede utilizarse para contar el número de personas de una imagen, dibuje indicadores en la imagen (p ej. para "etiquetado" las personas en una fotografía), o cualquier otra cosa que puede considerar.

Este código de CoreImage\SampleCode.cs muestra cómo crear y usar la detección de caras en una imagen incrustada:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

La matriz de características se rellenará con `CIFaceFeature` objetos (si se detectan cualquier caras). Hay un `CIFaceFeature` para cada cara. `CIFaceFeature` tiene las siguientes propiedades:

-  HasMouthPosition: si se ha detectado una boca para esta cara.
-  HasLeftEyePosition: si se ha detectado el ojo izquierdo para esta cara.
-  HasRightEyePosition: si se ha detectado el ojo derecho para esta cara. 
-  MouthPosition: las coordenadas de la boca para esta cara.
-  LeftEyePosition: las coordenadas del ojo izquierdo para esta cara.
-  RightEyePosition: las coordenadas del ojo para esta cara adecuada.


Las coordenadas para todas estas propiedades tienen su origen en la parte inferior izquierda: a diferencia de UIKit que usa la parte superior izquierda como el origen. Al usar las coordenadas en `CIFaceFeature` no olvide 'póngalos'. Esta vista de imagen personalizada básica en CoreImage\CoreImageViewController.cs muestra cómo se dibujan los triángulos 'indicador cara' en la imagen (tenga en cuenta el `FlipForBottomOrigin` método):

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

A continuación, en el archivo SampleCode.cs la imagen y las características se asignan antes de que se vuelve a dibujar la imagen:

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

La captura de pantalla muestra la salida de ejemplo: las ubicaciones de las características faciales detectadas se muestran en un UITextView y dibujadas en la imagen de origen mediante CoreGraphics.

Debido a la manera en que el reconocimiento facial funciona lo detectará ocasionalmente cosas además de caras humanas (por ejemplo, estos monos juguete!).

## <a name="filters"></a>Filtros

Hay más de 50 distintos filtros integrados y el marco de trabajo es extensible para que se pueden implementar los nuevos filtros.

## <a name="using-filters"></a>Uso de filtros

Aplicar un filtro a una imagen consta de cuatro pasos distintos: carga de la imagen, crear el filtro, aplicar el filtro y guardar (o mostrar) el resultado.

En primer lugar, cargue una imagen en un `CIImage` objeto.

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

En segundo lugar, cree la clase de filtro y establecer sus propiedades.

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

En tercer lugar, tener acceso a la `OutputImage` propiedad y llame a la `CreateCGImage` método para representar el resultado final.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Por último, puede asignar la imagen a una vista para ver el resultado. En una aplicación real, la imagen resultante podría estar guardada en el sistema de archivos, el álbum de fotos, un Tweet o correo electrónico.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Estas capturas de pantalla muestran el resultado de la `CISepia` y `CIHueAdjust` filtros que se muestran en la CoreImage.zip código de ejemplo.

Consulte la [ajustar contrato y el brillo de una imagen de receta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) para obtener un ejemplo de la `CIColorControls` filtro.

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>Lista de filtros y sus propiedades

Este código de CoreImage\SampleCode.cs genera una lista completa de los filtros integrados y sus parámetros.

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

El [referencia de clase CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) describe los 50 filtros integrados y sus propiedades. Con el código anterior, puede consultar las clases de filtro, incluidos los valores predeterminados para los parámetros y los valores permitidos de máximo y mínimos (que podrían usarse para validar las entradas antes de aplicar un filtro).

El resultado de las categorías de lista es similar al siguiente en el simulador, puede desplazarse por la lista para ver todos los filtros y sus parámetros.

 [![](introduction-to-coreimage-images/coreimage05.png "El resultado de las categorías de lista es similar al siguiente en el simulador")](introduction-to-coreimage-images/coreimage05.png#lightbox)

Cada filtro enumerado se expone como una clase en Xamarin.iOS, por lo que también puede explorar la API de Xamarin.iOS.CoreImage en el Explorador de ensamblados o usar Autocompletar en Visual Studio para Mac o Visual Studio. 

## <a name="summary"></a>Resumen

En este artículo se muestra cómo utilizar algunas de las nuevas características de iOS 5 imagen Core framework como la detección y aplicar filtros a una imagen. Existen docenas de filtros de imágenes diferentes disponibles en el marco de trabajo para su uso.

## <a name="related-links"></a>Vínculos relacionados

- [Imagen de Core (ejemplo)](https://developer.xamarin.com/samples/CoreImage/)
- [Ajustar el brillo de una imagen de receta y contrato](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [Uso de filtros de imágenes de núcleo](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Referencia de clase CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
