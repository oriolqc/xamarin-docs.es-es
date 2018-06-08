---
title: Reconocimiento de voz con la API de voz de Microsoft
description: La API de voz de Microsoft es una API basada en la nube que proporciona algoritmos para procesar el idioma hablado. Este artículo explica cómo usar la API de REST de reconocimiento de voz de Microsoft para convertir audio en texto en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: c8eb991f67d54f9bacbb776b350cc5649a04ab2b
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846859"
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>Reconocimiento de voz con la API de voz de Microsoft

_La API de voz de Microsoft es una API basada en la nube que proporciona algoritmos para procesar el idioma hablado. Este artículo explica cómo usar la API de REST de reconocimiento de voz de Microsoft para convertir audio en texto en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

La API de voz de Microsoft tiene dos componentes:

- Un reconocimiento de voz API para convertir las palabras en texto. El reconocimiento de voz puede realizarse a través de una API de REST, la biblioteca de cliente o la biblioteca de servicio.
- Un texto a voz API para convertir texto en palabras. Se efectúa una conversión de texto a voz a través de una API de REST.

En este artículo se centra en la realización de reconocimiento de voz a través de la API de REST. Mientras que las bibliotecas de cliente y el servicio admiten la devolución de resultados parciales, la API de REST solo puede devolver un resultado de reconocimiento único, sin los resultados parciales.

Para usar la API de voz de Microsoft, se debe obtener una clave de API. Esto se puede obtener en [intente cognitivos servicios](https://azure.microsoft.com/try/cognitive-services/).

Para obtener más información acerca de la API de voz de Microsoft, consulte [documentación de API de voz de Microsoft](/azure/cognitive-services/speech/home/).

## <a name="authentication"></a>Autenticación

Todas las solicitudes realizadas a la API de REST de voz de Microsoft requiere un token de acceso JSON Web Token (JWT), que se puede obtener desde el servicio de token de servicios cognitivos en `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Se puede obtener un token mediante la realización de una solicitud POST al servicio de token, especificar un `Ocp-Apim-Subscription-Key` encabezado que contiene la clave de API como su valor.

En el ejemplo de código siguiente se muestra cómo solicitar un acceso token desde el servicio de token:

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

El token de acceso devuelta, que es texto Base64, tiene una hora de expiración de 10 minutos. Por lo tanto, la aplicación de ejemplo renueva el token de acceso cada 9 minutos.

El token de acceso debe especificarse en cada API de REST de voz de Microsoft llamar como una `Authorization` prefijado con la cadena de encabezado `Bearer`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Si no se puede pasar un token de acceso válido a la API de REST de voz de Microsoft se producirá un error de 403 respuesta.

## <a name="performing-speech-recognition"></a>Realizar el reconocimiento de voz

El reconocimiento de voz se consigue mediante la realización de una solicitud POST a la `recognition` API en `https://speech.platform.bing.com/speech/recognition/`. Una única solicitud no puede contener más de 10 segundos de audio y la duración total de solicitudes no puede superar los 14 segundos.

El contenido de audio se debe colocar en el cuerpo de POST de la solicitud en formato wav.

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
    var speechResult = JsonConvert.DeserializeObject<SpeechResult>(response);

    fileStream.Dispose();
    return speechResult;
}
```

Audio se registra en cada proyecto específico de plataforma como datos de wav PCM y el `RecognizeSpeechAsync` método usa la `PCLStorage` paquete de NuGet para abrir el archivo de audio como una secuencia. La solicitud de reconocimiento de voz se genera el identificador URI y un token de acceso se recupera desde el servicio de token. La solicitud de reconocimiento de voz se registra en el `recognition` API, que devuelve una respuesta JSON que contiene el resultado. La respuesta JSON se deserializa, con lo que se devuelve al método de llamada para su presentación.

### <a name="configuring-speech-recognition"></a>Configurar el reconocimiento de voz

El proceso de reconocimiento de voz puede configurarse mediante la especificación de parámetros de consulta HTTP:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/en-us/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

La configuración principal llevada a cabo por el `GenerateRequestUri` método consiste en establecer la configuración regional del contenido del audio. Para obtener una lista de las configuraciones regionales admitidas, consulte [idiomas admitidos ](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/).

### <a name="sending-the-request"></a>Enviar la solicitud

El `SendRequestAsync` método realiza la solicitud POST a la API de REST de voz de Microsoft y devuelve la respuesta:

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);
    var response = await httpClient.PostAsync(url, content);
    return await response.Content.ReadAsStringAsync();
}
```

Este método crea la solicitud POST:

- Ajuste la secuencia de audio en un `StreamContent` instancia, lo que proporciona contenido HTTP basado en una secuencia.
- Establecer el `Content-Type` encabezado de la solicitud a `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Agregar el token de acceso a la `Authorization` encabezado, el prefijo con la cadena `Bearer`.

La solicitud POST, a continuación, se envía a `recognition` API. La respuesta, a continuación, lee y devuelve al método de llamada.

El `recognition` API enviará el código de estado HTTP 200 (OK) en la respuesta, siempre que la solicitud es válida, lo que indica que la solicitud es correcta y que la información solicitada está en la respuesta. Para obtener una lista de posibles respuestas de error, consulte [solución de problemas](/azure/cognitive-services/speech/troubleshooting).

### <a name="processing-the-response"></a>Procesamiento de la respuesta

La respuesta de la API se devuelve en formato JSON, con el texto reconocido que esté incluido en la `name` etiqueta. Los siguientes datos JSON muestran un mensaje de respuesta correcta típico:

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

En la aplicación de ejemplo, la respuesta JSON se deserializa en un `SpeechResult` instancia, con lo que se devuelve al método de llamada para su presentación, como se muestra en las siguientes capturas de pantalla:

![](speech-recognition-images/speech-recognition.png "Reconocimiento de voz")

## <a name="summary"></a>Resumen

Este artículo explica cómo usar la API de REST de voz de Microsoft para convertir audio en texto en una aplicación de Xamarin.Forms. Además de realizar el reconocimiento de voz, la API de voz de Microsoft también puede convertir texto en palabras.

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de Microsoft Speech API](/azure/cognitive-services/speech/home/).
- [Consumir un servicio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Lista de tareas cognitivos servicios (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
