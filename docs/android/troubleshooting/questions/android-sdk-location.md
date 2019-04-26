---
title: ¿Dónde puedo configurar mi ubicaciones de Android SDK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 11/16/2017
ms.openlocfilehash: c004fb7717f78750e7ac1e8dc1856a32ba808638
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227691"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>¿Dónde puedo configurar mi ubicaciones de Android SDK?

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En Visual Studio, vaya a **Herramientas > Opciones > Xamarin > configuración de Android** para ver y establecer la ubicación de Android SDK:

[![Pestaña ubicaciones de ejemplo en las preferencias](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

La ubicación predeterminada para cada ruta de acceso es como sigue:

- Ubicación de Java Development Kit: 

    **C:\\archivos de programa\\Java\\jdk1.8.0_131**

- Ubicación de Android SDK: 

    **C:\\Archivos de programa (x86)\\Android\\android-sdk**

- Ubicación de Android NDK: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

Tenga en cuenta que el número de versión del NDK puede variar. Por ejemplo, en lugar de **android-ndk-r13b**, podría ser una versión anterior como **android-ndk-r10e**.

Para establecer la ubicación de Android SDK, escriba la ruta de acceso completa del directorio de Android SDK en el **ubicación de Android SDK** cuadro. Puede navegar a la ubicación de Android SDK en el Explorador de archivos, copiar la ruta de acceso de la barra de direcciones y pegue esta ruta de acceso en el **ubicación de Android SDK** cuadro.
Por ejemplo, si es la ubicación de Android SDK en **C:\\usuarios\\username\\AppData\\Local\\Android\\Sdk**, desactive la antigua ruta en el  **Ubicación de Android SDK** cuadro, pegue en esta ruta de acceso y haga clic en **Aceptar**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, vaya a **Preferencias > proyectos > ubicaciones de SDK > Android**. En el **Android** página, haga clic en el **ubicaciones** pestaña para ver y establecer la ubicación del SDK:

[![Pestaña ubicaciones de ejemplo en las preferencias](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

La ubicación predeterminada para cada ruta de acceso es como sigue:

- Ubicación de Android SDK: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Ubicación de Android NDK: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Ubicación de Java (JDK) SDK: 

    **/usr**

Tenga en cuenta que el número de versión del NDK puede variar. Por ejemplo, en lugar de **android-ndk-r14b**, podría ser una versión anterior como **android-ndk-r10e**.

Para establecer la ubicación de Android SDK, escriba la ruta de acceso completa del directorio de Android SDK en el **ubicación de Android SDK** cuadro. Puede seleccionar la carpeta del SDK de Android en el Finder, presione **CTRL +&#8984;+ I** para ver información de la carpeta, haga clic en y arrastre la ruta de acceso a la derecha del **donde:**, copiar y luego péguelo en el **Android SDK Ubicación** cuadro el **ubicaciones** ficha. Por ejemplo, si la ubicación de Android SDK es **~/Library/Developer/Android/Sdk**, desactive la antigua ruta de acceso en el **ubicación de Android SDK** cuadro, pegue en esta ruta de acceso y haga clic en **Aceptar**.

-----
