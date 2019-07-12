---
title: Depuración en un dispositivo Wear
description: En este artículo se explica cómo depurar una aplicación de desgaste de Xamarin.Android en un dispositivo Wear.
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 816ec5c861b5889e1735eab6293ed10318c53644
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831879"
---
# <a name="debug-on-a-wear-device"></a>Depuración en un dispositivo Wear

_En este artículo se explica cómo depurar una aplicación de desgaste de Xamarin.Android en un dispositivo Wear._


## <a name="overview"></a>Información general

Si tiene un dispositivo Android Wear, como un Smartwatch Android Wear, puede ejecutar la aplicación en el dispositivo en lugar de usar un emulador. (Si todavía no está familiarizado con el proceso de implementar y ejecutar aplicaciones de Android Wear, vea [Hola, Wear](~/android/wear/get-started/hello-wear.md).)

## <a name="prepare-the-wear-device"></a>Preparar el dispositivo Wear:

Para habilitar la depuración en el dispositivo Android Wear, siga estos pasos:

1.  Abra el **configuración** menú en el dispositivo Android Wear.

2.  Desplácese hasta la parte inferior del menú y pulse **sobre**.

3.  Puntee en el número de compilación 7 veces.

4.  En el **configuración** menú, pulse **opciones del desarrollador**.

5.  Confirme que **ADB depuración** está habilitado.


## <a name="debugging-over-usb"></a>Depuración a través de USB

Si el dispositivo Wear tiene un puerto USB, puede conectar el dispositivo Wear a su equipo, implementar en él y ejecutar o depurar la aplicación como lo haría con un teléfono Android (para obtener más información, consulte [depurar en un dispositivo](~/android/deploy-test/debugging/debug-on-device.md)).


## <a name="debugging-over-bluetooth"></a>Depuración a través de Bluetooth

Si el dispositivo Wear no tiene un puerto USB, puede implementar la aplicación en el dispositivo Wear a través de Bluetooth mediante el enrutamiento de salida de depuración de la aplicación a un teléfono Android que está conectado al equipo. 

### <a name="prepare-your-phone"></a>Preparar el teléfono

Utilice los pasos siguientes para preparar su teléfono para realizar conexiones Bluetooth al dispositivo Wear: 

1.  Si aún no lo ha hecho, configure el teléfono para el desarrollo de Xamarin.Android como se explica en [configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

2.  Descargue e instale la versión gratuita [Android Wear](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) aplicación desde el Store de Google Play.

### <a name="connect-the-device"></a>Conecte el dispositivo

Siga estos pasos para conectar el dispositivo de desgaste a su teléfono:

1.  En el teléfono que actúe como intermediario de Bluetooth (configurada anteriormente), inicie la aplicación Android Wear. 

2.  Pulse el **configuración** icono.

3.  Habilitar **depuración a través de Bluetooth**. Debería ver el estado del siguiente aparece en la pantalla de la aplicación Android Wear:

        Host: disconnected
        Target: connected

4.  Conecte el teléfono a su equipo a través de USB. En el equipo, escriba los siguientes comandos:

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Si el puerto 4444 no está disponible, puede usar cualquier puerto disponible al que se tiene acceso. 

    > [!NOTE]
    > Si reinicia Visual Studio o Visual Studio para Mac, debe ejecutar estos comandos para configurar una conexión al dispositivo Wear.

5.  Cuando el dispositivo Wear le solicite, confirme que permitan **ADB depuración**. En la aplicación Android Wear, debería ver el estado cambie a:

        Host: connected
        Target: connected

6.  Después de completar los pasos anteriores, ejecute `adb devices` muestra el estado de teléfono y el dispositivo Android Wear:

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

En este momento, puede implementar la aplicación en el dispositivo de desgaste.

<a name="screenshots" />

### <a name="taking-screenshots"></a>Realizar capturas de pantalla

Puede realizar una captura de pantalla del dispositivo Wear escribiendo el comando siguiente: 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copie la captura de pantalla en el equipo escribiendo el comando siguiente:

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Eliminar la captura de pantalla en el dispositivo escribiendo el comando siguiente:

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>Desinstalación de una aplicación

Puede desinstalar una aplicación desde el dispositivo wear escribiendo el comando siguiente:

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Por ejemplo, para quitar la aplicación con el nombre del paquete `com.xamarin.weartest`, escriba el siguiente comando:

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Para obtener más información sobre la depuración de los dispositivos Android Wear a través de Bluetooth, consulte [depuración a través de Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Depurar una aplicación Wear con una aplicación complementaria de teléfono

Aplicaciones de Android Wear se empaquetan con una aplicación complementaria de teléfono Android para su distribución en Google Play (para obtener más información, consulte [trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md)). Sin embargo, todavía desarrollar su aplicación complementaria y la aplicación Wear por separado. Cuando libere su aplicación a través el Store de Google Play, la aplicación Wear se empaqueta con la aplicación complementaria de y se instala automáticamente si es posible.

Para depurar la aplicación Wear con una aplicación complementaria: 

1.  Compilar e implementar la aplicación complementaria en el teléfono.

2.  Haga clic en el proyecto de desgaste y establézcalo como proyecto de inicio predeterminado.

3.  Implementar el proyecto de desgaste en el dispositivo portátil.

4.  Ejecutar y depurar la aplicación desgaste del dispositivo.

 
## <a name="summary"></a>Resumen

En este artículo se explica cómo configurar un dispositivo Android Wear para depuración desgaste desde Visual Studio a través de Bluetooth y cómo depurar una aplicación Wear con una aplicación complementaria de teléfono. También se proporcionan sugerencias de depuración comunes para depurar una aplicación Wear a través de Bluetooth.
