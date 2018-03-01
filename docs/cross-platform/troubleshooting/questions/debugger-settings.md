---
title: "¿Qué configuración del proyecto es necesaria para que el depurador?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 6097dc8dfdff8807137ef68be86a08e4c9e23988
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>¿Qué configuración del proyecto es necesaria para que el depurador?

En orden para que el depurador para que funcione como se esperaba (visitas puntos de interrupción, mostrar registros de depuración, etc.), pantalla de información de instrumentación y depuración de desarrollador debe deben habilitarse.

Siga estos pasos para comprobar la configuración del entorno:

## <a name="visual-studio"></a>Programa para la mejora
1. Abrir las opciones de proyecto
2. Vaya a **de compilación > avanzadas...** Establecer información de depuración **completa**
3. Configuración para cada plataforma:
   - Vaya a **opciones Android > Opciones de depuración**. Marca el **habilitar la instrumentación de desarrollador** cuadro.
   - Vaya a **iOS compilación > Opciones de depuración**. Marca el **habilitar la depuración** cuadro.

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac
1. Abrir las opciones de proyecto
2. Vaya a **generar > compilador > Opciones generales**. Establecer información de depuración **completa**
3. Configuración para cada plataforma:
  - Vaya a **generar > compilación en Android > Opciones de depuración**. Marca el **habilitar la instrumentación de desarrollador** cuadro.
  - Vaya a **generar > iOS depuración**. Marca el **habilitar la depuración** cuadro.

