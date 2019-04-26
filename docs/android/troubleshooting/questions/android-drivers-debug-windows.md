---
title: ¿Qué controladores USB son necesario para depurar Android en Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 85045967f5c63eb39c45f917b957d2a393a3a068
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945568"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>¿Qué controladores USB son necesario para depurar Android en Windows?

## <a name="finding-usb-drivers"></a>Buscar controladores USB

Para depurar en un dispositivo Android al desarrollar en Windows; deberá instalar a un controlador USB compatible. Android SDK Manager incluye "Controlador USB de Google" de forma predeterminada, lo que agrega compatibilidad para dispositivos de Nexus como se describe aquí: [https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

Otros dispositivos requieren controladores USB específicamente publicados por el fabricante del dispositivo. En esta guía se incluyen algunos vínculos para los fabricantes más comunes: [https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Según la por el fabricante, puede ser difícil de rastrear el controlador USB exacto necesitado. Desarrollan algunas alternativas para probar aplicaciones de Android en Windows incluido mediante un emulador de Android o con servicios externos de pruebas. Algunas de ellas son:

- [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/) : las pruebas se ejecutan en cientos de dispositivos Android reales de los servicios de nube.

- [Emulador de Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)

