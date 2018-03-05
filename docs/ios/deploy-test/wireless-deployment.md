---
title: "Implementación inalámbrica"
description: "Esta característica en versión preliminar permite la implementación en dispositivos iOS o Apple TV a través de una conexión en red"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5AB4C5A9-4FBB-4DCB-BD72-0022D5439E65
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/09/2018
ms.openlocfilehash: 11961a21a7c4188c505c822a35531036fd953405
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="wireless-deployment"></a>Implementación inalámbrica

_Esta característica en versión preliminar permite la implementación en dispositivos iOS o Apple TV a través de una conexión de red._

![Versión preliminar](~/media/shared/preview.png)

Una parte importante del flujo de trabajo del desarrollador es la implementación en un dispositivo. Xcode 9 introdujo la opción de implementación en un dispositivo iOS o Apple TV a través de una red, en lugar de tener que conectar directamente los dispositivos cada vez que quiera implementar y depurar la aplicación. Esta característica se ha introducido en Visual Studio para Mac y Visual Studio versión 15.6, que se encuentra actualmente en versión preliminar.

En esta guía se detalla cómo emparejar e implementar en un dispositivo a través de la red.

## <a name="requirements"></a>Requisitos

La implementación inalámbrica está disponible como característica en **versión preliminar** tanto en Visual Studio para Mac como en Visual Studio.


Para usar la implementación inalámbrica, debe tener lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

- macOS 10.12.4
- La versión más reciente de Visual Studio para Mac 
    - Para instalar este modificador en el [canal alfa o beta](https://docs.microsoft.com/en-us/visualstudio/mac/update) de Visual Studio para Mac.
- Xcode 9.0 o posterior
- Un dispositivo con iOS 11.0 o tvOS 11.0 y versiones posteriores

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

- La [versión preliminar](https://www.visualstudio.com/vs/preview/) más reciente de Visual Studio
- Un dispositivo con iOS 11.0 o tvOS 11.0 y versiones posteriores

En el host de compilación del equipo Mac, deben estar instalados los componentes siguientes:

- macOS 10.12.4
- Visual Studio para Mac Preview
    - Para realizar la instalación del modificador en el [canal alfa o beta](https://docs.microsoft.com/en-us/visualstudio/mac/update) de Visual Studio para Mac.
- Xcode 9.0 o posterior

-----

## <a name="connecting-a-device"></a>Conexión de un dispositivo

Para implementar y depurar de manera inalámbrica en su dispositivo, debe emparejar el dispositivo iOS o Apple TV con Xcode en el equipo Mac. Una vez emparejado, puede seleccionarlo en la lista de destinos de dispositivo de Visual Studio. 

El siguiente proceso de emparejamiento solo tiene que producirse una vez por cada dispositivo. Xcode conservará la configuración de conexión.

<a name="pair" />

### <a name="pairing-an-ios-device-with-xcode"></a>Emparejamiento de un dispositivo iOS con Xcode

1. Abra Xcode y vaya a **Window > Devices and Simulators** (Ventana > Dispositivos y simuladores).
2. Conecte el dispositivo iOS a su equipo Mac con un cable relámpago. Puede que tenga que seleccionar la opción **Trust This Computer** (Confiar en este equipo) en el dispositivo.
3. Seleccione el dispositivo y luego active la casilla **Connect via network** (Conectar a través de la red) para emparejar el dispositivo: ![ventana Devices and Simulators (Dispositivos y simuladores) con la opción Connect via network (Conectar a través de la red) resaltada](wireless-deployment-images/image2.png)

### <a name="pairing-an-apple-tv-with-xcode"></a>Emparejamiento de un dispositivo Apple TV con Xcode

1. Asegúrese de que el equipo Mac y Apple TV están conectados a la misma red.

2. Abra Xcode y vaya a **Window > Devices and Simulators** (Ventana > Dispositivos y simuladores).

3. En Apple TV, vaya a **Settings > Remotes and Devices > Remote App and Devices** (Configuración > Remotos y dispositivos > Aplicación remota y dispositivos).

4. Seleccione Apple TV en el área **Discovered** (Detectado) de Xcode y escriba el código de verificación que aparece en el dispositivo Apple TV.

5. Haga clic en el botón **Connect** (Conectar). Cuando se empareja correctamente, aparece un icono de conexión de red al lado de Apple TV.

## <a name="deploy-to-a-device"></a>Implementar en un dispositivo

Cuando un dispositivo se conecta de forma inalámbrica y está listo para usarse para la implementación, aparece en la lista de destinos de dispositivo, como si el dispositivo se hubiera conectado a través de USB.

Para realizar pruebas en un dispositivo físico, el dispositivo debe estar [aprovisionado](~/ios/get-started/installation/device-provisioning/index.md). Asegúrese de hacerlo antes de intentar implementar en un dispositivo. 

Para implementar en un dispositivo iOS o tvOS, siga estos pasos:

1. Asegúrese de que la máquina de implementación y el dispositivo de destino se encuentran en la misma red inalámbrica. 

2. Seleccione el dispositivo de la lista de dispositivos de destino y ejecute la aplicación.

2. Si el dispositivo está bloqueado, se le pedirá que lo desbloquee. Cuando el dispositivo esté desbloqueado, la aplicación se implementa en él.

La depuración inalámbrica se habilita automáticamente tras la implementación inalámbrica, por lo que puede usar puntos de interrupción establecidos previamente y continuar el flujo de trabajo de depuración, tal y como lo ha hecho siempre.

## <a name="troubleshooting"></a>Solución de problemas

1. Asegúrese siempre de que el dispositivo iOS o Apple TV están conectados a la misma red que el equipo Mac.

2. Si el dispositivo no aparece en Visual Studio, compruebe la ventana **Devices and Simulators** (Dispositivos y simuladores) de Xcode. 

    * Si Xcode **no** muestra el dispositivo como conectado, pruebe a [emparejar](#pair) de nuevo el dispositivo.

    * Si Xcode sí muestra el dispositivo como conectado, pruebe a reiniciar Visual Studio y el dispositivo.

3. Si aún no lo ha hecho, tendrá que [aprovisionar](~/ios/get-started/installation/device-provisioning/index.md) el dispositivo.

4. Si tiene problemas con esta característica que los pasos anteriores no pueden corregir, informe de la existencia de un problema en [Developer Community](https://developercommunity.visualstudio.com/spaces/41/index.html).

## <a name="related-links"></a>Vínculos relacionados

- [Emparejamiento de un dispositivo inalámbrico con Xcode](https://help.apple.com/xcode/mac/9.0/index.html?localePath=en.lproj#/devbc48d1bad)
