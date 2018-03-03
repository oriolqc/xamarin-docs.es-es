---
title: "Seguridad de transporte de la aplicación"
description: "Seguridad de transporte de la aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: 60858e05e222725f05eb67bd7aaa4e56d2ff3880
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="app-transport-security"></a>Seguridad de transporte de la aplicación

_Seguridad de transporte de la aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación._

En este artículo se presentan los cambios de seguridad que exige la seguridad de transporte de la aplicación en una aplicación de iOS 9 y [lo que esto significa que para los proyectos de Xamarin.iOS](#xamarinsupport), cubrirá el [opciones de configuración de ATS](#config) y explicará cómo [desactivación de ATS](#optout) ATS si es necesario. Como ATS está habilitado de forma predeterminada, las conexiones de internet no seguras generará una excepción en aplicaciones de iOS 9 (a menos que haya permitido explícitamente lo).


## <a name="about-app-transport-security"></a>Acerca de la seguridad de transporte de la aplicación

Como se mencionó anteriormente, ATS garantiza que todas las comunicaciones de internet en iOS 9 y OS X El capitán se ajustan para proteger la conexión de los procedimientos recomendados, lo que evita que la divulgación accidental de información confidencial, ya sea directamente a través de la aplicación o una biblioteca que se encuentra consumir.

Para las aplicaciones existentes, implementar la `HTTPS` del protocolo siempre que sea posible. Para las aplicaciones de Xamarin.iOS nueva, debe usar `HTTPS` exclusivamente al comunicarse con recursos de internet. Además, comunicación alto nivel de API debe cifrarse con la versión 1.2 de TLS confidencialidad directa.

Las conexiones realizadas con [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) o [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) usará ATS predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán).

## <a name="default-ats-behavior"></a>Comportamiento ATS predeterminado

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán), todas las conexiones con [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) o [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) estará sujeto a Requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

### <a name="ats-connection-requirements"></a>Requisitos de conexión de ATS

ATS aplicará los siguientes requisitos para todas las conexiones de internet:

- Todos los cifrados de conexión deben utilizar confidencialidad directa. Ver la lista de cifrados aceptadas a continuación.
- El protocolo de seguridad de capa de transporte (TLS) debe ser la versión 1.2 o posterior.
- Al menos una huella digital del SHA256 con un 2048 bits o mayor clave RSA, o bien un 256 bits o mayor clave Elliptic Curve (ECC) debe utilizarse para todos los certificados.

Una vez más, porque ATS está habilitada de forma predeterminada en iOS 9, cualquier intento de establecer una conexión que no cumple estos requisitos producirá una excepción. 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>ATS Compatible cifrados

El siguiente tipo de cifrado de confidencialidad directa se aceptan por ATS protege las comunicaciones de internet:

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

