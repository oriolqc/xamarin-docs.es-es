---
title: Solución de problemas de tvOS 10 aplicaciones compiladas con Xamarin
description: Este artículo proporciona varias sugerencias de solución de problemas para trabajar con tvOS 10 en aplicaciones de Xamarin. Describe los problemas relacionados con la tienda de aplicaciones, la compatibilidad binaria, CFNetwork HttpProtocol, CloudKit, imagen Core, NSUserActivity y UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 4332caca2804da52bb565fe382932af691c39dab
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788815"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Solución de problemas de tvOS 10 aplicaciones compiladas con Xamarin

Las siguientes secciones enumeran algunos problemas conocidos que pueden producirse al utilizar tvOS 10 con Xamarin y la solución a estos problemas:

- [App Store](#App-Store)
- [Compatibilidad binaria](#Binary-Compatibility)
- [Protocolo de HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagen del núcleo](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>Tienda de aplicaciones

Problemas conocidos:

 - Al probar las compras de la aplicación en el entorno de espacio aislado, el cuadro de diálogo de autenticación puede aparecer dos veces.
 - Al probar las compras de la aplicación con contenido hospedado en el entorno de espacio aislado, aparecerá el cuadro de diálogo de contraseña cada vez que la aplicación se pone en primer plano hasta que se complete la descarga de contenido.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidad binaria

Problemas conocidos:

 - Al llamar a `NSObject.ValueForKey` le un `null` clave se producirá una excepción.
 - Referencia a una fuente con nombre cuando se llama a `UIFont.WithName` causará un bloqueo.
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

El `CIImageProcessor` API ahora es compatible con un número arbitrario de imagen de entrada. `CIImageProcessor` API que se incluía en tvOS 10 beta 1 que se va a quitar.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Después de una operación de entrega, el `UserInfo` propiedad de un `NSUserActivity` el objeto puede estar vacío. Llamar explícitamente a `BecomeCurrent` NSUserActivity' objeto actual para solucionar este problema.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemas conocidos:

 - Cambia a la apariencia del fondo `UINavigationBar`, `UITabBar` o `UIToolBar` puede dar lugar a un paso de diseño para resolver el nuevo aspecto. Al intentar modificar estos aspectos visuales dentro de un `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` o `DidUpdateSubviews` eventos pueden producir un bucle infinito de diseño.
 - En tvOS 10, al llamar a la `RemoveGestureRecognizer` método de una `UIView` objeto cancela explícitamente cualquier reconocedor de movimiento en curso.
 - Ver controladores de presentado ahora pueden afectar a la apariencia de la barra de estado.
 - tvOS 10 exige al programador que llame a `base.AwakeFromNib` cuando se crean subclases `UIViewController` e invalidar la `AwakeFromNib` método.
 - Aplicaciones con personalizado `UIView` subclases que reemplazan `LayoutSubviews` y desfasadas el diseño antes de llamar a `base.LayoutSubviews` puede desencadenar un bucle infinito de diseño en tvOS 10.
 - Los activos de imágenes específicos de dirección o flippable no son voltear cuando se asigna a `UIButton` objetos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
