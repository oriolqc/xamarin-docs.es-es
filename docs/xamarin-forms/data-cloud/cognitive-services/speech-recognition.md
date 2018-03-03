---
title: Mediante la API de Bing voz el reconocimiento de voz
description: "La API de Bing Speech es una API basada en la nube que proporciona algoritmos para procesar el idioma hablado. Este artículo explica cómo usar la API de REST de reconocimiento de voz de Bing para convertir audio en texto en una aplicación de Xamarin.Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 186ea6277ec7bd4ceb52855186e6fd88344b1b86
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="speech-recognition-using-the-bing-speech-api"></a>Mediante la API de Bing voz el reconocimiento de voz

_La API de Bing Speech es una API basada en la nube que proporciona algoritmos para procesar el idioma hablado. Este artículo explica cómo usar la API de REST de reconocimiento de voz de Bing para convertir audio en texto en una aplicación de Xamarin.Forms._

![](~/media/shared/preview.png "Esta API es actualmente una versión preliminar")

> [!NOTE]
> La API de Bing Speech aún está en vista previa. Allí puede ser cambios importantes a la API antes del lanzamiento final.

## <a name="overview"></a>Información general

La API de Bing Speech tiene dos componentes:

- Un reconocimiento de voz API para convertir las palabras en texto. El reconocimiento de voz puede realizarse a través de una API de REST, la biblioteca de cliente o la biblioteca de servicio.
- Un texto a voz API para convertir texto en palabras. Se efectúa una conversión de texto a voz a través de una API de REST.

En este artículo se centra en la realización de reconocimiento de voz a través de la API de REST. Mientras que las bibliotecas de cliente y el servicio admiten la devolución de resultados parciales, la API de REST solo puede devolver un resultado de reconocimiento único, sin los resultados parciales.

Se debe obtener una clave de API para usar la API de reconocimiento de voz de Bing. Esto se puede obtener en [Introducción gratuitamente](https://www.microsoft.com/cognitive-services/sign-up?ReturnUrl=/cognitive-services/subscriptions?productId=%2fproducts%2fBing.Speech.Preview) en microsoft.com.

Para obtener más información acerca de la API de voz de Bing, consulte [Bing Speech documentación](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview) en microsoft.com.

## <a name="authentication"></a>Autenticación

Todas las solicitudes realizadas a la API de REST de reconocimiento de voz de Bing requiere un token de acceso JSON Web Token (JWT), que se puede obtener desde el servicio de token de servicios cognitivos en `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Se puede obtener un token mediante la realización de una solicitud POST al servicio de token, especificar un `Ocp-Apim-Subscription-Key` encabezado que contiene la clave de API como su valor.

En el ejemplo de código siguiente se muestra cómo solicitar un acceso token desde el servicio de token:

```csharp
async Task<string> FetchTokenAsync(string fetchUri, string apiKey)
{
  using (var client = new HttpClient())
  {
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";

    var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
  }
}
```

El token de acceso devuelta, que es texto Base64, tiene una hora de expiración de 10 minutos. Por lo tanto, la aplicación de ejemplo renueva el token de acceso cada 9 minutos.

El token de acceso debe especificarse en cada API de REST de reconocimiento de voz de Bing llamar como una `Authorization` prefijado con la cadena de encabezado `Bearer`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
using (var httpClient = new HttpClient())
{
  httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
  ...
}  
```

Si no se puede pasar un token de acceso válido a la API de REST de reconocimiento de voz de Bing se producirá un error de 403 respuesta.

## <a name="performing-speech-recognition"></a>Realizar el reconocimiento de voz

El reconocimiento de voz se consigue mediante la realización de una solicitud POST al `recognize` API en `https://speech.platform.bing.com/recognize`. Una única solicitud no puede contener más de 10 segundos de audio y la duración total de solicitudes no puede superar los 14 segundos.

El contenido de audio se debe colocar en el cuerpo de POST de la solicitud en formato wav. Para obtener información acerca de los códecs admitidos, consulte [códecs admitidos](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-codecs) en microsoft.com.

En la aplicación de ejemplo, el `RecognizeSpeechAsync` método invoca el proceso de reconocimiento de voz:

```csharp
public async Task<SpeechResult> RecognizeSpeechAsync(string filename)
{
    ...

    // Read audio file to a stream
    var file = await PCLStorage.FileSystem.Current.LocalStorage.GetFileAsync(filename);
    var fileStream = await file.OpenAsync(PCLStorage.FileAccess.Read);

    // Send audio stream to Bing and deserialize the response
    string requestUri = GenerateRequestUri(Constants.SpeechRecognitionEndpoint);
    string accessToken = authenticationService.GetAccessToken();
    var response = await SendRequestAsync(fileStream, requestUri, accessToken, Constants.AudioContentType);
    var speechResults = JsonConvert.DeserializeObject<SpeechResults>(response);

    fileStream.Dispose();
    return speechResults.results.FirstOrDefault();
}
```

Audio se registra en cada proyecto específico de plataforma como datos de wav PCM y el `RecognizeSpeechAsync` método usa la `PCLStorage` paquete de NuGet para abrir el archivo de audio como una secuencia. La solicitud de reconocimiento de voz se genera el identificador URI y un token de acceso se recupera desde el servicio de token. La solicitud de reconocimiento de voz se registra en el `recognize` API, que devuelve una respuesta JSON que contiene el resultado. La respuesta JSON se deserializa, con lo que se devuelve al método de llamada para su presentación.

### <a name="configuring-speech-recognition"></a>Configurar el reconocimiento de voz

El proceso de reconocimiento de voz puede configurarse mediante la especificación de parámetros de consulta HTTP. Hay parámetros obligatorios y opcionales, con el método siguiente que muestra los parámetros obligatorios que se deben establecer:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    string requestUri = speechEndpoint;
    requestUri += @"?scenarios=ulm";                                    // websearch is the other option
    requestUri += @"&appid=D4D52672-91D7-4C74-8AD8-42B1D98141A5";       // You must use this ID
    requestUri += @"&locale=en-US";                                     // Other languages supported
    requestUri += string.Format("&device.os={0}", operatingSystem);     // Open field
    requestUri += @"&version=3.0";                                      // Required value
    requestUri += @"&format=json";                                      // Required value
    requestUri += @"&instanceid=fe34a4de-7927-4e24-be60-f0629ce1d808";  // GUID for device making the request
    requestUri += @"&requestid=" + Guid.NewGuid().ToString();           // GUID for the request
    return requestUri;
}
```

La configuración principal llevada a cabo por el `GenerateRequestUri` método consiste en establecer la configuración regional del contenido del audio. Para obtener una lista de las configuraciones regionales admitidas, consulte [configuraciones regionales admite ](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-locales) en microsoft.com.

Para obtener más información acerca de los posibles valores para los parámetros obligatorios, consulte [parámetros necesarios](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#required-parameters) en microsoft.com. Para obtener información acerca de los parámetros opcionales, vea [parámetros opcionales](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition) en microsoft.com.

### <a name="sending-the-request"></a>Enviar la solicitud

El `SendRequestAsync` método realiza la solicitud POST a la API de REST de reconocimiento de voz de Bing y devuelve la respuesta:

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);

    using (var httpClient = new HttpClient())
    {
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
        var response = await httpClient.PostAsync(url, content);

        return await response.Content.ReadAsStringAsync();
    }
}
```

