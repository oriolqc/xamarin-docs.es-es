---
title: Aplicación de Xamarin Player en vivo
description: Este documento describe la Xamarin Player en vivo en la aplicación, que puede utilizarse para obtener una vista previa de los cambios en el código en vivo en el dispositivo. Se tratan el programa de instalación, ejemplos, registros, configuración, administración de dispositivos y mucho más.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: topgenorth
ms.author: toopge
ms.date: 05/14/2017
ms.openlocfilehash: 88f7f62650484007c221aa7baaa684f872e0a8e9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794155"
---
# <a name="xamarin-live-player-app"></a>Aplicación de Xamarin Player en vivo

![Característica de vista previa](~/media/shared/preview.png)

Una vez haya instalado la aplicación en su teléfono, siga el [instrucciones de instalación](~/tools/live-player/install.md) para conectarse a su equipo. Pruebe una de las [aplicaciones de ejemplo](~/tools/live-player/samples.md) para que funcione.

En el inicio, la aplicación de Xamarin Player Live aspecto (en iOS y Android respectivamente):

![Captura de pantalla de aplicación de iOS de Reproductor de Live](player-images/app-iphone-sml.png) ![Captura de pantalla de aplicación Reproductor Android en vivo](player-images/app-android-sml.png)

Cuando se presiona **par de claves para Visual Studio**, use la cámara para escanear el código de barras que muestra en el equipo:

![Captura de pantalla del analizador de código de barras de iOS](player-images/scan-iphone-sml.png) ![Captura de pantalla del analizador de código de barras Android](player-images/scan-android-sml.png)

Si la conexión es correcta, el código debe ejecutarse casi de inmediato en el dispositivo (como el [ejemplo de la calculadora](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![Aplicación de calculadora de ejemplo con el dispositivo](player-images/basic-calculator-iphone-sml.png)

## <a name="options"></a>Opciones

Presione el botón información **(i)** en la parte inferior de la aplicación para revelar el **opciones** menú:

[![Captura de pantalla del menú de opciones](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Registros

Ver los registros para diagnosticar problemas.

### <a name="settings"></a>Configuración

- Alternar visualización de errores de compilación y en tiempo de ejecución.
- Información de versión.
- Envíe sus comentarios.

[![Captura de pantalla de la configuración](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>Administración de dispositivos

Para conectar un dispositivo por primera vez, siga las instrucciones que aparecen en [requisitos y el programa de instalación](~/tools/live-player/install.md). Puede asociar varios dispositivos (por ejemplo un iOS y un Android) y administrarlos a través del IDE.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En Visual Studio, elija **Herramientas > Xamarin Player Live > Administrar dispositivos...**

![Administrar la ventana de dispositivos](player-images/manage-tools-menu-vs.png)

Esta ventana permite hacer lo siguiente:

- Par de un nuevo dispositivo examinando el código
- O bien emparejar un dispositivo escribiendo el código que se muestra en su pantalla
- Quitar dispositivos existentes de la lista

También puede tener acceso a esta ventana desde la lista de dispositivos.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, elija **Herramientas > (Xamarin Player Live) administrar dispositivos...**

![Administrar la ventana de dispositivos](player-images/manage-tools-menu.png)

Esta ventana permite hacer lo siguiente:

- Par de un nuevo dispositivo examinando el código
- O bien emparejar un dispositivo escribiendo el código que se muestra en su pantalla
- Quitar dispositivos existentes de la lista

![Administrar la ventana de dispositivos](player-images/manage.png)

También puede tener acceso a esta ventana desde la lista de dispositivos:

![Elegir los dispositivos de Reproductor de Xamarin en directo de la lista de dispositivos](player-images/manage-device-menu.png)

-----

Si experimenta algún problema, vea [limitaciones y solución de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Vínculos relacionados

- [Limitaciones](~/tools/live-player/limitations.md)
- [Solución de problemas](~/tools/live-player/troubleshooting.md)
- [Ejemplos de Xamarin Live Player](samples.md)
