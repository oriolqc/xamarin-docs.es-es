---
title: Aceleración de hardware de Android Emulator
description: Cómo preparar el equipo para lograr el máximo rendimiento de Google Android Emulator
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/10/2018
ms.openlocfilehash: 2f0bb6f1371b9ce1b925b876851d58f3c4d01419
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2018
---
# <a name="android-emulator-hardware-acceleration"></a>Aceleración de hardware de Android Emulator

Google Android Emulator es demasiado lento sin aceleración de hardware. Es posible mejorar drásticamente el rendimiento de Google Android Emulator con imágenes especiales de hardware de emulador destinadas al hardware de x86 y una de dos tecnologías de virtualización:

1. **Hyper-V de Microsoft e Hypervisor Platform**: Hyper-V es un componente de virtualización disponible en Windows 10 que permite ejecutar sistemas informáticos virtualizados sobre un host físico. Se trata de la tecnología de virtualización recomendada para las imágenes aceleradas de Google Android Emulator. Para obtener más información sobre Hyper-V, vea la [guía de Hyper-V en Windows 10](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/).
2. **Hardware Accelerated Execution Manager (HAXM) de Intel** &ndash; es un motor de virtualización para equipos con CPU de Intel. Se trata del motor de virtualización recomendado para los desarrolladores que no pueden usar Hyper-V.

Android SDK Manager usa automáticamente la aceleración de hardware si está disponible y ejecuta una imagen de emulador específica para un dispositivo virtual basado en **x86** (como se explica en [Configuración y uso](~/android/deploy-test/debugging/android-sdk-emulator/index.md)).

## <a name="hyper-v-overview"></a>Información general de Hyper-V

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](~/media/shared/preview.png)

> [!NOTE]
> Hyper-V se encuentra actualmente en versión preliminar.

Se recomienda encarecidamente a los desarrolladores que usen Windows 10 (actualización de abril de 2018) que empleen Hyper-V de Microsoft. Visual Studio Tools para Xamarin facilita que los desarrolladores prueben y depuren las aplicaciones Xamarin.Android en situaciones en las que un dispositivo Android no está disponible o no es práctico.

Para empezar a usar Hyper-V y Google Android Emulator, siga estos pasos:

