---
title: Depurar en un dispositivo de uso
description: "En este artículo se explica cómo depurar una aplicación de uso de Xamarin.Android en un dispositivo de uso."
ms.topic: article
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 5ec627fad1695bab8d05d75a5089fe849ea2fd75
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="debug-on-a-wear-device"></a>Depurar en un dispositivo de uso

_En este artículo se explica cómo depurar una aplicación de uso de Xamarin.Android en un dispositivo de uso._


## <a name="overview"></a>Información general

Si tiene un dispositivo Android desgaste como un Smartwatch desgaste Android, puede ejecutar la aplicación en el dispositivo en lugar de utilizar un emulador. (Si no está familiarizado con el proceso de implementación y ejecución de aplicaciones usan Android, consulte [desgaste Hello,](~/android/wear/get-started/hello-wear.md).)

## <a name="prepare-the-wear-device"></a>Preparar el dispositivo de uso:

Siga estos pasos para habilitar la depuración en el dispositivo Android desgaste:

1.  Abra la **configuración** menú en el dispositivo Android desgaste.

2.  Desplácese hasta la parte inferior del menú y puntee **sobre**.

3.  Puntee en el número de compilación 7 veces.

4.  En el **configuración** menú, puntee **opciones del desarrollador**.

5.  Confirme que **ADB depuración** está habilitado.


## <a name="debugging-over-usb"></a>Depuración a través de USB

Si el dispositivo de desgaste tiene un puerto USB, puede conectar el dispositivo de uso en el equipo, implementar en el mismo y ejecutar o depurar la aplicación como lo haría con un teléfono Android (para obtener más información, consulte [depurar en un dispositivo](~/android/deploy-test/debugging/debug-on-device.md)).


## <a name="debugging-over-bluetooth"></a>Depuración a través de Bluetooth

Si el dispositivo desgaste no tiene un puerto USB, puede implementar la aplicación en el dispositivo de uso a través de Bluetooth mediante el enrutamiento de salida de depuración de la aplicación a un teléfono Android que esté conectado al equipo. 

### <a name="prepare-your-phone"></a>Preparar el teléfono

Utilice los pasos siguientes para preparar su teléfono para hacer que las conexiones de Bluetooth en el dispositivo de uso: 

1.  Si no lo ha hecho ya, configure el teléfono para el desarrollo de Xamarin.Android como se explica en [establecer dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

2.  Descargar e instalar gratuitamente [desgaste Android](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) aplicación desde Google Play Store.

### <a name="connect-the-device"></a>Conecte el dispositivo

Siga estos pasos para conectar el dispositivo desgaste a su teléfono:

1.  En el teléfono que actúan como intermediarios de Bluetooth (configurada anteriormente), inicie la aplicación Android desgaste. 

2.  Pulse la **configuración** icono.

3.  Habilitar **depuración a través de Bluetooth**. Debería ver los estados siguientes que se muestra en la pantalla de la aplicación usan Android:

        Host: disconnected
        Target: connected

4.  Conecte el teléfono a su equipo a través de USB. En el equipo, escriba los siguientes comandos:

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Si el puerto 4444 no está disponible, puede usar cualquier otro puerto disponible para el que tiene acceso. 

    **Tenga en cuenta**: si reinicia Visual Studio o Visual Studio para Mac, debe ejecutar estos comandos para configurar una conexión con el dispositivo de uso.

5.  Cuando el dispositivo desgaste solicite, confirme que va a permitir **ADB depuración**. En la aplicación Android desgaste, debería ver el estado cambie a:

        Host: connected
        Target: connected

6.  Después de completar los pasos anteriores, ejecutando `adb devices` muestra el estado de teléfono y el dispositivo Android desgaste:

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

En este momento, puede implementar la aplicación en el dispositivo de uso.

<a name="screenshots"/>

### <a name="taking-screenshots"></a>Realizar capturas de pantalla

Puede tomar una captura de pantalla del dispositivo desgaste escribiendo el comando siguiente: 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copiar la captura de pantalla en el equipo, escriba el comando siguiente:

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Eliminar la captura de pantalla en el dispositivo, escriba el comando siguiente:

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>Desinstalar una aplicación

Puede desinstalar una aplicación desde el dispositivo desgaste escribiendo el comando siguiente:

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Por ejemplo, para quitar la aplicación con el nombre del paquete `com.xamarin.weartest`, escriba el siguiente comando:

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Para obtener más información sobre la depuración de dispositivos Android desgaste a través de Bluetooth, consulte [depuración a través de Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Depurar una aplicación de uso con una aplicación de teléfono complementarias

Aplicaciones de Android desgaste se empaquetan con una aplicación de teléfono Android complementaria para su distribución en Google Play (para obtener más información, consulte [trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md)). Sin embargo, todavía desarrollar la aplicación de uso y su aplicación complementaria por separado. Al lanzar la aplicación a través de Google Play Store, la aplicación de uso se empaqueta con la aplicación complementaria y se instala automáticamente si es posible.

Para depurar la aplicación de uso con una aplicación complementaria: 

1.  Compilar e implementar la aplicación complementaria en el teléfono.

2.  Haga clic en el proyecto de desgaste y establézcalo como proyecto de inicio predeterminado.

3.  Implementar el proyecto de uso en el dispositivo wearable.

4.  Ejecutar y depurar la aplicación de uso en el dispositivo.

 
## <a name="summary"></a>Resumen

Este artículo explica cómo configurar un dispositivo Android desgaste para depuración desgaste desde Visual Studio a través de Bluetooth y cómo depurar una aplicación de uso con una aplicación de teléfono complementario. También se proporcionan sugerencias de depuración comunes para depurar una aplicación de uso a través de Bluetooth.
