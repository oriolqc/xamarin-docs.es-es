---
title: Backgrounding en Xamarin.iOS
description: Fondo de procesamiento o backgrounding es el proceso de permitir que las aplicaciones realizar tareas en segundo plano mientras se está ejecutando otra aplicación en primer plano. Esta guía sirve como una introducción al procesamiento de iOS en segundo plano.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b22f3ef3276129f7f46c23cc1d06666f151f5ac4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783547"
---
# <a name="backgrounding-in-xamarinios"></a>Backgrounding en Xamarin.iOS

_Fondo de procesamiento o backgrounding es el proceso de permitir que las aplicaciones realizar tareas en segundo plano mientras se está ejecutando otra aplicación en primer plano. Esta guía sirve como una introducción al procesamiento de iOS en segundo plano._

Backgrounding en las aplicaciones móviles es diferente que el concepto tradicional de multitarea en el escritorio. Equipos de escritorio tienen una gran variedad de recursos disponibles para una aplicación, incluidos el espacio real en pantalla, energía y la memoria. Las aplicaciones son puede ejecutar en paralelo y siguen siendo eficiente y se puede usar. En un dispositivo móvil, los recursos son mucho más limitados. Resulta difícil mostrar más de una aplicación en una pantalla pequeña y ejecuta varias aplicaciones a toda velocidad podría agotar la batería. Backgrounding es un compromiso constante entre darle a aplicaciones de los recursos necesarios para ejecutar las tareas en segundo plano que necesitan para realizar correctamente y que la aplicación foregrounded y el dispositivo siga respondiendo. IOS y Android tienen disposiciones para backgrounding, pero se controlan de maneras muy diferentes.

En iOS, backgrounding se reconoce como un estado de la aplicación y las aplicaciones se mueven en el estado de segundo plano según el comportamiento de la aplicación y el usuario. iOS también ofrece varias opciones para conectar una aplicación para que se ejecute en segundo plano, como pedir al sistema operativo de tiempo para completar una tarea importante, funciona como un tipo de aplicación de fondo necesario conocido, y actualizar el contenido de una aplicación en designado intervalos.

En esta guía y que acompaña a tutoriales, vamos a aprender a realizar tareas de la aplicación en segundo plano. Se cubren los conceptos claves y los procedimientos recomendados y, a continuación, recorre paso a paso cómo crear una aplicación del mundo real que recibe las actualizaciones de la ubicación en segundo plano.

## <a name="contents"></a>Contenido

1.  [Introducción al procesamiento en segundo plano en iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [Demostración del ciclo de vida de la aplicación](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [Técnicas de procesamiento en segundo plano de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [Tutoriales: procesamiento en segundo plano en iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [Guía del procesamiento en segundo plano de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Resumen

En esta guía, se introdujeron las distintas formas de realizar el procesamiento en segundo plano en iOS. Se tratan los Estados de la aplicación de iOS y examina el rol backgrounding desempeña en el ciclo de vida de aplicación de iOS. Además, hemos visto cómo podríamos registramos tareas individuales o aplicaciones completas para operar en segundo plano en iOS. Por último, se refuerza nuestra comprensión de backgrounding en iOS mediante la creación de aplicaciones que realizan las actualizaciones en segundo plano.



## <a name="related-links"></a>Vínculos relacionados

- [Backgrounding en Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (ejemplo)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [Ubicación (ejemplo)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Transferencia en segundo plano simple (ejemplo)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [iOS ejecución en segundo plano](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
