---
title: ¿Es posible conectarse a emuladores de Android ejecutando en un Mac de una máquina virtual de Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: a04258e6056b4572856d4ec05c40effec4e51910
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>¿Es posible conectarse a emuladores de Android ejecutando en un Mac de una máquina virtual de Windows?

Para conectarse a un emulador de Google Android que se ejecuta en un equipo Mac desde una máquina virtual de Windows, siga estos pasos:

1.  Inicie el emulador en el equipo Mac.

2.  Eliminar el `adb` servidor en el Mac:

    ```bash
    adb kill-server
    ```

3.  Tenga en cuenta que el emulador está escuchando en 2 puertos TCP en la interfaz de red de bucle invertido:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    El número impar es el puerto utilizado para conectarse a `adb`. Vea también [ http://developer.android.com/tools/devices/emulator.html#emulatornetworking ](http://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4.  _Opción 1_: Use [ `nc` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html) para reenviar los paquetes TCP de entrada recibidos externamente en el puerto 5555 (o cualquier otro puerto desee utilizar) al puerto impares de la interfaz de bucle invertido (**127.0.0.1 5555** en este ejemplo), y para reenviar los paquetes salientes para volver a la otra manera:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0 < backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Siempre y cuando la `nc` comandos de ejecución permanecen en una ventana de Terminal, se reenviarán los paquetes según lo previsto. Puede escribir CTRL-C en la ventana de Terminal para salir de la `nc` comandos una vez que haya terminado con el emulador.

    (Opción 1 es normalmente más fácil que la opción 2, especialmente si **preferencias del sistema > seguridad y privacidad > Firewall** está activada.) 

    _Opción 2_: Use [ `pfctl` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html) para redirigir los paquetes del puerto TCP `5555` (o cualquier otro puerto que desee) en el [compartido de red](http://kb.parallels.com/en/4948) al puerto impares de la interfaz en el interfaz de bucle invertido (`127.0.0.1:5555` en este ejemplo):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Este comando establece el puerto reenvío mediante el `pf packet filter` servicio del sistema. Los saltos de línea son importantes. Asegúrese de mantener intacta cuando copia pegado. También necesitará ajustar el nombre de la interfaz de *vmnet8* si usas Parallels. `vmnet8` es el nombre de la especial *un dispositivo NAT* para el *compartido de red* modo de fusión de VMWare. Es probable que la interfaz de red adecuada en Parallels [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5.  Conectar con el emulador desde el equipo de Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Reemplace "ip dirección-de-the-mac" con la dirección IP de Mac, como por ejemplo mostradas por el `ifconfig vmnet8 | grep 'inet '`. Si es necesario, reemplace `5555` con el otro puerto como desde el paso 4\. (Nota: una manera de obtener acceso de línea de comandos a `adb` es a través de [ **Herramientas > Android > símbolo del sistema de Android Adb** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) en Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Alternar usando técnica `ssh`

Si ha habilitado la _inicio de sesión remoto_ en el equipo Mac, a continuación, puede usar `ssh` reenvío para conectar con el emulador de puerto.

1.  Instale a un cliente de SSH en Windows. Una opción consiste en instalar [Git para Windows](https://git-for-windows.github.io/). El `ssh` comando, a continuación, estará disponible en la **Git Bash** símbolo del sistema.

2.  Siga los pasos del 1 al 3 anteriores para iniciar el emulador, elimine la `adb` servidor en el equipo Mac e identificar los puertos del emulador.

3.  Ejecutar `ssh` en Windows para configurar el reenvío de puerto bidireccional entre un puerto local en Windows (`localhost:15555` en este ejemplo) y el puerto de impares emulador en la interfaz de bucle invertido de Mac (`127.0.0.1:5555` en este ejemplo):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Reemplace `mac-username` con su nombre de usuario de Mac como se muestra por `whoami`. Reemplace `ip-address-of-the-mac` con la dirección IP de Mac.

4.  Conectar con el emulador mediante el puerto local de Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Nota: una forma sencilla de obtener acceso de línea de comandos a `adb` es a través de [ **Herramientas > Android > símbolo del sistema de Android Adb** en Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Una pequeña Precaución: Si utiliza el puerto `5555` para el puerto local, `adb` creer que el emulador se ejecuta localmente en Windows. Esto no causa ningún problema en Visual Studio, pero en Visual Studio para Mac hace que la aplicación se cierre inmediatamente después de iniciar.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Uso de la técnica alternativa `adb -H` aún no se admite

En teoría, otra alternativa sería utilizar `adb`de capacidad integrada para conectarse a un `adb` servidor se ejecuta en un equipo remoto (Véase, por ejemplo, [ http://stackoverflow.com/a/18551325 ](http://stackoverflow.com/a/18551325)).
Pero las extensiones Xamarin.Android IDE no proporcionan actualmente una manera de configurar esta opción.

## <a name="contact-information"></a>Información de contacto

Este documento describe el comportamiento actual a marzo de 2016. La técnica descrita en este documento no es parte de la serie de prueba estable para Xamarin, por lo que podría afectar en el futuro.

Si observa que la técnica deja de funcionar o si observa otros errores en el documento, no dude en Agregar a la discusión en el siguiente subproceso de foro: [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Gracias

