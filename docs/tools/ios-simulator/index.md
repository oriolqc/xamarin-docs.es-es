---
title: Simulador de iOS remoto para Windows
description: El simulador de Windows remoto de iOS le permite probar las aplicaciones en un simulador de iOS que se muestran en Windows junto con Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: lobrien
ms.author: laobri
ms.date: 11/14/2018
ms.openlocfilehash: 21229bc9d56865d6365f8ddfe4dd2046fbabc03d
ms.sourcegitcommit: ae34d048aeb23a99678ae768cdeef0c92ca36b51
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2018
ms.locfileid: "51681532"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulador de iOS remoto para Windows

El simulador de Windows remoto de iOS le permite probar las aplicaciones en un simulador de iOS que se muestran en Windows junto con Visual Studio 2017.

[![](images/hero-sml.png "Simulador de iOS que se ejecutan en Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Introducción

El simulador de iOS remoto para Windows se instala automáticamente como parte de Xamarin en Visual Studio 2017. Para ello, siga estos pasos:

1. [Emparejar Visual 2017 a un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. En Visual Studio 2017, inicie la depuración de un proyecto de iOS o tvOS. El simulador de iOS remoto para Windows aparecerá en el equipo de Windows.

Inspección [este vídeo](deploy.md) para obtener una guía paso a paso.

## <a name="simulator-window"></a>Ventana del simulador

La barra de herramientas en la parte superior de la ventana del simulador contiene un número de botones útiles:

- **Inicio** : simula el botón Inicio de un dispositivo iOS.
- **Bloqueo** : bloquea el simulador (pasar el dedo para desbloquear).
- **Captura de pantalla** – guarda una captura de pantalla del simulador (almacenados en **Pictures\Xamarin\iOS simulador\**).
- [**Configuración de** ](#settings) : muestra el teclado, ubicación y otras opciones.
- [**Otras opciones** ](#other-options) – abrirá varias opciones de simulador como rotación, gestos de agitar y Touch Id.

    [![](images/maps-app-sml.png "ejemplo de mapas de simulador de iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Configuración

Al hacer clic en el icono de engranaje de la barra de herramientas se abre el **configuración** ventana:

[![](images/settings-sml.png "configuración del simulador de iOS")](images/settings.png#lightbox)

Estas opciones permiten habilitar el teclado de hardware, elija una ubicación que el dispositivo debe informe (estáticas y mover las ubicaciones se admiten), habilitar Touch ID y restablecer el contenido y la configuración para el simulador.

## <a name="other-options"></a>Otras opciones

Botón de puntos suspensivos de la barra de herramientas revela otras opciones, como la rotación, gestos de agitar y reiniciar el sistema. Estas mismas opciones pueden verse como una lista con el botón secundario en cualquier lugar en la ventana del simulador:

[![](images/more-sml.png "configuración adicional del simulador de iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Compatibilidad con pantalla táctil

Los equipos de Windows más modernos tienen pantallas táctiles. Puesto que el simulador de Windows remoto de iOS es compatible con las interacciones táctiles, puede probar la aplicación con el mismo pinch, deslice el dedo y los gestos de toque del dedo múltiples que usa con dispositivos iOS físicos.

De forma similar, el simulador de Windows remoto de iOS trata la entrada de lápiz de Windows como entrada de lápiz de Apple.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Deshabilitar el simulador de Windows remoto de iOS

Para deshabilitar el simulador de Windows remoto de iOS, vaya a **Herramientas > Opciones > Xamarin > configuración de iOS** y desactive la opción **Remote Simulator en Windows**.

[![](images/options-sml.png "casilla de verificación para usar el simulador")](images/options.png#lightbox)

Con esta opción deshabilitada, la depuración se abre el simulador iOS en el equipo Mac conectado host de compilación.
