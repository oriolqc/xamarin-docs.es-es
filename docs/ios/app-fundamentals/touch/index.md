---
title: Controlar la entrada táctil en aplicaciones de Xamarin.iOS
description: Este documento incluye vínculos a guías que describen cómo trabajar con táctil, multitáctil, gestos y 3D en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/23/2017
ms.openlocfilehash: 5aabc3a3c2ffbcffc0e12379989f7eb43b03a902
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116633"
---
# <a name="handling-touch-in-xamarinios-apps"></a>Controlar la entrada táctil en aplicaciones de Xamarin.iOS

Al igual que otras plataformas móviles, iOS tiene un número de formas de controlar la interacción. Puede admitir multitoque, muchos puntos de contacto en la pantalla y gestos complejos. Esta guía presentan algunos de los conceptos, así como las particularidades de la implementación de tacto y gestos en iOS.

iOS encapsula datos táctiles en el `UITouch` (clase), que está disponible para las aplicaciones a través de una serie de `UIResponder` métodos. Las aplicaciones pueden invalidar estos métodos en las subclases de `UIView` y `UIViewController`, que heredan de `UIResponder`.

Además de capturar datos táctiles, iOS proporciona medios para interpretar los patrones de toques en los gestos. Estos los reconocedores de gestos a su vez pueden utilizarse para interpretar los comandos específicos de la aplicación, como una rotación de una imagen o un cambio de una página. iOS proporciona una amplia colección de clases para controlar los gestos comunes sin código adicional mínimo.

La elección entre dispositivos táctiles y los reconocedores de gestos puede ser un confuso. Esta guía, se recomienda que en general, debería dará preferencia a los reconocedores de gestos. Los reconocedores de gestos se implementan como clases discretas, que proporcionan mayor separación de intereses y una mejor encapsulación. Esto facilita compartir la lógica entre diferentes vistas, minimizar la cantidad de código escrito.

Sin embargo, hay veces cuando se necesitan para usar el procesamiento de táctil de bajo nivel y seguir incluso varios dedos, por ejemplo, para crear un programa finger-paint.

## <a name="sections"></a>Secciones

-  [Entrada táctil en iOS](touch-in-ios.md)
-  [Tutorial: uso de la entrada táctil en iOS](ios-touch-walkthrough.md)
-  [Seguimiento multitáctil](touch-tracking.md)

Esta guía sirve como introducción a la función táctil en iOS. Para obtener más información sobre el uso de 3D Touch y comentarios hápticos en iOS, que se introdujeron en iOS 9 y 10, respectivamente, consulte las guías específicas siguientes:

* [Entrada táctil 3D](~/ios/platform/3d-touch.md)
* [Provisión de comentarios hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Vínculos relacionados

- [iOS tocar inicio (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS toque Final (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
- [Pintura con los dedos (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
