---
title: Xamarin.Mac - macOS Sierra solución de problemas
description: Este documento proporciona varias sugerencias de solución de problemas para trabajar con macOS Sierra en aplicaciones de Xamarin.Mac. Sugerencias relacionadas con el Store de la aplicación de Mac, Apple Pay, compatibilidad binaria, CFNetwork, CloudKit y mucho más.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/22/2016
ms.openlocfilehash: 1b379bef98e498df4c58ba7209aa46b0b2542fe1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108841"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin.Mac - macOS Sierra solución de problemas

_En este artículo proporciona varias sugerencias de solución de problemas para trabajar con macOS Sierra en aplicaciones de Xamarin.Mac._

Las siguientes secciones enumeran algunos problemas conocidos que pueden producirse al usar macOS Sierra con Xamarin.mac y la solución a estos problemas:

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilidad binaria](#Binary-Compatibility)
- [Protocolo HTTP de CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagen de Core](#CoreImage)
- [Notificaciones](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemas conocidos:

- Al probar las compras de la aplicación en el entorno de recinto de seguridad, el cuadro de diálogo de autenticación puede aparecer dos veces.
- Al probar las compras de la aplicación con contenido hospedado en el entorno de recinto de seguridad, aparecerá el cuadro de diálogo de contraseña cada vez que la aplicación se pone en primer plano hasta que se complete la descarga de contenido.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Si se introduce una expiración incorrectos fecha o seguridad de código (CW) al agregar una nueva tarjeta de pago a Apple Pay, se terminará el proceso de aprovisionamiento de tarjeta.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidad binaria

Problemas conocidos:

- Una llamada a `NSObject.ValueForKey` le un `null` clave producirá una excepción.
- Ambos `NSURLSession` y la NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http://' las direcciones URL.
- Las aplicaciones pueden dejar de responder si se modifica la geometría de la supervista en uno el `ViewWillLayoutSubviews` o `LayoutSubviews` métodos.
- Para todas las conexiones SSL/TLS, el cifrado simétrico de RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que la aplicación dejen de usar SHA-1 y 3DES criptografía tan pronto como sea posible.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocolo HTTP de CFNetwork

El `HTTPBodyStream` propiedad de la `NSMutableURLRequest` clase debe establecerse en un flujo sin abrir desde `NSURLConnection` y `NSURLSession` ahora estrictamente aplicar este requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Las operaciones de larga ejecución devolverá una _"No tiene permiso para guardar el archivo"._ Error.

<a name="CoreImage" />

## <a name="core-image"></a>Imagen de Core

El `CIImageProcessor` API ahora admite un número arbitrario de imagen de entrada. `CIImageProcessor` Se quitarán las API que se incluyó en la versión beta 1 de Sierra de macOS.

<a name="Notifications" />

## <a name="notifications"></a>Notificaciones

Cuando se trabaja con las extensiones de contenido de notificación, los controladores de vista no se libera correctamente y puede dar lugar a un bloqueo cuando se alcanzan los límites de memoria de extensión.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Después de una operación de entrega, el `UserInfo` propiedad de un `NSUserActivity` el objeto puede estar vacío. Llamar explícitamente a `BecomeCurrent` `NSUserActivity` objeto como solución alternativa actual.

<a name="Safari" />

## <a name="safari"></a>Safari

Requiere un seguro WebGeolocation (`https://`) dirección URL para que funcione en iOS 10 y macOS Sierra para evitar el uso malintencionado de los datos de ubicación.







## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](https://developer.xamarin.com/samples/mac/)
- [Novedades en OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
