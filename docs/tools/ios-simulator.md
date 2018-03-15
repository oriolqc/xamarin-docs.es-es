---
title: "Envía de forma remota iOS Simulator (para Windows)"
description: Probar y depurar aplicaciones de iOS completamente dentro de Visual Studio en Windows
ms.topic: article
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/07/2017
ms.openlocfilehash: 20e8d843b73fca5eb49da21a7f834ebbb52f4aa9
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="remoted-ios-simulator-for-windows"></a>Envía de forma remota iOS Simulator (para Windows)

_Probar y depurar aplicaciones de iOS completamente dentro de Visual Studio en Windows_

[![](ios-simulator-images/hero-sml.png "Simulador de iOS con Windows")](ios-simulator-images/hero.png#lightbox)

## <a name="download-and-install"></a>Descargue e instale

Descargue el [installer](https://dl.xamarin.com/xamarin-simulator/Xamarin.Simulator.Installer.msi) e instalar en el equipo de Windows. Ya se debe instalar Visual Studio Tools para Xamarin.

> [!NOTE]
> Uso remoto de iOS simulador requiere Visual Studio un Mac conectado en red con Xamarin instalado.

## <a name="getting-started"></a>Introducción

Para usar el simulador de iOS remoto:

1. Asegúrese de que Visual Studio se ha conectado a su equipo Mac al menos una vez antes de iniciar el simulador de iOS remoto.
2. Asegúrese de que es una aplicación de iOS o tvOS el **proyecto de inicio** e iniciar la depuración.

Puede deshabilitar el simulador de iOS remoto de **Herramientas > Opciones > Xamarin > configuración de iOS** para ello, desactive la casilla de **simulador remoto a Windows** se muestra aquí:

[![](ios-simulator-images/options-sml.png "casilla de verificación para usar el simulador")](ios-simulator-images/options.png#lightbox)

El simulador de iOS, a continuación, se abrirá en el equipo Mac conectado. Seleccione esta opción para volver a activar el simulador de iOS remoto.

## <a name="features"></a>Características

El simulador de iOS remoto proporciona una manera de probar y depurar aplicaciones de iOS en el simulador completamente desde Visual Studio en Windows.

### <a name="simulator-window"></a>Ventana del simulador

La barra de herramientas de la ventana incluye una serie de botones que deben interactuar con el simulador:

- **Inicio** – simula el botón Inicio en el dispositivo.
- **Bloqueo** : bloquea el simulador (que puede deslizar rápidamente para desbloquear).
- **Captura de pantalla de** – guarda una captura de pantalla del simulador en el disco.
- [**Configuración de** ](#settings) : configurar la ubicación y el teclado.
- Otros [ **opciones** ](#options) – una variedad de opciones de simulador están disponible como apretón girar, o invocar otros Estados en el simulador. Cuando se ocultan algunas opciones, puede tener acceso desde el icono de puntos suspensivos que aparece en la barra de herramientas, o con el botón secundario en la ventana.

    [![](ios-simulator-images/maps-app-sml.png "ejemplo de mapas de simulador de iOS")](ios-simulator-images/maps-app.png#lightbox)


### <a name="settings"></a>Configuración

El icono de "engranaje" se abre la **configuración** ventana:

[![](ios-simulator-images/settings-sml.png "configuración de simulador de iOS")](ios-simulator-images/settings.png#lightbox)

Esto permite habilitar el teclado de hardware en el simulador y elegir qué ubicación se notifica en el dispositivo (incluidos una ubicación estática, u otras opciones de ubicación móvil).



### <a name="other-options"></a>Otras opciones

Haga clic en cualquier lugar en la ventana del simulador para ver todas las opciones disponibles en el simulador, como la rotación, desencadenar un gesto de apretón y reiniciando el simulador:

[![](ios-simulator-images/more-sml.png "Opciones de configuración adicionales del simulador de iOS")](ios-simulator-images/more.png#lightbox)

### <a name="touchscreen-support"></a>Compatibilidad con pantalla táctil

Los equipos de Windows más modernos tienen pantallas táctiles y el simulador de iOS remoto le permite tocan la ventana del simulador para probar las interacciones del usuario en la aplicación de iOS.

Esto incluye aprietan, Deslizar rápidamente y gestos de toque con un dedo de varios - cosas que anteriormente solo podrán fácilmente probarse en dispositivos físicos.

Compatibilidad con el lápiz en Windows también se traduce a la entrada del lápiz de Apple en el simulador.

<!--
<a name="knownissues" />

# Known Issues

 - Apple Watch devices may show in the Visual Studio device list, but are not yet supported.
 - Launching in **Release** mode may also start Apple’s simulator on the networked Mac.
 - Closing the remote iOS Simulator on Windows will not immediately stop debugging in Visual Studio. Stop debugging manually from the menu or the red button.
 - Opening too many different simulators simultaneously will produce unexpected results.
 - Exception of type `Foundation.NSErrorException` may be thrown while launching Simulators. Workaround is to kill csproxy (server process) on the Mac host and re-deploy to the simulator.
 - Performance may be slower when using Xcode 8
-->
