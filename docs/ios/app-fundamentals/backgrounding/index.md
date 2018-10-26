---
title: Procesamiento en segundo plano en Xamarin.iOS
description: En segundo plano de procesamiento o de procesamiento en segundo plano es el proceso de permitir a las aplicaciones realizar tareas en segundo plano mientras se está ejecutando otra aplicación en primer plano. Esta guía sirve como introducción al procesamiento de iOS en segundo plano.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2018
ms.openlocfilehash: a4f5112b6e77ab6e00453c19c766d1e905df1144
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122782"
---
# <a name="backgrounding-in-xamarinios"></a>Procesamiento en segundo plano en Xamarin.iOS

_En segundo plano de procesamiento o de procesamiento en segundo plano es el proceso de permitir a las aplicaciones realizar tareas en segundo plano mientras se está ejecutando otra aplicación en primer plano. Esta guía sirve como introducción al procesamiento de iOS en segundo plano._

Procesamiento en segundo plano en aplicaciones móviles es fundamentalmente diferente que el concepto tradicional de multitarea en el escritorio. Equipos de escritorio tienen una variedad de recursos disponibles para una aplicación, incluidos la memoria, energía y espacio en pantalla. Las aplicaciones son capaz de ejecutar en paralelo y permanecer eficaz y utilizable. En un dispositivo móvil, los recursos son mucho más limitados. Es difícil mostrar más de una aplicación en una pantalla pequeña y ejecutar varias aplicaciones a toda velocidad podría agotar la batería. Procesamiento en segundo plano es un compromiso constante entre ofrecer a las aplicaciones de los recursos necesarios para ejecutar las tareas en segundo plano que se necesitan para funcionar bien y que la aplicación foregrounded y el dispositivo siga respondiendo. IOS y Android tienen disposiciones para procesamiento en segundo plano, pero controlan de maneras muy diferentes.

En iOS, procesamiento en segundo plano se reconoce como un estado de la aplicación y las aplicaciones se mueven dentro y fuera el estado en segundo plano según el comportamiento de la aplicación y el usuario. iOS también ofrece varias opciones para conectar una aplicación para ejecutarse en segundo plano, incluidos solicitando al sistema operativo para el tiempo para completar una tarea importante, funciona como un tipo de aplicación en segundo plano necesarias conocido, y actualizar el contenido de la aplicación en designado intervalos.

En esta guía y que acompaña a los tutoriales, vamos a aprender a realizar tareas de aplicación en segundo plano. Se cubren los conceptos claves y los procedimientos recomendados y siga los pasos de creación de una aplicación del mundo real que recibe las actualizaciones de ubicación en segundo plano.

## <a name="contents"></a>Contenido

1.  [Introducción al procesamiento en segundo plano en iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [Demostración del ciclo de vida de la aplicación](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [Técnicas de procesamiento en segundo plano de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [Tutoriales: procesamiento en segundo plano en iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [Guía del procesamiento en segundo plano de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Resumen

En esta guía, presentamos las distintas formas de realizar el procesamiento en segundo plano en iOS. Se tratan los Estados de la aplicación de iOS y examina el rol de procesamiento en segundo plano se reproduce en el ciclo de vida de aplicación de iOS. Además, hemos aprendido cómo podríamos registramos tareas individuales o aplicaciones completas para operar en segundo plano en iOS. Por último, hemos reforzado nuestro estudio de procesamiento en segundo plano en iOS mediante la creación de aplicaciones que realizan actualizaciones en segundo plano.



## <a name="related-links"></a>Vínculos relacionados

- [Procesamiento en segundo plano en Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (ejemplo)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [Ubicación (ejemplo)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Transferencia en segundo plano simple (ejemplo)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [Ejecución en segundo plano de iOS](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
