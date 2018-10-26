---
title: Instalación de Xamarin en Visual Studio 2017
description: En este documento se describe cómo instalar Xamarin en Visual Studio 2017. Se explican los requisitos, el proceso de instalación y la comprobación de la instalación.
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: cd87c33394b21ce6a9338d239de8f013cc8ec1fa
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "37066957"
---
# <a name="installing-xamarin-in-visual-studio-2017"></a>Instalación de Xamarin en Visual Studio 2017

<a name="requirements" />

Compruebe los [requisitos del sistema](~/cross-platform/get-started/requirements.md) antes de empezar.

## <a name="installation"></a>Instalación

[!include[](~/cross-platform/includes/install-xamarin-windows.md)]

En Visual Studio 2017, verifique que Xamarin está instalado; para ello, haga clic en el menú **Ayuda**. Si está instalado, debería ver el elemento de menú **Xamarin**, como se muestra en esta captura de pantalla:

![Elemento de menú de Xamarin en el menú Ayuda](windows-images/12-xamarin-menu-item.png "Elemento de menú de Xamarin en el menú Ayuda")

Puede hacer clic en **Ayuda > Acerca de Microsoft Visual Studio** y desplazarse por la lista de productos instalados para ver si Xamarin está instalado:

![Pantalla de productos instalados de Visual Studio 2015](windows-images/13-xamarin-is-installed.png "Pantalla de productos instalados de Visual Studio 2015")

Para obtener más información sobre cómo encontrar la información de la versión, consulte [Where can I find my version information and logs?](~/cross-platform/troubleshooting/questions/version-logs.md) (¿Dónde puedo encontrar los registros y la información de la versión?).

## <a name="next-steps"></a>Pasos siguientes

La instalación de Xamarin en Visual Studio 2017 le permite empezar a escribir código para sus aplicaciones, pero requiere un paso más para configurarlo y poder compilar e implementar las aplicaciones en el simulador, el emulador y el dispositivo. Consulte las siguientes guías para completar la instalación y empezar a compilar aplicaciones multiplataforma.

### <a name="ios"></a>iOS

Para obtener información detallada, consulte la guía de [instalación de Xamarin.iOS en Windows](~/ios/get-started/installation/windows/index.md). 

1. [Instalación de Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)
2. [Conexión de Visual Studio con el host de compilación del equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
3. [Configuración del desarrollador de iOS](~/ios/get-started/installation/device-provisioning/index.md): necesaria para ejecutar la aplicación en el dispositivo
5. [Simulador remoto de iOS](~/tools/ios-simulator/index.md)
6. [Introducción a Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

### <a name="android"></a>Android

Para obtener información detallada, consulte la guía de [instalación de Xamarin.Android en Windows](~/android/get-started/installation/windows.md).

1. [Configuración de Xamarin.Android](~/android/get-started/installation/windows.md#configuration)
2. [Uso del administrador de Android SDK de Xamarin](~/android/get-started/installation/android-sdk.md?ide=vs)
3. [Emulador de Android SDK](~/android/get-started/installation/android-emulator/index.md)
4. [Configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md)
