---
title: Emulador de la línea de comandos
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 11/05/2018
ms.openlocfilehash: 995b0783604f752915daaa77a8362899ac61e174
ms.sourcegitcommit: f541a92b4f896474f6a5467ccff2028dafa6fee7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2018
ms.locfileid: "50983593"
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

Puede encontrar más información sobre parámetros adicionales en este sitio de Android: [https://developer.android.com/studio/run/emulator-commandline](https://developer.android.com/studio/run/emulator-commandline)
