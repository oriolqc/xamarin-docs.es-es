---
title: Requisitos de Xamarin.Forms
description: Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2018
ms.openlocfilehash: 504f20f6575e559d7c4965643b74b407d5e84de8
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293332"
---
# <a name="xamarinforms-requirements"></a>Requisitos de Xamarin.Forms

_Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms._

Consulte el artículo [Instalación](~/cross-platform/get-started/installation/index.md) para obtener información general de las prácticas de instalación y configuración que se aplican entre plataformas.

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

![](installation-images/options-android-vs-sml.png "Opciones de compilación de Android en Visual Studio")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

**Compilar > General**

![](installation-images/options-general-sml.png "Compilar > General")

**Compilar > Aplicación de Android**

![](installation-images/options-android-sml.png "Compilar > Aplicación de Android")

-----

## <a name="development-system-requirements"></a>Requisitos del sistema de desarrollo

Las aplicaciones de Xamarin.Forms se pueden desarrollar en equipos macOS y Windows. Sin embargo, Windows y Visual Studio son necesarios para producir versiones de Windows de la aplicación.

## <a name="mac-system-requirements"></a>Requisitos del sistema de equipos Mac

Puede usar Visual Studio para Mac para desarrollar aplicaciones de Xamarin.Forms en OS X El Capitan (10.11) o en versiones posteriores. Para desarrollar aplicaciones de iOS, se recomienda tener instalado al menos el SDK de iOS 10 y Xcode 8.

> [!NOTE]
>  Las aplicaciones de Windows no se pueden desarrollar en macOS.

## <a name="windows-system-requirements"></a>Requisitos del sistema de equipos Windows

Las aplicaciones de Xamarin.Forms para iOS y Android se pueden compilar en cualquier instalación de Windows que admita el desarrollo de Xamarin. Para ello se necesita Visual Studio 2017 o una versión más reciente que se ejecute en Windows 7 o posterior. Se necesita un equipo Mac en red para el desarrollo de iOS.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

El desarrollo de aplicaciones de Xamarin.Forms para UWP requiere lo siguiente:

- Windows 10 (se recomienda Fall Creators Update)

- Visual Studio 2017

- [SDK de Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Los proyectos de UWP se incluyen en las soluciones de Xamarin.Forms creadas en Visual Studio 2017, pero no en las soluciones creadas en Visual Studio 2017 para Mac.
Puede [agregar una aplicación de la Plataforma universal de Windows (UWP)](~/xamarin-forms/platform/windows/installation/index.md) a una solución existente de Xamarin.Forms en cualquier momento.
