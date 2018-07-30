---
title: Requisitos del sistema
description: En este documento se enumeran los requisitos del sistema para compilar aplicaciones con Xamarin en equipos Mac y Windows. Además, contiene vínculos a instrucciones de instalación.
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: conceptdev
ms.author: crdun
ms.date: 07/24/2018
ms.openlocfilehash: 6d16f01965b6b3bcba35cf14d4000f53a4400653
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241983"
---
# <a name="system-requirements"></a>Requisitos del sistema

Los productos de Xamarin dependen de los SDK de la plataforma de Apple y Google para fijar como destino iOS o Android para que nuestros requisitos del sistema coincidan con los suyos. Esta página describe la compatibilidad del sistema para la plataforma Xamarin, y el entorno de desarrollo y versiones del SDK recomendados.

- [Entornos de desarrollo](#devenv)
- [Requisitos de macOS](#mac)
- [Requisitos de Windows](#windows)

Visite las [instrucciones de instalación](#install) para obtener más información sobre cómo obtener el software y los SDK necesarios.

<a name="devenv" />

## <a name="development-environments"></a>Entornos de desarrollo

Esta tabla muestra qué plataformas se pueden crear con diferentes combinaciones de sistema operativo y herramientas de desarrollo:

[!include[](~/cross-platform/includes/development-environment.md)]


> [!NOTE]
> Para desarrollar para iOS en equipos Windows debe haber un [equipo Mac accesible en la red](~/ios/get-started/installation/windows/connecting-to-mac/index.md), para la depuración y compilación remota. Esto también funciona si ejecuta Visual Studio dentro de una máquina virtual de Windows en un equipo Mac.

<a name="mac" />

## <a name="macos-requirements"></a>Requisitos de macOS

El uso de un equipo Mac para el desarrollo de Xamarin requiere las siguientes versiones de software o del SDK. Compruebe la versión de sistema operativo y siga las instrucciones para el [instalador de Xamarin](#install).

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> Xcode se puede instalar (y actualizar) en [developer.apple.com](https://developer.apple.com/xcode/download/) o a través de Mac App Store.

### <a name="testing--debugging-on-macos"></a>Pruebas y depuración en macOS

Se pueden implementar aplicaciones Xamarin para dispositivos móviles en dispositivos físicos a través de USB para probar y depurar (las aplicaciones de Xamarin.Mac se pueden probar directamente en el equipo de desarrollo, las aplicaciones de Apple Watch se implementan en primer lugar en el iPhone emparejado).

[!include[](~/cross-platform/includes/macos-testing.md)]

<a name="windows" />

## <a name="windows-requirements"></a>Requisitos de Windows

El uso de un equipo Windows para el desarrollo de Xamarin requiere las siguientes versiones de software o del SDK.
Compruebe la versión del sistema operativo (y confirme que no está usando una versión *Express* de Visual Studio; si es así, considere la posibilidad de actualizar a una edición *Community*).
El instalador de Visual Studio 2017 incluye una opción para instalar Xamarin automáticamente (**Desarrollo para dispositivos móviles con .NET**).

[!include[](~/cross-platform/includes/windows-requirements.md)]

> [!NOTE]
>
>- Xamarin para Visual Studio admite cualquier edición de Visual Studio 2017 (Community, Professional y Enterprise).
>
>- Para desarrollar aplicaciones de Xamarin.Forms para la plataforma universal de Windows (UWP) es necesario Windows 10 con Visual Studio 2017.

### <a name="testing--debugging-on-windows"></a>Pruebas y depuración en Windows

Las aplicaciones móviles Xamarin se pueden implementar en dispositivos físicos a través de USB para probar y depurar (los dispositivos iOS deben estar conectados al equipo Mac, no al equipo que ejecuta Visual Studio).

[!include[](~/cross-platform/includes/windows-testing.md)]

<a name="install" />

## <a name="installation-instructions"></a>Instrucciones de instalación

Puede descargar la versión más reciente de Xamarin para Mac OS en [xamarin.com/download](http://xamarin.com/download). Para Windows, siga las instrucciones de instalación de [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

Una lista completa de las versiones de producto actuales está disponible en la [página Versiones actuales](http://developer.xamarin.com/releases/current/). Esta página también describe las versiones de productos individuales (e incluye vínculos a las notas de la versión) para nuestros canales alfa y beta.

Aquí están disponibles instrucciones de [instalación](~/cross-platform/get-started/installation/index.md) específicas para cada plataforma:

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

También hay información adicional sobre [las plataformas admitidas y los requisitos de Xamarin.Forms](~/xamarin-forms/get-started/installation.md).

## <a name="related-links"></a>Vínculos relacionados

- [Descargar Xamarin](https://visualstudio.microsoft.com/xamarin/)
- [Versiones actuales](https://developer.xamarin.com/releases/current/)
