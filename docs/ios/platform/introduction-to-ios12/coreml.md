---
title: Core ML 2 en Xamarin.iOS
description: Este documento describe las actualizaciones a Core ML disponible como parte de iOS 12. En concreto, examina las mejoras de rendimiento asociadas con la nueva API de predicción por lotes.
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/15/2018
ms.openlocfilehash: 2d62e42e755a0d3088283adb863dfd684ddeae28
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617584"
---
# <a name="core-ml-2-in-xamarinios"></a>Core ML 2 en Xamarin.iOS

ML básica es una tecnología disponible en iOS, macOS, tvOS y watchOS de aprendizaje automático. Permite a las aplicaciones realizar predicciones basadas en modelos de aprendizaje automático.

En iOS 12, ML Core incluye una API de procesamiento por lotes. Esta API aporta una mayor eficacia Core ML y proporciona mejoras de rendimiento en escenarios donde se usa un modelo para realizar una secuencia de predicciones.

## <a name="sample-app-marshabitatcoremltimer"></a>Aplicación de ejemplo: MarsHabitatCoreMLTimer

Para demostrar las predicciones por lotes con Core ML, eche un vistazo a la [MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer) aplicación de ejemplo. Este ejemplo se usa un modelo de aprendizaje automático de Core entrenado para predecir el costo de la creación de un habitat en Mars, basándose en diversas entradas: número de paneles solares, número de invernaderos y número de acres.

Los fragmentos de código en este documento proceden de este ejemplo.

## <a name="generate-sample-data"></a>Generar datos de ejemplo

En `ViewController`, la aplicación de ejemplo `ViewDidLoad` llamadas al método `LoadMLModel`, que carga el modelo de aprendizaje automático principales incluyen:

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

A continuación, la aplicación de ejemplo crea 100.000 `MarsHabitatPricerInput` objetos que se va a usar como entrada para las predicciones de Core ML secuenciales. Cada ejemplo generado tiene establecido un valor aleatorio para el número de paneles solares, el número de invernaderos y el número de acres:

```csharp
async void CreateInputs(int num)
{
    // ...
    Random r = new Random();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            double solarPanels = r.NextDouble() * MaxSolarPanels;
            double greenHouses = r.NextDouble() * MaxGreenHouses;
            double acres = r.NextDouble() * MaxAcres;
            inputs[i] = new MarsHabitatPricerInput(solarPanels, greenHouses, acres);
        }
    });
    // ...
}
```

Al puntear en cualquiera de los otros tres botones de la aplicación ejecuta dos secuencias de predicciones: uno con un `for` bucle y otro con el nuevo lote `GetPredictions` método introducida en iOS 12:

```csharp
async void RunTest(int num)
{
    // ...
    await FetchNonBatchResults(num);
    // ...
    await FetchBatchResults(num);
    // ...
}
```

## <a name="for-loop"></a>for (bucle)

El `for` versión de bucle de la prueba otra vez recorre en iteración el número especificado de entradas, una llamada a [ `GetPrediction` ](https://developer.xamarin.com/api/member/CoreML.MLModel.GetPrediction/) para cada uno y descartar el resultado. Tiempo de espera del método cuánto tarda en realizar las predicciones:

```csharp
async Task FetchNonBatchResults(int num)
{
    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            IMLFeatureProvider output = model.GetPrediction(inputs[i], out NSError error);
        }
    });
    stopWatch.Stop();
    nonBatchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="getpredictions-new-batch-api"></a>GetPredictions (API de lote nuevo)

Crea la versión de lote de la prueba una `MLArrayBatchProvider` objeto a partir de la matriz de entrada (como se trata de un parámetro de entrada necesario para el `GetPredictions` método), crea una [`MLPredictionOptions`](https://developer.xamarin.com/api/type/CoreML.MLPredictionOptions/)
objeto que impide que los cálculos de predicción estén restringidos a la CPU y usa el `GetPredictions` API para capturar las predicciones, vuelva a descartar el resultado:

```csharp
async Task FetchBatchResults(int num)
{
    var batch = new MLArrayBatchProvider(inputs.Take(num).ToArray());
    var options = new MLPredictionOptions()
    {
        UsesCpuOnly = false
    };

    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        model.GetPredictions(batch, options, out NSError error);
    });
    stopWatch.Stop();
    batchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="results"></a>Resultados

En el simulador y dispositivo, `GetPredictions` finalice más rápidamente que las predicciones de aprendizaje automático de Core basado en el bucle.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer)
- [Novedades de Core ML, parte 1 (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/708/)
- [Novedades de Core ML, Part 2 (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Introducción a ML básica de Xamarin.iOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [Core ML (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [Trabajar con modelos de ML básica](https://developer.apple.com/machine-learning/build-run-models/)
