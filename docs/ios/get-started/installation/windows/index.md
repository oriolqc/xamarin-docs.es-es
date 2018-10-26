---
title: Instalación de Xamarin.iOS en Windows
description: En este documento se describe cómo configurar un equipo Windows, como configurar un host de compilación de Mac y cómo emparejar el equipo Windows con el host de compilación de Mac para el desarrollo de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/16/2018
ms.openlocfilehash: 5e7b8a3fb703d8857312b15f4884399ee951b2d4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118544"
---
# <a name="installing-xamarinios-on-windows"></a>Instalación de Xamarin.iOS en Windows

_En este artículo, se describe cómo configurar una máquina de Windows y un host de compilación de Mac para el desarrollo de Xamarin.iOS._

## <a name="overview"></a>Información general

Para compilar aplicaciones de Xamarin.iOS con Visual Studio 2017 en Windows, necesitará:
 
-  Una máquina de Windows con Visual Studio 2017 instalado. Puede ser una máquina virtual o física.
    - [Requisitos del sistema de equipos Windows](~/cross-platform/get-started/requirements.md#windows-requirements)
    
-  Un equipo Mac accesible desde la red configurado con las herramientas de compilación de Apple y Xamarin.iOS. Visual Studio 2017 accede a esta máquina a través de una conexión de red para usar las herramientas de compilación de Apple, que son necesarias para compilar aplicaciones nativas de iOS. 
    - [Requisitos del sistema de equipos Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

## <a name="setup"></a>Programa de instalación

Para configurar el equipo para el desarrollo de Xamarin.iOS en Visual Studio 2017, siga estos pasos:

1. Configurar Windows (instalar Visual Studio 2017)

    Xamarin.iOS funciona con las ediciones de Visual Studio 2017 Community, Professional y Enterprise en una máquina virtual o independiente.
    
    - [Instale Visual Studio 2017](~/cross-platform/get-started/installation/windows.md).

2. Configurar un equipo Mac (instalar Xcode y Visual Studio para Mac)

    Para compilar, depurar y firmar aplicaciones de iOS para distribuirlas, Visual Studio 2017 debe tener acceso de red a un host de compilación de Mac configurado con las herramientas de desarrollo de Apple (Xcode) y Xamarin.iOS.

    - [Descargue e instale Xcode desde el App Store de Mac](https://itunes.apple.com/us/app/xcode/id497799835?mt=12). 
    - [Instale Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation), que también instala Xamarin.iOS.

    > [!NOTE] 
    > Si prefiere no instalar Visual Studio para Mac, a partir de [Visual Studio 2017 versión 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Visual Studio 2017 puede configurar automáticamente el host de compilación de Mac con el software necesario para compilar aplicaciones de Xamarin.iOS. Para obtener más información, consulte [Aprovisionamiento automático de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Emparejar con Mac (conectar Visual Studio 2017 con el equipo Mac)

    Para que Visual Studio 2017 use las herramientas de compilación de iOS en el equipo Mac, los dos equipos deben estar conectados a través de una red.

    - [Lea la guía Emparejar con Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="summary"></a>Resumen

En este artículo, se ha descrito cómo configurar una máquina de Windows y su host de compilación de Mac asociado para el desarrollo de Xamarin.iOS.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Xamarin.iOS para Visual Studio](introduction-to-xamarin-ios-for-visual-studio.md)
- [Configuración de Visual Studio 2017](config-options.md)
- [Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)
