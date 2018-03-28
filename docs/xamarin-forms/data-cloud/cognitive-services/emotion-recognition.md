---
title: Reconocimiento de expresión mediante la API de cara
description: La API de cara toma una expresión facial en una imagen como entrada y devuelve los datos que incluyen los niveles de confianza a través de un conjunto de emociones para cada cara de la imagen. En este artículo se explica cómo usar la API de cara a reconocer emociones, para calificar una aplicación de Xamarin.Forms.
ms.topic: article
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 0fc69fb1283ea2afd95900348cdecec5d6514ae0
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="emotion-recognition-using-the-face-api"></a>Reconocimiento de expresión mediante la API de cara

_La API de cara toma una expresión facial en una imagen como entrada y devuelve los datos que incluyen los niveles de confianza a través de un conjunto de emociones para cada cara de la imagen. En este artículo se explica cómo usar la API de cara a reconocer emociones, para calificar una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

La API de cara puede realizar emociones de detección para detectar ira, contempt, disgust, temor, satisfacción, neutral, tristeza y sorpresa, en una expresión facial. Universalmente y transculturalmente estas emociones se comunican a través de las mismas expresiones faciales básicas. Así como devolver un resultado de la expresión de una expresión facial, la API de cara puede también devuelve un cuadro de límite de caras detectados. Tenga en cuenta que se debe obtener una clave de API para usar la API de cara. Esto se puede obtener en [intente cognitivos servicios](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Reconocimiento de emoción puede realizarse a través de una biblioteca de cliente y una API de REST. En este artículo se centra en la ejecución del reconocimiento de emoción a través de la [Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/) biblioteca de cliente, que puede descargarse de NuGet.

La API de cara también puede utilizarse para reconocer las expresiones faciales de personas de vídeo y puede devolver un resumen de sus emociones. Para obtener más información, consulte [cómo analizar los vídeos en tiempo real](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

Para obtener más información acerca de la API de cara, consulte [API cara](/azure/cognitive-services/face/overview/).

## <a name="performing-emotion-recognition"></a>Realizar el reconocimiento de emociones

Reconocimiento de emoción se logra mediante la carga de un flujo de imagen a la API de cara. El tamaño de archivo de imagen no debe ser mayor que 4MB y formatos de archivo admitidos son JPEG, PNG, GIF y BMP.

En el ejemplo de código siguiente se muestra el proceso de reconocimiento emociones:

```csharp
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;

var faceServiceClient = new FaceServiceClient(Constants.FaceApiKey, Constants.FaceEndpoint);
// e.g. var faceServiceClient = new FaceServiceClient("a3dbe2ed6a5a9231bb66f9a964d64a12", "https://westus.api.cognitive.microsoft.com/face/v1.0/detect");

var faceAttributes = new FaceAttributeType[] { FaceAttributeType.Emotion };
using (var photoStream = photo.GetStream())
{
    Face[] faces = await faceServiceClient.DetectAsync(photoStream, true, false, faceAttributes);
    if (faces.Any())
    {
        // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
        emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
    }
    // Store emotion as app rating
    ...
}
```

Un `FaceServiceClient` instancia debe crearse para llevar a cabo el reconocimiento emociones, con la clave de API de cara y el punto de conexión que se pasan como argumentos para el `FaceServiceClient` constructor.

> [!NOTE]
> Debe usar la misma región en las llamadas de API de cara que utiliza para obtener las claves de la suscripción. Por ejemplo, si ha adquirido las claves de la suscripción desde el `westus` región, el punto de conexión de detección de cara será `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

El `DetectAsync` método, que se invoca en el `FaceServiceClient` de la instancia, se carga una imagen a la API de cara, como un `Stream`. La clave de API se enviarán a la API de cara cuando se invoca esta operación. Error al enviar una clave de API válida dará como resultado en un `Microsoft.ProjectOxford.Face.FaceAPIException` que se producen con el mensaje de excepción que indica que se envió una clave de API no válida.

El `DetectAsync` método devolverá un `Face` de las matrices, siempre que se ha identificado una cara. Cada devuelta cara contiene un rectángulo para indicar su ubicación, combinada con una serie de atributos de cara opcional, que se especifican mediante el `faceAttributes` argumento pasado a la `DetectAsync` método. Si no se detecta ninguna cara, vacío `Face` se devolverá la matriz.

Al interpretar los resultados de la API de cara, las emociones detectado deben interpretarse como la emoción con la puntuación más alta, tal y como se normalizan las puntuaciones a suma a uno. Por lo tanto, la aplicación de ejemplo muestra la emoción reconocida con la puntuación más alta para los que se enfrentan detectado más grande en la imagen, como se muestra en las siguientes capturas de pantalla:

![](emotion-recognition-images/emotion-recognition.png "Reconocimiento de emociones")

## <a name="summary"></a>Resumen

Este artículo explica cómo usar la API de cara a reconocer emociones, para calificar una aplicación de Xamarin.Forms. La API de cara toma una expresión facial en una imagen como entrada y devuelve los datos que incluyen la confianza entre un conjunto de emociones para cada cara de la imagen.

## <a name="related-links"></a>Vínculos relacionados

- [Se enfrentan a API](/azure/cognitive-services/face/overview/).
- [Lista de tareas cognitivos servicios (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/)
