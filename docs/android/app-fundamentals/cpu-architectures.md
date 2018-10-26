---
title: Arquitecturas de CPU
description: Xamarin.Android admite varias arquitecturas de CPU, incluidos los dispositivos de 32 bits y 64 bits. En este artículo se explica cómo elegir como destino una aplicación a una o varias arquitecturas de CPU compatible con Android.
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f2865858552d4445dff95c85767c41849c19cc29
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122275"
---
# <a name="cpu-architectures"></a>Arquitecturas de CPU

_Xamarin.Android admite varias arquitecturas de CPU, incluidos los dispositivos de 32 bits y 64 bits. En este artículo se explica cómo elegir como destino una aplicación a una o varias arquitecturas de CPU compatible con Android._

## <a name="cpu-architectures-overview"></a>Información general sobre las arquitecturas de CPU

Al preparar la aplicación para lanzamiento, debe especificar qué arquitecturas de CPU de la plataforma que admite la aplicación. Un APK único puede contener código máquina para admitir varias arquitecturas diferentes. Cada colección de código específico de la arquitectura está asociado con un *Application Binary Interface* (ABI). Cada ABI define cómo se espera este código máquina para interactuar con Android en tiempo de ejecución.
Para obtener más información acerca de cómo funciona esto, consulte [dispositivos de núcleo múltiple &amp; Xamarin.Android](~/android/deploy-test/multicore-devices.md).


## <a name="how-to-specify-supported-architectures"></a>Cómo especificar arquitecturas compatibles

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Normalmente, se explícitamente selecciona una arquitectura (o arquitecturas) cuando la aplicación está configurada para **versión**. Cuando la aplicación está configurada para **depurar**, el **usar entorno de ejecución compartido** y **usar implementación rápida** opciones están habilitadas, lo que deshabilita la selección de la arquitectura explícita.

En Visual Studio, haga doble clic en el proyecto en el **el Explorador de soluciones** y seleccione **propiedades**. En el **opciones de Android** página comprobación de la **las propiedades de empaquetado** sección y compruebe que **usar entorno de ejecución compartido** está deshabilitado (si se desactiva esta permite explícitamente Seleccione cuáles ABI para admitir). Haga clic en el **avanzadas** botón y, en **arquitecturas compatibles**, compruebe las arquitecturas que desea admitir:

[![Selección de armeabi y armeabi-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Normalmente, se explícitamente selecciona una arquitectura (o arquitecturas) cuando la aplicación está configurada para **versión**. Cuando la aplicación está configurada para **depurar**, **uso compartido en tiempo de ejecución Mono** y **implementación rápida de ensamblados** están habilitadas las opciones, que impidan la arquitectura explícita selección.

En Visual Studio para Mac, localice el proyecto en el **solución** rellenar, haga clic en el icono de engranaje junto al proyecto y seleccione **opciones**. En el **opciones de proyecto** cuadro de diálogo, haga clic en **compilación de Android**. Haga clic en el **General** ficha y compruebe que **uso compartido en tiempo de ejecución Mono** está deshabilitado (si se desactiva esta le permite seleccionar qué ABI para admitir de manera explícita). Haga clic en el **avanzadas** ficha y, en **admite ABI**, compruebe la ABI para las arquitecturas que desea admitir:

[![Selección de armeabi y armeabi-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android admite las siguientes arquitecturas:

-   **armeabi** &ndash; CPU basadas en ARM que admiten al menos el conjunto de instrucciones ARMv5TE. Tenga en cuenta que `armeabi` no es segura para subprocesos y no debe usarse en dispositivos con varias CPU.

-   **armeabi-v7a** &ndash; CPU basadas en ARM con operaciones de punto flotante de hardware y varios dispositivos de CPU (SMP). Tenga en cuenta que `armeabi-v7a` código máquina no se ejecutará en dispositivos de ARMv5.

-   **arm64 v8a** &ndash; CPU según la arquitectura de 64 bits ARMv8.

-   **x86** &ndash; CPU que admiten la x86 (o IA-32) conjunto de instrucciones. Este conjunto de instrucciones es equivalente a la de Pentium Pro, incluidas las instrucciones MMX, SSE, SSE2 y SSE3.

-   **x86_64** CPU que admiten la x86 64 bits (también conocido como *x64* y *AMD64*) conjunto de instrucciones.

Valor predeterminado de Xamarin.Android es `armeabi-v7a` para **versión** compilaciones. Esta configuración proporciona un rendimiento significativamente mejor que `armeabi`. Si tiene como destino una plataforma ARM de 64 bits (por ejemplo, Nexus 9), seleccione `arm64-v8a`. Si va a implementar la aplicación en un x86 dispositivo, seleccione `x86`. Si el dispositivo de destino x86 usa una arquitectura de CPU de 64 bits, seleccione `x86_64`.

## <a name="targeting-multiple-platforms"></a>Con varias plataformas de destino

Para tener como destino varias arquitecturas de CPU, puede seleccionar más de una ABI (a costa de mayor tamaño del archivo APK). Puede usar el **generar un paquete (.apk) por ABI seleccionado** opción (se describe en [establecer las propiedades de empaquetado](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) crear un APK independiente para cada arquitectura compatible.

No es necesario seleccionar **arm64 v8a** o **x86_64** como destino dispositivos de 64 bits; compatibilidad con 64 bits no es necesario para ejecutar la aplicación en hardware de 64 bits. Por ejemplo, los dispositivos ARM de 64 bits (como el [Nexus 9](http://www.google.com/nexus/9/)) puede ejecutar las aplicaciones configuradas para `armeabi-v7a`. La principal ventaja de habilitar la compatibilidad con 64 bits es hacer posible que la aplicación para direccionar más memoria.

> [!NOTE]
> compatibilidad en tiempo de ejecución de 64 bits es actualmente una característica experimental. Recuerde que los tiempos de ejecución de 64 bits son *no* necesarios para ejecutar la aplicación en dispositivos de 64 bits. 

## <a name="additional-information"></a>Información adicional

En algunas situaciones, es posible que deba crear un APK independiente para cada arquitectura (para reducir el tamaño de su APK, o porque la aplicación ha compartido bibliotecas que son específicas de una determinada arquitectura de CPU).
Para obtener más información acerca de este enfoque, consulte [compilar Apk específicos de ABI](~/android/deploy-test/building-apps/abi-specific-apks.md).
