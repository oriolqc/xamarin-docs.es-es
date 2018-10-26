---
title: Depurar Android Wear en un emulador
description: Estos artículos explica cómo depurar una aplicación de desgaste de Xamarin.Android en un emulador.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 699fb3cc3a5730e8ab2c677feb7cdfbdcf106aeb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120559"
---
# <a name="debug-android-wear-on-an-emulator"></a>Depurar Android Wear en un emulador

_Estos artículos explica cómo depurar una aplicación de desgaste de Xamarin.Android en un emulador._

## <a name="debug-wear-on-emulator-overview"></a>Depurar desgaste de información general del emulador

Desarrollo de aplicaciones de Android Wear requiere ejecutar la aplicación, ya sea en el hardware físico o un emulador o simulador. El uso de hardware es el mejor enfoque, pero no siempre es el más práctico. En muchos casos, puede ser más sencillo y más rentable simular o emular el hardware de Android Wear con un emulador, como se describe a continuación. Si todavía no está familiarizado con el proceso de implementar y ejecutar aplicaciones de Android Wear, vea [Hola, Wear](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Configurar el emulador de Android

Para ejecutar la aplicación Wear en un emulador, debe instalar el emulador de Android SDK de Android y configurarlo para Android Wear. Para el emulador de Android SDK instalación y configuración de información general, consulte [configuración de Android Emulator](~/android/get-started/installation/android-emulator/index.md).

Cuando se crea un dispositivo virtual de desgaste, seleccione un perfil de dispositivo Android Wear (como **Android Wear cuadrado**). Para mejorar el rendimiento, utilice el desgaste **x86** CPU/ABI tal como se muestra en este ejemplo:

[![Ejemplo de configuración del dispositivo virtual Wear](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>Inicie el dispositivo Virtual Wear 

Después de haber creado un dispositivo virtual Android Wear, puede elegir en el menú desplegable de dispositivo en el IDE antes de iniciar la depuración. Si el dispositivo virtual no está disponible en el menú desplegable del dispositivo, compruebe que el proyecto es un Android *Wear* de nivel de proyecto de aplicación (no un proyecto de aplicación de Android) y que su nivel de API de destino está establecido en la misma API que el dispositivo virtual. Por ejemplo:

[![Elegir un AVD Wear en el menú del dispositivo de Visual Studio](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Cuando se inicia el emulador de Android, Xamarin.Android implementará la aplicación de desgaste en el emulador. El emulador ejecuta la aplicación con la imagen de dispositivo virtual configurada.

No se sorprenda si ve esto (o en otra pantalla intersticial) al principio. El emulador de inspección puede tardar unos minutos en iniciarse: 

![Ver el emulador muestra sólo un minuto...](debug-on-emulator-images/please-wait.png)

El emulador puede dejarse en ejecución, de modo que no es necesario cerrarlo y reiniciarlo cada vez que se ejecuta la aplicación.

 
## <a name="summary"></a>Resumen
 
Esta guía explica cómo configurar el emulador de Android para el desarrollo de desgaste e iniciar un dispositivo virtual de desgaste para la depuración.
