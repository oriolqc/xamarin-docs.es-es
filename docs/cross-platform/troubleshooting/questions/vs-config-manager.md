---
title: ¿Por qué Visual Studio no incluye en mi compilación el proyecto de biblioteca al que se hace referencia?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: d7aeac2f433e8fdf231f5887f1537f15e2bd1976
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341313"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>¿Por qué Visual Studio no incluye en mi compilación el proyecto de biblioteca al que se hace referencia?

Visual Studio usa el **Administrador de configuración** para determinar qué proyectos de una solución se incluyen automáticamente en una configuración de implementación o compilación determinada.

Algunas de las plantillas que se generan con un proyecto de biblioteca al que se hace referencia ya incluyen en la configuración la biblioteca en cuestión, pero en caso contrario deberá establecerse manualmente.

## <a name="how-to-use-the-configuration-manager"></a>Cómo usar el Administrador de configuración

1. Abra **Compilar > Administrador de configuración**
2. Seleccione la configuración para personalizar, por ejemplo, **depurar | iPhone**
3. Active las casillas de verificación de los proyectos que quiere incluir.

> [!NOTE]
> Atenuados cuadros se controlan automáticamente y no necesitan ningún cambio.

Presentación en pantalla de estos pasos: [http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
