---
title: Emulador de Android de Visual Studio
description: En esta guía se explica cómo se configura y se usa el emulador de Android de Visual Studio para desarrollar aplicaciones de Xamarin.Android en Visual Studio 2015.
ms.prod: xamarin
ms.assetid: CD128CB9-499F-4558-B49F-77248824EFDF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2018
ms.openlocfilehash: bcc88ba4c46aa9abe9038d6ea65bc9c17f337da6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="visual-studio-android-emulator"></a>Emulador de Android de Visual Studio

_En esta guía se explica cómo se configura y usa Android Emulator de Visual Studio para desarrollar aplicaciones Xamarin.Android en Visual Studio 2015._

## <a name="visual-studio-android-emulator-overview"></a>Información general de Android Emulator de Visual Studio

Microsoft Visual Studio 2015 incluye un emulador de Android que se puede usar como destino para depurar una aplicación de Xamarin.Android: el *emulador de Visual Studio para Android*. Este emulador usa las funciones de Hyper-V del equipo de desarrollo, lo que garantiza unos tiempos de inicio y de ejecución más rápidos que los del emulador predeterminado que se incluye con Android SDK. El emulador de Visual Studio para Android puede usarse como alternativa al emulador predeterminado de Android SDK al desarrollar una aplicación de Xamarin.Android.

> [!NOTE]
> El emulador de Android de Visual Studio solo es compatible con Visual Studio 2015 y no funciona con Visual Studio 2017.

En esta guía se explica cómo iniciar el emulador de Microsoft Android desde Visual Studio para probar la aplicación y se describen las distintas características disponibles en el emulador. Obtendrá información sobre cómo seleccionar *perfiles de dispositivo* (similares a las definiciones de dispositivo en el emulador predeterminado de Android SDK) para simular diferentes tipos de dispositivos Android. Por último, en la sección de solución de problemas se explican los riesgos comunes y sus soluciones.

## <a name="requirements"></a>Requisitos

