---
title: .NET incrustación en Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: lobrien
ms.author: laobri
ms.date: 06/15/2018
ms.openlocfilehash: 6917267896cff796af4e5cff095720eaeccc7652
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671188"
---
# <a name="net-embedding-on-android"></a>.NET incrustación en Android

En algunos casos, es posible que desea agregar una biblioteca de .NET Xamarin a un proyecto de Android nativo existente. Para ello, puede usar el [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/) herramienta para convertir la biblioteca de .NET en una biblioteca nativa que se puede incorporar en una aplicación nativa de Android basadas en Java.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Requisitos de Xamarin.Android

Para que Xamarin.Android para que funcione con la inserción de. NET, necesita lo siguiente:

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) o posterior debe estar instalado.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) o posterior debe estar instalado.

-   **Kit para desarrolladores de Java** &ndash; [Java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior debe estar instalado.


## <a name="using-embeddinator-4000"></a>Uso de Embeddinator 4000

Para consumir una biblioteca de .NET en un proyecto de Android nativo, use los pasos siguientes:

1.  Crear un C# proyecto de biblioteca de Android.

2.  Instalar [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Busque **Embeddinator 4000.exe** y agréguelo a su **ruta de acceso**. Por ejemplo:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  Ejecute Embeddinator 4000 en el ensamblado de biblioteca. Por ejemplo:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Use el archivo AAR generado en un proyecto de Java en Android Studio.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Requisitos de Xamarin.Android

Para que Xamarin.Android para que funcione con la inserción de. NET, necesita lo siguiente:

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) o posterior debe estar instalado.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) o posterior debe estar instalado.

-   **Kit para desarrolladores de Java** &ndash; [Java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior debe estar instalado.

-   **Mono** &ndash; [5.0 Mono](https://www.mono-project.com/download/) o posterior debe estar instalado (mono se instala con Visual Studio para Mac).


## <a name="using-embeddinator-4000"></a>Uso de Embeddinator 4000

Para consumir una biblioteca de .NET en un proyecto de Android nativo, use los pasos siguientes:

1.  Crear un C# proyecto de biblioteca de Android.

2.  Instalar [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Busque **Embeddinator 4000.exe** y agregue **mono** a la ruta de acceso. Por ejemplo:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  Ejecute Embeddinator 4000 en el ensamblado de biblioteca. Por ejemplo:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Use el archivo AAR generado en un proyecto de Java en Android Studio.

-----

Opciones de uso y la línea de comandos se describen en la [Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) documentación.


## <a name="callbacks"></a>Devoluciones de llamada

Obtenga información sobre [realizar llamadas entre C# y Java](callbacks.md).

## <a name="samples"></a>Muestras

* [Aplicación de ejemplo del tiempo](https://github.com/jamesmontemagno/embeddinator-weather)
