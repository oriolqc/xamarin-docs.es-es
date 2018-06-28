---
title: .NET incrustación en Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: topgenorth
ms.author: toopge
ms.date: 06/15/2018
ms.openlocfilehash: e90d1e6258d4cfd9c918c566c9e18c358ee7668a
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067398"
---
# <a name="net-embedding-on-android"></a>.NET incrustación en Android

En algunos casos, puede que desee agregar una biblioteca .NET de Xamarin a un proyecto de Android nativo existente. Para ello, puede usar el [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/) herramienta para convertir la biblioteca de .NET en una biblioteca nativa que se puede incorporar en una aplicación nativa de Android basados en Java.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="xamarinandroid-requirements"></a>Requisitos de Xamarin.Android

Para que Xamarin.Android trabajar con la incrustación. NET, necesita lo siguiente:

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) o posterior debe estar instalado.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) o posterior debe estar instalado.

-   **Kit de desarrollo de Java** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior debe estar instalado.


## <a name="using-embeddinator-4000"></a>Usar Embeddinator 4000

Para usar una biblioteca de .NET en un proyecto de Android nativo, siga estos pasos:

1.  Cree un proyecto de biblioteca de C# Android.

2.  Instalar [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Busque **Embeddinator 4000.exe** y agréguelo a su **ruta de acceso**. Por ejemplo:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  Ejecute Embeddinator 4000 en el ensamblado de biblioteca. Por ejemplo:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Utilice el archivo AAR generado en un proyecto de Java en Android Studio.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="xamarinandroid-requirements"></a>Requisitos de Xamarin.Android

Para que Xamarin.Android trabajar con la incrustación. NET, necesita lo siguiente:

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) o posterior debe estar instalado.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) o posterior debe estar instalado.

-   **Kit de desarrollo de Java** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior debe estar instalado.

-   **Mono** &ndash; [5.0 Mono](http://www.mono-project.com/download/) o posterior debe estar instalado (mono se instala con Visual Studio para Mac).


## <a name="using-embeddinator-4000"></a>Usar Embeddinator 4000

Para usar una biblioteca de .NET en un proyecto de Android nativo, siga estos pasos:

1.  Cree un proyecto de biblioteca de C# Android.

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

5.  Utilice el archivo AAR generado en un proyecto de Java en Android Studio.

-----

Opciones de uso y la línea de comandos se describen en la [Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) documentación.


## <a name="callbacks"></a>Devoluciones de llamada

Obtenga información acerca de [realizar llamadas entre C# y Java](callbacks.md).

## <a name="samples"></a>Muestras

* [Aplicación de ejemplo del tiempo](https://github.com/jamesmontemagno/embeddinator-weather)
