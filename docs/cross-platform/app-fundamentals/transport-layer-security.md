---
title: Seguridad de la capa de transporte (TLS)
description: Habilitar TLS 1.2 para los proyectos de Xamarin en Android, iOS y Mac
ms.topic: article
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/10/2017
ms.openlocfilehash: b0f205c5ab2c65f0e2a99f912f3961f12a4f2b7a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="transport-layer-security-tls"></a>Seguridad de la capa de transporte (TLS)

_Habilitar TLS 1.2 para los proyectos de Xamarin en Android, iOS y Mac_

Con la versión más reciente de [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) es importante para asegurar las comunicaciones de red de aplicación son seguras.

> [!NOTE]
> Xamarin libera desde [febrero de 2017](https://releases.xamarin.com/stable-release-cycle-9/) usan TLS 1.2 en proyectos nuevos de forma predeterminada.

Soporte de TLS 1.2 ahora está disponible en:

* Mono 4.8 (incluye [soporte de TLS 1.2](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support))
* Xamarin.iOS
* Xamarin.Mac
* Xamarin.Android (requiere Android 5.0 o posterior)

Deben hacer referencia a proyectos de la **System.Net.Http** ensamblado. 

## <a name="updating-to-tls-12"></a>Actualización de TLS 1.2

Esta sección explica algunas de las opciones de configuración de red en los proyectos de Xamarin, por lo que puede actualizar su _existente_ aplicaciones para aprovechar las ventajas del protocolo más seguro.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Esta configuración puede encontrarse en **Project Options > opciones Android** y, a continuación, haga clic en el **avanzadas** botón: 

[![Configurar HttpClient y TLS en Visual Studio](transport-layer-security-images/properties-vs-sml.png)](transport-layer-security-images/properties-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
Esta configuración puede encontrarse en **propiedades del proyecto > Opciones de compilación > Opciones avanzadas** ficha:

[![Configurar HttpClient y TLS en Xamarin Studio y Visual Studio para Mac](transport-layer-security-images/properties-xs-sml.png)](transport-layer-security-images/properties-xs.png#lightbox)

-----


### <a name="httpclient-implementation"></a>Implementación de HttpClient

Los desarrolladores de Xamarin siempre han sido capaz de usar las clases nativas de red en su código, sin embargo, es también una opción que determina qué pila de red se usa por la `HttpClient` clases. Esto proporciona una API de .NET familiarizado con las ventajas de velocidad y la seguridad de la plataforma nativa.

Las opciones son:

- **Pila administrado** : la funcionalidad de red proporcionada por el Mono, o
- **Pila nativa** : varias redes las API proporcionadas por las plataformas subyacentes (Android, iOS o Mac OS).

La pila administrada proporciona el máximo nivel de compatibilidad con el código de .NET existente, sin embargo, puede ser más lento y mayor tamaño del archivo ejecutable como resultado.

Las opciones nativo pueden ser más rápidas y tiene la mejor seguridad (incluidas TLS 1.2), pero no puede proporcionar toda la funcionalidad y opciones de la `HttpClient` clase.


### <a name="ssltls-implementation"></a>Implementación de SSL/TLS

Opciones de proyecto también le permiten elegir qué implementación de SSL/TLS para admitir:

- **Mono/Managed** – TLS 1.1 en Android, TLS 1.0 en iOS y Mac OS.
- **Native** – TLS 1.2 en Mac OS, iOS y Android.

El valor predeterminado de nuevos proyectos de Xamarin para la implementación nativa que admita TLS 1.2 (lo que se recomienda para todos los proyectos), aunque puede cambiar el código administrado si necesarios por motivos de compatibilidad.

> [!IMPORTANT]
> El **Mono/Managed** opción se quitará en una [versión futura](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/).
>
> Se recomienda la opción nativo.

## <a name="platform-specific-details"></a>Detalles específicos de la plataforma

El resumen anterior explica la configuración de nivel de proyecto para la implementación HttpClient y SSL/TLS en proyectos de Xamarin. La implementación de HttpClient también puede establecerse dinámicamente en el código y en iOS hay dos opciones nativo para elegir.

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS y Mac**](~/cross-platform/macios/http-stack.md)


## <a name="summary"></a>Resumen

Aplicaciones deben utilizar la seguridad de la capa de transporte (TLS) 1.2 siempre que sea posible.
Nuevas aplicaciones predeterminado ahora a esta configuración, sin embargo, puede que necesite actualizar la configuración de las aplicaciones existentes en las instrucciones de este artículo.

## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
- [Entorno de Xamarin.Android](~/android/deploy-test/environment.md)
- [Ciclo de Xamarin 9 (febrero de 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Notas de la versión de mono 4.8 - soporte de TLS 1.2](http://www.mono-project.com/docs/about-monohttps://developer.xamarin.com/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient, HttpClientHandler y WebRequestHandler explicadas](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/en-us/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/en-us/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [Cliente HTTP (ejemplo)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