1. **Aplique la actualización de Windows 10 de abril de 2018 (compilación 1803)**: para comprobar qué versión de Windows se está ejecutando, haga clic en la barra de búsqueda de Cortana y escriba **Acerca de**. Seleccione **About your PC** (Acerca del equipo) en los resultados de búsqueda. Desplácese hacia abajo por el cuadro de diálogo **Acerca de** hasta la sección **Especificaciones de Windows**. La **versión** debe ser al menos la 1803:

    [![Especificaciones de Windows](hardware-acceleration-images/win/12-about-windows.w10-sml.png)](hardware-acceleration-images/win/12-about-windows.w10.png#lightbox)

2. **Habilite Hyper-V y Windows Hypervisor Platform**: en la barra de búsqueda de Cortana, escriba **Activar o desactivar características de Windows**.
   Desplácese hacia abajo por el cuadro de diálogo **Características de Windows** y asegúrese de que **Windows Hypervisor Platform** está habilitado.

    [![Hyper-V y Windows Hypervisor Platform habilitados](hardware-acceleration-images/win/13-windows-features.w10-sml.png)](hardware-acceleration-images/win/13-windows-features.w10.png#lightbox)

    Puede ser necesario reiniciar Windows después de habilitar Hyper-V y Windows Hypervisor Platform.

3. **Instale [Visual Studio 15.8 Preview 1 o una versión posterir](https://www.visualstudio.com/vs/preview/)** &ndash; Esta versión de Visual Studio proporciona compatibilidad con el IDE para iniciar Google Android Emulator con compatibilidad con Hyper-V.

4. **Instale el paquete 27.2.7 o superior de Google Android Emulator** &ndash; Para instalar este paquete, vaya a **Herramientas > Android > Android SDK Manager** en Visual Studio. Seleccione la pestaña **Herramientas** y asegúrese de que la versión del componente Android Emulator sea como mínimo la 27.2.7.

    [![Cuadro de diálogo Herramientas y Android SDK](hardware-acceleration-images/win/14-sdk-manager.w158-sml.png)](hardware-acceleration-images/win/14-sdk-manager.w158.png#lightbox)

5. Si la versión de Android Emulator es inferior a la 27.3.1, siga el paso adicional de solución alternativa que se explica en **Problemas conocidos** (a continuación).


### <a name="known-issues"></a>Problemas conocidos

-   Si la versión del emulador es al menos la 27.2.7, pero inferior a la 27.3.1, esta solución alternativa es necesaria para usar Hyper-V:
    1.  En la carpeta **C:\\Usuarios\\_nombre de usuario_\\.android**, cree un archivo denominado **advancedFeatures.ini**, si aún no existe.
    2.  Agregue la siguiente línea a **advancedFeatures.ini**:
        ```
        WindowsHypervisorPlatform = on
        ```

-   El rendimiento puede reducirse al usar determinados procesadores basados en Intel y AMD.

-   La aplicación Android puede tardar una cantidad de tiempo inusual en cargarse durante la implementación.

-   Un error de acceso a MMIO puede evitar intermitentemente el arranque de Android Emulator. Al reiniciar el emulador, dicho error se solucionará.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

La compatibilidad con Hyper-V requiere Windows 10. Vea los [requisitos de Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v#check-requirements) para obtener más detalles.

-----

## <a name="haxm-overview"></a>Información general sobre HAXM

HAXM es un motor de virtualización asistida por hardware (hipervisor) que usa la tecnología de virtualización (VT) Intel para acelerar la emulación de aplicaciones Android en un equipo host. En combinación con las imágenes de emulador x86 de Android proporcionadas por Intel y el Android SDK Manager oficial, HAXM permite una emulación de Android más rápida en sistemas compatibles con VT. 

Si está desarrollando en un equipo con una CPU de Intel que tiene funciones de VT, puede aprovechar las ventajas de HAXM para acelerar considerablemente Google Android Emulator. Si no sabe si la CPU es compatible con VT, vea [Determine If Your Processor Supports Intel Virtualization Technology](https://www.intel.com/content/www/us/en/support/processors/000005486.html) (Determinar si el procesador admite Intel Virtualization Technology).

> [!NOTE]
> No se puede ejecutar un emulador acelerado por una máquina virtual dentro de otra, como sería el caso de una hospedada en VirtualBox, VMWare o Docker. Es necesario ejecutar el emulador de Android de Google [directamente en el hardware del sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Conviene comprobar que HAXM esté instalado y disponible para Google Android Emulator antes de usar esta herramienta por primera vez.

### <a name="verifying-haxm-installation"></a>Comprobación de la instalación de HAXM

Para comprobar si HAXM está disponible, consulte la ventana **Starting Android Emulator** (Iniciando el emulador de Android) cuando se inicie el emulador. Para iniciar Google Android Emulator, haga lo siguiente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie el administrador del emulador de Android. Para ello, haga clic en **Herramientas > Android > Administrador del emulador de Android**:

    [![Ubicación del elemento de menú Administrador del emulador de Android](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. Si ve un cuadro de diálogo de **advertencia de rendimiento** parecido al siguiente, HAXM aún no está instalado o correctamente configurado en su equipo:

    ![Cuadro de diálogo de advertencia de rendimiento que indica que HAXM no está listo](hardware-acceleration-images/win/11-perf-warn.png)

   Si se muestra una cuadro de diálogo de **advertencia de rendimiento** como este, consulte [Advertencias de rendimiento](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn) para identificar su causa y resolver el problema.

3. Seleccione una imagen **x86** (por ejemplo, **VisualStudio\_android-23\_x86\_phone**), haga clic en **Iniciar** y, después, en **Inicio**:

    ![Inicio de Google Android Emulator con una imagen de dispositivo virtual predeterminada](hardware-acceleration-images/win/02-start-default-avd.png)

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

    [![Inicio de Google Android Emulator con una imagen de dispositivo virtual predeterminada](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. Espere a que aparezca el cuadro de diálogo **Starting Android Emulator** (Iniciando el emulador de Android) mientras se inicia el emulador. Si HAXM está instalado, verá el mensaje **HAX is working and emulator runs in fast virt mode** (HAX funciona y el emulador se ejecuta en modo virtual rápido), como se muestra en esta captura de pantalla:

    ![HAXM aparece disponible en el cuadro de diálogo Starting Android Emulator (Iniciando el emulador de Android)](hardware-acceleration-images/mac/03-haxm-detected.png)

   Si HAXM no está disponible en el equipo (por ejemplo, si ve un mensaje de error como _Asegúrese de que Intel HAXM está correctamente instalado y se puede usar_), siga los pasos de la sección siguiente para instalar HAXM.


-----

<a name="install-haxm" />

### <a name="installing-haxm"></a>Instalación de HAXM

Si el emulador no se inicia, es posible que tenga que instalar HAXM manualmente. En la página de [Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager) hay disponibles paquetes de instalación de HAXM para Windows y macOS. Siga estos pasos para descargar e instalar HAXM manualmente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el sitio web de Intel, descargue la última versión del instalador del [motor de virtualización de HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) para Windows. La ventaja de descargar el instalador de HAXM directamente desde el sitio web de Intel es que se asegura de que esté usando la versión más reciente.

   También puede usar SDK Manager para descargar el instalador de HAXM (en SDK Manager, haga clic en **Herramientas > Extras > Intel x86 Emulator Accelerator (instalador de HAXM)**). Android SDK suele descargar el instalador de HAXM en la ubicación siguiente:

   **C:\\Archivos de programa (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager**

   Tenga en cuenta que el administrador de SDK no instala HAXM, simplemente descarga el instalador HAXM en la ubicación anterior; tendrá que seguir iniciándolo de forma manual.

2. Ejecute **intelhaxm-android.exe** para iniciar el instalador de HAXM. Acepte los valores predeterminados en los cuadros de diálogo del instalador:

   ![Ventana de instalación de Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/05-haxm-installer.png)

## <a name="hardware-acceleration-and-amd-cpus"></a>Aceleración de hardware y CPU AMD

Dado que el emulador de Android de Google admite la aceleración de hardware AMD [únicamente en Linux](https://developer.android.com/studio/run/emulator-acceleration.html#dependencies), esta no está disponible para equipos basados en AMD que ejecuten Windows.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el sitio web de Intel, descargue la última versión del instalador del [motor de virtualización de HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) para macOS.

2. Ejecute el instalador de HAXM. Acepte los valores predeterminados en los cuadros de diálogo del instalador:

   [![Ventana de instalación de Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----


## <a name="related-links"></a>Vínculos relacionados

* [Ejecutar aplicaciones en Android Emulator](https://developer.android.com/studio/run/emulator)
