---
title: Depuración de Xamarin.Android en dispositivos y emuladores
description: Cómo probar y depurar la aplicación de Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: e1c2a591450d8a5fd0aebe2bceb1d914a711512e
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732224"
---
# <a name="debugging"></a>Depuración

En esta sección se describe cómo depurar una aplicación de Xamarin.Android en dispositivos o emuladores.

## <a name="debugging-overview"></a>Información general sobre depuración

Para desarrollar una aplicación Android es necesario ejecutarla, ya sea en un hardware físico o mediante un emulador. El uso de hardware es el mejor enfoque, pero no siempre es el más práctico. En muchos casos, puede resultar más sencillo y rentable simular o emular el hardware de Android mediante uno de los emuladores que se describen a continuación.

### <a name="debugging-with-the-google-android-emulatorandroiddeploy-testdebuggingandroid-sdk-emulatorindexmd"></a>[Depuración con Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md)

En estos artículos se explica cómo usar el emulador predeterminado que se proporciona con Android SDK. Este emulador está disponible para Visual Studio para Windows y Visual Studio para Mac.

### <a name="visual-studio-android-emulatorandroiddeploy-testdebuggingvisual-studio-android-emulatormd"></a>[Emulador de Android de Visual Studio](~/android/deploy-test/debugging/visual-studio-android-emulator.md)

En este artículo se explica cómo depurar y probar la aplicación de Xamarin.Android con la versión de Android Emulator integrada en Visual Studio 2015. Este emulador es una buena opción si usa Visual Studio 2015 y no necesita perfiles de dispositivo personalizados.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Depuración en un dispositivo](~/android/deploy-test/debugging/debug-on-device.md)

En este artículo se muestra cómo configurar un dispositivo Android físico para que la aplicación de Xamarin.Android se pueda implementar directamente desde Visual Studio o Visual Studio para Mac.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Registro de depuración de Android](~/android/deploy-test/debugging/android-debug-log.md)

Un truco muy común que emplean los desarrolladores para depurar sus aplicaciones es usar `Console.WriteLine`. Pero en una plataforma móvil como Android no hay consola. Los dispositivos Android proporcionan un registro que es probable que necesite usar al escribir aplicaciones. Este registro se conoce a veces como **logcat** por el comando escrito para recuperarlo. En este artículo se describe cómo usar **logcat**.

> [!WARNING]
> Tenga en cuenta que **Xamarin Android Player** está en desuso. Para obtener más información, consulte el [anuncio de esta entrada de blog](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/).
