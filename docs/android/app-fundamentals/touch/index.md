---
title: Entrada táctil y gestos en Xamarin.Android
description: 'Pantallas táctiles en muchos de los dispositivos actuales permiten a los usuarios interactuar rápida y eficaz con dispositivos de forma natural e intuitiva. Esta interacción no se limita solo a la detección simple toque: es posible utilizar gestos así. Por ejemplo, el movimiento de gesto de acercamiento es un ejemplo muy común de este empujando una parte de la pantalla con dos dedos que el usuario puede acercar o alejar. Esta guía examina táctil y gestos en Android.'
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 3700f9c73fb58fefcdba7987c9931e385cd52d38
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436295"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Entrada táctil y gestos en Xamarin.Android

_Pantallas táctiles en muchos de los dispositivos actuales permiten a los usuarios interactuar rápida y eficaz con dispositivos de forma natural e intuitiva. Esta interacción no se limita solo a la detección simple toque: es posible utilizar gestos así. Por ejemplo, el movimiento de gesto de acercamiento es un ejemplo muy común de este empujando una parte de la pantalla con dos dedos que el usuario puede acercar o alejar. Esta guía examina táctil y gestos en Android._

## <a name="touch-overview"></a>Información general de la entrada táctil

iOS y Android son similares en las formas controlan táctil. Puede admiten multitoque - muchos puntos de contacto en la pantalla - y los gestos complejas. Esta guía presenta algunas de las similitudes en conceptos, así como las particularidades de la implementación táctil y gestos en ambas plataformas.

Android usa un `MotionEvent` objeto para encapsular datos táctiles y métodos en el objeto de vista para realizar escuchas de los últimos retoques.

Además de capturar datos de entrada táctil, iOS y Android proporcionan medios para interpretar los patrones de los últimos retoques en movimientos. Estos identificadores de gestos a su vez pueden utilizarse para interpretar los comandos específicos de la aplicación, por ejemplo, una rotación de una imagen o un cambio de una página. Android proporciona una serie de movimientos compatibles, así como recursos para asegurarse de agregar gestos personalizados complejos fácil.

Si está trabajando en Android o iOS, la elección entre los últimos retoques y los identificadores de gestos puede ser uno confusa. Esta guía recomienda que por lo general, preferencias deberían proporcionarse a identificadores de gestos. Identificadores de gestos se implementan como clases discretas, que proporcionan mayor separación de aspectos y una mejor encapsulación. Esto facilita compartir la lógica entre las distintas vistas, minimizar la cantidad de código escrito.

Esta guía sigue un formato similar para cada sistema operativo: primero, táctil de la plataforma en la API se introdujo y explica, ya que son la base sobre qué táctil se crean las interacciones. A continuación, profundizar en el mundo de los identificadores de gestos: en primer lugar por explorar algunos movimientos comunes y finalizar la reunión con la creación de gestos personalizados para las aplicaciones. Por último, verá cómo realizar el seguimiento dedos individuales mediante el seguimiento de entrada táctil de bajo nivel para crear un programa de finger-paint.

## <a name="sections"></a>Secciones

-  [Entrada táctil de Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Tutorial: Usar táctil en Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Seguimiento de multitoque](touch-tracking.md)

## <a name="summary"></a>Resumen

En esta guía se examina táctil en Android. Para ambos sistemas operativos, hemos visto cómo habilitar la interacción táctil y cómo responder a los eventos de toque. A continuación, aprendimos movimientos y algunos de los identificadores de movimiento que iOS y Android se proporcionan para ocuparse de algunas de los escenarios más comunes. Se examina cómo crear gestos personalizados e implementarlas en las aplicaciones. Un tutorial muestra los conceptos y las API para cada sistema operativo en acción, y también ha visto cómo realizar el seguimiento dedos individuales.



## <a name="related-links"></a>Vínculos relacionados

- [Android Touch iniciar (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [Pintura con los dedos (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