Este método crea la solicitud POST:

- Ajuste la secuencia de audio en un `StreamContent` instancia, lo que proporciona contenido HTTP basado en una secuencia.
- Establecer el `Content-Type` encabezado de la solicitud a `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Agregar el token de acceso a la `Authorization` encabezado, el prefijo con la cadena `Bearer`.

La solicitud POST, a continuación, se envía a `recognize` API. La respuesta, a continuación, lee y devuelve al método de llamada.

El `recognize` API enviará el código de estado HTTP 200 (OK) en la respuesta, siempre que la solicitud es válida, lo que indica que la solicitud es correcta y que la información solicitada está en la respuesta. Para obtener una lista de posibles respuestas de error, consulte [las respuestas de Error](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#error-responses) en microsoft.com.

### <a name="processing-the-response"></a>Procesamiento de la respuesta

La respuesta de la API se devuelve en formato JSON, con el texto reconocido que esté incluido en la `name` etiqueta. Los siguientes datos JSON muestran un mensaje de respuesta correcta típico:

```csharp
{
  "version": "3.0",
  "header": {
    "status": "success",
    "scenario": "ulm",
    "name": "go shopping tomorrow",
    "lexical": "go shopping tomorrow",
    "properties": {
      "requestid": "e06c059d-fa37-4bb1-843f-4914350279a8",
      "HIGHCONF": "1"
    }
  },
  "results": [
    {
      "scenario": "ulm",
      "name": "go shopping tomorrow",
      "lexical": "go shopping tomorrow",
      "confidence": "0.9493451",
      "properties": {
        "HIGHCONF": "1"
      }
    }
  ]
}
```

En la aplicación de ejemplo, la respuesta JSON se deserializa en un `SpeechResult` instancia, con lo que se devuelve al método de llamada para su presentación, como se muestra en las siguientes capturas de pantalla:

![](speech-recognition-images/speech-recognition.png "Reconocimiento de voz")

Para obtener información acerca de los valores de cada etiqueta JSON, consulte [las respuestas de reconocimiento de voz](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#speech-recognition-responses) en microsoft.com.

## <a name="summary"></a>Resumen

Este artículo explica cómo usar la API de REST de reconocimiento de voz de Bing para convertir audio en texto en una aplicación de Xamarin.Forms. Además de realizar el reconocimiento de voz, la API de Bing Speech también puede convertir texto en palabras.



## <a name="related-links"></a>Vínculos relacionados

- [Documentación de voz de Bing](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview)
- [Consumir un servicio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Lista de tareas cognitivos servicios (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Reconocimiento de voz de Bing API de REST](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition)
