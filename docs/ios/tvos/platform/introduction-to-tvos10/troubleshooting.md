---
title: Solución de problemas de tvOS 10 aplicaciones crean con Xamarin
description: En este artículo proporciona varias sugerencias de solución de problemas para trabajar con tvOS 10 en aplicaciones de Xamarin. Describe problemas relacionados con la aplicación de Store, compatibilidad binaria, la CFNetwork HttpProtocol, CloudKit, imagen básica, NSUserActivity y UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 97cfe7220f8b351ec30a9f2c7a7347d318378fbc
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830486"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Solución de problemas de tvOS 10 aplicaciones crean con Xamarin

Las siguientes secciones enumeran algunos problemas conocidos que pueden producirse al usar tvOS 10 con Xamarin y la solución a estos problemas:

- [App Store](#App-Store)
- [Compatibilidad binaria](#Binary-Compatibility)
- [Protocolo HTTP de CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagen de Core](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemas conocidos:

- Al probar las compras de la aplicación en el entorno de recinto de seguridad, el cuadro de diálogo de autenticación puede aparecer dos veces.
- Al probar las compras de la aplicación con contenido hospedado en el entorno de recinto de seguridad, aparecerá el cuadro de diálogo de contraseña cada vez que la aplicación se pone en primer plano hasta que se complete la descarga de contenido.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidad binaria

Problemas conocidos:

- Una llamada a `NSObject.ValueForKey` le un `null` clave producirá una excepción.
- Referencia a una fuente por nombre cuando se llama a `UIFont.WithName` provocará un bloqueo.
- Ambos `NSURLSession` y `NSURLConnection` RC4 ya no los conjuntos de cifrado de durante el protocolo de enlace TLS para `http://` direcciones URL.
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

El `CIImageProcessor` API ahora admite un número arbitrario de imagen de entrada. `CIImageProcessor` Se quitarán las API que se incluyó en la versión beta 1 de tvOS 10.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Después de una operación de entrega, el `UserInfo` propiedad de un `NSUserActivity` el objeto puede estar vacío. Llamar explícitamente a `BecomeCurrent` NSUserActivity' objeto como solución alternativa actual.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemas conocidos:

- Cambia a la apariencia del fondo `UINavigationBar`, `UITabBar` o `UIToolBar` puede dar lugar a una fase de diseño para resolver el nuevo aspecto. Al intentar modificar estos aspectos visuales dentro de un `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` o `DidUpdateSubviews` pueden dar lugar a eventos en un bucle infinito de diseño.
- En tvOS 10, una llamada a la `RemoveGestureRecognizer` método de un `UIView` objeto cancela explícitamente ningún reconocedor de gestos en curso.
- Controladores de vista presenta ahora pueden afectar a la apariencia de la barra de estado.
- tvOS 10 requiere que el desarrollador llamar a `base.AwakeFromNib` cuando se crean subclases `UIViewController` e invalidar la `AwakeFromNib` método.
- Las aplicaciones con custom `UIView` subclases que reemplazan `LayoutSubviews` y de integridad en el diseño antes de llamar a `base.LayoutSubviews` puede desencadenar un bucle infinito de diseño en tvOS 10.
- Recursos de imágenes específicas de dirección o flippable son ni volteo cuando se asigna a `UIButton` objetos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
