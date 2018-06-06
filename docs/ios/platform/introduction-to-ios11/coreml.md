---
title: Introducción a CoreML en Xamarin.iOS
description: Este documento describe CoreML, lo que permite el aprendizaje automático en iOS. Este documento describe cómo empezar a trabajar con CoreML y cómo se usa con el marco de trabajo de visión.
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: b893fe5e56cc2d43a71870ffbbd20f0b8c6cfd18
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787502"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Introducción a CoreML en Xamarin.iOS

_Aprendizaje para las aplicaciones móviles en iOS 11 automático_

CoreML pone el aprendizaje automático para iOS: aplicaciones pueden aprovechar las ventajas de los modelos de aprendizaje de máquina entrenado para realizar a todo tipo de tareas, de solución de problemas para reconocimiento de imágenes.

Esta introducción trata lo siguiente:

- [Introducción a CoreML](#coreml)
- [Uso de CoreML con el marco de trabajo de visión](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Introducción a CoreML

Estos pasos describen cómo agregar CoreML a un proyecto de iOS. Hacer referencia a la [ejemplo Mars hábitat Pricer](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) para obtener un ejemplo práctico.

![Captura de pantalla de ejemplo Predictor de precios de hábitat de MARS](coreml-images/marspricer-heading.png)

### <a name="1-add-the-model-to-the-project"></a>1. Agregar el modelo al proyecto

Agregar un modelo compilado (un directorio con el **.modelc** extensión) a la **recursos** directorio del proyecto. Todo el contenido del directorio debe tener una acción de compilación de **BundleResource**:

![La carpeta de recursos debe contener el modelo compilado](coreml-images/resources-modelc.png)

El [ejemplos](https://developer.xamarin.com/samples/monotouch/ios11/) usar modelos se compilaba en Xcode 9 o manualmente mediante el siguiente comando de terminal:

```csharp
xcrun coremlcompiler compile {model.mlmodel} {outputFolder}
```

> [!NOTE]
> **.Model** archivos _debe_ compilarse a **.modelc** antes de que puedan utilizar por CoreML

### <a name="2-load-the-model"></a>2. Cargar el modelo

Antes de usar un modelo, puede cargar mediante el `MLModel.FromUrl` método estático:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.FromUrl(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Establezca los parámetros

Se pasan parámetros del modelo de entrada y salida mediante una clase de contenedor que implementa `IMLFeatureProvider`.

Clases de proveedor de características se comportan como un diccionario de cadena y `MLFeatureValue`s, donde cada valor de la característica podría ser una cadena simple número, una matriz o datos o un búfer de píxeles que contiene una imagen.

A continuación se muestra el código de un proveedor de características de valor único:

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

Usando las clases parecido a esto, se pueden proporcionar parámetros de entrada de forma que se entiende por CoreML. Los nombres de las características (como `myParam` en el ejemplo de código) debe coincidir con lo que espera el modelo.

### <a name="4-run-the-model"></a>4. Ejecutar el modelo

Utilizando el modelo requiere que los parámetros y el proveedor de características que se creen instancias establecen, la acción que el `GetPrediction` llamarse al método:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Extraer los resultados

El resultado de predicción `outFeatures` también es una instancia de `IMLFeatureProvider`; salida valores pueden tener acceso mediante `GetFeatureValue` con el nombre de cada parámetro de salida (como `theResult`), como en este ejemplo:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>Uso de CoreML con el marco de trabajo de visión

CoreML también puede utilizarse junto con el marco de trabajo de visión para realizar operaciones en la imagen, como reconocimiento de formas, identificación de objetos y otras tareas.

Los pasos siguientes describen cómo visión y CoreML se utilizan conjuntamente en el [CoreMLVision ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/). El ejemplo combina la [reconocimiento de rectángulos](~/ios/platform/introduction-to-ios11/vision.md#rectangles) desde el marco de trabajo de visión con el _MNINSTClassifier_ CoreML modelo para identificar un dígito escritas a mano en una fotografía.

![Reconocimiento de imágenes del número 3](coreml-images/vision3.png) ![Reconocimiento de imágenes de número 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Crear un modelo de Vision CoreML

El modelo de CoreML _MNISTClassifier_ se carga y, a continuación, se encapsula en un `VNCoreMLModel` que hace que el modelo esté disponible para tareas de visión. Este código también crea dos solicitudes Vision: en primer lugar para buscar los rectángulos en una imagen y, a continuación, para el procesamiento de un rectángulo con el modelo de CoreML:

```csharp
// Load the ML model
var assetPath = NSBundle.MainBundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
var mlModel = MLModel.FromUrl(assetPath, out NSError mlErr);
var vModel = VNCoreMLModel.FromMLModel(mlModel, out NSError vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(vModel, HandleClassification);
```

La clase debe seguir implementar la `HandleRectangles` y `HandleClassification` métodos para las solicitudes de visión, se muestra en los pasos 3 y 4, a continuación.

### <a name="2-start-the-vision-processing"></a>2. Iniciar el procesamiento de visión

El código siguiente inicia el procesamiento de la solicitud. En el **CoreMLVision** ejemplo, este código se ejecuta después de que el usuario ha seleccionado una imagen:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Este controlador pasa el `ciImage` para el marco de trabajo de visión `VNDetectRectanglesRequest` que creó en el paso 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Controlar los resultados del procesamiento de visión

Una vez completada la detección del rectángulo, se ejecuta el `HandleRectangles` método, que recorta la imagen para extraer el primer rectángulo, convierte la imagen de un rectángulo en escala de grises y lo pasa al modelo CoreML para la clasificación.

El `request` parámetro pasado a este método contiene los detalles de la solicitud de visión y el uso de la `GetResults<VNRectangleObservation>()` método, devuelve una lista de los rectángulos que se encuentra en la imagen. El primer rectángulo `observations[0]` se extrae y se pasa al modelo de CoreML:

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] { ClassificationRequest }, out NSError err);
  });
}
```

El `ClassificationRequest` se inicializó en el paso 1 para utilizar el `HandleClassification` método definido en el paso siguiente.

### <a name="4-handle-the-coreml"></a>4. Controlar la CoreML

El `request` parámetro pasado a este método contiene los detalles de la solicitud CoreML y el uso de la `GetResults<VNClassificationObservation>()` método, devuelve una lista de posibles resultados ordenados por confianza (confianza mayor primera):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```



## <a name="samples"></a>Muestras

Hay tres ejemplos de CoreML para probar:

* El [ejemplo Predictor de precios de Mars hábitat](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) tiene simples entradas y salidas numéricas.

* El [ejemplo Vision & CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/) acepta un parámetro de imagen y utiliza el marco de trabajo de visión para identificar regiones cuadradas en la imagen, que se pasan a un modelo de CoreML que reconoce decena.

* Por último, el [ejemplo de reconocimiento de imágenes de CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/) CoreML se utiliza para identificar las características de una foto. De forma predeterminada usa el menor **SqueezeNet** modelo (5MB), pero se ha escrito de modo que puede descargar e incorporar el mayor **VGG16** modelo (553 MB). Para obtener más información, consulte el [archivo Léame del ejemplo](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).


## <a name="related-links"></a>Vínculos relacionados

- [Aprendizaje automático (Apple)](https://developer.apple.com/machine-learning/)
- [Ejemplo de CoreML (Mars hábitat) (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML y visión (reconocimiento de número) (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [Reconocimiento de imágenes de CoreML (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML con Azure personalizado Vision (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [Introducción a CoreML (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/703/)
