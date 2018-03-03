---
title: "Reconocimiento de emoción mediante la API de emociones"
description: "La API emociones toma una expresión facial en una imagen como entrada y devuelve los niveles de confianza a través de un conjunto de emociones para cada cara de la imagen. En este artículo se explica cómo usar la API de emociones para reconocer emociones, para calificar una aplicación de Xamarin.Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 159bd1b23eb7505c5d5629570a34d54e0525567e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="emotion-recognition-using-the-emotion-api"></a>Reconocimiento de emoción mediante la API de emociones

_La API emociones toma una expresión facial en una imagen como entrada y devuelve los niveles de confianza a través de un conjunto de emociones para cada cara de la imagen. En este artículo se explica cómo usar la API de emociones para reconocer emociones, para calificar una aplicación de Xamarin.Forms._

![](~/media/shared/preview.png "Esta API es actualmente una versión preliminar")

> [!NOTE]
> La API de emoción aún está en vista previa. Allí puede ser cambios importantes a la API antes del lanzamiento final.

## <a name="overview"></a>Información general

La API de emoción puede detectar ira, contempt, disgust, temor, satisfacción, neutral, tristeza y sorpresa, en una expresión facial. Universalmente y transculturalmente estas emociones se comunican a través de las mismas expresiones faciales básicas. Además de devolver un resultado de la expresión de una expresión facial, la API de emoción también devuelve un cuadro de límite de caras detectados mediante la API de cara. Si un usuario ya ha llamado a la API de cara, puede enviar el rectángulo de cara como una entrada opcional. Tenga en cuenta que se debe obtener una clave de API para usar la API de emociones. Esto se puede obtener en [Introducción gratuitamente](https://www.microsoft.com/cognitive-services/sign-up) en microsoft.com.

Reconocimiento de emoción puede realizarse a través de una biblioteca de cliente y una API de REST. En este artículo se centra en la ejecución del reconocimiento de emoción a través de la [Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/) biblioteca de cliente, que puede descargarse de NuGet.

La API de emoción también puede utilizarse para reconocer las expresiones faciales de personas de vídeo y devuelve un resumen de sus emociones. Para obtener más información, consulte [emociones en vídeo](https://www.microsoft.com/cognitive-services/emotion-api/documentation#emotion-in-video) en microsoft.com.

Para obtener más información acerca de la API de emociones, consulte [documentación de la API de emoción](https://www.microsoft.com/cognitive-services/emotion-api/documentation) en microsoft.com.

## <a name="performing-emotion-recognition"></a>Realizar el reconocimiento de emociones

Reconocimiento de emoción se logra mediante la carga de un flujo de imagen a la API de emociones. El tamaño de archivo de imagen no debe ser mayor que 4MB y formatos de archivo admitidos son JPEG, PNG, GIF y BMP. En la imagen, el intervalo de tamaño de la cara detectables es 36 x 36 a 4096 x 4096 píxeles. No puede detectar cualquier caras estén fuera del intervalo.

En el ejemplo de código siguiente se muestra el proceso de reconocimiento emociones:

```csharp
using Microsoft.ProjectOxford.Emotion;
using Microsoft.ProjectOxford.Emotion.Contract;

var emotionClient = new EmotionServiceClient(Constants.EmotionApiKey);

using (var photoStream = photo.GetStream())
{
  Emotion[] emotionResult = await emotionClient.RecognizeAsync(photoStream);
  if (emotionResult.Any())
  {
    // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
    emotionResultLabel.Text = emotionResult.FirstOrDefault().Scores.ToRankedList().FirstOrDefault().Key;
  }
  // Store emotion as app rating
  ...
}
```

Un `EmotionServiceClient` instancia debe crearse para llevar a cabo el reconocimiento emociones, con la clave de API de expresión que se pasa como argumento a la `EmotionServiceClient` constructor.

El `RecognizeAsync` método, que se invoca en el `EmotionServiceClient` de la instancia, se carga una imagen a la API emociones, como un `Stream`. La clave de API se enviarán a la API de emoción cuando se invoca esta operación. Error al enviar una clave de API válida dará como resultado en un `Microsoft.ProjectOxford.Common.ClientException` que se producen con el mensaje de excepción que indica que se envió una clave de API no válida.

El `RecognizeAsync` método devolverá un `Emotion` de las matrices, siempre que se ha identificado una cara. Para cada imagen, el número máximo de caras que se pueden detectar es 64 y las caras se clasifican según el tamaño del rectángulo de cara en orden descendente. Si no se detecta ninguna cara, vacío `Emotion` se devolverá la matriz.

Al interpretar los resultados de la API de emoción, la emoción detectada debe interpretarse como la emoción con la puntuación más alta, tal y como se normalizan las puntuaciones a suma a uno. Por lo tanto, la aplicación de ejemplo muestra la emoción reconocida con la puntuación más alta para los que se enfrentan detectado más grande en la imagen, como se muestra en las siguientes capturas de pantalla:

![](emotion-recognition-images/emotion-recognition.png "Reconocimiento de emociones")

## <a name="summary"></a>Resumen

Este artículo explica cómo usar la API de emociones para reconocer emociones, para calificar una aplicación de Xamarin.Forms. La API de emoción toma una expresión facial en una imagen como entrada y devuelve la confianza entre un conjunto de emociones para cada cara de la imagen.


## <a name="related-links"></a>Vínculos relacionados

- [Emotion API documentación](https://www.microsoft.com/cognitive-services/emotion-api/documentation)
- [Lista de tareas cognitivos servicios (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)
- [Emotion API](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)
