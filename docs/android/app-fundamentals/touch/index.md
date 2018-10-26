---
title: Tacto y gestos en Xamarin.Android
description: 'Pantallas táctiles en muchos de los dispositivos actuales permiten a los usuarios interactuar rápida y eficaz con dispositivos de forma natural e intuitiva. Esta interacción no se limita solo a la detección simple toque: es posible usar gestos también. Por ejemplo, el gesto pinch a zoom es un ejemplo muy común de este empujando una parte de la pantalla con dos dedos, que el usuario puede acercar o alejar. Esta guía examina el toque y gestos en Android.'
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7f957c9ff5a0e7c3a0821978703860ed2f723a92
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123328"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Tacto y gestos en Xamarin.Android

_Pantallas táctiles en muchos de los dispositivos actuales permiten a los usuarios interactuar rápida y eficaz con dispositivos de forma natural e intuitiva. Esta interacción no se limita solo a la detección simple toque: es posible usar gestos también. Por ejemplo, el gesto pinch a zoom es un ejemplo muy común de este empujando una parte de la pantalla con dos dedos, que el usuario puede acercar o alejar. Esta guía examina el toque y gestos en Android._

## <a name="touch-overview"></a>Información general de toque

iOS y Android son similares en las formas que permiten controlar la interacción. Ambos pueden admitir multitoque - muchos puntos de contacto en la pantalla - y gestos complejos. Esta guía presenta algunas de las similitudes en los conceptos, así como las particularidades de la implementación de toque y gestos en ambas plataformas.

Android usa un `MotionEvent` objeto para encapsular datos táctiles y métodos en el objeto de vista para realizar escuchas de toques.

Además de capturar datos táctiles, iOS y Android proporcionan medios para interpretar los patrones de toques en los gestos. Estos los reconocedores de gestos a su vez pueden utilizarse para interpretar los comandos específicos de la aplicación, como una rotación de una imagen o un cambio de una página. Android proporciona un puñado de gestos compatibles, así como recursos para hacer que agregar gestos personalizados complejos fácil.

Si está trabajando en Android o iOS, la elección entre dispositivos táctiles y los reconocedores de gestos puede ser un confuso. Esta guía, se recomienda que en general, debería dará preferencia a los reconocedores de gestos. Los reconocedores de gestos se implementan como clases discretas, que proporcionan mayor separación de intereses y una mejor encapsulación. Esto facilita compartir la lógica entre diferentes vistas, minimizar la cantidad de código escrito.

Esta guía sigue un formato similar para cada sistema operativo: primero, interacción de la plataforma API se introdujo y se ha explicado, ya que son la base sobre qué táctil se compilan las interacciones. A continuación, profundizar en el mundo de los reconocedores de gestos: primero explorando algunos gestos comunes, y finalizando con la creación de gestos personalizados para las aplicaciones. Por último, verá cómo hacer un seguimiento con seguimiento táctil de bajo nivel para crear un programa finger-paint de dedos individuales.

## <a name="sections"></a>Secciones

-  [Entrada táctil de Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Tutorial: Usar táctil en Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Seguimiento Multitáctil](touch-tracking.md)

## <a name="summary"></a>Resumen

En esta guía se examina la entrada táctil en Android. Para ambos sistemas operativos, hemos aprendido cómo habilitar la interacción táctil y responder a los eventos de toque. A continuación, hemos aprendido acerca de los gestos y algunos de los reconocedores de gestos que iOS y Android se proporcionan para controlar algunos de los escenarios más comunes. Hemos visto cómo crear gestos personalizados e implementarlos en las aplicaciones. Un tutorial muestra los conceptos y API para cada sistema operativo en acción, y también vio cómo realizar el seguimiento de los dedos individuales.



## <a name="related-links"></a>Vínculos relacionados

- [Android tocar inicio (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [Pintura con los dedos (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
