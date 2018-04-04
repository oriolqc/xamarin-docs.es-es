---
title: ¿Por qué producirá un error en mi proyecto de Xamarin.Forms.Maps Android error inesperado de nivel superior de COMPILETODALVIK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>¿Por qué producirá un error en mi proyecto de Xamarin.Forms.Maps Android error inesperado de nivel superior de COMPILETODALVIK?

Este error puede aparecer en el panel de errores de Visual Studio para Mac o en la ventana de salida de compilación de Visual Studio; en proyectos de Android mediante Xamarin.Forms.Maps.

Normalmente, esto se resuelve si aumenta el tamaño del montón de Java para el proyecto Xamarin.Android. Siga estos pasos para aumentar el tamaño del montón:

## <a name="visual-studio"></a>Programa para la mejora

1. Haga clic en el proyecto de Android y abrir las opciones de proyecto.
2. Vaya a **-> Android opciones avanzadas**
3. En el cuadro de texto de tamaño de montón de Java escriba 1G.
4. Recompile el proyecto.

![Captura de pantalla de las opciones de proyecto de Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "opciones en Visual Studio de compilación de Android")

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1.  Haga clic en el proyecto de Android y abrir las opciones de proyecto.
2.  Vaya a **compilar -> compilación en Android -> avanzadas**
3.  En el cuadro de texto de tamaño de montón de Java escriba 1G.
4.  Recompile el proyecto.  

![Captura de pantalla de Visual Studio para Mac proyecto opciones](maps-compiletodalvik-error-images/xsjavaheap.png "opciones en Visual Studio para Mac de compilación de Android")

