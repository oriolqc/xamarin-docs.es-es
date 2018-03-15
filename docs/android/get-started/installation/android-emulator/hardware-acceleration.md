---
title: "Aceleración de hardware de Android Emulator"
description: "Describe cómo preparar el equipo para lograr el máximo rendimiento del emulador de Android SDK"
ms.topic: article
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 12/22/2017
ms.openlocfilehash: 7560900ace62a737ac765bcfe93f759f8985aca2
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="android-emulator-hardware-acceleration"></a>Aceleración de hardware de Android Emulator

Dado que el emulador de Android SDK es muy lento sin aceleración de hardware, se recomienda instalar Intel HAXM (Hardware Accelerated Execution Manager) para mejorar drásticamente el rendimiento del emulador de Android SDK.


## <a name="haxm-overview"></a>Información general sobre HAXM

HAXM es un motor de virtualización asistida por hardware (hipervisor) que usa la tecnología de virtualización (VT) Intel para acelerar la emulación de aplicaciones Android en un equipo host. En combinación con las imágenes de emulador x86 de Android proporcionadas por Intel y el Android SDK Manager oficial, HAXM permite una emulación de Android más rápida en sistemas compatibles con VT. Si está desarrollando en un equipo con una CPU de Intel que tiene funciones de VT, puede aprovechar las ventajas de HAXM para acelerar considerablemente el emulador de Android SDK (si no está seguro de si la CPU es compatible con VT, consulte [Determine If Your Processor Supports Intel Virtualization Technology](https://www.intel.com/content/www/us/en/support/processors/000005486.html) (Determinar si su procesador admite Intel Virtualization Technology).

El emulador de Android SDK usa HAXM automáticamente cuando está disponible. Cuando se selecciona un dispositivo virtual basado en **x86** (como se describe en [Configuration and Use](~/android/deploy-test/debugging/android-sdk-emulator/index.md) (Configuración y uso)), ese dispositivo virtual usará HAXM para la aceleración de hardware. Antes de usar el emulador de Android SDK por primera vez, conviene comprobar que HAXM esté instalado y disponible para él.

> [!NOTE]
> No puede ejecutar HAXM en una máquina virtual.


## <a name="verifying-haxm-installation"></a>Comprobación de la instalación de HAXM

Para comprobar si HAXM está disponible, consulte la ventana **Starting Android Emulator** (Iniciando el emulador de Android) cuando se inicie el emulador. Para iniciar el emulador de Android SDK, haga lo siguiente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie el administrador del emulador de Android. Para ello, haga clic en **Herramientas > Android > Administrador del emulador de Android**:

    [![Ubicación del elemento de menú Administrador del emulador de Android](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. Si ve un cuadro de diálogo de **advertencia de rendimiento** parecido al siguiente, HAXM aún no está instalado o correctamente configurado en su equipo:

    ![Cuadro de diálogo de advertencia de rendimiento que indica que HAXM no está listo](hardware-acceleration-images/win/11-perf-warn.png)

   Si se muestra una cuadro de diálogo de **advertencia de rendimiento** como este, consulte [Advertencias de rendimiento](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn) para identificar su causa y resolver el problema.

3. Seleccione una imagen **x86** (por ejemplo, **VisualStudio\_android-23\_x86\_phone**), haga clic en **Iniciar** y, después, en **Inicio**:

    ![Iniciar el emulador de Android SDK con una imagen predeterminada de dispositivo virtual](hardware-acceleration-images/win/02-start-default-avd.png)

4. Espere a que aparezca el cuadro de diálogo **Starting Android Emulator** (Iniciando el emulador de Android) mientras se inicia el emulador. Si HAXM está instalado, verá el mensaje **HAX is working and emulator runs in fast virt mode** (HAX funciona y el emulador se ejecuta en modo virtual rápido), como se muestra en esta captura de pantalla:

    ![HAXM aparece disponible en el cuadro de diálogo Starting Android Emulator (Iniciando el emulador de Android)](hardware-acceleration-images/win/03-haxm-detected.png)

   Si no ve este mensaje, lo más probable es que HAXM no esté instalado. Por ejemplo, aquí hay una captura de pantalla de un mensaje que puede aparecer si HAXM no está disponible:

    ![HAXM no está disponible en este equipo](hardware-acceleration-images/win/04-haxm-error.png)

   Si HAXM no está disponible en el equipo, siga los pasos de la sección siguiente para instalar HAXM.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie Android Emulator Manager. Para ello, haga clic en **Herramientas > Google Emulator Manager**:

    [![Ubicación del elemento de menú Administrador del emulador de Android](hardware-acceleration-images/mac/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/mac/01-avd-manager-menu-item.png#lightbox)

2. Si ve un cuadro de diálogo de **advertencia de rendimiento** parecido al siguiente, HAXM aún no está instalado o correctamente configurado en su equipo:

    ![Cuadro de diálogo de advertencia de rendimiento que indica que HAXM no está listo](hardware-acceleration-images/mac/04-avd-warning.png)

   Si se muestra una cuadro de diálogo de **advertencia de rendimiento** como este, consulte [Advertencias de rendimiento](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn) para identificar su causa y resolver el problema.

3. Seleccione la imagen **x86** (por ejemplo, **Android\_Accelerated\_x86**) y haga clic en **Start (Iniciar)** y en **Launch (Ejecutar)**:

    [![Inicio de Android SDK Emulator con una imagen de dispositivo predeterminada](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. Espere a que aparezca el cuadro de diálogo **Starting Android Emulator** (Iniciando el emulador de Android) mientras se inicia el emulador. Si HAXM está instalado, verá el mensaje **HAX is working and emulator runs in fast virt mode** (HAX funciona y el emulador se ejecuta en modo virtual rápido), como se muestra en esta captura de pantalla:

    ![HAXM aparece disponible en el cuadro de diálogo Starting Android Emulator (Iniciando el emulador de Android)](hardware-acceleration-images/mac/03-haxm-detected.png)

   Si HAXM no está disponible en el equipo (por ejemplo, si ve un mensaje de error como _Asegúrese de que Intel HAXM está correctamente instalado y se puede usar_), siga los pasos de la sección siguiente para instalar HAXM.


-----

<a name="install-haxm" />

## <a name="installing-haxm"></a>Instalación de HAXM

Si el emulador no se inicia, es posible que tenga que instalar HAXM manualmente. En la página de [Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager) hay disponibles paquetes de instalación de HAXM para Windows y macOS. Siga estos pasos para descargar e instalar HAXM manualmente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el sitio web de Intel, descargue la última versión del instalador del [motor de virtualización de HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) para Windows. La ventaja de descargar el instalador de HAXM directamente desde el sitio web de Intel es que se asegura de que esté usando la versión más reciente.

   También puede usar SDK Manager para descargar el instalador de HAXM (en SDK Manager, haga clic en **Herramientas > Extras > Intel x86 Emulator Accelerator (instalador de HAXM)**). Android SDK suele descargar el instalador de HAXM en la ubicación siguiente:

   **C:\\Archivos de programa (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager**

   Tenga en cuenta que el administrador de SDK no instala HAXM, simplemente descarga el instalador HAXM en la ubicación anterior; tendrá que seguir iniciándolo de forma manual.

2. Ejecute **intelhaxm-android.exe** para iniciar el instalador de HAXM. Acepte los valores predeterminados en los cuadros de diálogo del instalador:

   ![Ventana de instalación de Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/05-haxm-installer.png)

Si ve un cuadro de diálogo de error similar al siguiente (_Este equipo no es compatible con Intel Virtualization Technology (VT-x) o lo está usando exclusivamente Hyper-V_), se debe deshabilitar Hyper-V antes de instalar HAXM:

![HAXM no se puede instalar debido a un conflicto con Hyper-V](hardware-acceleration-images/win/06-cant-install-haxm.png)

En la siguiente sección se explica cómo deshabilitar Hyper-V.

<a name="disable-hyperv" />

## <a name="disabling-hyper-v"></a>Deshabilitar Hyper-V

Si usa Windows con Hyper-V habilitado, debe deshabilitarlo y reiniciar el equipo para instalar y usar HAXM. Puede deshabilitar Hyper-V desde el Panel de control mediante estos pasos:

1. En el cuadro de búsqueda de Windows, escriba **Programas** y luego haga clic en el resultado de búsqueda **Programas y características**.

2. En el cuadro de diálogo **Programas y características** del Panel de control, haga clic en **Activar o desactivar las características de Windows**:

    ![Activar o desactivar las características de Windows](hardware-acceleration-images/win/07-turn-windows-features.png)

3. Desactive **Hyper-V** y reinicie el equipo:

    ![Deshabilitar Hyper-V en el cuadro de diálogo Características de Windows](hardware-acceleration-images/win/08-uncheck-hyper-v.png)

También puede usar el siguiente cmdlet de Powershell para deshabilitar Hyper-V:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

HAXM de Intel y Microsoft Hyper-V no pueden estar activos al mismo tiempo. Desafortunadamente, actualmente no existe ninguna manera de alternar entre Hyper-V y HAXM sin reiniciar el equipo. Si quiere usar el [Emulador de Visual Studio para Android](~/android/deploy-test/debugging/visual-studio-android-emulator.md) (que depende de Hyper-V), no podrá usar el emulador de Android SDK sin reiniciar. Una manera de usar Hyper-V y HAXM es crear una configuración de arranque múltiple, como se explica en [Creating a no hypervisor boot entry](https://blogs.msdn.microsoft.com/virtual_pc_guy/2008/04/14/creating-a-no-hypervisor-boot-entry/) (Crear una entrada de arranque que no es de hipervisor).

En algunos casos, si Device Guard y Credential Guard están habilitados, con los pasos anteriores no se deshabilitará Hyper-V correctamente. Si no puede deshabilitar Hyper-V (o parece estar deshabilitado pero sigue habiendo errores en la instalación de HAXM), siga los pasos de la sección siguiente para deshabilitar Device Guard y Credential Guard.

<a name="disable-devguard" />

## <a name="disabling-device-guard"></a>Deshabilitación de Device Guard

Device Guard y Credential Guard pueden evitar que se deshabilite Hyper-V en equipos Windows. Por lo general, suele ser un problema para los equipos unidos a un dominio que se configuran y se controlan mediante una organización propietaria.
En Windows 10, siga estos pasos para comprobar si se está ejecutando **Device Guard**:

1. En **Windows Search**, escriba **Información del sistema** para iniciar la aplicación **Información del sistema**.

2. En **Resumen del sistema**, compruebe si está presente **Seguridad basada en la virtualización de Device Guard** y si su estado es **En ejecución**:

   [![Device Guard está presente y en ejecución](hardware-acceleration-images/win/09-device-guard-sml.png)](hardware-acceleration-images/win/09-device-guard.png#lightbox)

Si Device Guard está habilitado, siga estos pasos para deshabilitarlo:

1. Asegúrese de que **Hyper-V** esté deshabilitado (en **Activar o desactivar las características de Windows**) como se explica en la sección anterior.

2. En el cuadro de búsqueda de Windows, escriba **gpedit** y seleccione el resultado de búsqueda **Editar directiva de grupo**. Esto inicia el **Editor de directivas de grupo local**.

3. En el **Editor de directivas de grupo local**, vaya a **Configuración del equipo > Plantillas administrativas > Sistema > Device Guard**:

   [![Device Guard en el Editor de directivas de grupo local](hardware-acceleration-images/win/10-group-policy-editor-sml.png)](hardware-acceleration-images/win/10-group-policy-editor.png#lightbox)

4. Cambie **Activar la seguridad basada en la virtualización** a **Deshabilitado** (como se ha indicado arriba) y salga del **Editor de directivas de grupo local**.

5. En el cuadro de búsqueda de Windows, escriba **cmd**. Si aparece **Símbolo del sistema** en los resultados de búsqueda, haga clic con el botón derecho en **Símbolo del sistema** y seleccione **Ejecutar como administrador**.

6. Copie y pegue los siguientes comandos en la ventana del símbolo del sistema (si la unidad **Z:** se está usando, seleccione una letra de unidad no usada en su lugar):

        mountvol Z: /s
        copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
        bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
        bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
        mountvol Z: /d

7. Reinicie el equipo. En la pantalla de arranque debería ver un mensaje similar al siguiente:

   **¿Quiere deshabilitar Credential Guard?**

   Pulse la tecla indicada para deshabilitar Credential Guard como se pide.

8. Una vez reiniciado el equipo, compruebe de nuevo que Hyper-V está deshabilitado (como se ha explicado en los pasos anteriores).

Si Hyper-V aún no está deshabilitado, es posible que las directivas del equipo unido a dominio eviten que se deshabiliten Device Guard o Credential Guard. Si es así, puede solicitar una exención al administrador del dominio que le permita no usar Credential Guard. También puede usar un equipo que no esté unido a dominio para emplear HAXM.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el sitio web de Intel, descargue la última versión del instalador del [motor de virtualización de HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) para macOS.

2. Ejecute el instalador de HAXM. Acepte los valores predeterminados en los cuadros de diálogo del instalador:

   [![Ventana de instalación de Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----
