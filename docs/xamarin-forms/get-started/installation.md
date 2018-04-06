---
title: Requisitos de Xamarin.Forms
description: Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/19/2017
ms.openlocfilehash: e62c82b351bab759192a4fe879a3b63754cdf0af
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-requirements"></a>Requisitos de Xamarin.Forms

_Requisitos de sistema de desarrollo y plataformas para Xamarin.Forms._

Consulte el artículo [Instalación](~/cross-platform/get-started/installation/index.md) para obtener información general de las prácticas de instalación y configuración que se aplican entre plataformas.

## <a name="target-platforms"></a>Plataformas de destino

Las aplicaciones de Xamarin.Forms pueden escribirse para los siguientes sistemas operativos:

-  iOS 8 o versiones posteriores
-  Android 4.0.3 (API 15) o versiones posteriores ([más detalles](#android))
-  Plataforma universal de Windows de Windows 10 ([más detalles](#windows10))
-  Windows 8.1/Windows Phone 8.1 WinRT ([más detalles](#windows))
-  *Windows Phone 8 Silverlight (EN DESUSO)*

Se presupone que los desarrolladores están familiarizados con [Portable Class Libraries (Bibliotecas de clases portables)](~/cross-platform/app-fundamentals/pcl.md) y [Shared Projects (Proyectos compartidos)](~/cross-platform/app-fundamentals/shared-projects.md).

<a name="android" />

### <a name="android"></a>Android

Debe tener instalada la plataforma más reciente de Android SDK Tools y de la API de Android. Puede actualizar a las versiones más recientes con [Android SDK Manager](~/android/get-started/installation/android-sdk.md).

Además, la versión de compilación o de destino de los proyectos de Android **debe** establecerse en *Usar la última plataforma instalada*, aunque la versión mínima se puede establecer en API 15 para que pueda seguir admitiendo dispositivos que usen Android 4.0.3 y versiones más recientes. Estos valores se establecen en **Opciones del proyecto**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

**Opciones de proyecto > Aplicación > Propiedades de la aplicación**

![](installation-images/options-android-vs-sml.png "Opciones de compilación de Android en Visual Studio")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

**Compilar > General**

![](installation-images/options-general-sml.png "Compilar > General")

**Compilar > Aplicación de Android**

![](installation-images/options-android-sml.png "Compilar > Aplicación de Android")

-----


<a name="windows10" />

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Los proyectos de UWP de Windows 10 no se agregan al crear una solución en macOS. Para obtener instrucciones sobre cómo agregar estos proyectos a una solución existente, vea [Adding a Universal Windows Platform (UWP) App](~/xamarin-forms/platform/windows/installation/universal.md) (Agregar una aplicación de la Plataforma universal de Windows [UWP]).


<a name="windows" />

### <a name="windows-81--windows-phone-81-winrt"></a>Windows 8.1/Windows Phone 8.1 WinRT

Los proyectos de Windows 8.1/Windows Phone 8.1 WinRT no se agregan al crear una solución en macOS. Para obtener instrucciones sobre cómo agregar estos proyectos a una solución existente, vea [Adding a Windows Phone App](~/xamarin-forms/platform/windows/installation/phone.md) (Agregar una aplicación de Windows Phone) y [Adding a Windows App](~/xamarin-forms/platform/windows/installation/tablet.md) (Agregar una aplicación de Windows).


## <a name="development-system-requirements"></a>Requisitos del sistema de desarrollo

Las aplicaciones de Xamarin.Forms se pueden desarrollar en equipos macOS y Windows. Sin embargo, Windows y Visual Studio son necesarios para producir versiones de Windows de la aplicación.

## <a name="mac-system-requirements"></a>Requisitos del sistema de equipos Mac

Puede usar Visual Studio para Mac para desarrollar aplicaciones de Xamarin.Forms en OS X El Capitan (10.11) o en versiones posteriores. Para desarrollar aplicaciones de iOS, se recomienda tener instalado al menos el SDK de iOS 10 y Xcode 8.

> [!NOTE]
>  Las aplicaciones de Windows no se pueden desarrollar en macOS.

## <a name="windows-system-requirements"></a>Requisitos del sistema de equipos Windows

Las aplicaciones de Xamarin.Forms para iOS y Android se pueden compilar en cualquier instalación de Windows que admita el desarrollo de Xamarin. Para ello se necesita Visual Studio 2015 o una versión más reciente que se ejecute en Windows 7 o posterior. Se necesita un equipo Mac en red para el desarrollo de iOS.

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

El desarrollo de aplicaciones de Xamarin.Forms para UWP requiere lo siguiente:

* Windows 10 (se recomienda Fall Creators Update)

* Se recomienda Visual Studio 2017

* [SDK de Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Los proyectos de UWP se incluyen en las soluciones de Xamarin.Forms creadas en Visual Studio 2015 y Visual Studio 2017.
También puede [agregar una aplicación de la Plataforma universal de Windows (UWP)](~/xamarin-forms/platform/windows/installation/universal.md) a una solución existente de Xamarin.Forms.

