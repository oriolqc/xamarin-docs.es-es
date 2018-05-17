---
title: Solución de problemas de Google Android Emulator
description: Cómo identificar y resolver problemas de Google Android Emulator
ms.prod: xamarin
ms.assetid: 4B05C3C5-E1F6-47A9-B098-C31E630194F6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/04/2018
ms.openlocfilehash: 001fc21a519a251715d24b43acfdd4251b5fbc91
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="google-android-emulator-troubleshooting"></a>Solución de problemas de Google Android Emulator

En este artículo se explican los mensajes de advertencia y los problemas más comunes relacionados con Google Android Emulator y sus soluciones.
 
<a name="perfwarn" />

## <a name="performance-warnings"></a>advertencias de rendimiento

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A partir de la versión 15.4 de Visual Studio 2017, puede que se muestre un cuadro de diálogo de advertencia de rendimiento al implementar la aplicación en el emulador de Android SDK por primera vez. A continuación se explican estos cuadros de diálogo de advertencia.

### <a name="computer-does-not-contain-an-intel-procesor"></a>El equipo no contiene un procesador Intel

![El equipo no contiene un procesador Intel](troubleshooting-images/01-no-intel-processor.png)

Este cuadro de diálogo indica que el equipo no tiene un procesador Intel, lo cual es necesario para la aceleración del emulador de Android SDK. Si el equipo no tiene un procesador Intel, se recomienda usar un dispositivo Android físico para el desarrollo.

### <a name="hyper-v-is-installed-or-active"></a>Hyper-V está instalado o activo

![Hyper-V está instalado o activo](troubleshooting-images/02-hyper-v-active.png)

