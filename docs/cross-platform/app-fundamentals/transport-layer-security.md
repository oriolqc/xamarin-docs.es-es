---
title: Seguridad de la capa de transporte (TLS) 1.2
description: Este documento se describe cómo permitir que TLS 1.2 para proyectos de Xamarin.iOS, Xamarin.Android y Xamarin.Mac. Muestra cómo hacerlo en 2019 de Visual Studio y Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 26870ae0e84a84a7b78f7766a8e134ecfc7b223e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304807"
---
# <a name="transport-layer-security-tls-12"></a>Seguridad de la capa de transporte (TLS) 1.2

Con la versión más reciente de [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) es importante para asegurar las comunicaciones de red de aplicación son seguras.

> [!WARNING]
> **Abril de 2018** : debido a una mayor seguridad principales de los requisitos, incluido el cumplimiento del estándar PCI, los proveedores de nube y servidores web que se esperan para detener la compatibilidad con TLS versiones anteriores a 1.2.  Proyectos de Xamarin creados en versiones anteriores de Visual Studio de forma predeterminada usar versiones anteriores de TLS.
>
> Para asegurarse de que las aplicaciones seguirán funcionando con estos servidores y servicios, **debe actualizar los proyectos de Xamarin para usar las opciones siguientes, a continuación, volver a compilar y volver a implementar sus aplicaciones** a los usuarios.

Deben hacer referencia a los proyectos de la **System.Net.Http** ensamblado y se configura como se muestra a continuación.

## <a name="update-xamarinandroid-to-tls-12"></a>Actualización de Xamarin.Android para TLS 1.2

Actualización de la **implementación de HttpClient** y **implementación de SSL/TLS** opciones para habilitar la seguridad de TLS 1.2.

> [!NOTE]
> Requiere Android 5.0 o posterior.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Esta configuración se puede encontrar en **las propiedades del proyecto > Opciones de Android** y, a continuación, haga clic en el **avanzadas** botón:

[![Configurar HttpClient y TLS en Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Esta configuración se puede encontrar en **opciones de proyecto > compilar > compilación de Android** pestaña:

[![Configurar HttpClient y TLS en Visual Studio para Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Actualización de Xamarin.iOS a TLS 1.2

Actualización de la **implementación de HttpClient** opción para habilitar la seguridad TSL 1.2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Esta configuración puede encontrarse en **las propiedades del proyecto > compilación de iOS**:

[![Configurar HttpClient y TLS en Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Esta configuración puede encontrarse en **opciones de proyecto > compilar > compilación de iOS** pestaña:

[![Configurar HttpClient en Visual Studio para Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Actualizar Xamarin.Mac a TLS 1.2

En Visual Studio para Mac, para habilitar TLS 1.2 en una aplicación de Xamarin.Mac, actualice el **implementación de HttpClient** opción **opciones de proyecto > compilar > compilación de Mac**:

[![Configurar HttpClient en Visual Studio para Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> La próxima versión de Xamarin.Mac 4.8 solo será compatible con macOS 10.9 o versiones posteriores.
> Las versiones anteriores de Xamarin.Mac son compatibles con macOS 10.7 o versiones posteriores, pero estas versiones anteriores de macOS carecen de infraestructura TLS suficiente para admitir TLS 1.2. Para macOS 10.7 o Mac OS 10.8 de destino, use Xamarin.Mac 4.6 o una versión anterior.

## <a name="alternative-configuration-options"></a>Opciones de configuración alternativa

En esta sección se trata las alternativas a las configuraciones compatibles con TLS 1.2 mostradas anteriormente.
Los desarrolladores de aplicaciones solo deben considerar estas alternativas si comprenden los riesgos de usar diferentes niveles de compatibilidad con TLS.

### <a name="httpclient-implementation"></a>Implementación de HttpClient

Los desarrolladores de Xamarin siempre han sido capaz de utilizar las clases nativas de la red en su código, sin embargo, es también una opción que determina qué pila de red está usando el `HttpClient` clases. Esto proporciona una API de .NET está familiarizado con las ventajas de velocidad y seguridad de la plataforma nativa.

Las opciones son:

- **Pila administrado** : la funcionalidad de red proporcionado Mono, o
- **Pila nativa** : varias redes de las API proporcionadas por las plataformas subyacentes (Android, iOS o macOS).

La pila administrada proporciona el máximo nivel de compatibilidad con código .NET ya existente, sin embargo, puede ser más lento y dar lugar a mayor tamaño del archivo ejecutable.

Las opciones nativas pueden ser más rápidas y mejorar la seguridad (incluidas TLS 1.2), pero no puede proporcionar toda la funcionalidad y las opciones de la `HttpClient` clase.

### <a name="ssltls-implementation-android"></a>Implementación de SSL/TLS (Android)

Opciones de proyecto de Android también le permiten elegir qué implementación de SSL/TLS para admitir:

- **Mono/administrados** – TLS 1.1 en Android
- **Nativo** – TLS 1.2 en Android.

Nuevos proyectos de Xamarin de forma predeterminada la implementación nativa que admita TLS 1.2 (que se recomienda para todos los proyectos), pero puede cambiar al código administrado si necesario por motivos de compatibilidad.

> [!IMPORTANT]
> El **Mono administrada** opción ha sido [quitado de iOS y Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/) opciones del proyecto.
>
> La opción nativa siempre se utiliza en plataformas iOS y Mac.

## <a name="platform-specific-details"></a>Detalles específicos de plataforma

El resumen anterior explica las opciones de nivel de proyecto para la implementación de HttpClient y SSL/TLS en proyectos de Xamarin. La implementación de HttpClient también puede establecerse dinámicamente en el código. Consulte estas guías específicas de la plataforma para obtener más información:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS y Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>Resumen

Las aplicaciones deben usar la seguridad de capa de transporte (TLS) 1.2 siempre que sea posible.
Debe actualizar la configuración en las aplicaciones existentes según las instrucciones de este artículo, a continuación, volver a compilar y volver a implementar a sus clientes.

## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
- [Entorno de Xamarin.Android](~/android/deploy-test/environment.md)
- [Xamarin Cycle 9 (febrero de 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Notas de la versión de mono 4.8 - soporte de TLS 1.2](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient, HttpClientHandler y WebRequestHandler explican](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](https://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation.NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [Cliente HTTP (ejemplo)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