Para obtener más información sobre cómo trabajar con clases de comunicación de internet de iOS, vea de Apple [NSURLConnection Class Reference](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755), [CFURL referencia](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) o [NSURLSession referencia de clase ](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>Admitir ATS en Xamarin.iOS

Como ATS está habilitado de forma predeterminada en iOS 9 y OS X El capitán, si la aplicación de Xamarin.iOS o cualquier biblioteca o el servicio que está usando realiza la conexión a internet, deberá realizar alguna acción o las conexiones se producirán una excepción.

Para una aplicación existente, Apple sugiere que admite la `HTTPS` protocolo tan pronto como sea posible. Si bien no es posible porque está conectando un 3rd party servicio web que no es compatible con `HTTPS` o si se admite `HTTPS` no sería práctico, puede optar por no ATS. Consulte la [Opting horizontal de ATS](#Opting-Out-of-ATS) sección para obtener más detalles.

Para una nueva aplicación de Xamarin.iOS, debe usar `HTTPS` exclusivamente al comunicarse con recursos de internet. Una vez más, puede haber situaciones (como el uso de un servicio web de entidad 3rd) donde esto no es posible y deberá desactivación de ATS.

Además, ATS exige la comunicación con la API alto nivel con la versión 1.2 de TLS con confidencialidad directa. Consulte la [requisitos de conexión de ATS](#ATS-Connection-Requirements) y [ATS Compatible cifrados](#ATS-Compatible-Ciphers) secciones anteriores para obtener más detalles.

Aunque es posible que no esté familiarizado con TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) es el sucesor de SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) y proporciona una colección de los protocolos criptográficos para exigir la seguridad en conexiones de red.

El nivel TLS se controla mediante el servicio web que va a consumir y, por tanto, está fuera de control de la aplicación. Tanto el `HttpClient` y `ModernHttpClient` automáticamente se debe usar el máximo nivel de cifrado TLS admitido por el servidor.

Función en el servidor que está hablando con (especialmente si es un servicio de entidad 3rd), tendrá que deshabilitar confidencialidad directa o seleccione un nivel inferior de TLS. Consulte la [configurar opciones de ATS](#Configuring-ATS-Options) sección para obtener más detalles.

> [!IMPORTANT]
> **Nota:** seguridad de transporte de la aplicación no se aplica a aplicaciones de Xamarin con **implementaciones administradas HTTPClient**. Se aplica a las conexiones que usan CFNetwork **HTTPClient implementaciones** o **NSURLSession HTTPClient implementaciones** solo.

### <a name="setting-the-httpclient-implementation"></a>Configuración de la implementación de HTTPClient

Para establecer la implementación HTTPClient utilizada por una aplicación de iOS, haga doble clic en el **proyecto** en el **el Explorador de soluciones** para abrir el **Project Options**. Vaya a **iOS compilación** y seleccione el tipo de cliente deseado en el **HttpClient implementación** lista desplegable:

![](ats-images/client01.png "Establecer las opciones de compilación de iOS")


#### <a name="managed-handler"></a>Controlador administrado

El controlador administrado es el controlador HttpClient completamente administrado que se ha enviado con versiones anteriores de Xamarin.iOS y es el controlador predeterminado.

Profesionales de TI:

- Es el más compatible con Microsoft .NET y una versión anterior de Xamarin.

Inconvenientes:

- No se integra totalmente con iOS (por ejemplo, se limita a TLS 1.0).
- Es normalmente mucho más lento que las API nativas.
- Se requiere más código administrado y crea aplicaciones más grandes.

#### <a name="cfnetwork-handler"></a>Controlador CFNetwork

El controlador CFNetwork según se basa en nativo `CFNetwork` framework.

Profesionales de TI:

- Utiliza la API nativa para mejorar el rendimiento y tamaños de archivo ejecutables más pequeños.
- Agrega compatibilidad con los estándares más recientes, como TLS 1.2.

Inconvenientes:

- Requiere iOS 6 o posterior.
- No está disponible de watchOS.
- Algunas características de HttpClient y opciones no están disponibles.

#### <a name="nsurlsession-handler"></a>Controlador NSUrlSession

El controlador NSUrlSession según se basa en nativo `NSUrlSession` API.

Profesionales de TI:

- Utiliza la API nativa para mejorar el rendimiento y tamaños de archivo ejecutables más pequeños.
- Agrega compatibilidad con los estándares más recientes, como TLS 1.2.

Inconvenientes:

- Requiere iOS 7 o posterior.
- Algunas características de HttpClient y opciones no están disponibles. 

## <a name="diagnosing-ats-issues"></a>Diagnosticar problemas ATS

Al intentar conectarse a internet, ya sea directamente o de una vista web de iOS 9, podría aparecer un error en el formulario:

> Seguridad de transporte de la aplicación ha bloqueado una carga de recursos de texto no cifrado HTTP (http://www.-the-blocked-domain.com) ya que es inseguro. Excepciones temporales pueden configurarse a través de archivo Info.plist de la aplicación.

En iOS9, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación. Además, ATS requiere comunicación utilizando la `HTTPS` protocolo y comunicación de alto nivel API cifrarse con la versión 1.2 de TLS confidencialidad directa.

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán), todas las conexiones con `NSURLConnection`, `CFURL` o `NSURLSession` estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Apple también proporciona la [aplicación de ejemplo de TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) que se pueden compilar (u opcionalmente transcodifica Xamarin y C#) y usar para diagnosticar problemas ATS/TLS. Vea la [Opting horizontal de ATS](#Opting-Out_of_ATS) sección para obtener información sobre cómo resolver este problema.


<a name="config" />

## <a name="configuring-ats-options"></a>Configurar las opciones de ATS

Puede configurar algunas de las características de ATS estableciendo valores de claves específicos de la aplicación **Info.plist** archivo. Las claves siguientes están disponibles para controlar ATS (_con sangría para mostrar cómo están anidadas_):

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
- **NSAllowsArbitraryLoads** (`Boolean`): si `YES` ATS se deshabilitará para cualquier dominio **no** enumerados en `NSExceptionDomains`. En los dominios enumerados, se usará la configuración de seguridad especificada.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`): si `YES` permitirá páginas web cargar correctamente durante la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación.
- **NSExceptionDomains** (`Dictionary`)-una colección de dominios que y la configuración de seguridad que deben usar ATS para un dominio determinado.
- **< Domain-name-for-exception-as-string >** (`Dictionary`)-una colección de excepciones para un dominio determinado (p. ej. `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`): la versión mínima de TLS como `TLSv1.0`, `TLSv1.1` o `TLSv1.2` (que es el valor predeterminado).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`): si `NO` el dominio no tiene que usar un cifrado con la seguridad al día. El valor predeterminado es `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`): si `NO` (predeterminada) todas las comunicaciones con este dominio deben estar en el `HTTPS` protocolo.
- **NSRequiresCertificateTransparency** (`Boolean`): si `YES` Secure Sockets Layer (SSL del dominio) debe incluir los datos de transparencia válido. El valor predeterminado es `NO`.
- **NSIncludesSubdomains** (`Boolean`): si `YES` esta configuración invalida todos los subdominios de este dominio. El valor predeterminado es `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-versión de la TLS usa cuando el dominio es un servicio de entidad 3rd fuera de control del desarrollador.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`): si `YES` un dominio parte 3ª requiere confidencialidad directa.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`): si `YES` el ATS le permitirá la comunicación no segura con 3rd dominios de entidad.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Fuera de la aceptación de ATS

Mientras que Apple alta sugiere utilizando la `HTTPS` información basada en protocolo y una comunicación segura a internet, puede haber ocasiones en que esto no siempre es posible. Por ejemplo, si se está comunicando con un servicio web de entidad 3rd o entregada anuncios en la aplicación de internet.

Si la aplicación de Xamarin.iOS debe realizar una solicitud a un dominio no seguro, los siguientes cambios a la aplicación **Info.plist** archivo deshabilitará los valores predeterminados de seguridad que exige la ATS para un dominio determinado:

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

Dentro de Visual Studio para Mac, haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones**, cambie a la **origen** ver y agregar las claves anteriores:

[ ![](ats-images/ats01.png "La vista del origen del archivo Info.plist")](ats-images/ats01.png)


Si la aplicación debe cargar y mostrar el contenido de web de sitios no seguras, agregue lo siguiente a la aplicación **Info.plist** archivo para permitir que las páginas web cargar correctamente durante la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Si lo desea, puede realizar los siguientes cambios a la aplicación **Info.plist** archivo para deshabilitar completamente ATS para todos los dominios y comunicación de internet:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

Dentro de Visual Studio para Mac, haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones**, cambie a la **origen** ver y agregar las claves anteriores:

[ ![](ats-images/ats02.png "La vista del origen del archivo Info.plist")](ats-images/ats02.png)

> [!IMPORTANT]
> **Nota:** si la aplicación requiere una conexión a un sitio Web inseguro, debería **siempre** escriba el dominio como una excepción mediante `NSExceptionDomains` en lugar de desactivar que ATS totalmente mediante `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` sólo debe utilizarse en situaciones de emergencias extremas.




Una vez más, deshabilitar ATS debe _sólo_ usarse como último recurso, si cambia a proteger las conexiones no está disponible o poco práctico.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se introdujo la seguridad de transporte de aplicación (ATS) y se describe la forma en que refuerza las comunicaciones seguras con internet. En primer lugar, se tratan los cambios que ATS requiere para una aplicación de Xamarin.iOS que se ejecuta en iOS 9. A continuación, se tratan opciones y características de control ATS. Por último, hemos aprendido optar ATS en la aplicación Xamarin.iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
