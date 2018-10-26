---
title: ¿Qué controladores USB es necesario depurar Android en Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8b996a1cc89acedc47c7169ec579dfb99dae788f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114540"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>¿Qué controladores USB es necesario depurar Android en Windows?

## <a name="finding-usb-drivers"></a>Buscar controladores USB

Para depurar en un dispositivo Android al desarrollar en Windows; deberá instalar a un controlador USB compatible. Android SDK Manager incluye "Controlador USB de Google" de forma predeterminada, lo que agrega compatibilidad para dispositivos de Nexus como se describe aquí: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

Otros dispositivos requieren controladores USB específicamente publicados por el fabricante del dispositivo. En esta guía se incluyen algunos vínculos para los fabricantes más comunes: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Según la por el fabricante, puede ser difícil de rastrear el controlador USB exacto necesitado. Desarrollan algunas alternativas para probar aplicaciones de Android en Windows incluido mediante un emulador de Android o con servicios externos de pruebas. Algunas de ellas son:

- [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/) : las pruebas se ejecutan en cientos de dispositivos Android reales de los servicios de nube.

- [Emulador de Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)

