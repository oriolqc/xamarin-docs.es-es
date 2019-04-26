---
title: 'Error MT1009: Could not copy the assembly'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4a67537cc53aeecf1b86d11dbf041cea79587dd2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422014"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Error MT1009: Could not copy the assembly

> [!IMPORTANT]
> Se ha resuelto este problema en versiones recientes de Xamarin.iOS. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de salida del registro de compilación.

Como se describe en nuestra [notas de la versión](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/), se trata de un problema conocido en Xamarin.iOS 7.2.6. Este problema es debido a permisos de archivo que necesiten privilegios superiores cuando se instala Xamarin.iOS con otra cuenta de usuario, a continuación, cuenta principal del desarrollador.

Para solucionar el problema, abra el Terminal.app en la estación de trabajo de Mac y ejecute el siguiente comando:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

