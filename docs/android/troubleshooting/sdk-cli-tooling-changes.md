---
title: Cambios en las Herramientas de Android SDK
description: Cambios en cómo se administra el SDK de Android los niveles de API y AVD instalados.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: dbd3287e7c646be7fd969699eab685906a1c6c1a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093940"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Cambios en las Herramientas de Android SDK

_Cambios en cómo se administra el SDK de Android los niveles de API y AVD instalados._

## <a name="changes-to-android-sdk-tooling"></a>Cambios realizados en las herramientas de Android SDK

En versiones recientes de los SDK Tools para Android, Google ha quitado los administradores de AVD y SDK existentes en favor de las nuevas herramientas de CLI (interfaz de línea de comandos). El **android** se ha quitado el programa y los administradores de Google GUI (interfaz gráfica de usuario) en Visual Studio para Mac y las versiones anteriores de Visual Studio Tools para Xamarin dejarán de funcionar después de la versión 25.2.5 de Android SDK Tools. Por ejemplo, si intenta usar el **android** programa a través de la línea de comandos dará como resultado un mensaje de error similar al siguiente:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Las siguientes secciones explican cómo administrar el SDK de Android y dispositivos virtuales Android mediante Android SDK 25.3.0 y versiones posteriores.

### <a name="ui-tools"></a>Herramientas de interfaz de usuario

Visual Studio y Visual Studio para Mac ahora proporcionan reemplazos de Xamarin para los administradores basados en GUI de Google no incluidos:

-   Para descargar las herramientas de Android SDK, plataformas y otros componentes que necesita para desarrollar aplicaciones de Xamarin.Android, use el [Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md) el Administrador de SDK en lugar de Google heredado.

-   Para crear y configurar dispositivos virtuales Android, use el [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) el administrador del emulador en lugar de Google heredado.

Estas herramientas son funcionalmente equivalentes a basado en GUI de Google administradores reemplazan.

### <a name="cli-tools"></a>Herramientas de la CLI

Como alternativa, puede usar herramientas de la CLI para administrar y actualizar los emuladores y el SDK de Android. Los siguientes programas constituyen ahora la interfaz de línea de comandos para las herramientas de Android SDK:

#### <a name="sdkmanager"></a>sdkmanager

**Agregado en:** Android SDK Tools 25.2.3 (noviembre de 2016) y versiones posteriores.

Hay un programa nuevo llamado **sdkmanager** en el **herramientas/bin** carpeta del SDK de Android. Esta herramienta se utiliza para mantener el SDK de Android en la línea de comandos. Para obtener más información sobre el uso de esta herramienta, consulte [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Agregado en:** Android SDK Tools 25.3.0 (marzo de 2017) y versiones posteriores.

Hay un programa nuevo llamado **avdmanager** en el **herramientas/bin** carpeta del SDK de Android. Esta herramienta se utiliza para mantener el AVD para el emulador de Android. Para obtener más información sobre el uso de esta herramienta, consulte [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Degradar

Puede degradar el **Android SDK Tools** versión mediante la instalación de una versión anterior de Android SDK desde la [sitio Web para desarrolladores de Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Mediante la GUI del antigua

Todavía puede usar la interfaz gráfica de usuario original mediante la ejecución de la **android** programa dentro de su **herramientas** carpeta siempre y cuando se encuentra en **Android SDK Tools** versión **25.2.5**  o inferior.


## <a name="related-links"></a>Vínculos relacionados

- [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)
- [Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de la versión de SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
