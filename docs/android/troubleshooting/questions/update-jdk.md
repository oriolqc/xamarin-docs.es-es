---
title: ¿Cómo se puede actualizar la versión de Java Development Kit (JDK)?
description: En este artículo muestra cómo actualizar la versión de Java Development Kit (JDK) en Windows y Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: aa04d944f803dded0e9448de27ed7d5ced2efb54
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109190"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>¿Cómo se puede actualizar la versión de Java Development Kit (JDK)?

_En este artículo muestra cómo actualizar la versión de Java Development Kit (JDK) en Windows y Mac._

## <a name="overview"></a>Información general

Xamarin.Android usa Java Development Kit (JDK) para integrar con el SDK de Android para compilar aplicaciones de Android y ejecutar el Diseñador de Android. Las versiones más recientes de Android SDK (API 24 y versiones posterior) requieren JDK 8 (1.8). Como alternativa, puede instalar el [versión preliminar de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). Microsoft Mobile OpenJDK reemplazará finalmente JDK 8 para el desarrollo de Xamarin.Android.

Para actualizar a Microsoft Mobile OpenJDK, consulte [versión preliminar de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). Para actualizar a JDK 8, siga estos pasos:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Descargar el JDK 8 (1.8) desde el [sitio Web de Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de pantalla JDK de página de descarga en el sitio Web de Oracle](update-jdk-images/image1.png)

2.  Elija la versión de 64 bits para permitir que la representación de [controles personalizados](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols) en el Diseñador de Xamarin Android:

    ![Seleccionar el paquete de JDK Windows x64 para descargar desde la página de descarga JDK](update-jdk-images/image2.png)

3.  Ejecute el archivo .exe e instale el **herramientas de desarrollo**:

    ![Instalar las herramientas de desarrollo en el programa de instalación JDK](update-jdk-images/image3.png)

4.  Abra Visual Studio y actualice el **ubicación de Java Development Kit** para que apunte al nuevo JDK en **Herramientas > Opciones > Xamarin > configuración de Android > ubicación de Java Development Kit**:

    [![Configuración de la ruta de acceso para el JDK en la página de configuración de Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Asegúrese de reiniciar Visual Studio después de actualizar la ubicación.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Descargar el JDK 8 (1.8) desde el [sitio Web de Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de pantalla JDK de página de descarga en el sitio Web de Oracle](update-jdk-images/image1.png)

2.  Abra el archivo .dmg y ejecute al instalador .pkg:

    ![Se ejecuta el programa de instalación JDK en macOS](update-jdk-images/image5.png)

Mac OS establecerá automáticamente la nueva versión JDK como valor predeterminado mediante la actualización **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. A continuación, puede comprobar que la **SDK de Java (JDK)** ubicación se establece en el valor predeterminado esperado de **/usr** en **Visual Studio para Mac > Preferencias > proyectos > ubicaciones de SDK > Android > ubicaciones > ubicación de Java (JDK) SDK**:

[![Configuración de la ubicación de JDK en la pestaña ubicaciones de Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

