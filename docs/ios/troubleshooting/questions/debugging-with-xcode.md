---
title: Depurar aplicaciones de Xamarin.iOS con Xcode
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5FDDEDB3-AEB9-4D9C-9F7B-FEFAA9AF0031
ms.technology: xamarin-ios
author: migueldeicaza
ms.author: miguel
ms.date: 02/22/2018
ms.openlocfilehash: e0127d4b24236d350e5fa967110316544c320d0f
ms.sourcegitcommit: bf05041cc74fb05fd906746b8ca4d1403fc5cc7a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/04/2018
ms.locfileid: "39514712"
---
# <a name="debugging-xamarinios-apps-with-xcode"></a>Depurar aplicaciones de Xamarin.iOS con Xcode

Puede haber casos donde desee que usar Xcode para depurar algunos elementos de la aplicación de Xamarin.iOS. Aunque no podrá depurar el código. NET, aún podrá depurar código nativo y usar algunos de los visualizadores nativos en Xcode.

## <a name="walkthrough"></a>Tutorial

Aunque no hay ninguna compatibilidad integrada para la depuración de Xcode en Visual Studio para Mac, puede usar los siguientes pasos para lograr esto:

1. Crear una aplicación de iOS de Xcode con el mismo identificador que aparece en la aplicación de Xamarin.
   
    - Puede encontrar el identificador de paquete del proyecto Xamarin.iOS abriendo el **Info.plist** archivo:

        ![Edición de Info.plist](debugging-with-xcode-images/vsmac-infoplist.png "Info.list de edición")

    - En Xcode, establezca el identificador de paquete al crear el proyecto o seleccionando el destino del proyecto:

        ![Establecer el identificador de paquete en Xcode](debugging-with-xcode-images/xcode-bundle.png "establecer el identificador de paquete en Xcode")

2. Cambie el proyecto de Xcode para esperar el lanzamiento en lugar de iniciar automáticamente la aplicación:

    - Abra el **Editar combinación de Panel** seleccionando **producto > esquema > Editar combinación de** o mediante el **cmd⌘ + <** método abreviado de teclado.

    - Seleccione el **ejecutar** esquema y de la derecha del panel se debería ver **iniciar** opciones. Seleccione **espere a que el ejecutable que se iniciará** y haga clic en **cerrar**.

        ![Espere a que el ejecutable que se iniciará](debugging-with-xcode-images/xcode-schemes.png "espere a que el ejecutable que se iniciará")

3. Ejecute el proyecto de Xcode.

    Esto instalará la aplicación de Xcode ficticia en el dispositivo, pero no iniciará.

4. Ejecute la aplicación de Xamarin.

    Cuando inicia Xcode se debe asociar a la aplicación de Xamarin.

### <a name="caveats"></a>Advertencias

Es posible que deba realizar un pequeño cambio en la aplicación de Xamarin.iOS cada vez que inicie. En caso contrario, Visual Studio para Mac detectará que la aplicación no necesita crearse *y* ya está instalado, y no vuelva a instalarlo a través de la aplicación ficticia de Xcode.

## <a name="alternative---using-lldb"></a>Alternativa: usar lldb

Si está familiarizado con el uso de **lldb** desde la línea de comandos, no hay una solución mucho más sencilla.

En el shell, escriba el siguiente comando:

```bash
touch ~/.mtouch-launch-with-lldb
```

Obtendrá instrucciones en el **resultado de la aplicación** ventana sobre qué hacer, pero, básicamente, al ejecutar la aplicación, podrá usar **lldb** desde la línea de comandos para depurar la aplicación.
