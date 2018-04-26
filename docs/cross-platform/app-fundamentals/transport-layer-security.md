---
title: Seguridad de la capa de transporte (TLS) 1.2
description: Habilitar TLS 1.2 para los proyectos de Xamarin en Android, iOS y Mac
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 6205e8633ccdd2c1e568e7de8103c38eb9edbc2f
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="transport-layer-security-tls-12"></a>Seguridad de la capa de transporte (TLS) 1.2

Con la versión más reciente de [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) es importante para asegurar las comunicaciones de red de aplicación son seguras.

> [!WARNING]
> **Abril de 2018** : debido a una mayor seguridad requisitos, incluidos el cumplimiento del estándar PCI, las principales proveedores de nube y servidores web se deben interrumpir la compatibilidad con versiones TLS anteriores a 1.2.  Xamarin proyectos creados en versiones anteriores de forma predeterminada de Visual Studio para usar versiones anteriores de TLS.
>
> Para asegurarse de que las aplicaciones seguirán funcionando con estos servidores y servicios, **debe actualizar los proyectos de Xamarin para utilizar la configuración siguiente, a continuación, volver a compilar y volver a implementar las aplicaciones** a los usuarios.

Deben hacer referencia a proyectos de la **System.Net.Http** ensamblado y se configura como se muestra a continuación.

## <a name="update-android-to-tls-12"></a>Actualizar Android para TLS 1.2

Actualización de la **HttpClient implementación** y **implementación de SSL/TLS** opciones para habilitar la seguridad de TLS 1.2.

> [!NOTE]
> Requiere Android 5.0 o versiones más reciente.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Esta configuración puede encontrarse en **propiedades del proyecto > opciones Android** y, a continuación, haga clic en el **avanzadas** botón:

[![Configurar HttpClient y TLS en Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Esta configuración puede encontrarse en **Project Options > compilar > compilar Android** ficha:

[![Configurar HttpClient y TLS en Visual Studio para Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-ios-to-tls-12"></a>Actualizar iOS para TLS 1.2

Actualización de la **HttpClient implementación** opción para habilitar la seguridad TSL 1.2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Esta configuración puede encontrarse en **propiedades del proyecto > iOS compilación**:

[![Configurar HttpClient y TLS en Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Esta configuración puede encontrarse en **Project Options > compilar > iOS compilación** ficha:

[![Configurar HttpClient en Visual Studio para Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-macos-to-tls-12-in-visual-studio-for-mac"></a>Actualizar macOS para TLS 1.2 en Visual Studio para Mac

Actualización la **HttpClient implementación** opción **Project Options > compilar > compilar Mac** ficha para habilitar la seguridad TSL 1.2:

[![Configurar HttpClient en Visual Studio para Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

## <a name="alternative-configuration-options"></a>Opciones de configuración alternativa

En esta sección se trata las alternativas a las configuraciones compatibles con TLS 1.2 mostradas anteriormente.
Los desarrolladores de aplicaciones solo deben considerar estas alternativas si entienden los riesgos de usar diferentes niveles de compatibilidad con TLS.

### <a name="httpclient-implementation"></a>Implementación de HttpClient

Los desarrolladores de Xamarin siempre han sido capaz de usar las clases nativas de red en su código, sin embargo, es también una opción que determina qué pila de red se usa por la `HttpClient` clases. Esto proporciona una API de .NET familiarizado con las ventajas de velocidad y la seguridad de la plataforma nativa.

Las opciones son:

- **Pila administrado** : la funcionalidad de red proporcionada por el Mono, o
- **Pila nativa** : varias redes las API proporcionadas por las plataformas subyacentes (Android, iOS o Mac OS).

La pila administrada proporciona el máximo nivel de compatibilidad con el código de .NET existente, sin embargo, puede ser más lento y mayor tamaño del archivo ejecutable como resultado.

Las opciones nativo pueden ser más rápidas y tiene la mejor seguridad (incluidas TLS 1.2), pero no puede proporcionar toda la funcionalidad y opciones de la `HttpClient` clase.

### <a name="ssltls-implementation-android"></a>Implementación de SSL/TLS (Android)

Opciones de proyecto de Android también le permiten elegir qué implementación de SSL/TLS para admitir:

- **Mono o administrada** – TLS 1.1 en Android
- **Native** – TLS 1.2 en Android.

El valor predeterminado de nuevos proyectos de Xamarin para la implementación nativa que admita TLS 1.2 (lo que se recomienda para todos los proyectos), aunque puede cambiar el código administrado si necesarios por motivos de compatibilidad.

> [!IMPORTANT]
> El **Mono/Managed** opción ha sido [quitado de iOS y Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/) opciones de proyecto.
>
> La opción nativo siempre se utiliza en plataformas iOS y Mac.

## <a name="platform-specific-details"></a>Detalles específicos de la plataforma

El resumen anterior explica la configuración de nivel de proyecto para la implementación HttpClient y SSL/TLS en proyectos de Xamarin. La implementación de HttpClient también puede establecerse dinámicamente en el código. Consulte estas guías específica de la plataforma para obtener más información:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS y Mac**](~/cross-platform/macios/http-stack.md)


## <a name="summary"></a>Resumen

Aplicaciones deben utilizar la seguridad de la capa de transporte (TLS) 1.2 siempre que sea posible.
Debe actualizar la configuración de las aplicaciones existentes en las instrucciones de este artículo, a continuación, volver a compilar y volver a implementar a sus clientes.

## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
- [Entorno de Xamarin.Android](~/android/deploy-test/environment.md)
- [Ciclo de Xamarin 9 (febrero de 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Notas de la versión de mono 4.8 - soporte de TLS 1.2](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient, HttpClientHandler y WebRequestHandler explicadas](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [Cliente HTTP (ejemplo)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
