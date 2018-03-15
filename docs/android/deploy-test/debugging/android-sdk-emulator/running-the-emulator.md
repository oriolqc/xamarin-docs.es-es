---
title: Ejecutar el emulador de Android SDK
description: "Cómo depurar la aplicación con el emulador de Android SDK"
ms.topic: article
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 89768d2562814091f0e5894c4af2edd67d68cb00
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="running-the-android-sdk-emulator"></a>Ejecutar el emulador de Android SDK

En esta guía, aprenderá a iniciar un dispositivo virtual en el Emulador de Android SDK para depurar y probar la aplicación.

## <a name="using-a-pre-configured-virtual-device"></a>Uso de un dispositivo virtual preconfigurado

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio incluye dispositivos virtuales preconfigurados que aparecen en el menú desplegable del dispositivo. Por ejemplo, en la siguiente captura de pantalla de Visual Studio 2017, están disponibles varios dispositivos virtuales preconfigurados:

-   **VisualStudio\_android-23\_arm\_phone**

-   **VisualStudio\_android-23\_arm\_tablet**

-   **VisualStudio\_android-23\_x86\_phone** 

-   **VisualStudio\_android-23\_x86\_tablet** 

[![Dispositivos virtuales](running-the-emulator-images/win/01-virtual-devices-sml.png)](running-the-emulator-images/win/01-virtual-devices.png#lightbox)

Normalmente, se selecciona el dispositivo virtual **VisualStudio\_android-23\_x86\_phone** para probar y depurar una aplicación de teléfono. Si uno de estos dispositivos virtuales preconfigurados cumple los requisitos (es decir, coincide con el nivel de API de destino de la aplicación), vaya a [Inicio del emulador](#launching) para empezar a ejecutar la aplicación en el emulador. (Si todavía no está familiarizado con los niveles de API de Android, vea [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Información de los niveles de API de Android)).

Si el proyecto de Xamarin.Android usa un nivel de marco de destino que no es compatible con los dispositivos virtuales disponibles, en el menú desplegable se enumerarán los dispositivos virtuales que no se pueden usar, en **Dispositivos no compatibles**. Por ejemplo, el siguiente proyecto tiene un marco de destino establecido en **Android 7.1 Nougat (API 25)**, que es no es compatible con los dispositivos virtuales **Android 6.0** que se proporcionan de manera determinada:

[![Dispositivo virtual no compatible](running-the-emulator-images/win/02-incompatible-level-sml.png)](running-the-emulator-images/win/02-incompatible-level.png#lightbox)

Puede hacer clic en **Cambiar destino mínimo de Android** para cambiar la versión mínima de Android del proyecto de modo que coincida con el nivel de API de los dispositivos virtuales disponibles. También puede usar el **administrador del emulador de Android** para crear dispositivos virtuales compatibles con el nivel de API de destino, como se explica más adelante en [Configuración de dispositivos virtuales](#virtualdevice). Para poder configurar dispositivos virtuales para un nuevo nivel de API, debe instalar las imágenes del sistema correspondientes para ese nivel de API. Esto se explica en la sección siguiente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Visual Studio para Mac incluye dispositivos virtuales preconfigurados que aparecen en el menú desplegable del dispositivo. Por ejemplo, en la siguiente captura de pantalla, están disponibles dos dispositivos virtuales preconfigurados:

-   **Android\_Accelerated\_x86**

-   **Android\_ARMv7a**

[![Dispositivos virtuales](running-the-emulator-images/mac/01-virtual-devices-sml.png)](running-the-emulator-images/mac/01-virtual-devices.png#lightbox)

Normalmente, se selecciona el dispositivo virtual **Android\_Accelerated\_x86** para probar y depurar una aplicación para teléfonos. Si este dispositivo virtual preconfigurado cumple los requisitos, es decir, coincide con el nivel de API de destino de la aplicación, vaya a [Launching the Emulator (Inicio del emulador)](#launching) para empezar a ejecutar la aplicación en el emulador. (Si todavía no está familiarizado con los niveles de API de Android, vea [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) (Información de los niveles de API de Android)).

-----

## <a name="creating-custom-virtual-devices"></a>Crear dispositivos virtuales personalizados

Para crear dispositivos virtuales personalizados, debe usar el Administrador de dispositivos Android de Xamarin o el Administrador de emuladores de Google heredado que forma parte de Android SDK. Para más información sobre cómo crear y personalizar dispositivos virtuales, vea [Administrador de dispositivos Android de Xamarin](~/android/get-started/installation/android-emulator/xamarin-device-manager.md).
Si prefiere usar el Administrador de emuladores de Google heredado, vea [Administrador de emuladores de Google](~/android/get-started/installation/android-emulator/google-emulator-manager.md).

Tenga en cuenta que si va a desarrollar para Android 8.0 Oreo, debe usar el Administrador de dispositivos Android de Xamarin.

<a name="launching" />

## <a name="launching-the-emulator"></a>Inicio del emulador

Cerca de la parte superior del IDE, hay un menú desplegable que se puede utilizar para seleccionar el modo **Debug (Depuración)** o **Release (Versión)**. Al seleccionar **Debug (Depuración)**, se asocia el depurador al proceso de la aplicación que se ejecuta dentro del emulador. 

Tras elegir el dispositivo virtual en el menú desplegable de dispositivos, seleccione el modo de **Depuración** o **Versión**. Después, haga clic en el botón **Reproducir** para ejecutar la aplicación:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Modos de Depuración y Versión, botón Reproducir](running-the-emulator-images/win/17-debug-release-sml.png)](running-the-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Modos de Depuración y Versión, botón Reproducir](running-the-emulator-images/mac/16-debug-release-sml.png)](running-the-emulator-images/mac/16-debug-release.png#lightbox)

-----

Cuando se inicie el emulador de Android, Xamarin.Android implementará la aplicación en el emulador. El emulador ejecuta la aplicación con la imagen de dispositivo virtual configurada. A continuación se muestra una captura de pantalla de ejemplo del emulador de Android SDK (el emulador está ejecutando una aplicación en blanco denominada **MyApp**):

![El emulador está ejecutando una aplicación en blanco](running-the-emulator-images/emulator-running.png)

El emulador puede dejarse en ejecución, de modo que no es necesario cerrarlo y reiniciarlo cada vez que se ejecuta la aplicación. La primera vez que se ejecuta una aplicación de Xamarin.Android en el emulador, se instala el tiempo de ejecución compartido de Xamarin.Android para el nivel de API de destino, seguido de la aplicación. La instalación del tiempo de ejecución puede tardar unos minutos, así que tendrá que esperar. La instalación del tiempo de ejecución tiene lugar solamente cuando se implementa en el emulador la primera aplicación de Xamarin.Android, &ndash;de modo que las posteriores implementaciones serán más rápidas porque solo se copiará la aplicación en el emulador.

Para obtener más información sobre cómo usar el emulador de Android SDK, consulte los siguientes temas para desarrolladores de Android:

-   [Navegar en la pantalla](https://developer.android.com/studio/run/emulator.html#navigate)

-   [Realizar tareas básicas en el emulador](https://developer.android.com/studio/run/emulator.html#tasks)

-   [Trabajar con los controles extendidos, la configuración y la ayuda](https://developer.android.com/studio/run/emulator.html#extended)

