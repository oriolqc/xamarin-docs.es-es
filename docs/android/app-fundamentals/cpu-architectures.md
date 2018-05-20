---
title: Arquitecturas de CPU
description: Xamarin.Android es compatible con varias arquitecturas de CPU, incluidos los dispositivos de 32 bits y 64 bits. Este artículo explica cómo elegir como destino una aplicación a una o varias arquitecturas de CPU compatible con Android.
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: abfe22683de024f056d7798dc3ac2de13ebd953e
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2018
---
# <a name="cpu-architectures"></a>Arquitecturas de CPU

_Xamarin.Android es compatible con varias arquitecturas de CPU, incluidos los dispositivos de 32 bits y 64 bits. Este artículo explica cómo elegir como destino una aplicación a una o varias arquitecturas de CPU compatible con Android._

## <a name="cpu-architectures-overview"></a>Información general de arquitecturas de CPU

Al preparar la aplicación para su versión, debe especificar qué arquitecturas de CPU de la plataforma admite su aplicación. Un APK único puede contener código máquina para admitir varias arquitecturas diferentes. Cada colección de código específico de la arquitectura está asociado con un *interfaz binaria de aplicación* (ABI). Cada ABI define cómo se espera que este código máquina interactúe con Android en tiempo de ejecución.
Para obtener más información acerca de cómo funciona esto, consulte [dispositivos de varios núcleos &amp; Xamarin.Android](~/android/deploy-test/multicore-devices.md).


## <a name="how-to-specify-supported-architectures"></a>Cómo especificar arquitecturas compatibles

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Por lo general, solo seleccionar explícitamente una arquitectura (o arquitecturas) cuando la aplicación se configura para **versión**. Cuando se configura la aplicación para **depurar**, **en tiempo de ejecución compartido de uso** y **usar implementación rápida** opciones están habilitadas, que deshabilitar la selección de la arquitectura explícita.

En Visual Studio, haga doble clic en el proyecto en el **el Explorador de soluciones** y seleccione **propiedades**. En el **opciones Android** página comprobación de la **propiedades de empaquetado** sección y compruebe que **en tiempo de ejecución compartido de uso** está deshabilitado (si desactiva esta alerta permite explícitamente Seleccione qué ABIs para admitir). Haga clic en el **avanzadas** botón y, en **admite arquitecturas**, compruebe las arquitecturas que desea admitir:

[![Selección de armeabi y armeabi v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Por lo general, solo seleccionar explícitamente una arquitectura (o arquitecturas) cuando la aplicación se configura para **versión**. Cuando se configura la aplicación para **depurar**, el **uso compartido en tiempo de ejecución Mono** y **rápida implementación de ensamblado** opciones están habilitadas, lo que evita arquitectura explícita selección.

En Visual Studio para Mac, localice el proyecto en el **solución** rellenar, haga clic en el icono de engranaje situada junto a su proyecto y seleccione **opciones**. En el **opciones de proyecto** cuadro de diálogo, haga clic en **compilar Android**. Haga clic en el **General** ficha y compruebe que **uso compartido en tiempo de ejecución Mono** está deshabilitado (si desactiva esta alerta le permite seleccionar qué ABIs para admitir de manera explícita). Haga clic en el **avanzadas** ficha y, en **ABIs admite**, compruebe la ABIs para las arquitecturas que desea admitir:

[![Selección de armeabi y armeabi v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android admite las siguientes arquitecturas:

-   **armeabi** &ndash; CPU basado en ARM que admita al menos el conjunto de instrucciones ARMv5TE. Tenga en cuenta que `armeabi` no es segura para subprocesos y no debe usarse en dispositivos con varias CPU.

-   **armeabi v7a** &ndash; basado en ARM CPU con las operaciones de punto flotante de hardware y varios dispositivos de CPU (SMP). Tenga en cuenta que `armeabi-v7a` código máquina no se ejecutará en dispositivos de ARMv5.

-   **arm64 v8a** &ndash; CPU según la arquitectura de ARMv8 de 64 bits.

-   **x86** &ndash; CPU admitidas por la x86 (o IA-32) conjunto de instrucciones. Este conjunto de instrucciones es equivalente a la de Pentium Pro, incluidas las instrucciones MMX, SSE, SSE2 y SSE3.

-   **x86_64** CPU admitidas por la x86 64 bits (también conocido como *x64* y *AMD64*) conjunto de instrucciones.

Valor predeterminado es Xamarin.Android `armeabi-v7a` para **versión** compilaciones. Esta configuración proporciona un rendimiento significativamente mejor que `armeabi`. Si tiene como destino una plataforma ARM de 64 bits (por ejemplo, el 9 de Nexus), seleccione `arm64-v8a`. Si va a implementar la aplicación en un x86 dispositivo, seleccione `x86`. Si el dispositivo de destino x86 usa una arquitectura de CPU de 64 bits, seleccione `x86_64`.

## <a name="targeting-multiple-platforms"></a>Varias plataformas de destino

Para varias arquitecturas de CPU de destino, puede seleccionar más de un ABI (a costa de mayor tamaño de archivo APK). Puede usar el **generar un paquete (.apk) por ABI seleccionado** opción (se describe en [propiedades del conjunto de empaquetado](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) crear un APK independiente para cada arquitectura compatible.

No es necesario seleccionar **arm64 v8a** o **x86_64** como destino dispositivos de 64 bits; compatibilidad con 64 bits no es necesario para ejecutar la aplicación en hardware de 64 bits. Por ejemplo, los dispositivos ARM de 64 bits (como el [9 Nexus](http://www.google.com/nexus/9/)) puede ejecutar aplicaciones configuradas para `armeabi-v7a`. La principal ventaja de habilitar la compatibilidad de 64 bits es para que sea posible para la aplicación direccionar más memoria.

> [!NOTE]
> compatibilidad en tiempo de ejecución de 64 bits es actualmente una característica experimental. Recuerde que los tiempos de ejecución de 64 bits son *no* necesarios para ejecutar la aplicación en dispositivos de 64 bits. 

## <a name="additional-information"></a>Información adicional

En algunas situaciones, deberá crear un APK independiente para cada arquitectura (para reducir el tamaño de su APK, o porque la aplicación ha compartido de bibliotecas que son específicas de una determinada arquitectura de CPU).
Para obtener más información acerca de este enfoque, consulte [compilación específica de la ABI APKs](~/android/deploy-test/building-apps/abi-specific-apks.md).
