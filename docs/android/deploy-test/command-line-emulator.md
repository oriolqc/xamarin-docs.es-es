---
title: Emulador de la línea de comandos
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 513b06749d1616e9fd10f04d22259810c0b4d265
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120728"
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
