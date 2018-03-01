---
title: "¿Dónde puedo configurar mi ubicaciones de SDK de Android?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 11/16/2017
ms.openlocfilehash: 32a058ace69fe5647d3285ac46bc981ddef39a51
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>¿Dónde puedo configurar mi ubicaciones de SDK de Android?

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En Visual Studio, vaya a **Herramientas > Opciones > Xamarin > configuración de Android** para ver y establecer la ubicación de SDK de Android:

[![Pestaña de ubicaciones de ejemplo en las preferencias](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png)

La ubicación predeterminada de cada ruta de acceso es como sigue:

- Ubicación del Kit de desarrollo de Java: 

    **C:\\Program Files\\Java\\jdk1.8.0_131**

- Ubicación del SDK de Android: 

    **C:\\Archivos de programa (x86)\\Android\\android-sdk**

- Ubicación del NDK de Android: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

Tenga en cuenta que el número de versión de la NDK puede variar. Por ejemplo, en lugar de **ndk de android de r13b**, podría ser una versión anterior como **r10e de ndk de android**.

Para establecer la ubicación de SDK de Android, escriba la ruta de acceso completa del directorio del SDK de Android en el **ubicación de SDK de Android** cuadro. Puede navegar a la ubicación de SDK de Android en el Explorador de archivos, copie la ruta de acceso de la barra de direcciones y pegue esta ruta de acceso en la **ubicación de SDK de Android** cuadro.
Por ejemplo, si la ubicación del SDK de Android está en **C:\\usuarios\\nombre de usuario\\AppData\\Local\\Android\\Sdk**, borre la ruta de acceso anterior en el  **Ubicación de SDK de Android** , pegue en esta ruta de acceso y haga clic en **Aceptar**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En Visual Studio para Mac, vaya a **Preferencias > proyectos > ubicaciones de SDK > Android**. En el **Android** página, haga clic en el **ubicaciones** pestaña para ver y establecer la ubicación de SDK:

[![Pestaña de ubicaciones de ejemplo en las preferencias](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png)

La ubicación predeterminada de cada ruta de acceso es como sigue:

- Ubicación del SDK de Android: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Ubicación del NDK de Android: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Ubicación de Java (JDK) de SDK: 

    **/usr**

Tenga en cuenta que el número de versión de la NDK puede variar. Por ejemplo, en lugar de **ndk de android de r14b**, podría ser una versión anterior como **r10e de ndk de android**.

Para establecer la ubicación de SDK de Android, escriba la ruta de acceso completa del directorio del SDK de Android en el **ubicación de SDK de Android** cuadro. Puede seleccionar la carpeta del SDK de Android en la herramienta de búsqueda, presione **CTRL + &#8984; + I** para ver información de la carpeta, haga clic en y arrastre la ruta de acceso a la derecha del **donde:**, copiar, a continuación, péguelo en el **SDK de Android Ubicación** cuadro el **ubicaciones** ficha. Por ejemplo, si la ubicación del SDK de Android está en **~/Library/Developer/Android/Sdk**, borre la ruta de acceso anterior en el **ubicación de SDK de Android** , pegue en esta ruta de acceso y haga clic en **Aceptar**.

-----
