---
title: Simulador de iOS remoto para Windows
description: El simulador de Windows de iOS remoto le permite probar sus aplicaciones en un simulador de iOS que se muestran en Windows junto con Visual Studio de 2017.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: topgenorth
ms.author: toopge
ms.date: 05/11/2018
ms.openlocfilehash: b07cc24e63f4aa3ce4451e3bdb5819f1df1058c6
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34149812"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulador de iOS remoto para Windows

El simulador de Windows de iOS remoto le permite probar sus aplicaciones en un simulador de iOS que se muestran en Windows junto con Visual Studio de 2017.

[![](ios-simulator-images/hero-sml.png "Simulador de iOS con Windows")](ios-simulator-images/hero.png#lightbox)

## <a name="getting-started"></a>Introducción

El simulador de iOS remoto para Windows se instala automáticamente como parte de Xamarin en Visual Studio de 2017. Para usarla, siga estos pasos:

1. [Par de 2017 Visual a un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. En Visual Studio de 2017, empiece a depurar un proyecto de iOS o tvOS. El simulador de iOS remoto para Windows aparecerá en el equipo de Windows.

## <a name="simulator-window"></a>Ventana del simulador

La barra de herramientas en la parte superior de la ventana del simulador contiene un número de botones útiles:

- **Inicio** – simula el botón Inicio de un dispositivo iOS
- **Bloqueo** : bloquea el simulador (deslice el dedo para desbloquear)
- **Captura de pantalla de** – guarda una captura de pantalla del simulador
- [**Configuración de** ](#settings) : muestra el teclado, la ubicación y otros valores de configuración
- [**Otras opciones** ](#other-options) – abre varias opciones de simulador como gestos de rotación y apretón

    [![](ios-simulator-images/maps-app-sml.png "ejemplo de mapas de simulador de iOS")](ios-simulator-images/maps-app.png#lightbox)

## <a name="settings"></a>Configuración

Al hacer clic en el icono de engranaje de la barra de herramientas se abre la **configuración** ventana:

[![](ios-simulator-images/settings-sml.png "configuración de simulador de iOS")](ios-simulator-images/settings.png#lightbox)

Estas opciones permiten habilitar el teclado de hardware, elija una ubicación que el dispositivo debe informe (estáticas y mover ubicaciones se admiten), habilitar Touch ID y restablecer el contenido y la configuración para el simulador.

## <a name="other-options"></a>Otras opciones

Botón de puntos suspensivos de la barra de herramientas revela otras opciones como rotación, movimientos de apretón y reiniciar el sistema. Estas mismas opciones pueden verse como una lista con el botón secundario en cualquier parte en la ventana del simulador:

[![](ios-simulator-images/more-sml.png "Opciones de configuración adicionales del simulador de iOS")](ios-simulator-images/more.png#lightbox)

## <a name="touchscreen-support"></a>Compatibilidad con pantalla táctil

Los equipos de Windows más modernos tienen pantallas táctiles. Puesto que el simulador de Windows de iOS remoto es compatible con las interacciones táctiles, puede probar la aplicación con el mismo acercar, deslice el dedo y gestos de toque del dedo varios que se utilizan con los dispositivos de E/s físicas.

Del mismo modo, el simulador de Windows de iOS remoto trata la entrada del lápiz de Windows como entrada de lápiz de Apple.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Deshabilitar el acceso remoto iOS Simulator para Windows

Para deshabilitar el acceso remoto iOS Simulator para Windows, vaya a **Herramientas > Opciones > Xamarin > configuración de iOS** y desactive la opción **simulador remoto a Windows**.

[![](ios-simulator-images/options-sml.png "casilla de verificación para usar el simulador")](ios-simulator-images/options.png#lightbox)

Con esta opción deshabilitada, depuración abre el simulador iOS en el Mac conectado cree de host.
