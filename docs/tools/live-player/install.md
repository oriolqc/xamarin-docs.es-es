---
title: "Programa de instalación de Xamarin Player en vivo"
description: Editar y probar aplicaciones en tiempo real en el dispositivo iOS o Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/22/2017
ms.openlocfilehash: f3fa2502f55ad7ebf59d015923ff4008103b08bd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-live-player-setup"></a>Programa de instalación de Xamarin Player en vivo

Xamarin Player en directo le permite realizar modificaciones en vivo en la aplicación y reflejarán los cambios en vivo en el dispositivo. El código se ejecuta dentro de la aplicación de Xamarin Player Live: no es necesario para configurar los emuladores o usar cables para implementar. Este artículo describe cómo configurar el Reproductor de Xamarin en vivo.

![Característica de vista previa](~/media/shared/preview.png)

## <a name="1-get-the-app"></a>1. Obtener la aplicación

### <a name="xamarin-live-player-for-android"></a>Xamarin Player en vivo para Android
Xamarin Player en vivo está disponible para Android desde Google Play:

[ ![Disponible en Google Play](install-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

Para dispositivos Android sin Google Play está disponible a través de Xamarin Player Live [HockeyApp](https://aka.ms/xlp-hockeyapp) distribución. Además, anticipo genera para Android se puede instalar directamente desde Google Play mediante la aceptación de los [programa beta abierta](https://play.google.com/apps/testing/com.xamarin.live)

### <a name="xamarin-live-player-for-ios"></a>Xamarin Player Live para iOS
Le animamos a los usuarios unir el [aplicación Xamarin Player Live _iOS Preview_ ](https://aka.ms/liveplayeralpha) para disfrutar de un acceso rápido a las mejoras más recientes a través de TestFlight.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="2-get-visual-studio-2017-preview-on-windows-or-for-mactabsvsmac"></a>2. Obtener vista previa de 2017 de Visual Studio en Windows (o [para Mac](?tabs=vsmac))

Xamarin Player en vivo requiere:

- Visual Studio de 2017 [15.4](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/#visualstudio2017) u otra más reciente.
- Un equipo de Visual Studio y un dispositivo en la misma red Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Uso de Xamarin Player en vivo por primera vez

1. Abra **2017 de Visual Studio**.
2. Vaya a **Herramientas > Opciones...**  y seleccione la **Xamarin > otros** ficha.
3. Graduación **habilitar Xamarin Player en vivo**:

  ![Active la casilla Habilitar Xamarin Player en directo en la ventana de opciones](install-images/vs2017-options.png)

2. Cree o abra un proyecto de Xamarin (o [ejemplo](~/tools/live-player/samples.md)).
3. Elija **Live Reproductor** en la lista de dispositivos:

  ![Lista de dispositivos incluye una opción de Xamarin Player en vivo](install-images/devices-empty-windows.png)

  * Si ya ha emparejado un dispositivo, estará disponible como una opción.
  * En caso contrario, se le pedirá para emparejar un dispositivo cuando sea necesario.
4. Presione el **ejecutar** botón, o seleccione una de las siguientes opciones de la **ejecutar** o menú contextual:

  - **Iniciar sin depurar** : puede editar la aplicación y ver los cambios se producen en el dispositivo (la aplicación se reinicia cuando se realizan cambios y guardar el archivo).
  - **Iniciar depuración** : puede establecer puntos de interrupción e inspeccionar las variables, pero no se puede editar el código.

  Como alternativa, seleccione **Herramientas > Xamarin Player Live > Live vista actual ejecutar**, que permite editar la aplicación y ver los cambios se producen en el dispositivo. Se muestra la vista actual (en lugar de la pantalla principal de la aplicación).

5. Si un dispositivo ya está emparejado y se ejecuta la aplicación de Xamarin Player en vivo en el dispositivo, el código se ejecutará inmediatamente.

  Si no hay ningún dispositivo emparejada, aparecerá un código QR con instrucciones sobre cómo asociar un dispositivo:

    ![Par de una ventana de dispositivo](install-images/manage-empty-windows.png)

  Si no se puede conectar con el dispositivo para el emparejamiento, puede aparecer un error.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="2-get-visual-studio-for-mac-or-for-windowstabsvswin"></a>2. Obtener Visual Studio para Mac (o [para Windows](?tabs=vswin))

Xamarin Player en vivo requiere:

- OS X 10.11 macOS 10.12 o superior
- Visual Studio para Mac
- Un equipo Mac y un dispositivo en la misma red Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. Uso de Xamarin Player en vivo por primera vez

1. Abra **de Visual Studio para Mac**.
2. Vaya a **Visual Studio > Preferencias...**  y seleccione la **proyectos > Xamarin Player en vivo (versión preliminar)** ficha.
3. Graduación **habilitar Xamarin Player en vivo**:

  [ ![Active la casilla Habilitar Xamarin Player en directo en la ventana de opciones](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png)

2. Cree o abra un proyecto de Xamarin (o [ejemplo](~/tools/live-player/samples.md)).
3. Elija **Live Reproductor** en la lista de dispositivos.

  ![Lista de dispositivos incluye una opción de Xamarin Player en vivo](install-images/devices.png)

  * Si ya ha emparejado un dispositivo, estará disponible como una opción.
  * En caso contrario, se le pedirá para emparejar un dispositivo cuando sea necesario.
  * Elija **Xamarin Player en vivo dispositivos...**  para administrar los dispositivos que se va a utilizar con el Reproductor de Xamarin en vivo.

4. Presione el **ejecutar** botón, o seleccione una de las siguientes opciones de la **ejecutar** o menú contextual:

  ![Opciones de menú de ejecución](install-images/run-menu.png)

  - **Iniciar sin depurar** : puede editar la aplicación y ver los cambios se producen en el dispositivo (la aplicación se reinicia cuando se realizan cambios y guardar el archivo).
  - **Iniciar depuración** : puede establecer puntos de interrupción e inspeccionar las variables, pero no se puede editar el código.
  - **Ejecutar la vista actual de Live** : puede editar la aplicación y ver los cambios se producen en el dispositivo. Se muestra la vista actual (en lugar de la pantalla principal de la aplicación).

5. Si un dispositivo ya está emparejado y se ejecuta la aplicación de Xamarin Player en vivo en el dispositivo, el código se ejecutará inmediatamente.

  Si no hay ningún dispositivo emparejada, aparecerá un código QR con instrucciones sobre cómo asociar un dispositivo:

    ![Par de una ventana de dispositivo](install-images/manage-empty.png)

  Si no se puede conectar con el dispositivo para el emparejamiento, aparecerá un error:

    ![No se puede conectar al mensaje de error de dispositivo](install-images/error-cannot-connect.png)


-----

Si experimenta problemas, consulte [limitaciones y solución de problemas](~/tools/live-player/troubleshooting.md).


## <a name="related-links"></a>Vínculos relacionados

- [Limitaciones](~/tools/live-player/limitations.md)
- [Solución de problemas](~/tools/live-player/troubleshooting.md)
- [Ejemplos de Xamarin Player en vivo](~/tools/livehttps://developer.xamarin.com/samples.md)
