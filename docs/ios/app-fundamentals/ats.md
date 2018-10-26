---
title: Seguridad de transporte de la aplicación de Xamarin.iOS
description: Seguridad de transporte de aplicaciones (ATS) aplica las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación.
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 0645b326576a68c97479bc5b59aabaa104f87ae2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114267"
---
# <a name="app-transport-security-in-xamarinios"></a>Seguridad de transporte de la aplicación de Xamarin.iOS

_Seguridad de transporte de aplicaciones (ATS) aplica las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación._

Este artículo presentan los cambios de seguridad que exige la seguridad de transporte de la aplicación en una aplicación de iOS 9 y [implicaciones para los proyectos de Xamarin.iOS](#xamarinsupport), cubrirá el [las opciones de configuración de ATS](#config) y tratará cómo [participar de ATS](#optout) ATS si es necesario. Como ATS está habilitado de forma predeterminada, las conexiones no seguras de internet producirán una excepción en aplicaciones de iOS 9 (a menos que haya permitido explícitamente que).


## <a name="about-app-transport-security"></a>Acerca de la seguridad de transporte de la aplicación

Como se indicó anteriormente, ATS garantiza que todas las comunicaciones de internet en iOS 9 y OS X El Capitan se ajustan para proteger la conexión de los procedimientos recomendados, impidiendo así la revelación accidental de información confidencial, ya sea directamente a través de la aplicación o una biblioteca que está consumir.

Para las aplicaciones existentes, implementar el `HTTPS` protocolo siempre que sea posible. Para nuevas aplicaciones de Xamarin.iOS, debe usar `HTTPS` exclusivamente al comunicarse con los recursos de internet. Además, la comunicación de alto nivel API debe cifrarse con la versión 1.2 de TLS con confidencialidad directa.

Las conexiones realizadas con [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) o [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) usará ATS de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán).

## <a name="default-ats-behavior"></a>Comportamiento predeterminado de ATS

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán), todas las conexiones mediante [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) o [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) estará sujeto a Requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

### <a name="ats-connection-requirements"></a>Requisitos de ATS conexión

ATS hará cumplir los siguientes requisitos para todas las conexiones de internet:

- Todos los cifrados de conexión deben utilizar confidencialidad directa. Consulte la lista de cifrados aceptados a continuación.
- El protocolo de capa de transporte (TLS) debe ser la versión 1.2 o posterior.
- Para todos los certificados se debe usar al menos una huella digital de SHA256 con una 2048 bits o mayor clave RSA, o bien un 256 bits o una clave mayor Elliptic Curve (ECC).

De nuevo, ya que ATS está habilitada de forma predeterminada en iOS 9, cualquier intento de establecer una conexión que no cumple estos requisitos producirá una excepción. 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>Cifrados de ATS Compatible

El siguiente tipo de cifrado de confidencialidad directa acepta ATS protege las comunicaciones de internet:

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

