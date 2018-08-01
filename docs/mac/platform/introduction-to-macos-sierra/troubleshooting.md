---
title: Xamarin.Mac - macOS Sierra solucionar problemas
description: Este documento proporciona varias sugerencias de solución de problemas para trabajar con macOS Sierra en aplicaciones de Xamarin.Mac. Sugerencias se refieren a la tienda de aplicaciones Mac, Apple Pay, compatibilidad binaria, CFNetwork, CloudKit y mucho más.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/22/2016
ms.openlocfilehash: 5b2571d9562fd137257e2dd0ea2ada8f071bab92
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792334"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin.Mac - macOS Sierra solucionar problemas

_Este artículo proporciona varias sugerencias de solución de problemas para trabajar con macOS Sierra en aplicaciones de Xamarin.Mac._

Las siguientes secciones enumeran algunos problemas conocidos que pueden producirse al utilizar macOS Sierra con Xamarin.mac y la solución a estos problemas:

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilidad binaria](#Binary-Compatibility)
- [Protocolo de HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagen del núcleo](#CoreImage)
- [Notificaciones](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>Tienda de aplicaciones

Problemas conocidos:

- Al probar las compras de la aplicación en el entorno de espacio aislado, el cuadro de diálogo de autenticación puede aparecer dos veces.
- Al probar las compras de la aplicación con contenido hospedado en el entorno de espacio aislado, aparecerá el cuadro de diálogo de contraseña cada vez que la aplicación se pone en primer plano hasta que se complete la descarga de contenido.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Si se introduce una expiración incorrecto fecha o seguridad de código (CW) al agregar una nueva tarjeta de pago a pagar de Apple, se terminará el proceso de aprovisionamiento de tarjeta.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidad binaria

Problemas conocidos:

- Al llamar a `NSObject.ValueForKey` le un `null` clave se producirá una excepción.
- Ambos `NSURLSession` y NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http://' las direcciones URL.
- Aplicaciones pueden dejar de responder si se modifica la geometría del supervista en la vista la `ViewWillLayoutSubviews` o `LayoutSubviews` métodos.
- Para todas las conexiones SSL/TLS, el cifrado simétrico de RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no es compatible con SSLv3 y se recomienda que la aplicación deje de uso de la criptografía de SHA-1 y 3DES tan pronto como sea posible.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocolo de HTTP CFNetwork

El `HTTPBodyStream` propiedad de la `NSMutableURLRequest` clase debe estar establecida en un flujo sin abrir desde `NSURLConnection` y `NSURLSession` ahora estrictamente imponer este requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Las operaciones de larga ejecución devolverá un _"No tiene permiso para guardar el archivo."_ Error.

<a name="CoreImage" />

## <a name="core-image"></a>Imagen del núcleo

El `CIImageProcessor` API ahora es compatible con un número arbitrario de imagen de entrada. `CIImageProcessor` API que se incluía en macOS beta Sierra 1 que se va a quitar.

<a name="Notifications" />

## <a name="notifications"></a>Notificaciones

Cuando se trabaja con las extensiones de contenido de notificación, controladores de la vista no se lanzan correctamente y puede dar lugar a un bloqueo cuando se alcanzan los límites de memoria de la extensión.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Después de una operación de entrega, el `UserInfo` propiedad de un `NSUserActivity` el objeto puede estar vacío. Llamar explícitamente a `BecomeCurrent` `NSUserActivity` objeto actual para solucionar este problema.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation requiere una ubicación segura (`https://`) la dirección URL para trabajar en iOS 10 y macOS Sierra para evitar el uso malintencionado de los datos de ubicación.







## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](https://developer.xamarin.com/samples/mac/)
- [' S new en OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
