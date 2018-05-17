---
title: Configuración de Android Emulator
description: En esta sección se explica cómo preparar Google Android Emulator para probar la aplicación. Se explica cómo acelerar el emulador para obtener el máximo rendimiento y se muestra cómo se usa un administrador de emuladores para crear y personalizar los dispositivos virtuales.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/25/2018
ms.openlocfilehash: 215e298068b7a3a23b2e469e923f172c8303bbcb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="android-emulator-setup"></a>Configuración de Android Emulator

_En esta sección se explica cómo preparar Google Android Emulator para probar la aplicación. Se explica cómo acelerar el emulador para obtener el máximo rendimiento y se muestra cómo se usa un administrador de emuladores para crear y personalizar dispositivos virtuales._


## <a name="overview"></a>Información general

El emulador de Android SDK se puede ejecutar en varias configuraciones para simular diferentes dispositivos. Cada una de estas configuraciones se crea como un _dispositivo virtual_. En esta guía obtendrá información sobre cómo acelerar el emulador de Android para mejorar el rendimiento y cómo usar el Administrador de Android Emulator de Xamarin o el Administrador de emuladores de Google heredado para crear dispositivos virtuales.


> [!NOTE]
> A partir de la versión **26.0.1** de Android SDK Tools, Google ha quitado la compatibilidad con administradores de AVD/SDK existentes en favor de las nuevas herramientas de la CLI (interfaz de la línea de comandos). Debido a este cambio de degradación, ahora se usan administradores de SDK/dispositivos de Xamarin en lugar de administradores de SDK/emuladores de Google para las Herramientas de Android 26.0.1 y versiones posteriores. (Para más información sobre SDK Manager de Xamarin, vea [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)).


## <a name="sections"></a>Secciones

### <a name="hardware-accelerationandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Aceleración de hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Cómo preparar el equipo para lograr el máximo rendimiento de Google Android Emulator. Dado que Google Android Emulator puede ser demasiado lento sin aceleración de hardware, se recomienda habilitar la aceleración de hardware en el equipo antes de usarlo.

### <a name="xamarin-android-device-managerandroidget-startedinstallationandroid-emulatorxamarin-device-managermd"></a>[Administrador de dispositivos Android de Xamarin](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)

Cómo usar Xamarin Android Device Manager para crear y personalizar dispositivos virtuales de Google Android Emulator. El **Administrador de dispositivos Android de Xamarin**, actualmente en versión preliminar, está diseñado para reemplazar el administrador de emuladores de Google heredado. Si tiene como destino Android Oreo 8.0 o versiones posteriores, debe usar el Administrador de dispositivos Android de Xamarin en lugar del administrador de emuladores de Google.

### <a name="google-emulator-managerandroidget-startedinstallationandroid-emulatorgoogle-emulator-managermd"></a>[Administrador de emuladores de Google](~/android/get-started/installation/android-emulator/google-emulator-manager.md)

Cómo usar el Administrador del emulador de Google heredado para crear y personalizar dispositivos virtuales de Google Android Emulator. Puede seguir ejecutando el emulador de Android de Google con el Administrador de emuladores de Google si sigue usando Android SDK Tools versión 25.2.5 o anteriores.

Después de haber configurado el emulador de Android SDK, vea [Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md) para obtener información sobre cómo iniciar el emulador y usarlo para probar y depurar la aplicación.
