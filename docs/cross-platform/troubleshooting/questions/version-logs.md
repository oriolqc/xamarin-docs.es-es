---
title: ¿Dónde puedo encontrar mi información de versión y los registros?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: cdbe480c45e9c0117f1437b1ee632f6ea8f142e0
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>¿Dónde puedo encontrar mi información de versión y los registros?

## <a name="outline"></a>Esquema

- [Información de versión](#version-information)
    - Información de versión de Windows
    - Información de versión de direcciones MAC
    - Herramientas de compilación de herramientas, herramientas de la plataforma, SDK de Android
- [Registros de IDE y de instalador](#ide-and-installer-logs)
    - [Registros de Windows](#windows-logs)
        - Xamarin Studio
        - Xamarin para Visual Studio
        - Instalador Xamarin Universal
        - Individuales `.msi` instaladores, los registros detallados
        - Inicio Visual Studio, los registros detallados
    - [Registros de Mac](#mac-logs)
        - Crear host
    - Visual Studio para Mac
        - Xamarin Studio
        - Instalador de Xamarin
- [Resultados de la compilación detallado](#verbose-build-output-logs)
- [Registros de aplicaciones Xamarin.iOS y Xamarin.Android no compatibles de depuración](#debug-logs-for-xamarin-apps)
    - Android `adb` logcat registros
    - simulador de iOS sesión (Mac)
    - registra el dispositivo iOS (en Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Información de versión

Normalmente es mejor enviar hacer copia de toda la información de la **copiar información** botones. En caso contrario, a menudo necesitaremos solicitar información adicional. Por ejemplo, las versiones de sistema operativo, versión Xcode, instalan niveles de API de Android y versión de .NET puede ser importante al ayudar a solucionar un problema.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Información de versión de Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Ayuda > acerca de > Mostrar detalles > Copiar información [button]**

#### <a name="visual-studio"></a>Programa para la mejora

**Ayuda > acerca de Microsoft Visual Studio > Copiar información [button]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Información de versión de direcciones MAC

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Visual Studio > acerca de Visual Studio > Mostrar detalles > Copiar información [button]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Herramientas de compilación de herramientas, herramientas de la plataforma, SDK de Android

Abra el Administrador de SDK de Android y realice una captura de pantalla de la parte superior **herramientas** sección.

![](https://kb.xamarin.com/customer/portal/attachments/337323 "Captura de pantalla del Administrador de SDK de Android > carpeta de herramientas")

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Herramientas > Abrir Android SDK Manager**

#### <a name="visual-studio"></a>Programa para la mejora

Seleccione el icono de la barra de herramientas de administrador de SDK:

![](https://kb.xamarin.com/customer/portal/attachments/420238 "Iniciar el icono de la barra de herramientas de administrador de SDK de Android en Visual Studio")

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Registros de IDE y de instalador

Para cada ubicación del registro, asegúrese de comprimir y adjuntar la carpeta de registro completo.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Registros de Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Visual Studio Tools para Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio de 2017

[Cómo obtener los registros de instalación de Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio de 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Instalador de "Universal" Xamarin

`%LOCALAPPDATA%\Xamarin\Universal`

Se trata de los registros de la `XamarinInstaller.exe` instalador.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Individuales `.msi` instaladores, los registros detallados

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Referencia: [opciones de línea de comandos](http://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Inicio Visual Studio, los registros detallados

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Referencia: [/Log (devenv.exe)](http://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Registros de Mac

Puede seleccionar la **vaya > Ir a la carpeta** menú de elemento en Finder y, a continuación, copie y pegue cualquiera de estas rutas de acceso en el cuadro de diálogo.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio para Mac

`~/Library/Logs/VisualStudio/7.0` (este número puede cambiar dependiendo de la versión que usa)

Esta carpeta también se puede abrir a través de "Ayuda" -> Abrir directorio de registro".

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (este número puede cambiar dependiendo de la versión que usa)

Esta carpeta también se puede abrir a través de "Ayuda" -> Abrir directorio de registro".

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Instalador de "Universal" Xamarin

`~/Library/Logs/XamarinInstaller/Universal`

Se trata de los registros de la `XamarinInstaller.dmg` instalador.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Host de compilación de Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Resultados de la compilación detallado

1.  Habilitar [resultado del diagnóstico de MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2.  Para las aplicaciones de iOS, también permiten **mtouch detallado salida** agregando `-v -v -v -v` en **propiedades del proyecto > iOS compilación > General [tab] > opciones adicionales > argumentos adicionales mtouch**.

    **Visual Studio (Windows)**

    [![](https://kb.xamarin.com/customer/portal/attachments/337319 "Introduce cuatro dash v en el campo de entrada de argumentos de mtouch adicionales")](https://kb.xamarin.com/customer/portal/attachments/337319)

    **Visual Studio para Mac**

    [![](https://kb.xamarin.com/customer/portal/attachments/337316 "Introduce cuatro dash v en el campo de entrada de argumentos de mtouch adicionales")](https://kb.xamarin.com/customer/portal/attachments/337316)

3.  Limpie y recompile el proyecto.

4.  Copie y pegue el resultado de compilación desde el IDE en un archivo de texto.
     - Visual Studio (Windows): **Vista > salida > Mostrar resultados desde: compilación**
     - Visual Studio para Mac: **Vista > rellena > errores > salida de compilación [tab]**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Registros de aplicaciones Xamarin.iOS y Xamarin.Android no compatibles de depuración

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Vista > rellena > salida de la aplicación**

![](https://kb.xamarin.com/customer/portal/attachments/337290 "Icono de panel de salida de aplicación en Visual Studio para Mac")


(Tenga en cuenta que este elemento de menú sólo aparecerán una vez se ha iniciado la aplicación).

### <a name="visual-studio"></a>Programa para la mejora

**Vista > salida > Mostrar resultados desde: depurar**

![](https://kb.xamarin.com/customer/portal/attachments/337292 "Panel de salida que muestra la opción de depuración en Visual Studio")

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](http://developer.android.com/tools/help/adb.html) logcat registros

Una vez ejecutada la `adb` command, adjuntar back el **android_logcat.txt** archivo desde el escritorio. Estas instrucciones se supone que hay solo un dispositivo conectado.

Vea también el [el registro de depuración Android](~/android/deploy-test/debugging/android-debug-log.md) página.

#### <a name="visual-studio"></a>Programa para la mejora

1.  **Herramientas > Android > Inicie símbolo Adb Android**
2.  Limpiar el registro: `adb logcat -c`
3.  Reproduzca el problema.
4.  El registro de salida: `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1.  **Herramientas > Abra el símbolo del sistema de SDK de Android**
2.  Limpiar el registro: `adb logcat -c`
3.  Reproduzca el problema.
4.  El registro de salida: `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />simulador de iOS sesión (Mac)

*   Para acceder al registro del sistema, seleccione **Depurar > Abrir registro del sistema...**  en la aplicación del simulador de iOS.

    ![](https://kb.xamarin.com/customer/portal/attachments/382617 "Depurar, menú que muestra la opción de registro del sistema abierto")

*   Para ver los informes de bloqueo en el simulador, abra Console.app y vaya a `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />registra el dispositivo iOS (en Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Vista > rellena > registro del dispositivo iOS**

#### <a name="xcode"></a>Xcode 

**Ventana > dispositivos > ${DeviceName}**

Informes de bloqueo están disponibles en la **ver registros de dispositivo** botón. El registro del sistema para el dispositivo aparece en la parte inferior de la ventana, en la flecha de divulgación de información <img alt="Disclosure arrow" src="https://kb.xamarin.com/customer/portal/attachments/382618" style="width: 15px; height: 12px;" />.

#### <a name="xcode-5"></a>Xcode 5

**Ventana > Organizador > dispositivos [tab] > ${DeviceName}**
