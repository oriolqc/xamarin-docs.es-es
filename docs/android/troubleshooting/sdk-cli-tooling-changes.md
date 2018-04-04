---
title: Cambios en las Herramientas de Android SDK
description: Cambios en cómo el SDK de Android administra los niveles de API y AVDs instalado.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 4156d712b91ad069d482debdf0731be8b649287a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="changes-to-the-android-sdk-tooling"></a>Cambios en las Herramientas de Android SDK

_Cambios en cómo el SDK de Android administra los niveles de API y AVDs instalado._

## <a name="changes-to--android-sdk-tooling"></a>Cambios en las herramientas de SDK de Android

En las versiones actuales de las herramientas del SDK para Android, Google ha quitado los administradores existentes de AVD y SDK en favor de nuevas herramientas de CLI (interfaz de línea de comandos). El primero **android** se ha quitado el programa y los administradores de GUI (interfaz gráfica de usuario) en Visual Studio para Mac y versiones anteriores de Xamarin para Visual Studio dejará de funcionar después de la versión 25.2.5 de las herramientas de SDK de Android.


![Android menú IDE de Visual Studio](sdk-cli-tooling-changes-images/android-ide-menu.png)

Cualquier intento de usar el **android** programa a través de la línea de comandos dará como resultado un mensaje de error similar al siguiente:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Por lo tanto debe usar las herramientas CLI para administrar y actualizar los emuladores y el SDK de Android.

### <a name="cli-tools"></a>Herramientas CLI

Los siguientes programas ahora conforman la interfaz de línea de comandos para las herramientas de SDK de Android:

#### <a name="sdkmanager"></a>sdkmanager

**Agregado en:** herramientas de Android SDK 25.2.3 (noviembre de 2016) y versiones posteriores.

Hay un nuevo programa llamado **sdkmanager** en el **herramientas/bin** carpeta del SDK de Android. Esta herramienta se utiliza para mantener el SDK de Android en la línea de comandos. Para obtener más información sobre el uso de esta herramienta, consulte [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Agregado en:** herramientas de Android SDK 25.3.0 (marzo de 2017) y versiones posteriores.

Hay un nuevo programa llamado **avdmanager** en el **herramientas/bin** carpeta del SDK de Android. Esta herramienta se utiliza para mantener la AVDs para el emulador de Google Android. Para obtener más información sobre el uso de esta herramienta, consulte [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Degradar

Puede degradar el **herramientas de Android SDK** versión mediante la instalación de una versión anterior del SDK de Android desde la [sitio Web para desarrolladores de Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Mediante la GUI del antigua

Todavía puede usar la GUI original mediante la ejecución de la **android** programa dentro de su **herramientas** carpeta siempre y cuando se encuentra en **herramientas de Android SDK** versión **25.2.5**  o inferior.


## <a name="related-links"></a>Vínculos relacionados

- [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)
- [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de la versión de SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
