---
title: Introducción a CoreML en Xamarin.iOS
description: Este documento describe CoreML, que permite el aprendizaje automático en iOS. Este documento describe cómo empezar a trabajar con CoreML y cómo se usa con el marco de trabajo de visión.
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2017
ms.openlocfilehash: 13178d4530e3214c6cf31c1018b21815ccd2227f
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350696"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Introducción a CoreML en Xamarin.iOS

CoreML aporta machine learning para iOS: las aplicaciones pueden aprovechar los modelos de aprendizaje automático entrenado para realizar a todo tipo de tareas, desde la solución de problemas para el reconocimiento de imágenes.

Esta introducción trata lo siguiente:

- [Introducción a CoreML](#coreml)
- [Uso de CoreML con el marco de trabajo de visión](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Introducción a CoreML

Estos pasos describen cómo agregar CoreML a un proyecto de iOS. Hacer referencia a la [ejemplo Mars Habitat Pricer](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) para obtener un ejemplo práctico.

![Captura de pantalla de ejemplo de Predictor de precios de MARS Habitat](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1. Agregar el modelo de CoreML al proyecto

Agregar un modelo de CoreML (un archivo con el **.mlmodel** extensión) a la **recursos** directorio del proyecto. 

En las propiedades del archivo de modelo, su **acción de compilación** está establecido en **CoreMLModel**. Esto significa que se compilarán en un **.mlmodelc** archivo cuando se compila la aplicación.

### <a name="2-load-the-model"></a>2. El modelo de carga

Cargar el modelo mediante el `MLModel.Create` método estático:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Establezca los parámetros

Se pasan los parámetros del modelo de entrada y salida mediante una clase de contenedor que implementa `IMLFeatureProvider`.

Clases de proveedor de características se comportan como un diccionario de cadenas y `MLFeatureValue`s, donde cada valor de la característica podría ser una cadena sencilla o número, una matriz o datos o un búfer de píxeles que contiene una imagen.

A continuación se muestra código para un proveedor de características de valor único:

```csharp
public class MyInput : NSObject, IMLFeatureProvider
{
  public double MyParam { get; set; }
  public NSSet<NSString> FeatureNames => new NSSet<NSString>(new NSString("myParam"));
  public MLFeatureValue GetFeatureValue(string featureName)
  {
    if (featureName == "myParam")
      return MLFeatureValue.FromDouble(MyParam);
    return MLFeatureValue.FromDouble(0); // default value
  }
```

Usando las clases como esta, se pueden proporcionar parámetros de entrada de manera que entienda CoreML. Los nombres de las características (como `myParam` en el ejemplo de código) debe coincidir con lo que espera el modelo.

### <a name="4-run-the-model"></a>4. Ejecutar el modelo

Uso del modelo requiere que los parámetros y el proveedor de características de creación de instancias establecen, a continuación, que el `GetPrediction` se llama al método:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Extraer los resultados

El resultado de predicción `outFeatures` también es una instancia de `IMLFeatureProvider`; salida pueden tener acceso a valores mediante `GetFeatureValue` con el nombre de cada parámetro de salida (como `theResult`), como en este ejemplo:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>Uso de CoreML con el marco de trabajo de visión

CoreML también puede usarse junto con el marco de trabajo de visión para realizar operaciones en la imagen, como reconocimiento de formas, identificación de objetos y otras tareas.

Los pasos siguientes describen cómo CoreML y visión se utilizan conjuntamente en el [CoreMLVision ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/). El ejemplo se combinan la [reconocimiento rectángulos](~/ios/platform/introduction-to-ios11/vision.md#rectangles) desde el marco de trabajo de visión con el _MNINSTClassifier_ CoreML modelo para identificar un dígito escritas a mano en una fotografía.

![Reconocimiento de imágenes de número 3](coreml-images/vision3.png) ![Reconocimiento de imágenes de número 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Crear un modelo de visión CoreML

El modelo de CoreML _MNISTClassifier_ se carga y, a continuación, se encapsula en un `VNCoreMLModel` que hace que el modelo que estén disponibles para las tareas de visión. Este código también crea dos solicitudes Vision: en primer lugar para buscar los rectángulos en una imagen y, a continuación, para el procesamiento de un rectángulo con el modelo de CoreML:

```csharp
// Load the ML model
var bundle = NSBundle.MainBundle;
var assetPath = bundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
NSError mlErr, vnErr;
var mlModel = MLModel.Create(assetPath, out mlErr);
var model = VNCoreMLModel.FromMLModel(mlModel, out vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(model, HandleClassification);
```

La clase aún debe implementar la `HandleRectangles` y `HandleClassification` métodos para las solicitudes de visión, se muestra en los pasos 3 y 4, a continuación.

### <a name="2-start-the-vision-processing"></a>2. Iniciar el procesamiento de visión

El siguiente código empieza a procesar la solicitud. En el **CoreMLVision** ejemplo, este código se ejecuta después de que el usuario ha seleccionado una imagen:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Este controlador pasa el `ciImage` para el marco de trabajo de visión `VNDetectRectanglesRequest` que se creó en el paso 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Controlar los resultados del procesamiento de visión

Una vez completada la detección de rectángulo, se ejecuta el `HandleRectangles` método, que recorta la imagen para extraer el primer rectángulo, convierte la imagen de un rectángulo en escala de grises y lo pasa al modelo de clasificación CoreML.

El `request` parámetro pasado a este método contiene los detalles de la solicitud de visión y el uso de la `GetResults<VNRectangleObservation>()` método, devuelve una lista de los rectángulos se encuentra en la imagen. Primer rectángulo `observations[0]` se extraen y se pasa al modelo CoreML:

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] {ClassificationRequest}, out NSError err);
  });
}
```

El `ClassificationRequest` se inicializó en el paso 1 para utilizar el `HandleClassification` método definido en el paso siguiente.

### <a name="4-handle-the-coreml"></a>4. Controlar la CoreML

El `request` parámetro pasado a este método contiene los detalles de la solicitud de CoreML y el uso de la `GetResults<VNClassificationObservation>()` método, devuelve una lista de posibles resultados ordenados por confianza (confianza mayor primera):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  // ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```

## <a name="samples"></a>Muestras

Hay tres muestras CoreML probar:

* El [ejemplo Predictor de precios de Mars Habitat](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) tiene simple entradas y salidas numéricas.

* El [ejemplo Vision y CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/) acepta un parámetro de imagen y usa el marco de trabajo de visión para identificar regiones cuadradas en la imagen, que se pasan a un modelo de CoreML que reconoce solo dígitos.

* Por último, el [ejemplo de reconocimiento de imágenes de CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/) CoreML se utiliza para identificar las características de una foto. De forma predeterminada usa la menor **SqueezeNet** modelo (5MB), pero se ha escrito para que se puede descargar e incorporar el mayor **VGG16** modelo (553 MB). Para obtener más información, consulte el [archivo Léame del ejemplo](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).

## <a name="related-links"></a>Vínculos relacionados

- [Aprendizaje automático (Apple)](https://developer.apple.com/machine-learning/)
- [Ejemplo de CoreML (Mars Habitat) (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML y visión (reconocimiento de número) (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [Reconocimiento de imágenes de CoreML (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML con Azure Custom Vision (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [Introducción a CoreML (sesión WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/703/)
