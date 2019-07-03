---
title: Pila HttpClient y Selector de implementación de SSL/TLS para Android
description: Los selectores de pila HttpClient e implementación de SSL/TLS determinan la implementación de HttpClient y SSL/TLS que se usará en las aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: e6d8ccea7331f53d2e7262630689075a8b9a1ff1
ms.sourcegitcommit: 450106d5f05b4473bf7f5b9100b2eaf18c9110de
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67522970"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Pila HttpClient y Selector de implementación de SSL/TLS para Android

Los selectores de pila HttpClient e implementación de SSL/TLS determinan la implementación de HttpClient y SSL/TLS que se usará en las aplicaciones de Xamarin.Android.

Deben hacer referencia a los proyectos de la **System.Net.Http** ensamblado.

> [!WARNING]
> **Abril de 2018** : debido a una mayor seguridad principales de los requisitos, incluido el cumplimiento del estándar PCI, los proveedores de nube y servidores web que se esperan para detener la compatibilidad con TLS versiones anteriores a 1.2.  Proyectos de Xamarin creados en versiones anteriores de Visual Studio de forma predeterminada usar versiones anteriores de TLS.
>
> Para asegurarse de que las aplicaciones seguirán funcionando con estos servidores y servicios, **debe actualizar los proyectos de Xamarin con el `Android HttpClient` y `Native TLS 1.2` configuración que se muestra a continuación, a continuación, volver a compilar y volver a implementar sus aplicaciones** a su usuarios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

La configuración de Xamarin.Android HttpClient es en **opciones de proyecto > Opciones de Android**, a continuación, haga clic en el **opciones avanzadas** botón.

Se trata de la configuración recomendada para la compatibilidad con TLS 1.2:

