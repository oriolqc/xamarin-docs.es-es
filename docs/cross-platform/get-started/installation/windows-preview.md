---
title: Instalación de la versión preliminar de Xamarin en Windows
ms.prod: xamarin
ms.assetid: 9F730444-06E8-4B3F-8A19-CA95CD484FFA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/20/2018
ms.openlocfilehash: 4f89b888faace39e050e0994f7de9de45415a49a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="installing-xamarin-preview-on-windows"></a>Instalación de la versión preliminar de Xamarin en Windows

Visual Studio 2017 no admite los canales alfa, beta ni estable de la misma manera que las versiones anteriores. En su lugar, hay solo dos opciones:

- **Lanzamiento**: equivalente al canal _estable_ en Visual Studio para Mac
- **Versión preliminar**: equivalente a los canales _alfa_ y _beta_ en Visual Studio para Mac

> [!TIP] 
> Para probar las características de versiones preliminares, debe [descargar el instalador de Visual Studio de 2017 Preview](https://www.visualstudio.com/vs/preview/), que le ofrece la opción de instalar **versiones preliminares** de Visual Studio en paralelo con la versión estable (versión de lanzamiento). Puede encontrar más información sobre las novedades de Visual Studio 2017 en las [notas de la versión](/visualstudio/releasenotes/vs2017-preview-relnotes).

La versión preliminar de Visual Studio puede contener las versiones preliminares correspondientes de la funcionalidad de Xamarin, incluidas:

- Xamarin.Forms
- Xamarin.iOS
- Xamarin.Android
- Generador de perfiles de Xamarin
- Libros/Inspector de Xamarin
- Simulador iOS remoto de Xamarin

La captura de pantalla del **instalador de versión preliminar** siguiente muestra las opciones de versión preliminar y lanzamiento (tenga en cuenta los números de versión en gris: la versión 15.0 es de lanzamiento y la versión 15.1 es una versión preliminar):

![instalador que muestra las opciones de versión preliminar](windows-images/vs2017-installer.jpg)

Durante el proceso de instalación, se puede aplicar un **alias de instalación** a la instalación en paralelo (por lo que pueden distinguirse en el menú Inicio), tal y como se muestra a continuación:

[![editar alias antes de instalar](windows-images/vs2017-nickname-sml.png "edit nickname before installing")](windows-images/vs2017-nickname.png#lightbox)

### <a name="uninstalling-visual-studio-2017-preview"></a>Desinstalar Visual Studio 2017 Preview

El **Instalador de Visual Studio** también debe utilizarse para desinstalar las versiones preliminares de Visual Studio de 2017. Lea la [guía de desinstalación de Xamarin](uninstalling-xamarin.md#uninstallvs2017) para más información.
