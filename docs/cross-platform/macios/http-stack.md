---
title: "Pila HttpClient y Selector de implementación de SSL/TLS para iOS/macOS"
description: "La pila de HttpClient y el Selector de la implementación de SSL/TLS determina la implementación HttpClient y SSL/TLS que va a utilizar la aplicación de iOS, tvOS o macOS de Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 06/12/2017
ms.openlocfilehash: eff096b1dca15b9b11038a599987f632bca2352f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-iosmacos"></a>Pila HttpClient y Selector de implementación de SSL/TLS para iOS/macOS

## <a name="httpclient-stack-selector"></a>Selector de pila de HttpClient

Disponible para Xamarin.iOS, Xamarin.tvOS y Xamarin.Mac: esta opción controla que `HttpClient` implementación va a utilizar. El valor predeterminado sigue siendo un HttpClient que funciona con `HttpWebRequest`, mientras que, opcionalmente, ahora puede cambiar a una implementación que usa iOS, tvOS o macOS transportes nativo (`NSUrlSession` o `CFNetwork` según el sistema operativo). La ventaja es binarios más pequeños y descargas más rápidas, la desventaja es que requiere el bucle de eventos se esté ejecutando para que operaciones asincrónicas que se ejecute.

Deben hacer referencia a proyectos de la **System.Net.Http** ensamblado.

<a name="Selecting-a-HttpClient-Stack" />

### <a name="selecting-a-httpclient-stack"></a>Al seleccionar una pila de HttpClient

Para ajustar el HttpClient siendo utilizado por la aplicación:

1. Haga doble clic en el **nombre del proyecto** en el **el Explorador de soluciones** para abrir las opciones de proyecto.
2. Cambie a la **generar** configuración del proyecto (por ejemplo, **iOS compilación** para una aplicación de Xamarin.iOS).
3. Desde el **HttpClient implementación** lista desplegable, seleccione el HttpClient escriba como uno de los siguientes: **administrada**, **CFNetwork** o **NSUrlSession**.

[![Elegir HttpClient implementación administrada, CFNetwork o NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

<a name="Managed" />

### <a name="managed-default"></a>Administrado (valor predeterminado)

El controlador administrado es el controlador HttpClient completamente administrado que se ha enviado con una versión anterior de Xamarin.

#### <a name="pros"></a>Profesionales de TI:

 - Tiene la característica más compatible con Microsoft .NET y las versiones anteriores de Xamarin.

#### <a name="cons"></a>Inconvenientes:

 - No está totalmente integrado con los sistemas operativos de Apple y se limita a TLS 1.0.
 - Se suele ser más lento en cosas como el cifrado de las API nativas.
 - Requiere más código administrado, creando así una aplicación de mayor tamaño distribuible.

<a name="CFNetwork" />

### <a name="cfnetwork"></a>CFNetwork

El controlador de CFNetwork se basa en nativo `CFNetwork` framework disponible en iOS 6 y versiones más reciente.

#### <a name="pros"></a>Profesionales de TI:

 - Usa las API nativas para mejorar el rendimiento y el menor tamaño de archivo ejecutable.
 - Admite los estándares más recientes como TLS 1.2.

#### <a name="cons"></a>Inconvenientes:

 - Requiere iOS 6 o posterior.
 - No está disponible en watchOS.
 - Algunas características de HttpClient/opciones no están disponibles.

<a name="NSUrlSession" />

### <a name="nsurlsession"></a>NSUrlSession

El controlador de NSURLSession se basa en nativo `NSURLSession` framework disponible en iOS 7 y versiones más reciente.

#### <a name="pros"></a>Profesionales de TI:

 - Usa las API nativas para mejorar el rendimiento y el menor tamaño de archivo ejecutable.
 - Admite los estándares más recientes, como TLS 1.2.

#### <a name="cons"></a>Inconvenientes:

 - Requiere iOS 7 o posterior.
 - Algunas características de HttpClient/opciones no están disponibles.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Establecer mediante programación el objeto httpmessagehandler que

Además de la configuración de todo el proyecto que se muestra arriba, puede también crear instancias de un `HttpClient` e inyectar lo que se desea `HttpMessageHandler` a través del constructor, como se muestra en estos fragmentos de código:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Esto hace posible usar otro `HttpMessageHandler` de lo que se declara en el **Project Options** cuadro de diálogo.

<a name="New-SSL-TLS-implementation-build-option" />
<a name="Selecting-a-SSL-TLS-implementation" />
<a name="Apple-TLS" />

## <a name="ssltls-implementation-build"></a>Compilación de la implementación de SSL/TLS

SSL (capa de sockets seguros) y su sucesor, TLS (seguridad de capa de transporte), proporcionan compatibilidad para HTTP y otras conexiones de red a través de `System.Net.Security.SslStream`. Xamarin.iOS, Xamarin.tvOS o de Xamarin.Mac `System.Net.Security.SslStream` implementación llamará implementación de SSL/TLS nativa de Apple en lugar de usar la implementación administrada proporcionada por Mono. Implementación nativa de Apple es compatible con TLS 1.2.

<a name="Mono" />
> [!WARNING]
> El **Mono/Managed** TLS proveedor está limitado a v3 SSL y TLS v1. Este proveedor TLS ha quedado desusado y ya no está disponible para aplicaciones de Xamarin.iOS. 

<a name="App-Transport-Security" />

## <a name="app-transport-security"></a>Seguridad de transporte de la aplicación

Apple _seguridad de transporte de la aplicación_ (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación. ATS garantiza que todas las comunicaciones de internet se ajustan para proteger la conexión de los procedimientos recomendados, lo que evita que la divulgación accidental de información confidencial, ya sea directamente a través de la aplicación o una biblioteca que está consumiendo.

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, tvOS 9 y OS X 10.11 (El capitán) y versiones más recientes, todas las conexiones con `NSUrlConnection`, `CFUrl` o `NSUrlSession` estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

En función de las selecciones de pila HttpClient y la implementación de SSL/TLS, debe realizar modificaciones en la aplicación para funcionar correctamente con ATS.

Para obtener más información acerca de ATS, visite nuestro [Guía de seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problemas conocidos

Esta sección explica problemas conocidos de compatibilidad con TLS en Xamarin.iOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>No se pudo cargar con el error "Valor solicitado no se encontró AppleTLS" el proyecto

Xamarin.iOS 9.8 introdujo algunos configuración nueva contenida la **.csproj** archivo para una aplicación de Xamarin.iOS. Estos cambios pueden causar problemas cuando se abre el proyecto con versiones anteriores de Xamarin.iOS. Captura de pantalla siguiente se muestra un ejemplo del mensaje de error que puede mostrarse en este escenario:

![Captura de pantalla de error al intentar cargar el proyecto, solicita heredado del valor no se encuentra](http-stack-images/tlserror-xs.png)

Este error se debe a la introducción de la `MtouchTlsProvider` si se establece en el archivo de proyecto en Xamarin.iOS 9.8. Si no es posible actualizar a Xamarin.iOS 9.8 (o superior), la solución es modificar manualmente el **.csproj** aplicación de archivos, quite el `MtouchTlsprovider` elemento y, a continuación, guarde el archivo de proyecto modificado.

El fragmento de código siguiente es un ejemplo de lo que el `MtouchTlsProvider` configuración podría tener el aspecto como dentro de un **.csproj** archivo:

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de la capa de transporte (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
