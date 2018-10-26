---
title: Marco de trabajo de visión de Xamarin.iOS
description: Este documento describe cómo usar iOS 11 Vision Framework en Xamarin.iOS. En concreto, describe la detección de rectángulo y detección de caras.
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/31/2017
ms.openlocfilehash: 291cbdb93cfb6ac123d740e98065ba877bb44da5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104835"
---
# <a name="vision-framework-in-xamarinios"></a>Marco de trabajo de visión de Xamarin.iOS

El marco de trabajo de visión, agrega un número de nuevas características a iOS 11, incluidos de procesamiento de imágenes:

- [Detección de rectángulo](#rectangles)
- [Detección de caras](#faces)
- Machine Learning análisis de imágenes (se describe en [CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- Detección de código de barras
- Análisis de alineación de imagen
- Detección de texto
- Detección de horizonte
- Seguimiento de & detección de objetos

![Fotografiar con tres rectángulos detectados](vision-images/found-rectangles-tiny.png) ![Fotografiar con dos caras detectadas](vision-images/xamarin-home-faces-tiny.png)

Detección de rectángulo y detección de caras se tratan con más detalle a continuación.

<a name="rectangles" />

## <a name="rectangle-detection"></a>Detección de rectángulo

El [VisionRects ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) muestra cómo procesar una imagen y dibujar los rectángulos detectados en él.

### <a name="1-initialize-the-vision-request"></a>1. Inicializar la solicitud de visión

En `ViewDidLoad`, cree un `VNDetectRectanglesRequest` que hace referencia a la `HandleRectangles` método que se llamará al final de cada solicitud:

El `MaximumObservations` también se debe establecer la propiedad, en caso contrario, el valor predeterminado será 1 y se devolverá sólo un único resultado.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. Iniciar el procesamiento de visión

El siguiente código empieza a procesar la solicitud. En el **VisionRects** ejemplo, este código se ejecuta después de que el usuario ha seleccionado una imagen:

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Este controlador pasa el `ciImage` para el marco de trabajo de visión `VNDetectRectanglesRequest` que se creó en el paso 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Controlar los resultados del procesamiento de visión

Una vez completada la detección de rectángulo, el marco ejecuta el `HandleRectangles` método, a continuación se muestra un resumen de los cuales:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. Mostrar los resultados

El `OverlayRectangles` método en el **VisionRectangles** ejemplo tiene tres funciones:

- Representación de la imagen de origen
- Dibuja un rectángulo para indicar donde se detectó cada uno de ellos, y
- Agregar una etiqueta de texto de cada rectángulo usando CoreGraphics.

Ver el [origen del ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) para el método CoreGraphics exacto.

![Fotografiar con tres rectángulos detectados](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. Procesamiento posterior

Detección de rectángulo a menudo es solo el primer paso en una cadena de operaciones, como con [en este ejemplo CoreMLVision](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), donde los rectángulos se pasan a un modelo de CoreML para analizar los dígitos escritos a mano.


<a name="faces" />

## <a name="face-detection"></a>Detección de caras

El [VisionFaces ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) funciona de manera similar a la **VisionRectangles** de ejemplo, mediante una clase diferente de solicitud de visión.

### <a name="1-initialize-the-vision-request"></a>1. Inicializar la solicitud de visión

En `ViewDidLoad`, cree un `VNDetectFaceRectanglesRequest` que hace referencia a la `HandleRectangles` método que se llamará al final de cada solicitud.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. Iniciar el procesamiento de visión

El siguiente código empieza a procesar la solicitud. En el **VisionFaces** este código se ejecuta después de que el usuario ha seleccionado una imagen de ejemplo:

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Este controlador pasa el `ciImage` para el marco de trabajo de visión `VNDetectFaceRectanglesRequest` que se creó en el paso 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Controlar los resultados del procesamiento de visión

Una vez completada la detección de caras, el controlador ejecuta la `HandleRectangles` método que realiza el control de errores y muestra los límites de las caras detectadas y llama a la `OverlayRectangles` para dibujar rectángulos en la imagen original:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. Mostrar los resultados

El `OverlayRectangles` método en el **VisionFaces** ejemplo tiene tres funciones:

- Representación de la imagen de origen
- Dibuja un rectángulo para cada rostro detectado, y
- Agregar una etiqueta de texto para cada cara con CoreGraphics.

Ver el [origen del ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) para el método CoreGraphics exacto.

![Fotografiar con dos caras detectadas](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. Procesamiento posterior

El marco de trabajo de visión incluye funcionalidades adicionales para detectar características faciales, como los ojos y boca. Use la `VNDetectFaceLandmarksRequest` tipo, lo cual devolverá `VNFaceObservation` resultados que en el paso 3 anterior, pero otras `VNFaceLandmark` datos.


## <a name="related-links"></a>Vínculos relacionados

- [Rectángulos de visión (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)
- [Visión caras (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)
- [Avances en la imagen de Core - filtros, sistema operativo, visión y más (sesión WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/510/)
