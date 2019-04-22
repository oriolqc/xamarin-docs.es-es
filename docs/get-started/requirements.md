---
title: Requisitos de Xamarin.Forms
description: Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2018
ms.openlocfilehash: 66785fcd4b38f29ca0358e1e8885e6b7da59f5a7
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854747"
---
# <a name="xamarinforms-requirements"></a>Requisitos de Xamarin.Forms

_Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms._

Consulte el artículo [Instalación](installation/index.md) para obtener información general de las prácticas de instalación y configuración que se aplican entre plataformas.

## <a name="target-platforms"></a>Plataformas de destino

Las aplicaciones de Xamarin.Forms pueden escribirse para los siguientes sistemas operativos:

- iOS 8 o versiones posteriores
- Android 4.4 (API 19) o versiones posteriores ([más detalles](#android))
- Plataforma universal de Windows de Windows 10 ([más detalles](#windows10))

Se presupone que los desarrolladores están familiarizados con [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) y [Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md).

### <a name="additional-platform-support"></a>Compatibilidad con plataforma adicional

El estado de estas plataformas está disponible en el [GitHub de Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support):

- Samsung Tizen
- macOS
- GTK#
- WPF

### <a name="platforms-from-earlier-versions"></a>Plataformas de versiones anteriores

No se admiten estas plataformas cuando se usa Xamarin.Forms 3.0:

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*

### <a name="android"></a>Android

Debe tener instalada la plataforma más reciente de Android SDK Tools y de la API de Android. Puede actualizar a las versiones más recientes con [Android SDK Manager](~/android/get-started/installation/android-sdk.md).

Además, la versión de compilación o de destino de los proyectos de Android **debe** establecerse en *Usar la última plataforma instalada*, aunque la versión mínima se puede establecer en API 19 para que pueda seguir admitiendo dispositivos que usen Android 4.4 y versiones más recientes. Estos valores se establecen en **Opciones del proyecto**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

**Opciones de proyecto > Aplicación > Propiedades de la aplicación**

![Opciones de compilación de Android en Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

**Compilar > General**

![Seleccione la última versión de framework de destino](requirements-images/options-general-sml.png)

**Compilar > Aplicación de Android**

![Seleccione como mínimo y las versiones de Android para la aplicación de destino](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>Requisitos del sistema de desarrollo

Las aplicaciones de Xamarin.Forms se pueden desarrollar en equipos macOS y Windows. Sin embargo, Windows y Visual Studio son necesarios para producir versiones de Windows de la aplicación.

## <a name="mac-system-requirements"></a>Requisitos del sistema de equipos Mac

Puede usar Visual Studio para Mac para desarrollar aplicaciones de Xamarin.Forms en macOS High Sierra (10.13) o una versión posterior. Para desarrollar aplicaciones de iOS, se recomienda tener al menos el SDK de iOS 10 y Xcode 9 instalado.

> [!NOTE]
>  Las aplicaciones de Windows no se pueden desarrollar en macOS.

## <a name="windows-system-requirements"></a>Requisitos del sistema de equipos Windows

Las aplicaciones de Xamarin.Forms para iOS y Android se pueden compilar en cualquier instalación de Windows que admita el desarrollo de Xamarin. Para ello se necesita Visual Studio 2017 o una versión más reciente que se ejecute en Windows 7 o posterior. Se necesita un equipo Mac en red para el desarrollo de iOS.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

El desarrollo de aplicaciones de Xamarin.Forms para UWP requiere lo siguiente:

- Windows 10 (versión más reciente recomendada, mínimo Fall Creators Update)

- Visual Studio de 2019 recomendado (Visual Studio 2017 versión 15,8 mínimo)

- [SDK de Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Puede [agregar una aplicación de la Plataforma universal de Windows (UWP)](~/xamarin-forms/platform/windows/installation/index.md) a una solución existente de Xamarin.Forms en cualquier momento.
