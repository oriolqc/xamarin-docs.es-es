---
title: HttpClient y Selector de implementación de SSL/TLS para iOS y macOS
description: La pila HttpClient y SSL/TLS selector implementación determina la implementación de HttpClient y SSL/TLS que se usará en la aplicación de Xamarin iOS, tvOS o macOS.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: fd48c7148aadd8d156544113e2d719295294bf40
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2018
ms.locfileid: "40250976"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>Selector de implementación de HttpClient y SSL/TLS para iOS y macOS

El **Selector de implementación de HttpClient** para Xamarin.iOS, Xamarin.tvOS y Xamarin.Mac controla a qué `HttpClient` implementación va a utilizar. Puede cambiar a una implementación que usa los transportes nativos de iOS, tvOS o macOS (`NSUrlSession` o `CFNetwork`, según el sistema operativo). La ventaja es binarios TLS 1.2 el soporte técnico, más pequeños y descargas con más rapidez; la desventaja es que requiere el bucle de eventos se esté ejecutando para que operaciones asincrónicas que se ejecutará.

Deben hacer referencia a los proyectos de la **System.Net.Http** ensamblado.

> [!WARNING]
> **Abril de 2018** : debido a una mayor seguridad principales de los requisitos, incluido el cumplimiento del estándar PCI, los proveedores de nube y servidores web que se esperan para detener la compatibilidad con TLS versiones anteriores a 1.2.  Proyectos de Xamarin creados en versiones anteriores de Visual Studio de forma predeterminada usar versiones anteriores de TLS.
>
> Para asegurarse de que las aplicaciones seguirán funcionando con estos servidores y servicios, **debe actualizar los proyectos de Xamarin con el `NSUrlSession` , a continuación, establecer se muestra a continuación, volver a compilar y volver a implementar sus aplicaciones** a los usuarios.

### <a name="selecting-an-httpclient-stack"></a>Seleccionar una pila HttpClient

Para ajustar el `HttpClient` usando la aplicación:

1. Haga doble clic en el **nombre del proyecto** en el **el Explorador de soluciones** para abrir las opciones del proyecto.
2. Cambie a la **compilar** configuración del proyecto (por ejemplo, **compilación de iOS** para una aplicación de Xamarin.iOS).
3. Desde el **implementación de HttpClient** lista desplegable, seleccione el `HttpClient` escriba como uno de los siguientes: **NSUrlSession** (recomendado), **CFNetwork**, o  **Managed**.

