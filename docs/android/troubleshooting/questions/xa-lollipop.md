---
title: "¿Qué versión de Xamarin.Android agrega compatibilidad con círculo?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 75810a1261a40d87c8c18eb3253393373606ce0d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>¿Qué versión de Xamarin.Android agrega compatibilidad con círculo?

**Nota:** esta guía se escribió originalmente para la vista previa de L Android.

-   [Xamarin.Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/) ha agregado compatibilidad con Android L vista previa.
-   [Xamarin.Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/) ha agregado compatibilidad con Android círculo.

Xamarin activamente solo es compatible con la versión estable actual de las herramientas de Xamarin. La siguiente información se proporciona "como-es" para las versiones anteriores de las herramientas. Para obtener la información más reciente sobre las versiones de Xamarin, consulte [aquí](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"No se encuentra android.jar para API nivel 21" en la vista previa de L Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

El siguiente mensaje de error (o similar) puede aparecer:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Este mensaje significa que la plataforma de SDK de Android para API nivel 21 no está instalada. Se instale en el Administrador de SDK de Android (Herramientas > Abrir Administrador de SDK de Android...), o cambiar el proyecto Xamarin.Android que tenga como destino una versión de API que está instalada.

Existen algunas soluciones para este problema:

1. Cambiar el proyecto para que apunte a API 19 o inferior.

2. Cambiar el nombre de la carpeta de 21 android desde android-21 para android-L. (En el mejor, sólo debe utilizarse como solución temporal y podría no funcionar muy bien en absoluto.)

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. Cambiar temporalmente a la vista previa de Android API nivel 21 "L" [1]:

    1.  Eliminar el **% LOCALAPPDATA %\\Android\\android-sdk\\plataformas\\android-21** 
    2.  Extraer [1] en **C:\\usuarios\\<username>\\AppData\\Local\\Android\\android-sdk\\plataformas** para crear un **android-L** carpeta.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

El siguiente mensaje de error (o similar) puede aparecer:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Esto significa que la plataforma de SDK de Android para API nivel 21 no está instalada. Se instale en el Administrador de SDK de Android (Herramientas > Administrador de SDK...), o cambiar el proyecto Xamarin.Android que tenga como destino una versión de API que está instalada.

Existen algunas soluciones para este problema:

1. Cambiar el proyecto para que apunte a API 19 o inferior.

2. Cambiar el nombre de la carpeta de 21 android desde android-21 para android-L. (En el mejor, sólo debe utilizarse como solución temporal y podría no funcionar muy bien en absoluto.)

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Cambiar temporalmente a la vista previa de Android API nivel 21 "L" [1]:

    1.  Delete **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  Extraer [1] en **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** para crear un **android-L** carpeta.

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
