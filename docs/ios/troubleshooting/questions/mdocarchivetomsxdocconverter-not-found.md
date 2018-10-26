---
title: MDocArchiveToMsxDocConverter.exe no encontró el ase. BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 0746174857f66843ef9a09429b6286f2efca90d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119766"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe no encontró el ase. BaseCommand.OnRequest

> [!IMPORTANT]
> Se ha resuelto este problema en versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de salida del registro de compilación.


## <a name="error-message"></a>Mensaje de error

Este error puede aparecer en el *registro del servidor Mac* en Visual Studio:

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

Hay 2 problemas independientes en este mensaje:

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Este error es inofensivo, pero también se presta a confusión. Lo [quitará](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) en una versión futura.

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Este error es el problema real. Desafortunadamente, debido a un [limitación](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) este seguimiento de pila de excepción es *incompleta*. Si observa un seguimiento de pila incompleta similar al siguiente en el registro del servidor Mac, puede comprobar el `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` archivo en el host de compilación de Mac para buscar el seguimiento de pila completo.
