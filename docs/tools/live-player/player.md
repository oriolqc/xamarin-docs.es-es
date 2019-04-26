---
title: Aplicación Xamarin Live Player
description: Este documento describe la aplicación, que puede usarse para obtener una vista previa de cambios de código en vivo en dispositivos Xamarin Live Player. Describe el programa de instalación, ejemplos, los registros, configuración, administración de dispositivos y mucho más.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: lobrien
ms.author: laobri
ms.date: 08/08/2017
ms.openlocfilehash: 89795e5df00b426c0f11c04a0844993071df1e25
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61279518"
---
# <a name="xamarin-live-player-app"></a>Aplicación Xamarin Live Player

![Características en versión preliminar](~/media/shared/preview.png)

> [!NOTE]
> La vista previa activa el Reproductor sólo está disponible en Visual Studio 2017.

Una vez que haya instalado la aplicación en su teléfono, siga el [instrucciones de instalación](~/tools/live-player/install.md) para conectarse a su equipo. Pruebe uno de los [aplicaciones de ejemplo](~/tools/live-player/samples.md) para que funcione.

En el inicio, la aplicación Xamarin Live Player tiene este aspecto:

![Captura de pantalla de aplicación Android Player en vivo](player-images/app-android-sml.png)

Al presionar **emparejar con Visual Studio**, use la cámara para escanear el código de barras que muestra en el equipo:

![Captura de pantalla del analizador de código de barras de Android](player-images/scan-android-sml.png)

Si la conexión es correcta, el código debe ejecutarse en el dispositivo casi de inmediato (como el [ejemplo Calculator](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![Aplicación de calculadora de ejemplo que se ejecutan en el dispositivo](player-images/basic-calculator-sml.png)

## <a name="options"></a>Opciones

Presione el botón información **(i)** en la parte inferior de la aplicación para que se muestre el **opciones** menú:

[![Captura de pantalla del menú Opciones](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Registros

Visualización de registros para diagnosticar problemas.

### <a name="settings"></a>Configuración

- Alternar visualización de errores de compilación y tiempo de ejecución.
- Información de versión.
- Envíe sus comentarios.

[![Captura de pantalla de la configuración](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>Administración de dispositivos

Para conectar un dispositivo por primera vez, siga las instrucciones de [requisitos & programa de instalación](~/tools/live-player/install.md). Puede emparejar varios dispositivos y administrarlos a través del IDE.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En Visual Studio, elija **Herramientas > Xamarin Live Player > Administrar dispositivos...**

![Administrar la ventana de dispositivos](player-images/manage-tools-menu-vs.png)

Esta ventana le permite hacer lo siguiente:

- Emparejamiento de un dispositivo nuevo examinando el código
- O bien emparejar un dispositivo escribiendo el código se muestra en la pantalla
- Quitar dispositivos existentes de la lista

También puede acceder a esta ventana de la lista de dispositivos.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, elija **Herramientas > (Xamarin Live Player) administrar dispositivos...**

![Administrar la ventana de dispositivos](player-images/manage-tools-menu.png)

Esta ventana le permite hacer lo siguiente:

- Emparejamiento de un dispositivo nuevo examinando el código
- O bien emparejar un dispositivo escribiendo el código se muestra en la pantalla
- Quitar dispositivos existentes de la lista

![Administrar la ventana de dispositivos](player-images/manage.png)

También puede acceder a esta ventana de la lista de dispositivos:

![Elija los dispositivos de Xamarin Live Player en la lista de dispositivos](player-images/manage-device-menu.png)

-----

Si experimenta algún problema, vea [limitaciones y solución de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Vínculos relacionados

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
- [Ejemplos de uso con Live Player](https://developer.xamarin.com/samples/xamarin-live-player/all/)
