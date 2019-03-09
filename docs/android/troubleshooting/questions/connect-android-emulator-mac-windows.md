---
title: ¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 35bfdb92ccfffe54f0ca10dc001d8919703a5bd8
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668158"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?

Para conectar con el emulador de Android que se ejecutan en un equipo Mac desde una máquina virtual de Windows, siga estos pasos:

1.  Iniciar el emulador en el equipo Mac.

2.  Eliminar el `adb` server en el equipo Mac:

    ```bash
    adb kill-server
    ```

3.  Tenga en cuenta que el emulador está escuchando en 2 puertos TCP en la interfaz de bucle invertido de red:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    El número impar es el puerto utilizado para conectarse a `adb`. Vea también [ https://developer.android.com/tools/devices/emulator.html#emulatornetworking ](https://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4.  _Opción 1_: Use [`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    para reenviar paquetes entrantes TCP reciben externamente en el puerto 5555 (o cualquier otro puerto que desee utilizar) al puerto impares de la interfaz de bucle invertido (**127.0.0.1 5555** en este ejemplo), y para reenviar los paquetes salientes de nuevo la otra manera:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Siempre y cuando el `nc` comandos de ejecución permanecen en una ventana de Terminal, se reenviará los paquetes según lo previsto. Puede escribir CTRL-C en la ventana de Terminal para salir de la `nc` comandos una vez que haya terminado con el emulador.

    (Opción 1 normalmente es más fácil que la opción 2, especialmente si **preferencias del sistema > seguridad y privacidad > Firewall** está encendida.) 

    _Opción 2_: Use [`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    Para redirigir los paquetes del puerto TCP `5555` (o cualquier otro puerto que desee) en el [compartido de red](http://kb.parallels.com/en/4948) interfaz al puerto impares de la interfaz de bucle invertido (`127.0.0.1:5555` en este ejemplo):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Este comando configura el puerto reenvío mediante el `pf packet filter` servicio del sistema. Los saltos de línea son importantes. Asegúrese de mantener intacto cuando se copia pegado. También deberá ajustar el nombre de la interfaz de *vmnet8* si usa Parallels. `vmnet8` es el nombre de especial *dispositivo NAT* para el *compartido de red* modo en VMWare Fusion. Es probable que la interfaz de red adecuada en Parallels [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5.  Conectarse al emulador desde la máquina de Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Reemplace "ip: dirección-de-la-mac" con la dirección IP del equipo Mac, como por ejemplo mostradas por el `ifconfig vmnet8 | grep 'inet '`. Si fuera necesario, reemplace `5555` con el otro puerto que desee desde el paso 4\. (Nota: una manera de obtener acceso de línea de comandos a `adb` es a través de [ **Herramientas > Android > Android símbolo del sistema Adb** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) en Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Alternar utilizando técnica `ssh`

Si ha habilitado _inicio de sesión remoto_ en el equipo Mac, a continuación, puede usar `ssh` reenvío para conectarse al emulador de puerto.

1.  Instale a un cliente SSH en Windows. Una opción consiste en instalar [Git para Windows](https://git-for-windows.github.io/). El `ssh` comando estarán disponible en el **Git Bash** símbolo del sistema.

2.  Siga los pasos del 1 al 3 anteriores para iniciar el emulador, elimine el `adb` server en el equipo Mac e identificar los puertos del emulador.

3.  Ejecute `ssh` en Windows para configurar el reenvío de puerto bidireccional entre un puerto local en Windows (`localhost:15555` en este ejemplo) y el puerto impares emulador en la interfaz de bucle invertido del equipo Mac (`127.0.0.1:5555` en este ejemplo):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Reemplace `mac-username` con el nombre de usuario de Mac como se muestra con `whoami`. Reemplace `ip-address-of-the-mac` con la dirección IP del equipo Mac.

4.  Conectar con el emulador con el puerto local en Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Nota: una forma sencilla de obtener acceso de línea de comandos a `adb` es a través de [ **Herramientas > Android > Android símbolo del sistema Adb** en Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Una pequeño Precaución: si usa el puerto `5555` para el puerto local, `adb` creer que el emulador se ejecuta localmente en Windows. Esto no causa ningún problema en Visual Studio, pero en Visual Studio para Mac hace que la aplicación salir inmediatamente después de iniciarla.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Uso de la técnica alternativa `adb -H` aún no se admite

En teoría, otro enfoque sería usar `adb`de capacidad integrada para conectarse a un `adb` server que se ejecutan en un equipo remoto (ver por ejemplo [ https://stackoverflow.com/a/18551325 ](https://stackoverflow.com/a/18551325)).
Pero las extensiones del IDE de Xamarin.Android no proporciona actualmente una manera de configurar dicha opción.

## <a name="contact-information"></a>Información de contacto

Este documento describe el comportamiento actual a partir de marzo de 2016. La técnica descrita en este documento no forma parte de la serie de pruebas estable para Xamarin, por lo que podría dejar de hacerlo en el futuro.

Si observa que la técnica deja de funcionar o si observa que los otros errores en el documento, puede agregar a la discusión en el siguiente subproceso de foro: [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Gracias

