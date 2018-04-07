---
title: ¿Por qué Visual Studio no incluye en mi compilación el proyecto de biblioteca al que se hace referencia?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: cb9b3689ab6a12d99f9694583cd0fd50a6f5c72c
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>¿Por qué no Visual Studio incluye mi proyecto de biblioteca que se hace referencia en la compilación

Visual Studio usa el **Administrador de configuración** para determinar qué proyectos de una solución se incluyen automáticamente en una configuración de implementación o compilación determinada.

Algunas de las plantillas que se generan con un proyecto de biblioteca al que se hace referencia ya incluyen en la configuración la biblioteca en cuestión, pero en caso contrario deberá establecerse manualmente.

## <a name="how-to-use-the-configuration-manager"></a>Cómo usar el Administrador de configuración

1. Abra **Compilar > Administrador de configuración**
2. Seleccione la configuración para personalizar, por ejemplo, **depurar | iPhone**
3. Active las casillas de verificación de los proyectos que quiere incluir.

> [!NOTE]
> Atenuados cuadros se administran automáticamente y no debe necesario realizar ningún cambio.

Presentación en pantalla de estos pasos: [http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
