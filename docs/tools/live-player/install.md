---
title: Configuración de Xamarin Live Player
description: Este documento describe cómo configurar la cuenta de Xamarin Live Player y usarlo para realizar las modificaciones dinámicas en una aplicación en ejecución.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: dd987b6d1a6db8e27544ddd95cdc219bb5f783b5
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526655"
---
# <a name="xamarin-live-player-setup"></a>Configuración de Xamarin Live Player

Xamarin Live Player le permite modificar el código de su aplicación y reflejar los cambios en el dispositivo en directo. El código se ejecuta dentro de la aplicación Xamarin Live Player, por lo que no es necesario configurar ningún emulador ni usar ningún cable para implementar su aplicación. En este artículo se describe cómo configurar Xamarin Live Player.


![Característica de vista previa](~/media/shared/preview.png)

## <a name="1-get-the-android-app"></a>1. Obtener la aplicación de Android

Xamarin Live Player está disponible para Android mediante Google Play:

[ ![Disponible en Google Play](install-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

ara dispositivos que usan Android sin Google Play, Xamarin Live Player está disponible mediante la distribución [HockeyApp](https://aka.ms/xlp-hockeyapp). Además, las primeras versiones preliminares para Android se pueden instalar directamente desde Google Play mediante la aceptación en los [programas de versión beta abierta](https://play.google.com/apps/testing/com.xamarin.live)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="2-get-visual-studio-2017"></a>2. Obtener Visual Studio 2017

Xamarin Live Player requiere:

- Visual Studio 2017 15.4 o versiones más recientes.
- Un equipo con Visual Studio y un dispositivo con Xamarin Live Player en la misma red Wi-Fi.

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Primer uso de Xamarin Live Player

1. Abra **Visual Studio 2017**.
2. Vaya a **Herramientas > Opciones...**  y seleccione la opción **Xamarin > otros**.
3. Seleccione **Habilitar Xamarin Live Player**:

    ![Seleccione la casilla Habilitar Xamarin Live Player en la ventana de opciones](install-images/vs2017-options.png)

4. Cree o abra un proyecto de Xamarin (o un [ejemplo](~/tools/live-player/samples.md)).
5. Elija **Live Player** en la lista de dispositivos:

    ![La lista de dispositivos incluye una opción de Xamarin Live Player](install-images/devices-empty-windows.png)

    - Si ya ha emparejado un dispositivo, este estará disponible como una opción.
    - En caso contrario, se le pedirá para emparejar un dispositivo cuando sea necesario.

6. Presione el botón **Ejecutar** o seleccione una de las siguientes opciones del menú **Ejecutar** o del menú contextual:

    - **Iniciar sin depurar** : puede editar la aplicación y ver los cambios que se producen en el dispositivo (la aplicación se reinicia cuando se realizan cambios y guarda el archivo).
    - **Iniciar depuración** : puede establecer puntos de interrupción e inspeccionar las variables, pero no se puede editar el código.

    Como alternativa, seleccione **Herramientas > Xamarin Live Player > Ejecutar vista actual en vivo**, que permite editar la aplicación y ver los cambios que se producen en el dispositivo. Se muestra la vista actual (en lugar de la pantalla principal de la aplicación).

7. Si un dispositivo ya está emparejado y se ejecuta la aplicación Xamarin Live Player en este, el código se ejecutará inmediatamente.

    Si no hay ningún dispositivo emparejado, aparecerá un código QR con instrucciones sobre cómo asociar uno:

    ![Ventana para emparejar un dispositivo](install-images/manage-empty-windows.png)

    Si no se puede conectar con el dispositivo de emparejamiento, puede aparecer un error.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="2-get-visual-studio-for-mac"></a>2. Obtenga Visual Studio para Mac

Xamarin Live Player requiere:

- OS X 10.11, macOS 10.12 o superior
- Visual Studio para Mac
- Un equipo Mac y un dispositivo en la misma red Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Primer uso de Xamarin Live Player

1. Abra **Visual Studio para Mac**.
2. Vaya a **Visual Studio > Preferencias...**  y seleccione la pestaña **Proyectos > Xamarin Live Player (versión preliminar)**.
3. Seleccione **Habilitar Xamarin Live Player**:

    [![Seleccione la casilla Habilitar Xamarin Live Player en la ventana de opciones](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. Cree o abra un proyecto de Xamarin (o un [ejemplo](~/tools/live-player/samples.md)).
5. Elija **Live Player** en la lista de dispositivos.

    ![La lista de dispositivos incluye una opción de Xamarin Live Player](install-images/devices.png)

    - Si ya ha emparejado un dispositivo, este estará disponible como una opción.
    - En caso contrario, se le pedirá para emparejar un dispositivo cuando sea necesario.
    - Elija **Dispositivos con Xamarin Live Player...**  para administrar los dispositivos que quiera usar con Xamarin Live Player.

6. Presione el botón **Ejecutar** o seleccione una de las siguientes opciones del menú **Ejecutar** o del menú contextual:

    ![Opciones del menú Ejecutar](install-images/run-menu.png)

    - **Iniciar sin depurar** : puede editar la aplicación y ver los cambios que se producen en el dispositivo (la aplicación se reinicia cuando se realizan cambios y guarda el archivo).
    - **Iniciar depuración** : puede establecer puntos de interrupción e inspeccionar las variables, pero no se puede editar el código.
    - **Ejecutar vista actual en vivo** : puede editar la aplicación y ver los cambios que se producen en el dispositivo. Se muestra la vista actual (en lugar de la pantalla principal de la aplicación).

7. Si un dispositivo ya está emparejado y se ejecuta la aplicación Xamarin Live Player en este, el código se ejecutará inmediatamente.

    Si no hay ningún dispositivo emparejado, aparecerá un código QR con instrucciones sobre cómo asociar uno:

    ![Ventana para emparejar un dispositivo](install-images/manage-empty.png)

    Si no se puede conectar el dispositivo para el emparejamiento, aparecerá un error:

    ![No se puede conectar al mensaje de error de dispositivo](install-images/error-cannot-connect.png)

-----

Si experimenta problemas o no se puede conectar, consulte [limitaciones y solución de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Vínculos relacionados

- [Limitaciones](~/tools/live-player/limitations.md)
- [Solución de problemas](~/tools/live-player/troubleshooting.md)
- [Ejemplos de Xamarin Live Player](~/tools/live-player/samples.md)
