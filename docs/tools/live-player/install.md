---
title: Configuración de Xamarin Live Player Visual Studio
description: Este documento describe cómo usar Xamarin Live Player para realizar las modificaciones dinámicas en una aplicación en ejecución.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: a29a637526c2829b44ae89d505dac37a648dee77
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157738"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Configuración de Xamarin Live Player Visual Studio

![Características en versión preliminar](~/media/shared/preview.png)

> [!WARNING]
> La versión Xamarin Live Player preliminar finalizó. La aplicación ya no está disponible. Las instrucciones siguientes se proporcionan para los clientes siguen usando la versión preliminar con Visual Studio 2017.

> [!TIP]
> Puede usar el [controlador de vista previa de XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) de 2019 de Visual Studio o Visual Studio para Mac para ver los diseños de pantalla al editarlas.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>Uso de Xamarin Live Player

Ya debe tener la aplicación de Xamarin Live Player en el dispositivo. Ya no está disponible para descargar.

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

## <a name="using-xamarin-live-player"></a>Uso de Xamarin Live Player

Ya debe tener la aplicación de Xamarin Live Player en el dispositivo. Ya no está disponible para descargar.

1. Abra **Visual Studio para Mac**.
2. Vaya a **Visual Studio > Preferencias...**  y seleccione la pestaña **Proyectos > Xamarin Live Player (versión preliminar)** .
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

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
