---
title: "Pila HttpClient y Selector de implementación de SSL/TLS para Android"
description: "Los selectores de pila HttpClient y la implementación de SSL/TLS determinan la implementación HttpClient y SSL/TLS que se usará en las aplicaciones de Xamarin.Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: bcb6f033c7fad76a17a7a5aa82f48a76b1ae501d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Pila HttpClient y Selector de implementación de SSL/TLS para Android

_Los selectores de pila HttpClient y la implementación de SSL/TLS determinan la implementación HttpClient y SSL/TLS que se usará en las aplicaciones de Xamarin.Android._

## <a name="overview"></a>Información general

Xamarin.Android proporciona dos cuadros combinados que controlan la configuración de TLS para una aplicación Android. Un cuadro combinado que identificará `HttpMessageHandler` va a usar al crear instancias de un `HttpClient` del objeto, mientras que la otra identifica qué implementación de TLS se usará en las solicitudes web.

> [!NOTE]
> **Nota:** proyectos deben hacer referencia a la **System.Net.Http** ensamblado.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

La configuración de la pila HttpClient se encuentra en las opciones de proyecto para un proyecto de Xamarin.Android. Haga clic en el **opciones Android** ficha y, a continuación, haga clic en el **opciones avanzadas** botón. Esto mostrará la **opciones avanzadas de Android** cuadro de diálogo que tiene cuadros combinados, uno para la implementación de HttpClient y otro para la implementación de SSL/TLS:


[ ![Opciones de Android de Visual Studio](http-stack-images/tls07-vs-sml.png)](http-stack-images/tls07-vs.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

La configuración de la pila HttpClient se encuentra en las opciones de proyecto para un proyecto de Xamarin.Android. Haga clic en el **generar > compilar Android** configuración y haga clic en el **General** ficha:

[ ![Visual Studio para Mac Android opciones](http-stack-images/tls07-xs-sml.png)](http-stack-images/tls07-xs.png)


-----

## <a name="httpclient-stack-selector"></a>Selector de pila de HttpClient

Esta opción de proyecto que controla `HttpMessageHandler` implementación se usará cada vez que un `HttpClient` se crea una instancia de objeto. De forma predeterminada, es el administrado `HttpClientHandler`.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![Android cuadro combinado de implementación HttpClient en Visual Studio](http-stack-images/tls04-vs-sml.png)](http-stack-images/tls04-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Android cuadro combinado de implementación HttpClient en Visual Studio para Mac](http-stack-images/tls04-xs.png )

-----

### <a name="managed-httpclienthandler"></a>Administrado (HttpClientHandler)

Controlador administrado es el controlador HttpClient completamente administrado que se ha enviado con versiones anteriores de Xamarin.Android.

#### <a name="pros"></a>Profesionales de TI:

- Es más compatible (funciones) con MS .NET y las versiones anteriores de Xamarin.

#### <a name="cons"></a>Inconvenientes:

- No está totalmente integrado con el sistema operativo (p. ej. se limita a TLS 1.0).
- Es normalmente mucho más lento (p. ej. cifrado) de la API nativa.
- Requiere más código administrado, crear aplicaciones de mayor tamaño.

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler es el controlador nuevo que delega en el código Java/OS nativo en lugar de implementar todo el contenido en código administrado.

#### <a name="pros"></a>Profesionales de TI:

- Use API nativa para mejorar el rendimiento y el menor tamaño de archivo ejecutable.
- Compatibilidad con los estándares más recientes, p. ej. TLS 1.2.

#### <a name="cons"></a>Inconvenientes:

- Requiere Android 5.0 o posterior.
- Algunas características de HttpClient/opciones no están disponibles.


### <a name="programatically-using-androidclienthandler"></a>Mediante programación, con `AndroidClientHandler`

El `Xamarin.Android.Net.AndroidClientHandler` es un `HttpMessageHandler` implementación específicamente para Xamarin.Android. Las instancias de esta clase utilizarán nativo `java.net.URLConnection` implementación para todas las conexiones HTTP. En teoría, esto le dará un aumento de rendimiento de HTTP y menores APK.

Este fragmento de código es un ejemplo de cómo explícitamente para una única instancia de la `HttpClient` clase:

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
>  **Tenga en cuenta**: el dispositivo Android subyacente debe admitir TLS 1.2 (Internet Explorer. Android 5.0 y versiones posteriores)


## <a name="ssltls-implementation-build-option"></a>Opción de compilación de la implementación de SSL/TLS

Esta opción de proyecto controla qué biblioteca TLS subyacente se usará en todas las solicitudes web, ambos `HttpClient` y `WebRequest`. De forma predeterminada, se selecciona TLS 1.2:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![Cuadro combinado de implementación de TLS/SSL en Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[ ![Cuadro combinado de implementación de TLS/SSL en Visual Studio para Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png)

-----

Por ejemplo:

```csharp
var client = new HttpClient();
```

Si la implementación HttpClient se estableció en **administrada** y la implementación de TLS estaba establecida como **nativo TLS 1.2 +**, la `client` objeto usaría los recursos administrados `HttpClientHandler` y TLS 1.2 (proporcionado por la biblioteca de BoringSSL) para sus solicitudes HTTP.

Sin embargo, si la **HttpClient implementación** está establecido en `AndroidHttpClient`, a continuación, todos los `HttpClient` objetos utilizará la clase de Java subyacente `java.net.URLConnection` y no se verán afectados por la **deimplementacióndeTLS/SSL** valor. `WebRequest` objetos utilizaría la biblioteca BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Otras formas de controlar la configuración de SSL/TLS

Hay tres maneras de que una aplicación de Xamarin.Android puede controlar la configuración de TLS:

1. Seleccione la biblioteca TLS HttpClient, implementación y de forma predeterminada en las opciones de proyecto.
2. Mediante programación, con `Xamarin.Android.Net.AndroidClientHandler`.
3. Declare las variables de entorno (opcional).

De las tres opciones, el enfoque recomendado es usar las opciones de proyecto Xamarin.Android para declarar el valor predeterminado `HttpMessageHandler` y TLS para toda la aplicación. A continuación, si es necesario, mediante programación crear instancias `Xamarin.Android.Net.AndroidClientHandler` objetos.
Estas opciones se describen anteriormente.

La tercera opción &ndash; usar variables de entorno &ndash; se explica más adelante.

### <a name="declare-environment-variables"></a>Declarar Variables de entorno

Hay dos variables de entorno que están relacionados con el uso de TLS en Xamarin.Android:

-   `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; El valor predeterminado declara esta variable de entorno `HttpMessageHandler` que va a usar la aplicación. Por ejemplo:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

-   `XA_TLS_PROVIDER` &ndash; Esta variable de entorno declarará biblioteca TLS en la que se usará, ya sea `btls`, `legacy`, o `default` (que es lo mismo que omitir esta variable):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Esta variable de entorno se establece mediante la adición de un _archivo entorno_ al proyecto. Un archivo de entorno es un archivo de texto con formato Unix con una acción de compilación de **AndroidEnvironment**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Captura de pantalla de la acción de compilación AndroidEnvironment en Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Captura de pantalla de la AndroidEnvironment generar la acción en Visual Studio para Mac.](http-stack-images/tls03-xs.png)

-----

Vea la [Xamarin.Android entorno](~/android/deploy-test/environment.md) guía para obtener más información sobre las variables de entorno y Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de la capa de transporte (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
