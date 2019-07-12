---
title: ¿Dónde se puede encontrar mi información de versión y los registros?
description: Este documento describe dónde buscar para buscar los registros y la información de versión de Xamarin. Esta información es útil al diagnosticar problemas, enviando los errores o la obtención de soporte técnico.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: e389bc33538ec3c3d36eb749c746f5a4723aab3c
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830355"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>¿Dónde se puede encontrar mi información de versión y los registros?

## <a name="outline"></a>Contorno

- [Información de versión](#version-information)
    - Información de versión de Windows
    - Información de versión de Mac
    - Herramientas de compilación de Android SDK Tools, herramientas de la plataforma,
- [Registros de IDE y el instalador](#ide-and-installer-logs)
    - [Registros de Windows](#windows-logs)
        - Xamarin Studio
        - Xamarin para Visual Studio
        - Instalador de Xamarin Universal
        - Individuales `.msi` instaladores, los registros detallados
        - Inicio de Visual Studio, los registros detallados
    - [Registros de Mac](#mac-logs)
        - Host de compilación
    - Visual Studio para Mac
        - Xamarin Studio
        - Instalador de Xamarin
- [Salida de compilación detallados](#verbose-build-output-logs)
- [Depurar registros para aplicaciones de Xamarin.Android y Xamarin.iOS](#debug-logs-for-xamarin-apps)
    - Android `adb` logcat registros
    - registra el simulador de iOS (en Mac)
    - registra el dispositivo iOS (en Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Información de versión

Normalmente, es mejor enviar hacer una copia de toda la información de la **copiar información** botones. En caso contrario, a menudo necesitaremos solicitar información adicional. Por ejemplo, las versiones de sistema operativo, versión de Xcode, instalan los niveles de API de Android y versión de .NET puede ser importante al ayudar a solucionar un problema.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Información de versión de Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Ayuda > acerca de > Mostrar detalles > Copiar información [button]**

#### <a name="visual-studio"></a>Visual Studio

**Ayuda > acerca de Microsoft Visual Studio > Copiar información [button]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Información de versión de Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Visual Studio > acerca de Visual Studio > Mostrar detalles > Copiar información [button]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Herramientas de compilación de Android SDK Tools, herramientas de la plataforma,

Abrir Android SDK Manager y tomar una captura de pantalla de la parte superior **herramientas** sección.

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Herramientas > Abrir Android SDK Manager**

#### <a name="visual-studio"></a>Visual Studio

**Herramientas > Android > Abrir Android SDK Manager...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Registros de IDE y el instalador

Para cada ubicación del registro, asegúrese de que comprima y adjuntar la carpeta de registro completo.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Registros de Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Visual Studio Tools para Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Cómo obtener los registros de instalación de Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Instalador de Xamarin "Universal"

`%LOCALAPPDATA%\Xamarin\Universal`

Estos son los registros desde el `XamarinInstaller.exe` instalador.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Individuales `.msi` instaladores, los registros detallados

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Referencia: [Opciones de línea de comandos](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Inicio de Visual Studio, los registros detallados

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Referencia: [/Log (devenv.exe)](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Registros de Mac

Puede seleccionar la **vaya > vaya a la carpeta** menú de elemento en el Finder y, a continuación, copie y pegue cualquiera de estas rutas de acceso en el cuadro de diálogo.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio para Mac

`~/Library/Logs/VisualStudio/7.0` (este número puede cambiar dependiendo de la versión que está usando)

Esta carpeta también se puede abrir a través de "Ayuda -> Abrir directorio de registro".

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (este número puede cambiar dependiendo de la versión que está usando)

Esta carpeta también se puede abrir a través de "Ayuda -> Abrir directorio de registro".

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Instalador de Xamarin "Universal"

`~/Library/Logs/XamarinInstaller/Universal`

Estos son los registros desde el `XamarinInstaller.dmg` instalador.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Host de compilación de Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Salida de compilación detallados

1.  Habilitar [salida de diagnóstico de MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2.  Para las aplicaciones de iOS, también permiten **mtouch detallado salida** agregando `-v -v -v -v` en **las propiedades del proyecto > compilación de iOS > General (pestaña) > opciones adicionales > argumentos mtouch adicionales**.

3.  Limpie y recompile el proyecto.

4.  Copie y pegue la salida de compilación desde el IDE en un archivo de texto.
     - Visual Studio (Windows): **Ver > salida > Mostrar resultados desde: Compilación**
     - Visual Studio para Mac: **Vista > paneles > errores > salida de la compilación (ficha)**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Depurar registros para aplicaciones de Xamarin.Android y Xamarin.iOS

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Vista > paneles > resultado de la aplicación**

(Tenga en cuenta que este elemento de menú sólo aparecerán después de que se ha iniciado la aplicación.)

### <a name="visual-studio"></a>Visual Studio

**Ver > salida > Mostrar resultados desde: Depurar**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpsdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](https://developer.android.com/tools/help/adb.html) logcat registros

Después de ejecutar el `adb` comando, adjunte back el **android_logcat.txt** archivo desde el escritorio. Estas instrucciones se supone que tiene solo un dispositivo conectado.

Vea también el [registro de depuración de Android](~/android/deploy-test/debugging/android-debug-log.md) página.

#### <a name="visual-studio"></a>Visual Studio

1. **Herramientas > Android > Inicie el símbolo del sistema Adb de Android**
2. Limpiar el registro: `adb logcat -c`
3. Reproduzca el problema.
4. El registro de salida: `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. **Herramientas > Abrir símbolo del sistema de SDK de Android**
2. Limpiar el registro: `adb logcat -c`
3. Reproduzca el problema.
4. El registro de salida: `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />registra el simulador de iOS (en Mac)

* Para acceder al registro del sistema, seleccione **Depurar > Abrir registro del sistema...**  en la aplicación del simulador de iOS.

* Para ver los informes de bloqueo desde el simulador, abra Console.app y vaya a `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />registra el dispositivo iOS (en Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Vista > paneles > registro de dispositivos iOS**

#### <a name="xcode"></a>Xcode

**Ventana > dispositivos > ${DeviceName}**

Informes de bloqueo están disponibles en el **ver registros de dispositivo** botón. El registro del sistema para el dispositivo aparece en la parte inferior de la ventana en la flecha de cierre.

#### <a name="xcode-5"></a>Xcode 5

**Ventana > Organizador > dispositivos (pestaña) > ${DeviceName}**
