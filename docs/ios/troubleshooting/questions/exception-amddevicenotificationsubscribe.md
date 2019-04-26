---
title: System.Exception AMDeviceNotificationSubscribe returned ...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4fb0712366422e8810a2db60d40c3b85d9f4cd82
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421949"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe returned ...

> [!IMPORTANT]
> Se ha resuelto este problema en versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de salida del registro de compilación.


## <a name="fix"></a>Solución

1.  Eliminar el `usbmuxd` procesar para que el sistema reiniciará:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2.  Si no se soluciona el problema, reinicie el equipo Mac.

## <a name="error-message"></a>Mensaje de error

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

Este mensaje puede aparecer un cuadro de diálogo de error primera vez que inicie Visual Studio para Mac o en el `mtbserver.log` archivo en la aplicación Host de compilación de Xamarin.iOS (**Host de compilación de Xamarin.iOS > ver el registro de Host de compilación**).

Tenga en cuenta que esto es un problema habitual. Si Visual Studio está teniendo problemas para conectarse al host de compilación de Mac, hay otros errores que están más probables que aparezcan en el `mtbserver.log` archivo.

### <a name="errors-in-systemlog"></a>Errores en system.log

En algún error de los casos en los siguientes dos mensajes también pueden aparecer varias veces en `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Información adicional

Una estimación de la causa del error es que los servicios del sistema responsables de notificar la información de dispositivo y el simulador de iOS pueden en raras ocasiones de OS X entrar en un estado inesperado. Xamarin no puede interactuar correctamente con los servicios del sistema en este estado. Reiniciar el equipo reinicia los servicios del sistema y resuelve el problema.

En función de los errores de `system.log` parece que este problema podría estar relacionado con Bonjour (`mDNSResponder`). Cambiar entre distintas redes Wi-Fi parece aumentar las posibilidades de que se produzca el problema.

## <a name="references"></a>Referencias

*   [Error 11789 - MonoTouch.MobileDevice.MobileDeviceException: AMDeviceNotificationSubscribe devuelve: 0XE8000063 [NORESPONSE RESUELTO]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
