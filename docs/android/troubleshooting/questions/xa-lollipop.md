---
title: ¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: db0cb221ee18998fbb98449ae03c442c84bcfcd7
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829713"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>¿Qué versión de Xamarin.Android agrega compatibilidad con Lollipop?

> [!NOTE]
> Esta guía se escribió originalmente para la versión preliminar de L Android.

-   [Xamarin.Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/) ha agregado compatibilidad con la versión preliminar de L Android.
-   [Xamarin.Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/) agrega compatibilidad con Android Lollipop.

Xamarin activamente solo es compatible con la versión estable actual de las herramientas de Xamarin. La siguiente información se proporciona "como-es" para versiones anteriores de las herramientas. Para obtener la información más reciente acerca de las versiones de Xamarin, consulte [aquí](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Faltan android.jar para API nivel 21" en la versión preliminar de L Android

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

El mensaje de error siguiente (o similar) puede aparecer:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Este mensaje significa que no está instalada la plataforma Android SDK para API nivel 21. Instálela en Android SDK Manager (**Herramientas > Abrir Android SDK Manager...** ), o cambiar el proyecto de Xamarin.Android para tener como destino una versión de API que está instalada.

Existen algunas soluciones para resolver este problema:

1. Cambiar el proyecto para que apunte a API 19 o inferior.

2. Cambiar el nombre de la carpeta de 21 de android desde android 21 a android-L. (En el mejor, debe usarse como una corrección temporal y podría no funcionar muy bien en absoluto).

   **% LOCALAPPDATA %\\Android\\android-sdk\\plataformas\\21 de android**

3. Cambiar temporalmente a la versión preliminar de Android API nivel 21 "L" [1]:

    1.  Eliminar el **% LOCALAPPDATA %\\Android\\android-sdk\\plataformas\\21 de android** 
    2.  Extraer [1] en **C:\\usuarios\\&lt;username&gt;\\AppData\\Local\\Android\\android-sdk\\plataformas**  para crear un **android-L** carpeta.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

El mensaje de error siguiente (o similar) puede aparecer:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Esto significa que no está instalada la plataforma Android SDK para API nivel 21. Instálela en Android SDK Manager (Herramientas > SDK Manager...), o cambiar el proyecto de Xamarin.Android para tener como destino una versión de API que está instalada.

Existen algunas soluciones para resolver este problema:

1. Cambiar el proyecto para que apunte a API 19 o inferior.

2. Cambiar el nombre de la carpeta de 21 de android desde android 21 a android-L. (En el mejor, debe usarse como una corrección temporal y podría no funcionar muy bien en absoluto).

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Cambiar temporalmente a la versión preliminar de Android API nivel 21 "L" [1]:

    1.  Delete **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  Extraer [1] en **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** para crear un **android-L** carpeta.

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
