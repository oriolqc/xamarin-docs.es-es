---
title: 'Error MT1009: Could not copy the assembly'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: b01701566cec88ad4a1493cf5ab9778bd38792b0
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511819"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Error MT1009: Could not copy the assembly

> [!IMPORTANT]
> Este problema se ha resuelto en las versiones recientes de Xamarin. iOS. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

Como se describe en las notas de la [versión](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_7/xamarin.ios_7.2/index.md), se trata de un problema conocido en Xamarin. iOS 7.2.6. Este problema se debe a los permisos de archivo que necesitan más privilegios cuando Xamarin. iOS se instala con una cuenta de usuario diferente a la cuenta principal del desarrollador.

Para solucionar el problema, abra la aplicación terminal. app en la estación de trabajo Mac y ejecute el siguiente comando:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`