[![Opciones de Android de Visual Studio](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

La configuración de Xamarin.Android HttpClient es en **opciones de proyecto > compilar > compilación de Android** configuración y haga clic en el **General** ficha.

Se trata de la configuración recomendada para la compatibilidad con TLS 1.2:

[![Visual Studio para Mac opciones de Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Opciones de configuración alternativa

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler es el controlador nuevo que delega en el código de Java/SO nativo en lugar de implementar todo en código administrado.
**Esta es la opción recomendada.**

#### <a name="pros"></a>Profesionales

- Use la API nativa para mejorar el rendimiento y tamaño del archivo ejecutable más pequeño.
- Compatibilidad con los estándares más recientes, por ejemplo. TLS 1.2.

#### <a name="cons"></a>Desventajas

- Requiere Android 4.1 o posterior.
- Algunas características de HttpClient/opciones no están disponibles.

### <a name="managed-httpclienthandler"></a>Administrado (HttpClientHandler)

Controlador administrado es el controlador de HttpClient totalmente administrado que se ha enviado con las versiones anteriores de Xamarin.Android.

#### <a name="pros"></a>Profesionales

- Es más compatible (características) con Microsoft .NET y las versiones anteriores de Xamarin.

#### <a name="cons"></a>Desventajas

- No está totalmente integrado con el sistema operativo (p ej. se limita a TLS 1.0).
- Es normalmente mucho más lento (p ej. cifrado) de la API nativa.
- Requiere más código administrado, crear aplicaciones más grandes.



### <a name="choosing-a-handler"></a>Elegir un controlador

La elección entre `AndroidClientHandler` y `HttpClientHandler` depende de las necesidades de su aplicación. `AndroidClientHandler` se recomienda para la compatibilidad de seguridad más actualizada, por ejemplo.

-   Necesita que admitir TLS 1.2 +.
-   Destina la aplicación (API 16) de Android 4.1 o posterior.
-   Necesita TLS 1.2 + soporte para `HttpClient`.
-   No necesita compatibilidad con TLS 1.2 + `WebClient`.

`HttpClientHandler` es una buena elección si necesita TLS 1.2 + admite, pero debe ser compatible con versiones de Android anteriores a Android 4.1. También es una buena elección si necesita TLS 1.2 + soporte para `WebClient`.

A partir de Xamarin.Android 8.3, `HttpClientHandler` Boring SSL tiene como valor predeterminado (`btls`) como el proveedor TLS subyacente. El proveedor de TLS de SSL aburridas ofrece las siguientes ventajas:

-   Es compatible con TLS 1.2 +.
-   Admite todas las versiones de Android.
-   Proporciona compatibilidad con TLS 1.2 + para ambos `HttpClient` y `WebClient`.

La desventaja de utilizar Boring SSL como el proveedor TLS subyacente es que puede aumentar el tamaño del APK resultante (agrega aproximadamente 1MB de tamaño adicional de APK por ABI admitido).

A partir de Xamarin.Android 8.3, el proveedor TLS de forma predeterminada es Boring SSL (`btls`). Si no desea usar Boring SSL, puede revertir a la implementación de SSL administrada histórica estableciendo el `$(AndroidTlsProvider)` propiedad `legacy` (para obtener más información sobre cómo establecer las propiedades de compilación, véase [proceso de compilación](~/android/deploy-test/building-apps/build-process.md)).


### <a name="programatically-using-androidclienthandler"></a>Utilizando mediante programación `AndroidClientHandler`

El `Xamarin.Android.Net.AndroidClientHandler` es un `HttpMessageHandler` implementación específicamente para Xamarin.Android.
Las instancias de esta clase utilizarán nativo `java.net.URLConnection` implementación para todas las conexiones HTTP. En teoría, esto le proporcionará un aumento de rendimiento de HTTP y los tamaños más pequeños de APK.

Este fragmento de código es un ejemplo de cómo explícitamente para una única instancia de la `HttpClient` clase:

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> El dispositivo Android subyacente debe admitir TLS 1.2 (es decir. Android 4.1 y versiones posteriores). Tenga en cuenta que es la compatibilidad oficial con TLS 1.2 en Android 5.0 y versiones posteriores. Sin embargo, algunos dispositivos admiten TLS 1.2 en Android 4.1 +.


## <a name="ssltls-implementation-build-option"></a>Opción de compilación de implementación de SSL/TLS

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

Si se estableció la implementación de HttpClient en **administrada** y la implementación de TLS se estableció en **TLS 1.2 + nativo**, el `client` objeto usaría administrado `HttpClientHandler` y TLS 1.2 (proporcionado por la biblioteca BoringSSL) para sus solicitudes HTTP.

Sin embargo, si la **implementación de HttpClient** está establecido en `AndroidHttpClient`, a continuación, todos `HttpClient` objetos usará la clase de Java subyacente `java.net.URLConnection` y no se verán afectados por la **deimplementacióndeTLS/SSL** valor. `WebRequest` objetos usaría la biblioteca BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Otras formas de controlar la configuración de SSL/TLS

Hay tres maneras de que una aplicación de Xamarin.Android puede controlar la configuración de TLS:

1. Seleccione la biblioteca HttpClient TLS de implementación y de forma predeterminada en las opciones del proyecto.
2. Mediante programación utilizando `Xamarin.Android.Net.AndroidClientHandler`.
3. Declare las variables de entorno (opcional).

De las tres opciones, el enfoque recomendado es utilizar las opciones de proyecto de Xamarin.Android para declarar el valor predeterminado `HttpMessageHandler` y TLS para toda la aplicación. A continuación, si es necesario, mediante programación crear instancias `Xamarin.Android.Net.AndroidClientHandler` objetos. Estas opciones se han descrito anteriormente.

La tercera opción &ndash; mediante variables de entorno &ndash; se explica a continuación.

### <a name="declare-environment-variables"></a>Declare las Variables de entorno

Hay dos variables de entorno que están relacionados con el uso de TLS en Xamarin.Android:

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; El valor predeterminado declara esta variable de entorno `HttpMessageHandler` que va a usar la aplicación. Por ejemplo:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; Esta variable de entorno declarará se usará la biblioteca de TLS, ya sea `btls`, `legacy`, o `default` (que es lo mismo que omitir esta variable):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Esta variable de entorno se establece mediante la adición de un _archivo entorno_ al proyecto. Un archivo de entorno es un archivo de texto con formato Unix con una acción de compilación **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Captura de pantalla de la acción de compilación AndroidEnvironment en Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Captura de pantalla de la AndroidEnvironment generar la acción en Visual Studio para Mac.](http-stack-images/tls03-xs.png)

-----

Consulte la [Xamarin.Android entorno](~/android/deploy-test/environment.md) guía para obtener más información acerca de las variables de entorno y Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de la capa de transporte (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
