---
title: Configuración de Google Android Emulator
description: Google Android Emulator se puede ejecutar en varias configuraciones para simular diferentes dispositivos. En esta guía se explica cómo preparar Android Emulator para probar la aplicación.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: e5ba2cc23ea9751ca60644d3eb5b7e3f31bbb6bb
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732536"
---
# <a name="google-android-emulator-setup"></a>Configuración de Google Android Emulator

_En esta guía se explica cómo preparar Google Android Emulator para probar la aplicación._


## <a name="overview"></a>Información general

Google Android Emulator se puede ejecutar en varias configuraciones para simular diferentes dispositivos. Cada configuración se denomina _dispositivo virtual_. Al implementar y probar la aplicación en el emulador, debe seleccionar un dispositivo virtual preconfigurado o personalizado que simule un dispositivo Android físico, como un teléfono Nexus o Pixel.

En las secciones siguientes se describe cómo acelerar Google Android Emulator para obtener el máximo rendimiento, cómo usar Android Device Manager para crear y personalizar dispositivos virtuales y cómo personalizar las propiedades de perfil de un dispositivo virtual. Además, en la sección de solución de problemas se explican los problemas de configuración comunes y sus soluciones.

## <a name="sections"></a>Secciones

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Aceleración de hardware para el rendimiento del emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Describe cómo preparar el equipo para lograr el máximo rendimiento de Android Emulator.
Dado que Google Android Emulator puede ser demasiado lento sin aceleración de hardware, se recomienda habilitar la aceleración de hardware en el equipo antes de usarlo.

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[Administración de dispositivos virtuales con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Describe cómo usar Android Device Manager para crear y personalizar dispositivos virtuales.

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[Editar las propiedades del dispositivo virtual Android](~/android/get-started/installation/android-emulator/device-properties.md)

Describe cómo usar Android Device Manager para editar las propiedades de perfil de un dispositivo virtual Android.

### <a name="troubleshooting-emulator-setup-problemsandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[Solucionar problemas de configuración del emulador](~/android/get-started/installation/android-emulator/troubleshooting.md)

Describe cómo diagnosticar y corregir los problemas de Android Device Manager que pueden surgir al configurar Android Emulator.


Después de configurar Android Emulator, vea [Debugging with the Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md) (Depurar con Google Android Emulator) para obtener información sobre cómo iniciar el emulador y usarlo para probar y depurar la aplicación.


> [!NOTE]
> A partir de la versión **26.0.1** de Android SDK Tools, Google ha quitado la compatibilidad con administradores de AVD/SDK existentes en favor de las nuevas herramientas de la CLI (interfaz de la línea de comandos). Debido a este cambio de degradación, ahora se usan administradores de SDK/dispositivos de Xamarin en lugar de administradores de SDK/dispositivos de Google para las Herramientas de Android 26.0.1 y versiones posteriores. Para más información sobre SDK Manager de Xamarin, vea [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md).

