---
title: Configuración e instalación
description: Este artículo le guía a través de los pasos de instalación y los detalles de configuración necesarios para preparar el equipo y los dispositivos para el desarrollo de desgaste Android. Al final de este artículo, tendrá una instalación Xamarin.Android desgaste integrada en Visual Studio para Mac o Microsoft Visual Studio y en funcionamiento, y estará listo para comenzar a crear su primera aplicación de uso de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 86ed62075f5c39eaf7b0e348020f4a9361764727
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="setup-and-installation"></a>Configuración e instalación

_Este artículo le guía a través de los pasos de instalación y los detalles de configuración necesarios para preparar el equipo y los dispositivos para el desarrollo de desgaste Android. Al final de este artículo, tendrá una instalación Xamarin.Android desgaste integrada en Visual Studio para Mac o Microsoft Visual Studio y en funcionamiento, y estará listo para comenzar a crear su primera aplicación de uso de Xamarin.Android._

## <a name="requirements"></a>Requisitos

Para crear aplicaciones basados en Xamarin Android desgaste, es necesario lo siguiente:

-   **Visual Studio o Visual Studio para Mac** &ndash; que si usa Visual Studio, Visual Studio 2015 Professional o posterior es necesario.

-   **Xamarin.Android** &ndash; Xamarin.Android 4.17 o posterior debe instalarse y configurarse con Visual Studio o Visual Studio para Mac.

-   **SDK de Android** -SDK de Android 5.0.1 (API de 21) o posterior debe estar instalado mediante el Administrador de SDK de Android.

-   **Kit de desarrollo de Java** &ndash; el desarrollo de Xamarin para Android requiere [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) si es mayor o desarrollar para el nivel de API 24 (JDK 1.8 también admite niveles de API anteriores a 24).

Se puede seguir usando [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si es desarrollar específicamente para el nivel de API 23 o una versión anterior.

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

## <a name="installation"></a>Instalación

Después de haber instalado Xamarin.Android, realice los pasos siguientes para que esté listo para compilar y probar aplicaciones Android desgaste: 

1.  Instalar las herramientas y SDK de Android necesarios.
2.  Configurar un dispositivo de prueba.
3.  Crear su primera aplicación Android desgaste.

Estos pasos se describen en las secciones siguientes.


### <a name="install-android-sdk-and-tools"></a>Instalar Android SDK y herramientas 

Iniciar el **Administrador de Android SDK**: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cómo iniciar el Administrador de SDK de Android en Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Cómo iniciar el Administrador de SDK de Android en Visual Studio para Mac](installation-images/xs/sdk-menu.png)

-----


Asegúrese de que tiene el siguiente del SDK de Android y las herramientas instaladas:

* Android SDK Tools v 24.0.0 o superior, y
* Android 4.4W (API20), o
* Android 5.0.1 (API21) o superior.

Si no tiene el SDK más reciente y las herramientas instaladas, descargue las herramientas del SDK requiere *y* los bits de API (es podrán que deba desplazarse un poco para encontrarlos &ndash; a continuación se muestra la selección de la API): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Captura de pantalla de administrador de SDK de ejemplo de cómo habilitar 5.0.1 Android componentes](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Captura de pantalla de administrador de SDK de ejemplo de cómo habilitar Android 4.4 y 5.0.1 componentes](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configuración

Para poder usar probar la aplicación, debe configurar un emulador Android desgaste o un dispositivo Android desgaste real. 


### <a name="android-wear-emulator"></a>Emulador de Android desgaste

Para poder usar un emulador Android desgaste, debe configurar un desgaste Android dispositivos virtuales Android (AVD) con el **administrador del emulador de Google**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cómo iniciar el administrador del emulador de Android desde Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Cómo iniciar el administrador del emulador de Android desde Visual Studio para Mac](installation-images/xs/emulator-menu.png)

-----

Para obtener más información acerca de cómo configurar un emulador desgaste Android, consulte [depurar Android desgaste en un emulador](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Dispositivos Android desgaste

Si tiene un dispositivo Android desgaste como un Smartwatch desgaste Android, puede depurar la aplicación en este dispositivo en lugar de utilizar un emulador. Para obtener información sobre el desarrollo con un dispositivo de uso, consulte [depurar en un dispositivo desgaste](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Crear su primera aplicación de uso de Android

Siga el [¡Hola, desgaste](~/android/wear/get-started/hello-wear.md) instrucciones para crear su primera aplicación de inspección.


## <a name="packaging-your-app"></a>Empaquetar la aplicación

Las aplicaciones de Android desgaste siempre se distribuyen con una aplicación de teléfono Android complementario. 

Al agregar la aplicación Android desgaste como una referencia a la aplicación de Android principal automáticamente se supone que un proyecto Android desgaste y generará todos los metadatos y XML necesario para usted. Además, comprobará que los números de paquete y versión coinciden por lo que puede enviar fácilmente sus aplicaciones para Google Play. 

Para obtener más información acerca de empaquetar aplicaciones de uso, vea [trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Vínculos relacionados

- [SkeletonWear (ejemplo)](https://developer.xamarin.com/samples/SkeletonWear/)
