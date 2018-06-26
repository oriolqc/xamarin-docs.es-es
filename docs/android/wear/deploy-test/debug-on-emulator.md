---
title: Depurar Android desgaste en un emulador
description: Dichos artículos explican cómo depurar una aplicación de uso de Xamarin.Android en un emulador.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: baa8df87caf2c05d7b6202d5160c930e51656e10
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2018
ms.locfileid: "36934985"
---
# <a name="debug-android-wear-on-an-emulator"></a>Depurar Android desgaste en un emulador

_Dichos artículos explican cómo depurar una aplicación de uso de Xamarin.Android en un emulador._

## <a name="debug-wear-on-emulator-overview"></a>Depurar desgaste en información general del emulador

Desarrollo de aplicaciones usan Android requiere ejecutar la aplicación, ya sea en un hardware físico o al utilizar un emulador o simulador. El uso de hardware es el mejor enfoque, pero no siempre es el más práctico. En muchos casos, puede ser más sencillo y más rentable para simular o emular el hardware desgaste Android mediante un emulador, como se describe a continuación. Si no está familiarizado con el proceso de implementación y ejecución de aplicaciones usan Android, consulte [desgaste Hello,](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Configurar el emulador de Android

Para ejecutar la aplicación de uso en un emulador, debe instalar el emulador de Android SDK de Android y configurarlo para desgaste Android. Para general emulador de Android SDK información de instalación y configuración, consulte [el programa de instalación de emulador Android](~/android/get-started/installation/android-emulator/index.md).

Cuando se crea un dispositivo virtual desgaste, seleccione un perfil de dispositivo Android desgaste (como **Android desgaste cuadrado**). Para mejorar el rendimiento, utilice el desgaste **x86** CPU/ABI tal como se muestra en este ejemplo:

[![Ejemplo de configuración del dispositivo virtual desgaste](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>Inicie el dispositivo Virtual desgaste 

Después de haber creado un dispositivo virtual desgaste Android, puede elegir en el menú desplegable de dispositivo en el IDE antes de iniciar la depuración. Si el dispositivo virtual no está disponible en el menú desplegable de dispositivo, compruebe que el proyecto es un Android *desgaste* nivel de proyecto de aplicación (no en un proyecto de aplicación de Android) y que su objetivo de nivel de API se establece en la misma API como el dispositivo virtual. Por ejemplo:

[![Elegir un AVD desgaste en el menú del dispositivo de Visual Studio](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Cuando se inicie el emulador de Android, Xamarin.Android se implementará la aplicación de uso en el emulador. El emulador ejecuta la aplicación con la imagen de dispositivo virtual configurada.

No se sorprenda si ve esto (o de otra pantalla intersticial) al principio. El emulador de inspección puede tardar bastante tiempo en iniciarse: 

![Ver el emulador muestra tan sólo un minuto...](debug-on-emulator-images/please-wait.png)

El emulador puede dejarse en ejecución, de modo que no es necesario cerrarlo y reiniciarlo cada vez que se ejecuta la aplicación.

 
## <a name="summary"></a>Resumen
 
Esta guía explica cómo configurar el emulador de Android para el desarrollo de desgaste e iniciar un dispositivo virtual de uso para la depuración.
