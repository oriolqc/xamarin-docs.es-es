---
title: ¿Cómo se puede actualizar la versión del Kit de desarrollo de Java (JDK)?
description: Este artículo explica cómo actualizar la versión de Java Development Kit (JDK) en Windows y Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: dcfc5e406e60ac72fb1ca1e9cfb0395d17074b2c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>¿Cómo se puede actualizar la versión del Kit de desarrollo de Java (JDK)?

_Este artículo explica cómo actualizar la versión de Java Development Kit (JDK) en Windows y Mac._

## <a name="overview"></a>Información general

Xamarin.Android usa el Kit de desarrollo de Java (JDK) para integrar con el SDK de Android para compilar aplicaciones de Android y ejecutar el Diseñador de Android. Las versiones más recientes del SDK de Android (API de 24 y versiones posterior) requieren JDK 8 (1.8). Si aún no ha actualizado a JDK 8, siga estos pasos para instalar y habilitar:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Descargar JDK 8 (1.8) desde el [sitio Web de Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de pantalla de JDK página de descarga en el sitio Web de Oracle](update-jdk-images/image1.png)

2.  Elección de la versión de 64 bits para permitir el procesamiento de [controles personalizados](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols) en el Diseñador de Xamarin para Android:

    ![Al seleccionar el paquete de JDK Windows x64 para descargar desde la página de descarga JDK](update-jdk-images/image2.png)

3.  Ejecute el .exe e instale el **herramientas de desarrollo de**:

    ![Instalar las herramientas de desarrollo en el programa de instalación JDK](update-jdk-images/image3.png)

4.  Abra Visual Studio y actualizar la **ubicación de Kit de desarrollo de Java** para que apunte a la nueva JDK en **Herramientas > Opciones > Xamarin > configuración de Android > ubicación del Kit de desarrollo de Java > cambio**:

    ![Configuración de ruta de acceso para el JDK en la página de configuración de Android de opciones de IDE](update-jdk-images/image4.png)

No olvide reiniciar Visual Studio después de actualizar la ubicación.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Descargar JDK 8 (1.8) desde el [sitio Web de Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de pantalla de JDK página de descarga en el sitio Web de Oracle](update-jdk-images/image1.png)

2.  Abra el archivo .dmg y ejecute al instalador .pkg:

    ![Ejecuta el programa de instalación JDK en Mac OS](update-jdk-images/image5.png)

Mac OS establecerá automáticamente la nueva versión JDK como valor predeterminado mediante la actualización de **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. A continuación, puede comprobar que la **SDK de Java (JDK)** ubicación se establece en el valor predeterminado esperado de **/usr** en **Visual Studio para Mac > Preferencias > proyectos > ubicaciones de SDK > Android > Java (JDK) SDK > ubicación**:

![Establecer la ubicación de JDK en la página ubicación de SDK de Android](update-jdk-images/image6.png)

-----