Este cuadro de diálogo indica que Hyper-V está instalado o está activo y debe deshabilitarse. En [Deshabilitar Hyper-V](#disable-hyperv) se explica cómo resolver este problema.

### <a name="haxm-is-not-installed"></a>HAXM no está instalado

![HAXM no está instalado](troubleshooting-images/03-haxm-not-installed.png)

Este cuadro de diálogo indica que el equipo tiene un procesador Intel y Hyper-V está deshabilitado, pero HAXM no está instalado.
En [Instalación de HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) se describen los pasos para instalar HAXM.

### <a name="haxm-process-not-running"></a>No se está ejecutando el proceso de HAXM

![No se está ejecutando el proceso de HAXM](troubleshooting-images/04-haxm-process-not-running.png)

Este cuadro de diálogo se muestra si el equipo tiene un procesador Intel, Hyper-V está deshabilitado e Intel HAXM está instalado, pero no se está ejecutando el proceso de HAXM. Para resolver este problema, abra una ventana de símbolo del sistema elevado y escriba el comando siguiente:

```cmd
sc query intelhaxm
```

Si se está ejecutando el proceso HAXM, el resultado debería ser parecido a este:

```cmd
SERVICE_NAME: intelhaxm
    TYPE               : 1  KERNEL_DRIVER
    STATE              : 4  RUNNING
                            (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
    WIN32_EXIT_CODE    : 0  (0x0)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```


Si el valor **STATE** (Estado) no está establecido en **RUNNING** (Ejecutándose), consulte [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Cómo usar Intel Hardware Accelerated Execution Manager) para resolver el problema.


### <a name="other-failures"></a>Otros errores

![Otros errores](troubleshooting-images/05-other-failure.png)

Este cuadro de diálogo se muestra si el equipo tiene un procesador Intel, Hyper-V está deshabilitado, Intel HAXM está instalado, el proceso de HAXM se está ejecutando, pero el emulador no puede iniciarse por algún motivo desconocido.
Para obtener ayuda y resolver el problema, consulte [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Cómo usar Intel Hardware Accelerated Execution Manager).

### <a name="disabling-performance-warnings"></a>Deshabilitar las advertencias de rendimiento

Si prefiere que no se muestren advertencias de rendimiento, puede deshabilitarlas. En Visual Studio, haga clic en **Herramientas > Opciones > Xamarin > Configuración de Android** y deshabilite la opción **Advertir si la aceleración de AVD no es compatible (HAXM)**:

[![Deshabilitar las advertencias de aceleración de AVD](troubleshooting-images/win/06-disable-perf-warnings-sml.png)](troubleshooting-images/win/06-disable-perf-warnings.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A partir de la compilación 559 de Visual Studio para Mac 7.2, puede que se muestre un cuadro de diálogo de advertencia de rendimiento al implementar la aplicación en Google Android Emulator por primera vez. A continuación se explican estos cuadros de diálogo de advertencia.

### <a name="haxm-is-not-installed"></a>HAXM no está instalado

![HAXM no está instalado](troubleshooting-images/03-haxm-not-installed.png)

Este cuadro de diálogo indica que HAXM no está instalado.
En [Instalación de HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) se describen los pasos para instalar HAXM.

### <a name="haxm-process-not-running"></a>No se está ejecutando el proceso de HAXM

![No se está ejecutando el proceso de HAXM](troubleshooting-images/04-haxm-process-not-running.png)

Este cuadro de diálogo se muestra si no se está ejecutando el proceso de HAXM. Para obtener información detallada que le ayude a resolver el problema, consulte [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Cómo usar Intel Hardware Accelerated Execution Manager). 

### <a name="other-failures"></a>Otros errores

![Otros errores](troubleshooting-images/05-other-failure.png)

Este cuadro de diálogo se muestra si el emulador no puede iniciarse por algún motivo desconocido. Para obtener ayuda y resolver el problema, consulte [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Cómo usar Intel Hardware Accelerated Execution Manager).

-----


## <a name="solutions-to-common-problems"></a>Soluciones a problemas comunes

Muchos de los problemas habituales de Google Android Emulator pueden resolverse al realizar cambios en la configuración del equipo o al instalar software adicional. En las secciones siguientes se describen estos problemas y se aportan soluciones.


### <a name="deployment-issues"></a>Problemas de implementación

Si se produce un error al instalar el APK en el emulador o al ejecutar Android Debug Bridge (**adb**), compruebe que Android SDK puede conectarse con el emulador. Para ello, siga los pasos siguientes:

1. Inicie el emulador desde **Android Virtual Device (AVD) Manager**. Para ello, seleccione el dispositivo virtual y haga clic en **Start (Iniciar)**.

2. Abra un símbolo del sistema y vaya a la carpeta donde está instalado **adb**. Por ejemplo, en Windows, podría estar en **C:\\Archivos de programa (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**.

3. Escriba el comando siguiente:

   ```shell
   adb devices
   ```

4. Si el emulador es accesible desde Android SDK, el emulador debe aparecer en la lista de dispositivos conectados. Por ejemplo:

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Si el emulador no aparece en esta lista, inicie **Android SDK Manager**, aplique todas las actualizaciones e intente iniciar el emulador de nuevo.



### <a name="haxm-issues"></a>Problemas con HAXM

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Si Google Android Emulator no se inicia correctamente, esto suele deberse a problemas relacionados con HAXM. Normalmente los problemas de HAXM son consecuencia de conflictos con otras tecnologías de virtualización, configuraciones incorrectas o un controlador HAXM que no está actualizado.

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>Conflictos de virtualización de HAXM

HAXM puede entrar en conflicto con otras tecnologías que usan la virtualización, como Hyper-V y Windows Device Guard, y cierto software antivirus:

- **Hyper-V** &ndash; Si está usando Windows con Hyper-V habilitado, siga los pasos de [Deshabilitación de Hyper-V](#disable-hyperv).

- **Device Guard**: Device Guard y Credential Guard pueden impedir que se deshabilite Hyper-V en máquinas con Windows. Para deshabilitar Device Guard y Credential Guard, consulte [Deshabilitación de Device Guard](#disable-devguard).

- **Software antivirus**: si está ejecutando software antivirus que usa la virtualización asistida por hardware (por ejemplo, Avast), deshabilite o desinstale este software, reinicie el equipo e intente iniciar de nuevo el emulador de Android SDK.


#### <a name="incorrect-bios-settings"></a>Configuración del BIOS incorrecta

Si está usando HAXM en un equipo con Windows, HAXM no funcionará a menos que la tecnología de virtualización (Intel VT-x) esté habilitada en el BIOS. Si VT-x está deshabilitada, aparece un error similar al siguiente al intentar iniciar Google Android Emulator:

**This computer meets the requirements for HAXM, but Intel Virtualization Technology (VT-x) is not turned on.** (Este equipo cumple los requisitos de HAXM, pero Intel Virtualization Technology (VT-x) no está activada.)

Para corregir este error, arranque el equipo en el BIOS, habilite VT-x y SLAT (traducción de direcciones de segundo nivel) y reinicie el equipo en Windows.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Si Google Android Emulator no se inicia correctamente, esto suele deberse a problemas relacionados con HAXM. Normalmente los problemas de HAXM son consecuencia de conflictos con otras tecnologías de virtualización, configuraciones incorrectas o un controlador HAXM que no está actualizado. Intente volver a instalar el controlador HAXM mediante los pasos detallados en [Instalación de HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm).

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

<a name="disable-hyperv" />

### <a name="disabling-hyper-v"></a>Deshabilitar Hyper-V

Si usa Windows con Hyper-V habilitado, debe deshabilitarlo y reiniciar el equipo para instalar y usar HAXM. Puede deshabilitar Hyper-V desde el Panel de control mediante estos pasos:

1. En el cuadro de búsqueda de Windows, escriba **Programas** y luego haga clic en el resultado de búsqueda **Programas y características**.

2. En el cuadro de diálogo **Programas y características** del Panel de control, haga clic en **Activar o desactivar las características de Windows**:

    ![Activar o desactivar las características de Windows](troubleshooting-images/win/07-turn-windows-features.png)

3. Desactive **Hyper-V** y reinicie el equipo:

    ![Deshabilitar Hyper-V en el cuadro de diálogo Características de Windows](troubleshooting-images/win/08-uncheck-hyper-v.png)

También puede usar el siguiente cmdlet de Powershell para deshabilitar Hyper-V:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

HAXM de Intel y Microsoft Hyper-V no pueden estar activos al mismo tiempo. Desafortunadamente, actualmente no existe ninguna manera de alternar entre Hyper-V y HAXM sin reiniciar el equipo. Si quiere usar el [Emulador de Visual Studio para Android](~/android/deploy-test/debugging/visual-studio-android-emulator.md) (que depende de Hyper-V), no podrá usar el emulador de Android SDK sin reiniciar. Una manera de usar Hyper-V y HAXM es crear una configuración de arranque múltiple, como se explica en [Creating a no hypervisor boot entry](https://blogs.msdn.microsoft.com/virtual_pc_guy/2008/04/14/creating-a-no-hypervisor-boot-entry/) (Crear una entrada de arranque que no es de hipervisor).

En algunos casos, si Device Guard y Credential Guard están habilitados, con los pasos anteriores no se deshabilitará Hyper-V correctamente. Si no puede deshabilitar Hyper-V (o parece estar deshabilitado pero sigue habiendo errores en la instalación de HAXM), siga los pasos de la sección siguiente para deshabilitar Device Guard y Credential Guard.

<a name="disable-devguard" />

#### <a name="disabling-device-guard"></a>Deshabilitación de Device Guard

Device Guard y Credential Guard pueden evitar que se deshabilite Hyper-V en equipos Windows. Por lo general, suele ser un problema para los equipos unidos a un dominio que se configuran y se controlan mediante una organización propietaria.
En Windows 10, siga estos pasos para comprobar si se está ejecutando **Device Guard**:

1. En **Windows Search**, escriba **Información del sistema** para iniciar la aplicación **Información del sistema**.

2. En **Resumen del sistema**, compruebe si está presente **Seguridad basada en la virtualización de Device Guard** y si su estado es **En ejecución**:

   [![Device Guard está presente y en ejecución](troubleshooting-images/win/09-device-guard-sml.png)](troubleshooting-images/win/09-device-guard.png#lightbox)

Si Device Guard está habilitado, siga estos pasos para deshabilitarlo:

1. Asegúrese de que **Hyper-V** esté deshabilitado (en **Activar o desactivar las características de Windows**) como se explica en la sección anterior.

2. En el cuadro de búsqueda de Windows, escriba **gpedit** y seleccione el resultado de búsqueda **Editar directiva de grupo**. Esto inicia el **Editor de directivas de grupo local**.

3. En el **Editor de directivas de grupo local**, vaya a **Configuración del equipo > Plantillas administrativas > Sistema > Device Guard**:

   [![Device Guard en el Editor de directivas de grupo local](troubleshooting-images/win/10-group-policy-editor-sml.png)](troubleshooting-images/win/10-group-policy-editor.png#lightbox)

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


# <a name="visual-studiotabvsmac"></a>[Visual Studio](#tab/vsmac)

Hyper-V no está disponible en OS X ni macOS.

-----

