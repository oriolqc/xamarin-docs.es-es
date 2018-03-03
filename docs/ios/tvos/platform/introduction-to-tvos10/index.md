---
title: "Introducción a tvOS 10"
description: "Este artículo detallan todas las API y características disponibles en tvOS 10 nueva y modificada para desarrolladores de Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 8bc379e507287cd609dca8440b1210b7f1514114
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-tvos-10"></a>Introducción a tvOS 10

_Este artículo detallan todas las API y características disponibles en tvOS 10 nueva y modificada para desarrolladores de Xamarin.tvOS._

Con el nuevo tvOS 10 Apple SDK incluye nuevas API y servicios que permiten a los desarrolladores crear nuevas categorías de aplicaciones y características. 

Para obtener más información sobre tvOS 10, vea de Apple [tvOS + aplicaciones](https://developer.apple.com/tvos/) documentación.

## <a name="whats-new-in-tvos-10"></a>What's New en tvOS 10

Apple ha agregado varias nuevas API y servicios en tvOS 10 junto con muchas mejoras en las características existentes, incluyendo:

## <a name="new-user-interface-styles"></a>Estilos de la interfaz de usuario nueva

tvOS 10 ahora admite un oscuro y la interfaz de usuario de luz que tendrán todos lo UIKit de compilación controla automáticamente el tema adaptar, según las preferencias del usuario.

Al crear e implementar nuevos controles de interfaz de usuario personalizados, el programador debe utilizar el [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) clase adaptarse a tema seleccionado del usuario.

Para obtener más información, vea nuestra [nuevos estilos de interfaz de usuario](~/ios/tvos/platform/user-interface-styles.md) documentación.

## <a name="security-and-privacy-enhancements"></a>Mejoras de seguridad y privacidad

Apple ha realizado varias mejoras en la seguridad y privacidad en tvOS 10 que le ayudará a los desarrolladores mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final.

Como resultado, aplicaciones que se ejecutan en watchOS 3 (o posterior) deben declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de una o varias claves específicas de privacidad de sus `Info.plist` archivos que explican al usuario ¿por qué la aplicación desea tener acceso.

Dado que tvOS 10 comparte estos cambios con iOS 10, visite nuestro iOS 10 [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) guía para obtener más información.

## <a name="video-subscriber-account"></a>Cuenta del suscriptor de vídeo

Nueva tvos 10, el marco de trabajo de la cuenta del suscriptor de vídeo permite aplicaciones esa compatibilidad autenticado con transmisión por secuencias o vídeo bajo demanda para autenticarse con su proveedor de TV por cable o satélite mediante una experiencia de inicio de sesión único para el usuario final.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Color amplia

tvOS 10 amplía la compatibilidad con formatos de píxel de rango extendido y los espacios de la amplia gama de colores en todo el sistema incluidos los marcos, como los gráficos esenciales, imagen Core, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas de color amplia adicional se ve facilitado por proporcionar este comportamiento en toda la pila completa de gráficos.

Además, `UIKit` se ha modificado para que funcione en el nuevo extendidos **sRGB** espacio de color, lo que facilita la combinación de colores de gamas de color amplia sin pérdida de rendimiento significativas.

Apple ofrece las siguientes prácticas recomendadas al trabajar con colores ancho:

 - `UIColor` Ahora se utiliza un espacio de colores sRGB y se dejará sujete valores para la `0.0` a `1.0` intervalo. Si la aplicación se basa en el comportamiento de clamp anterior, será necesario modificar tvos 10.
 - Si la aplicación realiza la representación personalizada de `UIImages`, use la nueva [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) clase para especificar el uso de los formatos de rango extendido o intervalo estándar.
 - Cuando se usa una API de bajo nivel, como gráficos de núcleo o sistema operativo para proporcionar procesamiento de imágenes, la aplicación debe usar un rango extendido píxeles y espacio en formato de color que es compatible con los valores de punto flotante de 16 bits. En caso necesario, la aplicación tendrá que sujete manualmente los valores de componente de color.
 - Para los gráficos esenciales, imagen Core y los sombreadores de rendimiento de sistema operativo proporcionan nuevos métodos para convertir entre los espacios de dos color.

Para obtener más información, visite nuestro [Introducción a Color amplia](~/ios/platform/wide-color.md) guía.

## <a name="newly-available-existing-frameworks"></a>Marcos existentes disponibles recientemente

Varios marcos de trabajo que no estaban disponibles en iOS (y no tvOS), se hayan lanzado tvos 10 como:

 - ExternalAccessory
 - HomeKit
 - MultipeerConnectivity
 - Fotos
 - ReplayKit
 - UserNotification

## <a name="additional-framework-changes"></a>Cambios de Framework adicionales

Además de los cambios principales framework y adiciones enumeradas anteriormente, Apple ha realizado muchos cambios de marco secundarias adicionales en tvOS 10.

Para obtener más información, visite nuestro [Framework modificándola](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) guía.

## <a name="deprecated-apis"></a>Interfaces API en desuso

Ninguna API o marcos de trabajo se han dejado de utilizar tvOS 10. Vea de Apple [tvOS 10 diferencias en las API](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) documentación para obtener una lista completa de las modificaciones de API.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
