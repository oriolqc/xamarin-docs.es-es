---
title: ¿Qué controladores USB es necesario depurar Android en Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/19/2017
ms.openlocfilehash: 9b249c67395c98c73526d741f442b7779a871873
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>¿Qué controladores USB es necesario depurar Android en Windows?

## <a name="finding-usb-drivers"></a>Buscar controladores USB

Para depurar en un dispositivo Android para el desarrollo en Windows; debe instalar a un controlador USB compatible. El Administrador de Android SDK incluye el "controlador USB de Google" de forma predeterminada, que agrega compatibilidad para dispositivos de Nexus como se describe aquí: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

Otros dispositivos requieren controladores USB específicamente publicados por el fabricante del dispositivo. En esta guía se incluyen algunos vínculos para los fabricantes más comunes: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Según la por el fabricante, puede ser difícil realizar un seguimiento hacia abajo el controlador USB exacto necesitado. Algunas alternativas para probar aplicaciones de Android desarrollan en Windows incluidas mediante un emulador de Android o con los servicios de pruebas externos. Algunas de ellas son:

- [Prueba de la aplicación Centro](https://docs.microsoft.com/appcenter/test-cloud/) : pruebas se ejecutan en cientos de dispositivos Android real de los servicios de nube.

- [Emulador de Visual Studio para Android](https://www.visualstudio.com/en-us/features/msft-android-emulator-vs.aspx)

- [Emulador de Google Android](~/android/deploy-test/debugging/android-sdk-emulator/index.md)