Para ejecutar el emulador, el equipo debe cumplir los requisitos para ejecutar Hyper-V. Hyper-V requiere una versión de 64 bits de la edición Pro de Windows 8, Windows 8.1, Windows 10 o superior. Para obtener más información sobre los requisitos, consulte [Requisitos de sistema del emulador de Visual Studio para Android](https://msdn.microsoft.com/en-us/library/mt228280.aspx).

> [!NOTE]
> No puede usar HAXM (que utiliza el Emulador de Android SDK) mientras esté habilitado Hyper-V. Para obtener más información sobre las limitaciones y los posibles problemas relacionados con HAXM, vea [Conflictos de virtualización de HAXM](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#virt-conflicts).


## <a name="running-the-emulator"></a>Ejecutar el emulador

Visual Studio ofrece varios perfiles preconfigurados del dispositivo de destino en el menú desplegable **Destino de depuración** (como se ve en la siguiente captura de pantalla). Los destinos del emulador de Android de Microsoft vienen precedidos por **VS Emulator**:

[![Perfiles de dispositivo de destino preconfigurados](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs-sml.png)](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs.png#lightbox)

Cuando Visual Studio inicia una aplicación de Xamarin.Android, el emulador se inicia con el destino de dispositivo seleccionado y la aplicación se implementa en el emulador. Aparecerá un mensaje en la esquina inferior izquierda de Visual Studio en el que se indica que se está iniciando el emulador:

[![Inicio del emulador VS](visual-studio-android-emulator-images/02-emulator-starting-vs-sml.png)](visual-studio-android-emulator-images/02-emulator-starting-vs.png#lightbox)

Después de un retraso de inicio, aparece la pantalla del emulador tal como se muestra en la imagen de abajo a la izquierda. Arrastre el icono de candado de la pantalla hacia arriba para desbloquear el dispositivo.
Después, la aplicación de Xamarin.Android debería ejecutarse en el emulador, como se muestra a la derecha:

[![Capturas de pantalla del emulador](visual-studio-android-emulator-images/03-first-screen-vs-sml.png)](visual-studio-android-emulator-images/03-first-screen-vs.png#lightbox)

Al igual que en el emulador predeterminado de Android SDK, es posible establecer puntos de interrupción en el código, inspeccionar variables y ver la pila de llamadas. La barra de herramientas vertical a la derecha del emulador proporciona acceso a las características del emulador:

[![Botones de la barra de herramientas vertical](visual-studio-android-emulator-images/04-vertical-toolbar-vs-sml.png)](visual-studio-android-emulator-images/04-vertical-toolbar-vs.png#lightbox)

En la lista siguiente se resume la función de cada botón de la barra de herramientas vertical:

-   **Cerrar**: cierra la aplicación de emulador. Este botón no se usa a menudo, ya que, normalmente, el emulador se deja en ejecución después del primer inicio (para evitar el retraso de reinicio del emulador) y solo se cierra cuando ya no es necesario.

-   **Minimizar**: deja el emulador en ejecución, pero lo minimiza en la barra de tareas.

-   **Inicio/Apagado**: simula la activación y la desactivación del dispositivo. (El emulador sigue ejecutándose).

-   **Multitáctil**: superpone varios puntos en la pantalla del dispositivo que actúan como puntos táctiles para reducir y acercar.
    Si se arrastra un punto, el otro punto se mueve en la dirección opuesta, lo que simula el movimiento con dos dedos.

-   **Entrada de un único punto**: devuelve el dispositivo a la entrada de un único punto (después de usar la entrada multitáctil).

-   **Girar a la izquierda/Girar a la derecha**: ayuda a probar cómo responde la aplicación a los cambios de orientación. Por ejemplo, la primera vez que se haga clic en el botón *Girar a la izquierda*, el emulador cambiará al modo horizontal. Cuando se pulse el botón *Girar a la derecha*, el emulador volverá al modo vertical.

-   **Ajustar a la pantalla**: amplía el tamaño de la pantalla del emulador para que se ajuste a la pantalla del escritorio.

-   **Zoom**: ajusta la pantalla del emulador a una escala del 33 %, 50 %, 66 %, 100 % o un porcentaje personalizado determinado.


El botón de *herramientas adicionales* abre un cuadro de diálogo en el que se muestran las características adicionales del emulador:

[![Cuadro de diálogo Additional Tools (Herramientas adicionales)](visual-studio-android-emulator-images/05-additional-tools-vs-sml.png)](visual-studio-android-emulator-images/05-additional-tools-vs.png#lightbox)


Cada característica adicional está disponible en una fila de pestañas en la parte superior del cuadro de diálogo:

-   **Acelerómetro**: simula el movimiento del dispositivo en un espacio 3D.

-   **Ubicación**: presenta un mapa que se puede usar para seleccionar y simular una ubicación de GPS. En este mapa, se pueden crear *puntos de mapa* para simular el movimiento entre ubicaciones.

-   **Batería**: proporciona un control deslizante para simular la cantidad de carga que queda en la batería.

-   **Captura de pantalla**: en esta pestaña, el botón **Capturar** toma una captura de pantalla y muestra una vista previa instantánea. El botón *Guardar* guardará la captura de pantalla.

-   **Cámara**: simula que se toma una fotografía mediante una imagen animada fija, una imagen de un archivo o una cámara web conectada al equipo host. Es posible seleccionar la cámara frontal o trasera.

-   **Tarjeta SD**: el emulador puede hacer que una carpeta del equipo host esté disponible para el dispositivo como tarjeta SD.
    Cuando la aplicación lee y escribe archivos en la tarjeta SD simulada, es posible tener acceso a ellos directamente desde el escritorio sin usar el comando `adb`.

-   **Red**: muestra un resumen de la configuración de red del emulador (el emulador reutiliza la conexión de red del equipo host).

Para obtener más información sobre el uso de estas características, consulte [Introducing Visual Studio's Emulator for Android](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/) (Introducción al emulador de Visual Studio para Android).



## <a name="configuring-device-profiles"></a>Configurar perfiles de dispositivos

El emulador de Android de Microsoft incluye un conjunto de perfiles de dispositivo que representan las versiones, tamaños de pantalla y propiedades de hardware más populares de los dispositivos Android del mercado. Además, estos perfiles de dispositivo ya están configurados para varias versiones de Android, como KitKat, Lollipop y Marshmallow.

El *administrador del emulador* se usa para instalar, desinstalar e iniciar perfiles de dispositivo. En el menú **Herramientas**, seleccione **Emulador de Visual Studio para Android…** como se indica en esta captura de pantalla:

[![Inicio del emulador desde el menú Herramientas](visual-studio-android-emulator-images/06-launch-emulator-manager-vs-sml.png)](visual-studio-android-emulator-images/06-launch-emulator-manager-vs.png#lightbox)

Se abrirá el cuadro de diálogo **Device Profiles** (Perfiles de dispositivo). Los perfiles instalados aparecen resaltados en la parte superior de la lista de perfiles de dispositivo. Los perfiles que no están instalados (pero están disponibles para la instalación) aparecen atenuados:

[![Iconos de Device Profiles (Perfiles de dispositivo)](visual-studio-android-emulator-images/07-device-profiles-vs-sml.png)](visual-studio-android-emulator-images/07-device-profiles-vs.png#lightbox)

Para instalar un perfil nuevo, haga clic en el icono de instalación del perfil (una flecha que apunta hacia abajo, como se muestra en la captura de pantalla anterior). Por ejemplo, al hacer clic en el icono de instalación del perfil **5.7" Marshmallow (6.0.0) XHDPI Phone**, el administrador del emulador descarga el perfil como se muestra aquí:

[![Ejemplo de descarga de perfiles](visual-studio-android-emulator-images/08-downloading-profile-vs-sml.png)](visual-studio-android-emulator-images/08-downloading-profile-vs.png#lightbox)

Una vez que se haya descargado el perfil de dispositivo, se resalta para indicar que el perfil se ha instalado correctamente. Haga clic en el icono *Mostrar detalles* para ver el tipo de plataforma, la arquitectura de CPU, el tamaño o la resolución de la pantalla y la memoria disponible en el dispositivo:

[![Detalles de perfil de dispositivo](visual-studio-android-emulator-images/09-show-details-vs-sml.png)](visual-studio-android-emulator-images/09-show-details-vs.png#lightbox)

Cuando se abra el menú desplegable **Destino de depuración** de Visual Studio, el perfil de dispositivo recién instalado estará disponible como destino:

[![Perfil nuevo en el menú desplegable de destino](visual-studio-android-emulator-images/10-debug-target-vs-sml.png)](visual-studio-android-emulator-images/10-debug-target-vs.png#lightbox)

Para reducir esta lista, haga clic en **Desinstalar este perfil** en el *administrador del emulador*. De este modo, se quitarán los perfiles de dispositivo que no se usan. Tenga en cuenta que actualmente no se puede crear un perfil de dispositivo personalizado en este emulador.


## <a name="troubleshooting"></a>Solución de problemas

En esta sección se describen algunos errores comunes y soluciones alternativas al usar el *emulador de Visual Studio para Android* con Xamarin.Android.

<a name="cant_connect" />

### <a name="emulator-will-not-start"></a>El emulador no se inicia

En algunos casos, el emulador no se inicia si hay incompatibilidades entre el procesador host y la máquina virtual de Hyper-V. Para solucionar este problema, configure Hyper-V de modo que limite las características de procesador que puede tener una máquina virtual. Así mejora la compatibilidad de la máquina virtual con versiones diferentes del procesador host.
Para realizar este cambio, siga estos pasos:

1.  Haga clic en el botón **Inicio**, escriba **MMC** y pulse **ENTRAR**. Haga clic en **Administrador de Hyper-V**, como se muestra aquí:

    [![Administrador de Hyper-V](visual-studio-android-emulator-images/15-launch-hyperv-manager.png)](visual-studio-android-emulator-images/15-launch-hyperv-manager.png#lightbox)

2.  En el panel **Máquinas virtuales** del Administrador de Hyper-V, haga clic con el botón derecho en el emulador que va a editar y haga clic en **Configuración…**:

    [![Elemento de menú Virtual Machines Settings (Configuración de máquinas virtuales)](visual-studio-android-emulator-images/16-vm-settings.png)](visual-studio-android-emulator-images/16-vm-settings.png#lightbox)

3.  En la ventana de configuración, busque la sección **Compatibilidad** (en **Hardware > Procesador**) y habilite **Migrar a un equipo físico con una versión de procesador distinta**:

    [![Opción de migración activada](visual-studio-android-emulator-images/17-set-compatibility-vs-sml.png)](visual-studio-android-emulator-images/17-set-compatibility-vs.png#lightbox)

4.  Haga clic en **Aceptar** y cierre la ventana del Administrador de Hyper-V.



### <a name="app-deploys-and-starts-but-fails-immediately"></a>La aplicación se implementa y se inicia, pero se produce un error inmediatamente

En esta situación, el emulador se inicia, la aplicación se implementa correctamente en el emulador y la aplicación se inicia, pero inmediatamente se produce un error en la aplicación.
En muchos casos, esto también se debe a incompatibilidades entre el procesador host y la máquina virtual de Hyper-V. Para resolver este error, siga las instrucciones que se indican en [El emulador no se inicia](#cant_connect) (anteriormente).


### <a name="emulator-stops-with-the-diagnostic-message-libaot-mscorlibdllso-not-found"></a>El emulador se detiene con el mensaje de diagnóstico **No se encontró libaot-mscorlib.dll.so**

Para resolver este error, siga estos pasos para deshabilitar la implementación rápida:

1.  Siga las instrucciones que se indican en [El emulador no se inicia](#cant_connect) (anteriormente).

2.  Haga doble clic en las **Propiedades** del proyecto.

3.  Haga clic en **Android Options** (Opciones de Android) y anule la selección de **Use Fast Deployment (debug mode only)** (Usar implementación rápida (solo en modo de depuración)):

    [![Opción Use Fast Deployment (Usar implementación rápida) desactivada](visual-studio-android-emulator-images/18-fast-deployment-vs-sml.png)](visual-studio-android-emulator-images/18-fast-deployment-vs.png#lightbox)



### <a name="drag-and-drop-does-not-work"></a>La operación de arrastrar y colocar no funciona

Si inicia como administrador el *emulador de Visual Studio para Android* (o si lo inicia desde Visual Studio mientras este se está ejecutando con privilegios de administrador), la operación de arrastrar y colocar archivos .APK o .ZIP podría no funcionar. Para solucionar este problema, ejecute el *emulador de Visual Studio para Android* sin permisos elevados (es decir, no como administrador).


### <a name="other-errors"></a>Otros errores

Las anteriores sugerencias tratan sobre los problemas más habituales al usar el emulador de Android de Visual Studio con Xamarin.Android. Para obtener información más completa sobre la solución de problemas del emulador de Android de Visual Studio, consulte [Solución de problemas del emulador de Visual Studio para Android](https://msdn.microsoft.com/en-us/library/mt228282.aspx).



## <a name="summary"></a>Resumen

En este artículo de introducción al *emulador de Visual Studio para Android*, se ha explicado cómo usar el emulador para depurar aplicaciones de Xamarin.Android en Visual Studio, se han descrito las funciones de los botones de la barra de herramientas vertical y se ha proporcionado un breve resumen de las características disponibles en el cuadro de diálogo de **herramientas adicionales**. En él se explica cómo usar el *administrador del emulador* para instalar, desinstalar e iniciar perfiles de dispositivo. En la sección *Solución de problemas*, se han explicado los problemas comunes y las soluciones alternativas al usar el emulador.


## <a name="related-links"></a>Vínculos relacionados

- [Emulador de Visual Studio para Android](https://www.visualstudio.com/en-us/explore/msft-android-emulator-vs.aspx)
- [Introducing Visual Studio's Emulator for Android](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/) (Introducción al Emulador de Visual Studio para Android)
