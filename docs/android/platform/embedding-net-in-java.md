---
title: "La incrustación de .NET en Java"
description: "Cómo utilizar una biblioteca .NET de Xamarin en un proyecto de Android nativo basado en Java"
ms.topic: article
ms.prod: xamarin
ms.assetid: A489EEF3-1008-4257-BF63-FE21D8C23821
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 1a25f4bc39e39ce58a07ed399082bf13284c16e9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="embedding-net-in-java"></a>La incrustación de .NET en Java

En algunos casos, puede que desee agregar una biblioteca .NET de Xamarin a un proyecto de Android nativo existente. Para ello, puede usar el [Embeddinator 4000](https://mono.github.io/Embeddinator-4000/) herramienta para convertir la biblioteca de .NET en una biblioteca nativa que se puede incorporar en una aplicación nativa de Android basados en Java.

 
## <a name="requirements"></a>Requisitos

Para usar el 4000 Embeddinator con Java en Android, necesitará lo siguiente:

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/preview/index.html) o posterior debe estar instalado.

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.4.99](https://jenkins.mono-project.com/view/Xamarin.Android/job/xamarin-android/lastSuccessfulBuild/Azure/) o posterior debe estar instalado.

-   **Kit de desarrollo de Java** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior debe estar instalado.

-   **Mono** &ndash; [Mono 5.0](http://www.mono-project.com/download/) o posterior debe estar instalado.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Puede usar Visual Studio para editar y compilar el código de C#.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Puede usar Visual Studio para Mac para editar y compilar el código de C#.

-----

 
## <a name="using-the-embeddinator-4000"></a>Usar Embeddinator-4000

Para usar una biblioteca de .NET en un proyecto de Android nativo, siga estos pasos:

1.  Cree un proyecto de biblioteca de C# Android.

2.  Instalar Embeddinator 4000 a través de NuGet.

3.  Ejecute Embeddinator en el ensamblado de biblioteca de Android.

4.  Utilice el archivo AAR generado en un proyecto de Java en Android Studio.

Estos pasos se describen en detalle en la [Embeddinator 4000](https://mono.github.io/Embeddinator-4000/getting-started-java-android.html) documentación.
