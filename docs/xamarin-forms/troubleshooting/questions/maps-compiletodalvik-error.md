---
title: ¿Por qué un error mi proyecto de xamarin.Forms.Maps para Android con ERROR de nivel superior inesperado COMPILETODALVIK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250500"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>¿Por qué un error mi proyecto de xamarin.Forms.Maps para Android con ERROR de nivel superior inesperado COMPILETODALVIK?

Este error puede aparecer en el panel de errores de Visual Studio para Mac o en la ventana de salida de la compilación de Visual Studio; en los proyectos mediante xamarin.Forms.Maps para Android.

Normalmente, esto se resuelve al aumentar el tamaño del montón de Java para el proyecto de Xamarin.Android. Siga estos pasos para aumentar el tamaño del montón:

## <a name="visual-studio"></a>Programa para la mejora

1. Haga clic en el proyecto de Android y abrir las opciones del proyecto.
2. Vaya a **-> Android opciones avanzadas**
3. En el cuadro de texto de tamaño de montón de Java, escriba 1G.
4. Recompile el proyecto.

![Captura de pantalla de las opciones de proyecto de Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Android opciones de compilación en Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1.  Haga clic en el proyecto de Android y abrir las opciones del proyecto.
2.  Vaya a **compilar -> compilación de Android -> avanzado**
3.  En el cuadro de texto de tamaño de montón de Java, escriba 1G.
4.  Recompile el proyecto.  

![Captura de pantalla de Visual Studio para Mac opciones del proyecto](maps-compiletodalvik-error-images/xsjavaheap.png "Android opciones de compilación en Visual Studio para Mac")

