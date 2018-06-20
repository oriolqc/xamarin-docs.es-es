---
title: Requisitos y la instalación de inspector
description: Este documento describe cómo instalar el Xamarin Inspector y describe los sistemas operativos, IDE y plataformas de aplicaciones.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: topgenorth
ms.author: toopge
ms.date: 06/19/2018
ms.openlocfilehash: f7c5217a9c2d3881ca29094c3186e448975db6a3
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268974"
---
# <a name="inspector-installation-and-requirements"></a>Requisitos y la instalación de inspector

## <a name="download-and-installation"></a>Descarga e instalación

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Descargue e instale [Visual Studio Enterprise](https://www.visualstudio.com/vs/) y seleccione la **desarrollo móvil con .NET** carga de trabajo.
1. [Inicie sesión en](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) para habilitar la suscripción de la empresa.
1. [Inspeccionar](~/tools/inspector/inspect.md) su propia aplicación.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Descargue e instale [Visual Studio para Mac](https://www.visualstudio.com/vs/mac/).
1. [Inicie sesión en](https://docs.microsoft.com/visualstudio/mac/activation) para habilitar la suscripción de la empresa.
1. [Inspeccionar](~/tools/inspector/inspect.md) su propia aplicación.

-----

## <a name="requirements"></a>Requisitos

### <a name="supported-operating-systems"></a>Sistemas operativos admitidos

- **Mac** -OS X 10.11 o superior
- **Windows** -Windows 7 o posterior (con Internet Explorer 11 o superior y .NET 4.6.1 o superior)

### <a name="supported-ides"></a>IDE compatible

- Visual Studio para Mac
- Visual Studio de 2017 con **desarrollo móvil con .NET** carga de trabajo

Inspección de la aplicación activa está disponible para los clientes de empresa.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Plataformas de aplicaciones compatibles

|Plataforma de aplicaciones|Compatibilidad con IDE|Notas|
|--- |--- |--- |
|Mac|Solo se admite en Visual Studio para Mac|
|iOS|Compatible con 2017 de Visual Studio y Visual Studio para Mac| |
|Android|Compatible con 2017 de Visual Studio y Visual Studio para Mac|Debe tener como destino Android > = 4.0.3, con **fastdev** habilitado.<br />Debe usar los emuladores de Google, Visual Studio o Xamarin Android. Los emuladores de 7 Android no se permita la inspección en este momento.|
|WPF|Solo se admite en Visual Studio de 2017|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Informes errores

Se deberían notificar errores directamente a través de Visual Studio:

- **Ayuda > Enviar comentarios > notificar un problema**

Incluya toda la información siguiente:

### <a name="platform-version-information"></a>Información de versión de plataforma

Esta información es vital.

Visual Studio para Mac

- **Visual Studio > sobre Visual Studio > Mostrar detalles > Copiar información**
- Pegue en el informe de errores

Programa para la mejora

- **Ayuda > acerca de Visual Studio > Copiar información**
- Háganos saber la versión del sistema operativo y si se ejecuta Windows de 32 bits o 64 bits.

### <a name="log-files"></a>Archivos de registro

Adjunte siempre IDE y Inspector de archivos de registro de cliente.

Cliente de inspector

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4. x también incluye la capacidad de seleccionar el archivo de registro en Finder (Mac OS) o en el explorador (Windows) directamente desde el menú principal:

- **Ayuda > revelar el archivo de registro**

Visual Studio para Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Programa para la mejora

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- El contenido de Visual Studio **salida** panel también se pueden proporcionar información útil.

### <a name="project-settings"></a>Configuración del proyecto

Si puede adjuntar el **.csproj** para el proyecto que desea inspeccionar, sería muy útil. Esto es más fácil que preguntando opciones de configuración individuales.

También confirme que está en la configuración de depuración.

### <a name="selected-devices"></a>Dispositivos seleccionados

Para iOS y Android, es vital que sabemos cuál es el dispositivo que está depurando en cuando desea inspeccionar. Necesitamos saber:

- Nombre del dispositivo tal como se muestra en el IDE
- Versión del sistema operativo del dispositivo
- Android: Compruebe que está usando un x86 emulador
- Android: ¿En qué plataforma emulador se está usando? ¿Emulador de Google? ¿Emulador de Android de Visual Studio? ¿Xamarin Android Player?
- ¿La aplicación que se está depurando correctamente aparecen y funcionando en el dispositivo?
- ¿El dispositivo tiene conectividad de red (comprobar a través del explorador web)?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
