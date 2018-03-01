---
title: "¿Qué controladores USB es necesario depurar Android en Windows?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/19/2017
ms.openlocfilehash: 97803c0496c7f5f6ea40e86023caad66c675037e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>¿Qué controladores USB es necesario depurar Android en Windows?

## <a name="finding-usb-drivers"></a>Buscar controladores USB

Para depurar en un dispositivo Android para el desarrollo en Windows; debe instalar a un controlador USB compatible. El Administrador de Android SDK incluye el "controlador USB de Google" de forma predeterminada, que agrega compatibilidad para dispositivos Nexus como se describe aquí: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

Otros dispositivos requieren controladores USB específicamente publicados por el fabricante del dispositivo. Algunos vínculos para los fabricantes más comunes se incluyen en esta guía: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Según la por el fabricante, puede ser difícil realizar un seguimiento hacia abajo el controlador USB exacto necesitado. Algunas alternativas para probar aplicaciones de Android desarrollan en Windows incluidas mediante un emulador de Android o con los servicios de pruebas externos. Algunas de ellas son:

- [Xamarin Test Cloud](https://xamarin.com/test-cloud) : pruebas se ejecutan en cientos de dispositivos Android real de los servicios de nube.

- [Emulador de Visual Studio para Android](https://www.visualstudio.com/en-us/features/msft-android-emulator-vs.aspx)

- [Emulador del SDK de Google Android](~/android/deploy-test/debugging/android-sdk-emulator/index.md)

