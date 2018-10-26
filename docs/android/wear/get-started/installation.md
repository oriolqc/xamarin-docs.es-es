---
title: 'Instalación y configuración de desgastan onXamarin.Android del sistema operativo '
description: En este artículo le guía por los pasos de instalación y los detalles de configuración necesarios para preparar el equipo y los dispositivos para el desarrollo de Android Wear. Al final de este artículo, tendrá una instalación de desgaste de Xamarin.Android integrada en Visual Studio para Mac o Microsoft Visual Studio en funcionamiento, y estará listo para comenzar a crear su primera aplicación de Xamarin.Android desgaste.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: ed6d3c1cfc14bd0dece5ffe494cc02fd8f608b1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117283"
---
# <a name="setup-and-installation"></a>Configuración e instalación

_En este artículo le guía por los pasos de instalación y los detalles de configuración necesarios para preparar el equipo y los dispositivos para el desarrollo de Android Wear. Al final de este artículo, tendrá una instalación de desgaste de Xamarin.Android integrada en Visual Studio para Mac o Microsoft Visual Studio en funcionamiento, y estará listo para comenzar a crear su primera aplicación de Xamarin.Android desgaste._

## <a name="requirements"></a>Requisitos

Para crear aplicaciones basadas en Xamarin Android Wear, se requiere lo siguiente:

-   **Visual Studio o Visual Studio para Mac** &ndash; que si usa Visual Studio, Visual Studio 2015 Professional o posterior es necesario.

-   **Xamarin.Android** &ndash; Xamarin.Android 4.17 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

-   **SDK de Android** -SDK de Android 5.0.1 (API 21) o posterior debe estar instalado a través de Android SDK Manager.

-   **Kit para desarrolladores de Java** &ndash; requiere el desarrollo de Xamarin Android [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) si es desarrollar para el nivel de API 24 o superior (JDK 1.8 también admite los niveles de API anteriores al 24).

Aún puede usar [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si está desarrollando contenido específicamente para el nivel de API 23 o una versión anterior.

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

## <a name="installation"></a>Instalación

Después de haber instalado Xamarin.Android, realice los pasos siguientes para que esté listo para compilar y probar aplicaciones Android Wear: 

1.  Instalar las herramientas y Android SDK necesarios.
2.  Configurar un dispositivo de prueba.
3.  Cree su primera aplicación Android Wear.

Estos pasos se describen en las secciones siguientes.


### <a name="install-android-sdk-and-tools"></a>Instalar Android SDK y herramientas 

Iniciar el **Android SDK Manager**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cómo iniciar Android SDK Manager en Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cómo iniciar Android SDK Manager en Visual Studio para Mac](installation-images/xs/sdk-menu.png)

-----


Asegúrese de que tiene el siguiente Android SDK y las herramientas instaladas:

* Android SDK Tools v 24.0.0 o superior, y
* Android 4.4W (API20), o
* Android 5.0.1 (API21) o superior.

Si no tiene el SDK y herramientas instaladas más reciente, descargue las herramientas SDK necesarias *y* los bits de la API (es posible que deba desplazarse un poco para encontrarlos &ndash; a continuación se muestra la selección de API): 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Captura de pantalla de ejemplo de SDK Manager de la habilitación de Android 5.0.1 componentes](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Captura de pantalla de ejemplo de SDK Manager de la habilitación de Android 4.4 y 5.0.1 componentes](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configuración

Para poder usar probar su aplicación, debe configurar un emulador de Android Wear o un dispositivo Android Wear real. 


### <a name="android-wear-emulator"></a>Emulador de Android Wear

Para poder usar un emulador de Android Wear, debe configurar un Android Wear Android Virtual Device (AVD) utilizando el **Google Emulator Manager**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cómo iniciar Android Emulator Manager desde Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cómo iniciar Android Emulator Manager desde Visual Studio para Mac](installation-images/xs/emulator-menu.png)

-----

Para obtener más información acerca de cómo configurar un emulador de Android Wear, consulte [depurar Android Wear en un emulador](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Dispositivo Android Wear

Si tiene un dispositivo Android Wear, como un Smartwatch Android Wear, puede depurar la aplicación en este dispositivo en lugar de usar un emulador. Para obtener información sobre el desarrollo con un dispositivo Wear, consulte [depurar en un dispositivo Wear](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Cree su primera aplicación Android Wear

Siga el [Hola, Wear](~/android/wear/get-started/hello-wear.md) instrucciones sobre cómo crear su primera aplicación de inspección.


## <a name="packaging-your-app"></a>Empaquetar la aplicación

Las aplicaciones de Android wear siempre se distribuyen con una aplicación complementaria de teléfono Android. 

Al agregar la aplicación Android Wear como una referencia a la aplicación de Android principal automáticamente se supone que es un proyecto de Android Wear y generará todos los XML y los metadatos necesarios para usted. Además, comprobará que coinciden con los números de paquete y versión por lo que puede enviar fácilmente las aplicaciones en Google Play. 

Para obtener más información sobre cómo empaquetar aplicaciones desgaste, vea [trabajar con el empaquetado](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Vínculos relacionados

- [SkeletonWear (ejemplo)](https://developer.xamarin.com/samples/SkeletonWear/)
