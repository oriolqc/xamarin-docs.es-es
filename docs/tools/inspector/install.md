---
title: Los requisitos e instalación de inspector
description: Este documento describe cómo instalar Xamarin Inspector y sistema operativo compatible, IDE y plataformas de aplicaciones.
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2bbf0bda42b7bce483d9d036ebf39314dcb73072
ms.sourcegitcommit: fabd3b51dca6d904009d0670137c5fb5ee6c32ef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249195"
---
# <a name="inspector-installation-and-requirements"></a>Los requisitos e instalación de inspector

## <a name="download-and-installation"></a>Descarga e instalación

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Descargue e instale [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) y seleccione el **desarrollo móvil con .NET** carga de trabajo.
1. [Inicie sesión en](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio) para habilitar la suscripción Enterprise.
1. [Inspeccionar](~/tools/inspector/inspect.md) su propia aplicación.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Descargue e instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).
1. [Inicie sesión en](https://docs.microsoft.com/visualstudio/mac/activation) para habilitar la suscripción Enterprise.
1. [Inspeccionar](~/tools/inspector/inspect.md) su propia aplicación.

-----

## <a name="requirements"></a>Requisitos

### <a name="supported-operating-systems"></a>Sistemas operativos admitidos

- **Mac** -OS X 10.11 o posterior
- **Windows** -Windows 7 o superior (con Internet Explorer 11 o posterior y .NET 4.6.1 o posterior)

### <a name="supported-ides"></a>IDE compatibles

- Visual Studio para Mac
- Visual Studio 2017 con **desarrollo móvil con .NET** carga de trabajo

Inspección de la aplicación activa está disponible para los clientes empresariales.

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>Plataformas de aplicaciones compatibles

|Plataforma de aplicaciones|Compatibilidad con IDE|Notas|
|--- |--- |--- |
|Mac|Solo se admiten en Visual Studio para Mac|
|iOS|Compatible en Visual Studio 2017 y Visual Studio para Mac| Comportamiento del enlazador debe establecerse en **no vincular** (bajo **compilación de iOS** opciones del proyecto) |
|Android|Compatible en Visual Studio 2017 y Visual Studio para Mac|Debe tener como destino Android > = 4.0.3, con **fastdev** habilitado.<br />Debe usar los emuladores de Google, Visual Studio o Xamarin Android. Los emuladores de Android 7 no pueden permitir que la inspección en este momento.|
|WPF|Solo se admite en Visual Studio 2017|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>Informar de errores

Directamente a través de Visual Studio, se deben notificar los errores:

- **Ayuda > Enviar comentarios > notificar un problema**

Incluya toda la información siguiente:

### <a name="platform-version-information"></a>Información de versión de plataforma

Esta información es vital.

Visual Studio para Mac

- **Visual Studio > acerca de Visual Studio > Mostrar detalles > Copiar información**
- Pegue en el informe de errores

Programa para la mejora

- **Ayuda > acerca de Visual Studio > Copiar información**
- Háganos saber la versión del sistema operativo y si se están ejecutando Windows de 32 bits o 64 bits.

### <a name="log-files"></a>Archivos de registro

Adjunte siempre IDE e Inspector de archivos de registro de cliente.

Cliente de inspector

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4. x también incluye la capacidad para seleccionar el archivo de registro en el buscador (macOS) o el explorador (Windows) directamente desde el menú principal:

- **Ayudar a > Mostrar archivo de registro**

Visual Studio para Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Programa para la mejora

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- El contenido de Visual Studio **salida** panel también puede ser informativo.

### <a name="project-settings"></a>Configuración del proyecto

Si se puede adjuntar el **.csproj** para el proyecto está intentando inspeccionar, que sería muy útil. Esto es más fácil que preguntar sobre configuraciones individuales.

También confirme que está en la configuración de depuración.

### <a name="selected-devices"></a>Dispositivos seleccionados

Para iOS y Android, es vital que sabemos qué dispositivo está depurando en cuando desea inspeccionar. Se necesita conocer:

- Nombre del dispositivo tal como se muestra en el IDE
- Versión del sistema operativo del dispositivo
- Android: Compruebe que está usando un x86 emulador
- Android: ¿Qué plataforma emulador usa? ¿Emulador de Google? ¿Emulador de Android de Visual Studio? ¿Xamarin Android Player?
- ¿La aplicación que está depurando correctamente aparecerá y funcionará en el dispositivo?
- ¿Tiene conectividad de red (comprobar a través del explorador web) en el dispositivo?

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
