---
title: Pila HttpClient y Selector de implementación de SSL/TLS para Android
description: Los selectores de pila HttpClient y la implementación de SSL/TLS determinan la implementación HttpClient y SSL/TLS que se usará en las aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/20/2018
ms.openlocfilehash: 765c51346ac63a00838fec52bde87b38091e2dd9
ms.sourcegitcommit: a4c2a63ba76b839cda99e4474e7ab46fe307cd39
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/01/2018
ms.locfileid: "34689479"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Pila HttpClient y Selector de implementación de SSL/TLS para Android

Los selectores de pila HttpClient y la implementación de SSL/TLS determinan la implementación HttpClient y SSL/TLS que se usará en las aplicaciones de Xamarin.Android.

Deben hacer referencia a proyectos de la **System.Net.Http** ensamblado.

> [!WARNING]
> **Abril de 2018** : debido a una mayor seguridad requisitos, incluidos el cumplimiento del estándar PCI, las principales proveedores de nube y servidores web se deben interrumpir la compatibilidad con versiones TLS anteriores a 1.2.  Xamarin proyectos creados en versiones anteriores de forma predeterminada de Visual Studio para usar versiones anteriores de TLS.
>
> Para asegurarse de que las aplicaciones seguirán funcionando con estos servidores y servicios, **debe actualizar los proyectos de Xamarin con la `Android HttpClient` y `Native TLS 1.2` configuración se muestra a continuación, a continuación, volver a compilar y volver a implementar las aplicaciones** a su usuarios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

La configuración de Xamarin.Android HttpClient está en **Project Options > opciones Android**, a continuación, haga clic en el **opciones avanzadas** botón.

Se trata de la configuración recomendada para la compatibilidad con TLS 1.2:

[![Opciones de Android de Visual Studio](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

La configuración de Xamarin.Android HttpClient está en **Project Options > compilar > compilar Android** configuración y haga clic en el **General** ficha.

Se trata de la configuración recomendada para la compatibilidad con TLS 1.2:

[![Visual Studio para Mac Android opciones](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Opciones de configuración alternativa

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler es el controlador nuevo que delega en el código Java/OS nativo en lugar de implementar todo el contenido en código administrado.
**Esta es la opción recomendada.**

#### <a name="pros"></a>Profesionales de TI

- Use API nativa para mejorar el rendimiento y el menor tamaño de archivo ejecutable.
- Compatibilidad con los estándares más recientes, p. ej. TLS 1.2.

#### <a name="cons"></a>Inconvenientes

- Requiere Android 5.0 o posterior.
- Algunas características de HttpClient/opciones no están disponibles.

### <a name="managed-httpclienthandler"></a>Administrado (HttpClientHandler)

Controlador administrado es el controlador HttpClient completamente administrado que se ha enviado con versiones anteriores de Xamarin.Android.

#### <a name="pros"></a>Profesionales de TI

- Es más compatible (funciones) con MS .NET y las versiones anteriores de Xamarin.

#### <a name="cons"></a>Inconvenientes

- No está totalmente integrado con el sistema operativo (p. ej. se limita a TLS 1.0).
- Es normalmente mucho más lento (p. ej. cifrado) de la API nativa.
- Requiere más código administrado, crear aplicaciones de mayor tamaño.



### <a name="choosing-a-handler"></a>Elegir un controlador

La elección entre `AndroidClientHandler` y `HttpClientHandler` depende de las necesidades de su aplicación. `AndroidClientHandler` se recomienda para la compatibilidad de seguridad más actualizada, p. ej.

-   Necesita que admitir TLS 1.2 +.
-   La aplicación está destinado a Android 5.0 (API de 21) o una versión posterior.
-   Necesita TLS 1.2 + soporte para `HttpClient`.
-   No necesita compatibilidad con TLS 1.2 + `WebClient`.

`HttpClientHandler` es una buena elección si necesita TLS 1.2 + admiten, pero debe ser compatible con versiones de Android anteriores a Android 5.0. También es una buena elección si necesita TLS 1.2 + soporte para `WebClient`.

A partir de 8.3 Xamarin.Android, `HttpClientHandler` el valor predeterminado es aburrido SSL (`btls`) como el proveedor subyacente de TLS. El proveedor de perforación SSL TLS ofrece las siguientes ventajas:

-   Es compatible con TLS 1.2 +.
-   Admite todas las versiones de Android.
-   Proporciona soporte de TLS 1.2 + para ambos `HttpClient` y `WebClient`.

La desventaja de utilizar SSL de perforación como el proveedor TLS subordinado es que puede aumentar el tamaño de la APK resultante (agrega aproximadamente 1MB de tamaño APK adicionales por ABI compatible).

A partir de Xamarin.Android 8.3, el proveedor TLS predeterminado es aburrido SSL (`btls`). Si no desea utilizar SSL de perforación, puede revertir a la implementación de SSL administrado histórica estableciendo la `$(AndroidTlsProvider)` propiedad `legacy` (para obtener más información acerca de cómo establecer las propiedades de compilación, consulte [proceso de compilación](~/android/deploy-test/building-apps/build-process.md)).


### <a name="programatically-using-androidclienthandler"></a>Mediante programación, con `AndroidClientHandler`

El `Xamarin.Android.Net.AndroidClientHandler` es un `HttpMessageHandler` implementación específicamente para Xamarin.Android.
Las instancias de esta clase utilizarán nativo `java.net.URLConnection` implementación para todas las conexiones HTTP. En teoría, esto le dará un aumento de rendimiento de HTTP y menores APK.

Este fragmento de código es un ejemplo de cómo explícitamente para una única instancia de la `HttpClient` clase:

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> El dispositivo Android subyacente debe admitir TLS 1.2 (Internet Explorer. Android 5.0 y versiones posteriores)


## <a name="ssltls-implementation-build-option"></a>Opción de compilación de la implementación de SSL/TLS

Esta opción de proyecto controla qué biblioteca TLS subyacente se usará en todas las solicitudes web, ambos `HttpClient` y `WebRequest`. De forma predeterminada, se selecciona TLS 1.2:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Cuadro combinado de implementación de TLS/SSL en Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Cuadro combinado de implementación de TLS/SSL en Visual Studio para Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

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

De las tres opciones, el enfoque recomendado es usar las opciones de proyecto Xamarin.Android para declarar el valor predeterminado `HttpMessageHandler` y TLS para toda la aplicación. A continuación, si es necesario, mediante programación crear instancias `Xamarin.Android.Net.AndroidClientHandler` objetos. Estas opciones se describen anteriormente.

La tercera opción &ndash; usar variables de entorno &ndash; se explica más adelante.

### <a name="declare-environment-variables"></a>Declarar Variables de entorno

Hay dos variables de entorno que están relacionados con el uso de TLS en Xamarin.Android:

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; El valor predeterminado declara esta variable de entorno `HttpMessageHandler` que va a usar la aplicación. Por ejemplo:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; Esta variable de entorno declarará biblioteca TLS en la que se usará, ya sea `btls`, `legacy`, o `default` (que es lo mismo que omitir esta variable):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Esta variable de entorno se establece mediante la adición de un _archivo entorno_ al proyecto. Un archivo de entorno es un archivo de texto con formato Unix con una acción de compilación de **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Captura de pantalla de la acción de compilación AndroidEnvironment en Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Captura de pantalla de la AndroidEnvironment generar la acción en Visual Studio para Mac.](http-stack-images/tls03-xs.png)

-----

Vea la [Xamarin.Android entorno](~/android/deploy-test/environment.md) guía para obtener más información sobre las variables de entorno y Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de la capa de transporte (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
