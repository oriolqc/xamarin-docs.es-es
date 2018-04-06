---
title: Emulador de la línea de comandos
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: b1ca1c2b441a9c9ca26de5668f318312bea156a3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="command-line-emulator"></a>Emulador de la línea de comandos


## <a name="running-the-android-emulator-from-the-command-line"></a>Ejecución de Android Emulator desde la línea de comandos

Para habilitar la ejecución de Android Emulator desde la línea de comandos, puede usar la herramienta "emulador" que se proporciona en Android SDK. Esta herramienta se puede usar para ejecutar el emulador desde Terminal en OS X o desde el símbolo del sistema en una máquina Windows.

Para iniciar un emulador de Android específico, ejecute el siguiente comando desde el directorio de herramientas en la ubicación de Android SDK (por ejemplo, C:\android-sdk-windows\tools):

En Windows

```cmd
emulator.exe -avd NameOfYourEmulator -partition-size 512
```

En macOS

```bash
./emulator -avd NameOfYourEmulator -partition-size 512
```

El motivo de necesitar el tamaño de partición es permitir que el emulador disponga de mucho espacio para instalarse en él la plataforma de Xamarin.Android dado que, de forma predeterminada, el tamaño del emulador es pequeño.

Puede encontrar más información sobre parámetros adicionales en este sitio de Android: [http://developer.android.com/guide/developing/tools/emulator.html](http://developer.android.com/guide/developing/tools/emulator.html)