[![Elija la implementación de HttpClient en administrado, CFNetwork o NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Compatibilidad con TLS 1.2 el `NSUrlSession` es la opción recomendada.

### <a name="nsurlsession"></a>NSUrlSession

El `NSURLSession`-en función de controlador se basa en nativo `NSURLSession` framework disponible en iOS 7 y versiones más reciente. 
**Esta es la configuración recomendada.**

#### <a name="pros"></a>Profesionales

- Usa las API nativas para mejorar el rendimiento y tamaño del archivo ejecutable más pequeño.
- Compatibilidad con los estándares más recientes, como TLS 1.2.

#### <a name="cons"></a>Desventajas

- Requiere iOS 7 o posterior.
- Algunos `HttpClient` características/opciones no están disponibles.

### <a name="cfnetwork"></a>CFNetwork

El `CFNetwork`-en función de controlador se basa en nativo `CFNetwork` framework disponible en iOS 6 y versiones más reciente.

#### <a name="pros"></a>Profesionales

- Usa las API nativas para mejorar el rendimiento y tamaño del archivo ejecutable más pequeño.
- Compatibilidad con los estándares más recientes, como TLS 1.2.

#### <a name="cons"></a>Desventajas

- Requiere iOS 6 o posterior.
- No está disponible en watchOS.
- Algunas características de HttpClient/opciones no están disponibles.

### <a name="managed"></a>Administrado

El controlador administrado es el controlador de HttpClient totalmente administrado que se ha enviado con la versión anterior de Xamarin.

#### <a name="pros"></a>Profesionales

- Tiene la característica más compatible establecido con Microsoft .NET y las versiones anteriores de Xamarin.

#### <a name="cons"></a>Desventajas

- No está totalmente integrado con los sistemas operativos de Apple y se limita a TLS 1.0. No puede ser capaz de conectarse para proteger los servidores web o servicios en la nube en el futuro.
- Se suele ser más lento en cosas como el cifrado de las API nativas.
- Requiere más código administrado, creando así una aplicación de mayor tamaño distribuible.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Establecer mediante programación el objeto httpmessagehandler que

Además de la configuración de todo el proyecto que se muestra arriba, puede también crear instancias de un `HttpClient` e inyectar deseado `HttpMessageHandler` a través del constructor, como se muestra en estos fragmentos de código:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Esto hace posible usar otra `HttpMessageHandler` de lo que se declara en el **opciones de proyecto** cuadro de diálogo.

## <a name="ssltls-implementation"></a>Implementación de SSL/TLS

SSL (capa de sockets seguros) y su sucesor, TLS (Transport Layer Security), proporcionan compatibilidad para HTTP y otras conexiones de red a través de `System.Net.Security.SslStream`. Xamarin.iOS, Xamarin.tvOS o de Xamarin.Mac `System.Net.Security.SslStream` implementación llamará la implementación de SSL/TLS nativa de Apple en lugar de usar la implementación administrada proporcionada por Mono. Implementación nativa de Apple es compatible con TLS 1.2.

> [!WARNING]
> La próxima versión de Xamarin.Mac 4.8 solo será compatible con macOS 10.9 o versiones posteriores.
> Las versiones anteriores de Xamarin.Mac admiten macOS 10.7 o superior, pero estas versiones anteriores de macOS carecen de infraestructura TLS suficiente para admitir TLS 1.2. Para macOS 10.7 o Mac OS 10.8 de destino, use Xamarin.Mac 4.6 o una versión anterior.

## <a name="app-transport-security"></a>Seguridad de transporte de la aplicación

Apple _App Transport Security_ (ATS) aplica las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación. ATS garantiza que todas las comunicaciones de internet se ajustan para proteger la conexión de los procedimientos recomendados, impidiendo así la revelación accidental de información confidencial, ya sea directamente a través de la aplicación o una biblioteca que lo consume.

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, tvOS 9 y OS X 10.11 (El capitán) y versiones más recientes, todas las conexiones mediante `NSUrlConnection`, `CFUrl` o `NSUrlSession` estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Según las selecciones de pila HttpClient e implementación de SSL/TLS, es posible que deba realizar modificaciones en la aplicación funcione correctamente con ATS.

Para obtener más información acerca de ATS, consulte nuestra [Guía de seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problemas conocidos

Esta sección explica problemas conocidos de compatibilidad con TLS en Xamarin.iOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>Proyecto no se pudo cargar el error "No se encontró AppleTLS de valor solicitado"

Xamarin.iOS 9.8 introdujo algunas opciones nuevos contenidos el **.csproj** archivo para una aplicación de Xamarin.iOS. Estos cambios pueden provocar problemas cuando se abre el proyecto con versiones anteriores de Xamarin.iOS. Captura de pantalla siguiente es un ejemplo de mensaje de error que puede aparecer en este escenario:

![Captura de pantalla de error al intentar cargar el proyecto, solicita heredado del valor no encontrado](http-stack-images/tlserror-xs.png)

Este error se produce por la introducción de la `MtouchTlsProvider` si se establece en el archivo de proyecto en 9.8 de Xamarin.iOS. Si no es posible actualizar a la 9.8 de Xamarin.iOS (o posterior), la solución es modificar manualmente el **.csproj** aplicación de archivos, quite el `MtouchTlsprovider` elemento y, a continuación, guarde el archivo de proyecto modificado.

El fragmento de código siguiente es un ejemplo de lo que el `MtouchTlsProvider` configuración puede tener un aspecto como dentro de un **.csproj** archivo:

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de la capa de transporte (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
