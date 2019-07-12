---
title: ¿Qué configuración del proyecto es necesaria para el depurador?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: e7a383f899fab0400104493fa89b125788d610aa
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831334"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>¿Qué configuración del proyecto es necesaria para el depurador?

En orden para que el depurador funcione según lo esperado (puntos de interrupción de posicionamiento, para mostrar los registros de depuración, etc.), pantalla de información de instrumentación y depuración de desarrollador debe habilitarse.

Siga estos pasos para comprobar la configuración del entorno:

## <a name="visual-studio"></a>Visual Studio
1. Abra las opciones del proyecto
2. Vaya a **compilar > avanzadas...** Establecer la información de depuración en **completa**
3. Configuración para cada plataforma:
   - Vaya a **opciones de Android > Opciones de depuración**. Marca el **habilitar instrumentación de desarrollador** cuadro.
   - Vaya a **depuración de iOS > depuración e instrumentación**. Marca el **Habilitar depuración** cuadro.

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac
1. Abra las opciones del proyecto
2. Vaya a **compilar > compilador > Opciones generales**. Establecer la información de depuración en **completa**
3. Configuración para cada plataforma:
    - Vaya a **compilar > compilación de Android > Opciones de depuración**. Marca el **habilitar instrumentación de desarrollador** cuadro.
    - Vaya a **compilar > iOS depuración**. Marca el **Habilitar depuración** cuadro.

