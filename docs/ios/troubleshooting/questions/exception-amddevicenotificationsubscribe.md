---
title: Devuelve System.Exception AMDeviceNotificationSubscribe...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 257e0c14de3c23825b6abe6601c25438db81c58e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776487"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>Devuelve System.Exception AMDeviceNotificationSubscribe...

> [!IMPORTANT]
> Este problema se ha resuelto en versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre una [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de los resultados de registro de compilación.


## <a name="fix"></a>Corregir

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

Este mensaje puede aparecer en un cuadro de diálogo de error al iniciar por primera vez Visual Studio para Mac o en la `mtbserver.log` archivo en la aplicación Host de compilación de Xamarin.iOS (**Host de compilación de Xamarin.iOS > ver el registro de Host de compilación**).

Tenga en cuenta que se trata de un problema habitual. Si Visual Studio está teniendo problemas para conectarse al host de compilación de Mac, hay otros errores que están más probables que aparezcan en el `mtbserver.log` archivo.

### <a name="errors-in-systemlog"></a>Errores en el registro del sistema

En algunos errores de casos las dos siguientes también pueden aparecer varias veces en los mensajes `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Información adicional

Un intento en la causa del error es que los servicios del sistema responsables de la notificación de la información de dispositivo y del simulador de iOS pueden en casos poco frecuentes de OS X entrar en un estado inesperado. Xamarin no puede interactuar correctamente con los servicios del sistema en este estado. Al reiniciar el equipo reinicia los servicios del sistema y resuelve el problema.

En función de los errores de `system.log` parece que este problema podría estar relacionado con Bonjour (`mDNSResponder`). Cambiar entre distintas redes Wi-Fi parece aumentar las posibilidades de que se produzca el problema.

## <a name="references"></a>Referencias

*   [Error 11789 - MonoTouch.MobileDevice.MobileDeviceException: AMDeviceNotificationSubscribe devolvió: 0xe8000063 [resuelto NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
