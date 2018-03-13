---
title: "Entrada táctil"
description: "Pantallas táctiles en muchos de los dispositivos actuales permiten a los usuarios interactuar rápida y eficaz con dispositivos de forma natural e intuitiva. Esta interacción no se limita solo a la detección simple toque: es posible usar los movimientos así. Por ejemplo, el movimiento de gesto de acercamiento es un ejemplo muy común de esto: empujando una parte de la pantalla con dos dedos que el usuario puede acercar o alejar. Esta guía examina táctiles y los gestos de iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/23/2017
ms.openlocfilehash: b0e1cf8b1cb18982fe319fef7c524aeb70be4a9b
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="touch"></a>Entrada táctil

_Pantallas táctiles en muchos de los dispositivos actuales permiten a los usuarios interactuar rápida y eficaz con dispositivos de forma natural e intuitiva. Esta interacción no se limita solo a la detección simple toque: es posible usar los movimientos así. Por ejemplo, el movimiento de gesto de acercamiento es un ejemplo muy común de esto: empujando una parte de la pantalla con dos dedos que el usuario puede acercar o alejar. Esta guía examina táctiles y los gestos de iOS._


Al igual que otras plataformas móviles, iOS tiene un número de formas de controlar la entrada táctil. Puede admitir multitoque: muchos puntos de contacto en la pantalla y los gestos complejas. Esta guía presentan algunos de los conceptos, así como las particularidades de la implementación táctiles y los gestos en iOS.

iOS encapsula los datos de entrada táctil en el `UITouch` (clase), que estará disponible para las aplicaciones a través de una serie de `UIResponder` métodos. Las aplicaciones pueden invalidar estos métodos en subclases de `UIView` y `UIViewController`, las cuales heredan de `UIResponder`.

Además de capturar datos de entrada táctil, iOS proporciona medios para interpretar los patrones de los últimos retoques en movimientos. Estos identificadores de gestos a su vez pueden utilizarse para interpretar los comandos específicos de la aplicación, por ejemplo, una rotación de una imagen o un cambio de una página. iOS proporciona una amplia colección de clases para controlar los gestos común con el mínimo código agregado.

La elección entre los últimos retoques y los identificadores de gestos puede ser uno confusa. Esta guía recomienda que por lo general, preferencias deberían proporcionarse a identificadores de gestos. Identificadores de gestos se implementan como clases discretas, que proporcionan mayor separación de aspectos y una mejor encapsulación. Esto facilita compartir la lógica entre las distintas vistas, minimizar la cantidad de código escrito.

Sin embargo, hay ocasiones cuando necesite utilizar el procesamiento de bajo nivel táctil e incluso realizar un seguimiento de muchos dedos, por ejemplo, para crear un programa de finger-paint.

## <a name="sections"></a>Secciones

-  [Entrada táctil en iOS](touch-in-ios.md)
-  [Tutorial: Usar táctil en iOS](ios-touch-walkthrough.md)
-  [Seguimiento multitáctil](touch-tracking.md)

Esta guía le servirá como introducción a la entrada táctil en iOS. Para obtener más información sobre el uso de 3D táctiles y comentarios hápticos en iOS, que se introdujeron en iOS 9 y 10 respectivamente, consulte las guías específicas siguientes:

* [Entrada táctil 3D](~/ios/platform/3d-touch.md)
* [Provisión de comentarios hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)



## <a name="related-links"></a>Vínculos relacionados

- [iOS Touch iniciar (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS toque Final (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
- [Pintura con los dedos (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