Para obtener más información sobre cómo trabajar con clases de comunicación de internet de iOS, consulte Apple [referencia de clase de la NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755), [CFURL referencia](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) o [NSURLSession referencia de clase ](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>Compatibilidad de ATS en Xamarin.iOS

Como ATS está habilitado de forma predeterminada en iOS 9 y OS X El capitán, si la aplicación de Xamarin.iOS con cualquier biblioteca o se está usando el servicio realiza la conexión a internet, deberá realizar alguna acción o las conexiones se producirán una excepción producida.

Para una aplicación existente, Apple sugiere que admite la `HTTPS` protocolo tan pronto como sea posible. Si bien no es posible porque está conectando a 3rd party servicio web que no es compatible con `HTTPS` o si se admite `HTTPS` no sería práctico, puede participar de ATS. Consulte la [Opting horizontal de ATS](#optout) sección para obtener más detalles.

Para una nueva aplicación de Xamarin.iOS, debe usar `HTTPS` exclusivamente al comunicarse con los recursos de internet. De nuevo, puede haber situaciones (por ejemplo, mediante un servicio web de entidad 3rd) donde esto no es posible y deberá participar de ATS.

Además, ATS establece comunicación alto nivel de API a cifrarse con la versión 1.2 de TLS con confidencialidad directa. Consulte la [requisitos de conexión de ATS](#ATS-Connection-Requirements) y [cifrados compatibles ATS](#ATS-Compatible-Ciphers) secciones anteriores para obtener más detalles.

Aunque es posible que no esté familiarizado con TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) es el sucesor de SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) y proporciona una colección de protocolos criptográficos para reforzar la seguridad a través de conexiones de red.

El nivel TLS se controla mediante el servicio web que va a consumir y, por tanto, está fuera del control de la aplicación. Tanto el `HttpClient` y `ModernHttpClient` automáticamente se debe usar el máximo nivel de cifrado TLS admitido por el servidor.

Según el servidor que se comunica con (especialmente si es un servicio de terceros 3rd), es posible que deba deshabilitar confidencialidad directa o seleccione un nivel más bajo de TLS. Consulte la [configurar las opciones de ATS](#Configuring-ATS-Options) sección para obtener más detalles.

> [!IMPORTANT]
> Seguridad de transporte de la aplicación no es aplicable a las aplicaciones de Xamarin con **implementaciones administradas HTTPClient**. Se aplica a las conexiones que usan CFNetwork **HTTPClient implementaciones** o **NSURLSession HTTPClient implementaciones** solo.

### <a name="setting-the-httpclient-implementation"></a>Configuración de la implementación de HTTPClient

Para establecer la implementación de HTTPClient usados por una aplicación de iOS, haga doble clic en el **proyecto** en el **el Explorador de soluciones** para abrir el **opciones de proyecto**. Vaya a **compilación de iOS** y seleccione el tipo de cliente deseado en el **implementación de HttpClient** lista desplegable:

![](ats-images/client01.png "Establecer las opciones de compilación de iOS")


#### <a name="managed-handler"></a>Controlador administrado

El controlador administrado es el controlador de HttpClient totalmente administrado que se ha enviado con las versiones anteriores de Xamarin.iOS y es el controlador predeterminado.

Ventajas:

- Es más compatible con Microsoft .NET y una versión anterior de Xamarin.

Inconvenientes:

- No está totalmente integrado con iOS (por ejemplo, se limita a TLS 1.0).
- Es normalmente más lento que las API nativas.
- Se requiere más código administrado y crea aplicaciones más grandes.

#### <a name="cfnetwork-handler"></a>Controlador CFNetwork

El controlador CFNetwork según se basa en nativo `CFNetwork` framework.

Ventajas:

- Utiliza la API nativa para mejorar el rendimiento y los tamaños más pequeños de ejecutable.
- Agrega compatibilidad con los estándares más recientes, como TLS 1.2.

Inconvenientes:

- Requiere iOS 6 o posterior.
- No está disponible de watchOS.
- Algunas características de HttpClient y las opciones no están disponibles.

#### <a name="nsurlsession-handler"></a>Controlador NSUrlSession

El controlador NSUrlSession según se basa en nativo `NSUrlSession` API.

Ventajas:

- Utiliza la API nativa para mejorar el rendimiento y los tamaños más pequeños de ejecutable.
- Agrega compatibilidad con los estándares más recientes, como TLS 1.2.

Inconvenientes:

- Requiere iOS 7 o posterior.
- Algunas características de HttpClient y las opciones no están disponibles. 

## <a name="diagnosing-ats-issues"></a>Diagnóstico de problemas ATS

Al intentar conectarse a internet, ya sea directamente o desde una vista web en iOS 9, podría obtener un error en el formulario:

> Seguridad de transporte de la aplicación ha bloqueado un texto no cifrado HTTP (http://www.-the-blocked-domain.com) carga de los recursos, ya que es insegura. Excepciones temporales se pueden configurar mediante el archivo Info.plist de la aplicación.

En IOS 9, App Transport Security (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación. Además, ATS requiere comunicación utilizando la `HTTPS` protocolo y comunicación de alto nivel API a cifrarse con la versión 1.2 de TLS con confidencialidad directa.

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán), todas las conexiones mediante `NSURLConnection`, `CFURL` o `NSURLSession` estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Apple también proporciona la [aplicación de ejemplo TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) que se puede compilar (u opcionalmente transcodificados a Xamarin y C#) y usar para diagnosticar problemas ATS/TLS. Consulte la [Opting horizontal de ATS](#optout) sección para obtener información sobre cómo resolver este problema.


<a name="config" />

## <a name="configuring-ats-options"></a>Configurar las opciones de ATS

Puede configurar algunas de las características de ATS estableciendo los valores de claves específicas de la aplicación **Info.plist** archivo. Las claves siguientes están disponibles para controlar ATS (_con sangría para mostrar cómo están anidadas_):

```csharp
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

Cada clave tiene el tipo y el significado siguiente:

- **NSAppTransportSecurity** (`Dictionary`): contiene todas las claves de configuración y los valores de ATS.
- **NSAllowsArbitraryLoads** (`Boolean`): si `YES` ATS se deshabilitará para cualquier dominio **no** enumerados en `NSExceptionDomains`. Para los dominios enumerados, se usará la configuración de seguridad especificada.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`): si `YES` permitirá que las páginas web cargar correctamente mientras la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación.
- **NSExceptionDomains** (`Dictionary`): una colección de dominios que y la configuración de seguridad que ATS debe usar para un dominio determinado.
- **< Domain-name-for-exception-as-string >** (`Dictionary`): una colección de excepciones para un dominio determinado (p ej. `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`): la versión TLS mínima como `TLSv1.0`, `TLSv1.1` o `TLSv1.2` (que es el valor predeterminado).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`): si `NO` el dominio no tiene que usar un cifrado con la seguridad hacia delante. El valor predeterminado es `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`): si `NO` (predeterminado) todas las comunicaciones con este dominio deben estar en el `HTTPS` protocolo.
- **NSRequiresCertificateTransparency** (`Boolean`): si `YES` capa de Sockets seguros (SSL del dominio de la) debe incluir datos transparencia válido. El valor predeterminado es `NO`.
- **NSIncludesSubdomains** (`Boolean`): si `YES` esta configuración invalida todos los subdominios de este dominio. El valor predeterminado es `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`): versión de TLS usa cuando el dominio es un servicio de terceros 3rd fuera del control del desarrollador.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`): si `YES` un dominio parte 3ª requiere confidencialidad directa.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`): si `YES` las tecnologías de asistencia permitirá la comunicación no segura con 3 dominios de terceros.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Salida de participación de ATS

Mientras que Apple alta sugiere el uso de la `HTTPS` información basada en protocolo y una comunicación segura a internet, puede haber ocasiones en que esto no siempre es posible. Por ejemplo, si se está comunicando con un servicio web de entidad 3rd o mediante internet entregado anuncios en la aplicación.

Si la aplicación Xamarin.iOS debe realizar una solicitud a un dominio no seguro, los siguientes cambios a la aplicación **Info.plist** archivo deshabilitará los valores predeterminados de seguridad que ATS establece para un dominio determinado:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

Dentro de Visual Studio para Mac, haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones**, cambie a la **origen** ver y agregar las claves anteriores:

[![](ats-images/ats01.png "La vista del origen del archivo Info.plist")](ats-images/ats01.png#lightbox)


Si la aplicación debe cargar y mostrar el contenido de web de sitios no seguros, agregue lo siguiente a la aplicación **Info.plist** archivo para permitir que las páginas web cargar correctamente mientras la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Si lo desea, puede realizar los siguientes cambios a la aplicación **Info.plist** archivo completamente deshabilitar ATS para todos los dominios y comunicación de internet:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

Dentro de Visual Studio para Mac, haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones**, cambie a la **origen** ver y agregar las claves anteriores:

[![](ats-images/ats02.png "La vista del origen del archivo Info.plist")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> Si la aplicación requiere una conexión a un sitio Web no segura, debe **siempre** escriba el dominio como una excepción mediante `NSExceptionDomains` en lugar de desactivar que ATS completamente utilizando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` solo debe usarse en situaciones de emergencias extremas.




Nuevamente, deshabilitar ATS debe _sólo_ usarse como último recurso, si cambia a las conexiones seguras no está disponible o no sea práctico.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se introdujo App Transport Security (ATS) y se describe la manera en que aplica las comunicaciones seguras con internet. En primer lugar, hemos tratado los cambios que ATS se requiere para una aplicación de Xamarin.iOS que se ejecutan en iOS 9. A continuación, hemos tratado las opciones y controlar las características ATS. Por último, hemos tratado la exclusión de ATS en la aplicación de Xamarin.iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
