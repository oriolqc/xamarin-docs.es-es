---
title: 'MT1009 de error: No se pudo copiar el ensamblado'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: de1d7ea8ce4c358ad69150be3da6b38fb6c730ae
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>MT1009 de error: No se pudo copiar el ensamblado

> [!IMPORTANT]
> Este problema se ha resuelto en versiones recientes de Xamarin.iOS. Sin embargo, si el problema se produce en la versión más reciente del software, registre una [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de los resultados de registro de compilación.

Como se describe en nuestro [notas de la versión](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/), se trata de un problema conocido en Xamarin.iOS 7.2.6. Este problema es debido a permisos de archivo que necesiten privilegios más elevados cuando se instala Xamarin.iOS con otra cuenta de usuario, a continuación, cuenta principal del desarrollador.

Para solucionar el problema, abra el Terminal.app en la estación de trabajo de Mac y ejecute el siguiente comando:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

