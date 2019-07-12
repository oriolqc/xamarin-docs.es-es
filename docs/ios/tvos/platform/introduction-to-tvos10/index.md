---
title: Introducción a tvOS 10
description: Este artículo presenta todas las características disponibles en tvOS 10 y las API nuevas y modificadas para desarrolladores de Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 6bad05f62fa9168818aa9a2e73cc1e2830af03b3
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830035"
---
# <a name="introduction-to-tvos-10"></a>Introducción a tvOS 10

_Este artículo presenta todas las características disponibles en tvOS 10 y las API nuevas y modificadas para desarrolladores de Xamarin.tvOS._

Con el nuevo tvOS 10 de SDK Apple ha incluido nuevas API y servicios que permiten a los desarrolladores crear nuevas categorías de aplicaciones y características. 

Para obtener más información sobre tvOS 10, consulte Apple [tvOS + aplicaciones](https://developer.apple.com/tvos/) documentación.

## <a name="whats-new-in-tvos-10"></a>Novedades de tvOS 10

Apple ha agregado varias nuevas API y servicios en tvOS 10 junto con muchas mejoras en las características existentes, incluyendo:

## <a name="new-user-interface-styles"></a>Nuevos estilos de interfaz de usuario

tvOS 10 ahora admite un oscuro y la interfaz de usuario de la luz tema todas la compilación en UIKit controla tendrá automáticamente adaptar, según las preferencias del usuario.

Al crear e implementar nuevos controles de interfaz de usuario personalizados, el programador debe utilizar el [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) clase para adaptarse a tema seleccionado del usuario.

Para obtener más información, consulte nuestra [nuevos estilos de interfaz de usuario](~/ios/tvos/platform/user-interface-styles.md) documentación.

## <a name="security-and-privacy-enhancements"></a>Mejoras de seguridad y privacidad

Apple ha realizado varias mejoras en seguridad y privacidad en tvOS 10 que le ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final.

Como resultado, las aplicaciones que se ejecutan en watchOS 3 (o posterior) deben declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de uno o más claves específicas de privacidad de sus `Info.plist` archivos que explican al usuario por qué la aplicación quiere tener acceso.

Dado que tvOS 10 comparte estos cambios con iOS 10, consulte nuestra iOS 10 [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) guía para obtener más información.

## <a name="video-subscriber-account"></a>Cuenta de suscriptor de vídeo

Novedad de tvOS 10, el marco de trabajo de la cuenta de suscriptor de vídeo permite aplicaciones esa compatibilidad autenticado con transmisión por secuencias o vídeo bajo demanda para autenticarse con su proveedor de TV por cable o satélite mediante una experiencia de inicio de sesión único para el usuario final.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Color amplio

tvOS 10 amplía la compatibilidad con formatos de píxel de rango ampliado y espacios de la amplia gama de colores en todo el sistema incluidos marcos como gráficos de Core, imagen básica, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas a color amplia es aún más disminuido proporcionando este comportamiento a lo largo de la pila de gráficos completa.

Además, `UIKit` se ha modificado para que funcione en el nuevo extendidos **sRGB** espacio de color, lo que facilita la combinación de colores en gamas de color amplia sin pérdida de rendimiento significativas.

Apple ofrece las siguientes prácticas recomendadas al trabajar con colores amplios:

- `UIColor` Ahora se utiliza espacio de color sRGB y se dejará de fijar los valores para el `0.0` a `1.0` intervalo. Si la aplicación se basa en el comportamiento de bloqueo anterior, deberá modificarse para tvOS 10.
- Si la aplicación realiza una representación personalizada de `UIImages`, use la nueva [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) clase para especificar el uso de los formatos de intervalo extendido o intervalo estándar.
- Cuando se usa una API de bajo nivel como gráficos básicos o sistema operativo para proporcionar procesamiento de imágenes, la aplicación debe usar un rango extendido píxeles y el espacio de formato de color que es compatible con los valores de punto flotante de 16 bits. En caso necesario, la aplicación deberá fijar manualmente los valores de componentes de color.
- Gráficos básicos, imagen Core y los sombreadores de rendimiento de sistema operativo proporcionan nuevos métodos para la conversión entre los espacios de dos colores.

Para obtener más información, consulte nuestra [Introducción a Color amplia](~/ios/platform/wide-color.md) guía.

## <a name="newly-available-existing-frameworks"></a>Marcos existentes recién disponibles

Varios marcos que no estaban disponibles en iOS (y tvOS no), están disponibles para tvOS 10, como:

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- Fotos
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>Cambios de marco de trabajo adicionales

Además de los cambios en la plataforma principal y adiciones enumeradas anteriormente, Apple ha realizado muchos cambios de marco secundarias adicionales en tvOS 10.

Para obtener más información, consulte nuestra [cambios adicionales de Framework](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) guía.

## <a name="deprecated-apis"></a>Interfaces API desusadas

Ninguna API o marcos de trabajo han quedado en desuso por tvOS 10. Consulte Apple [tvOS 10 diferencias en las API](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) documentación para obtener una lista completa de las modificaciones de la API.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
