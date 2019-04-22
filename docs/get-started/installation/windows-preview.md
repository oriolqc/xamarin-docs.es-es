---
title: Instalación de la versión preliminar de Xamarin en Windows
description: Este documento describe cómo instalar una versión preliminar de Xamarin en Visual Studio de 2019 mediante el canal de versión de vista previa.
ms.prod: xamarin
ms.assetid: 9F730444-06E8-4B3F-8A19-CA95CD484FFA
author: asb3993
ms.author: amburns
ms.date: 03/20/2018
ms.openlocfilehash: 9ce56891eeab73f661a9c22cc047c4d0bcb10337
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854800"
---
# <a name="installing-xamarin-preview-on-windows"></a>Instalación de la versión preliminar de Xamarin en Windows

Visual Studio 2019 y Visual Studio 2017 no admiten alfa, beta y estables canales del mismo modo que en versiones anteriores. En su lugar, hay solo dos opciones:

- **Lanzamiento**: equivalente al canal _estable_ en Visual Studio para Mac
- **Versión preliminar**: equivalente a los canales _alfa_ y _beta_ en Visual Studio para Mac

> [!TIP]
> Para probar características de versiones preliminares, debe [descargar el instalador de Visual Studio Preview](https://visualstudio.microsoft.com/vs/preview/), que le ofrece la opción para instalar **Preview** versiones de Visual Studio side-by-Side con el (estable Versión de lanzamiento). Para obtener más información sobre lo que es nuevo en Visual Studio de 2019 puede encontrarse en el [notas de la versión](https://docs.microsoft.com/visualstudio/releases/2019/release-notes).

La versión preliminar de Visual Studio puede contener las versiones preliminares correspondientes de la funcionalidad de Xamarin, incluidas:

- Xamarin.Forms
- Xamarin.iOS
- Xamarin.Android
- Generador de perfiles de Xamarin
- Xamarin Inspector
- Simulador iOS remoto de Xamarin

La captura de pantalla del **instalador de versión preliminar** siguiente muestra las opciones de versión preliminar y lanzamiento (tenga en cuenta los números de versión en gris: la versión 15.0 es de lanzamiento y la versión 15.1 es una versión preliminar):

![instalador que muestra las opciones de versión preliminar](windows-images/vs2017-installer.jpg)

Durante el proceso de instalación, se puede aplicar un **alias de instalación** a la instalación en paralelo (por lo que pueden distinguirse en el menú Inicio), tal y como se muestra a continuación:

[![editar alias antes de instalar](windows-images/vs2017-nickname-sml.png "edit nickname before installing")](windows-images/vs2017-nickname.png#lightbox)

### <a name="uninstalling-visual-studio-2019-preview"></a>Desinstalar Visual Studio Preview de 2019

El **instalador de Visual Studio** también se debe usar para desinstalar las versiones de vista previa de Visual Studio de 2019. Lea la [guía de desinstalación de Xamarin](uninstalling-xamarin.md#uninstallvs2017) para más información.
