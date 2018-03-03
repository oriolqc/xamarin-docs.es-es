---
title: "Conversión de texto mediante el traductor de API"
description: "La API de traductor de Microsoft se puede utilizar para traducir el texto de voz y a través de una API de REST. Este artículo explica cómo usar la API de conversión de texto de Microsoft para traducir el texto de un idioma a otro en una aplicación de Xamarin.Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: f403ebaffdf742c22e61b73aee7a42648fe597dc
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="text-translation-using-the-translator-api"></a>Conversión de texto mediante el traductor de API

_La API de traductor de Microsoft se puede utilizar para traducir el texto de voz y a través de una API de REST. Este artículo explica cómo usar la API de conversión de texto de Microsoft para traducir el texto de un idioma a otro en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

La API de traductor tiene dos componentes:

- Una API de REST para traducir el texto de un idioma en el texto de otro idioma de traducción de texto. La API detecta automáticamente el idioma del texto que se envió antes de la traducción de la misma.
- Una traducción de voz de API de REST para transcribir voz de un idioma en el texto de otro idioma. La API también integra las capacidades de texto a voz para hablar de nuevo el texto traducido.

En este artículo se centra en traducir el texto de un idioma a otro utilizando la API de conversión de texto.

Para usar la API de conversión de texto se debe obtener una clave de API. Esto puede obtenerse siguiendo las instrucciones de [Introducción](http://docs.microsofttranslator.com/text-translate.html) en [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

Para obtener más información acerca de la API de traductor de Microsoft, consulte [Microsoft Translator documentación](https://www.microsoft.com/cognitive-services/translator-api/documentation/TranslatorInfo/overview) en microsoft.com.

## <a name="authentication"></a>Autenticación

Todas las solicitudes realizadas a la API de conversión de texto requiere un token de acceso JSON Web Token (JWT), que se puede obtener desde el servicio de token de servicios cognitivos en `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Se puede obtener un token mediante la realización de una solicitud POST al servicio de token, especificar un `Ocp-Apim-Subscription-Key` encabezado que contiene la clave de API como su valor.

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

El token de acceso debe especificarse en cada API de traducción de texto llamar como una `Authorization` prefijado con la cadena de encabezado `Bearer`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
using (var httpClient = new HttpClient())
{
  httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
  ...
}  
```

Para obtener más información acerca de los servicios de token cognitivas, consulte [API de Token de autenticación](http://docs.microsofttranslator.com/oauth-token.html) en [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

## <a name="performing-text-translation"></a>Realizar la conversión a texto

Traducción de texto se puede lograr mediante la realización de una solicitud GET en el `Translate` API en `https://api.microsofttranslator.com/v2/http.svc/Translate`. En la aplicación de ejemplo, el `TranslateTextAsync` método invoca el proceso de traducción de texto:

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

El `TranslateTextAsync` método genera un URI de solicitud y recupera un token de acceso desde el servicio de token. A continuación, se envía la solicitud de traducción de texto a la `Translate` API, que devuelve una respuesta XML que contiene el resultado. Se analiza la respuesta XML y se devuelve el resultado de la traducción para el método que realiza la llamada para su presentación.

Para obtener más información acerca de las API de REST de traducción de texto, consulte [código de ejemplo](http://docs.microsofttranslator.com/text-translate.html#/default) en [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

### <a name="configuring-text-translation"></a>Configurar la traducción de texto

El proceso de traducción de texto puede configurarse mediante la especificación de parámetros de consulta HTTP. Hay parámetros obligatorios y opcionales, con el método siguiente que muestra los parámetros obligatorios que se deben establecer:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Este método establece el texto que se deben traducir y el idioma para traducir el texto. Para obtener una lista de los idiomas admitidos por Microsoft Translator, consulte [idiomas](https://www.microsoft.com/translator/languages.aspx) en microsoft.com.

> [!NOTE]
> Si una aplicación necesita conocer el lenguaje en que el texto está en, el `Detect` puede llamar a API para detectar el idioma de la cadena de texto.

Para obtener más información acerca de los parámetros obligatorios y opcionales, consulte [texto Translation API](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate) en [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

### <a name="sending-the-request"></a>Enviar la solicitud

El `SendRequestAsync` método realiza la solicitud GET a la API de REST de traducción de texto y devuelve la respuesta:

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
  using (var httpClient = new HttpClient())
  {
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
  }
}
```

Este método crea la solicitud GET al agregar el token de acceso a la `Authorization` encabezado, el prefijo con la cadena `Bearer`. A continuación, se envía la solicitud GET a la `Translate` API, con la dirección URL de solicitud especifica el texto que se deben traducir y el idioma para traducir el texto a. La respuesta, a continuación, lee y devuelve al método de llamada.

El `Translate` API enviará el código de estado HTTP 200 (OK) en la respuesta, siempre que la solicitud es válida, lo que indica que la solicitud es correcta y que la información solicitada está en la respuesta. Para obtener una lista de posibles respuestas de error, vea mensajes de respuesta en [obtener traducir](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate) en [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

### <a name="processing-the-response"></a>Procesamiento de la respuesta

La respuesta de la API se devuelve en formato XML. Los siguientes datos XML muestran un mensaje de respuesta correcta típico:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

En la aplicación de ejemplo, se analiza la respuesta XML en un `XDocument` instancia, con el valor de raíz XML se devuelva al método de llamada para su presentación tal y como se muestra en las capturas de pantalla siguiente:

![](text-translation-images/text-translation.png "Traducción de texto en alemán")

## <a name="summary"></a>Resumen

Este artículo explica cómo usar la API de conversión de texto de Microsoft para traducir el texto de un idioma en el texto de otro idioma en una aplicación de Xamarin.Forms. Además de traducir el texto, la API de traductor de Microsoft también pueden transcribir voz de un idioma en el texto de otro idioma.



## <a name="related-links"></a>Vínculos relacionados

- [Documentación de Microsoft Translator](https://www.microsoft.com/cognitive-services/translator-api/documentation/TranslatorInfo/overview)
- [Consumir un servicio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Lista de tareas cognitivos servicios (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Traducción de texto API](http://docs.microsofttranslator.com/text-translate.html)
