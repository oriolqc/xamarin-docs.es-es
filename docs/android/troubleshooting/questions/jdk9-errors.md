---
title: Kit de desarrollo de Java 9 y Xamarin.Android
description: Este artículo explica cómo resolver Java Development Kit (JDK) 9 o posterior errores en Xamarin.Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: d8c64ff79367d93e282edd9534ffb98f5bb90c93
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2018
ms.locfileid: "36269678"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Kit de desarrollo de Java 9 o posterior y Xamarin.Android

_Este artículo explica cómo resolver Java Development Kit (JDK) 9 o posterior errores en Xamarin.Android._


## <a name="overview"></a>Información general

Xamarin.Android usa Java Development Kit (JDK) para integrar con el SDK de Android para compilar aplicaciones de Android y ejecutar el Diseñador de Android. Las versiones más recientes de Android SDK (API 24 y versiones posterior) requieren JDK 8 (1.8) o la versión preliminar de Microsoft Mobile OpenJDK. **Dado que las herramientas de Android SDK está disponibles en Google aún no son compatibles con JDK 9, Xamarin.Android no funciona con JDK 9 o posterior.**

## <a name="jdk-errors"></a>Errores del JDK

Si intenta compilar un proyecto de Xamarin.Android con una versión de JDK posterior al 8 de JDK, obtendrá un error explícito que indica que no se admite esta versión de JDK. Por ejemplo:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

Para resolver estos errores, debe instalar JDK 8 (1.8) como se explica en [¿cómo se puede actualizar la versión de Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md).
Como alternativa, puede instalar el [versión preliminar de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md) OpenJDK de The Microsoft Mobile reemplazará finalmente a JDK 8 para el desarrollo de Xamarin.Android.


## <a name="checking-the-jdk-version"></a>Comprobación de la versión JDK

También puede comprobar qué versión de Java que se ha instalado, escriba el comando siguiente (el JDK `bin` directorio debe estar en su `PATH`):

```shell
java -version
```

Si instaló JDK 9, verá un mensaje similar al siguiente:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Si está instalado el JDK 9 o posterior, debe instalar Java JDK 8 (1.8) o la versión preliminar de Microsoft Mobile OpenJDK. Para obtener información sobre cómo instalar JDK 8, vea [¿cómo se puede actualizar la versión de Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md). Para obtener información sobre cómo instalar Microsoft Mobile OpenJDK, consulte [versión preliminar de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md).

Tenga en cuenta que no es necesario desinstalar una versión posterior del JDK; Sin embargo, debe asegurarse de que está utilizando Xamarin JDK 8 en lugar de una versión posterior de JDK. En Visual Studio, haga clic en **Herramientas > Opciones > Xamarin > configuración de Android**. Si **ubicación de Java Development Kit** no está establecida en una ubicación de JDK 8 (como **C:\\archivos de programa\\Java\\jdk1.8.0_111**), haga clic en **cambio**  y establézcalo en la ubicación donde está instalado el JDK 8. En Visual Studio para Mac, vaya a **Preferencias > proyectos > ubicaciones de SDK > Android > SDK de Java (JDK)** y haga clic en **examinar** para actualizar esta ruta de acceso.

## <a name="known-issues-with-jdk-9"></a>Problemas conocidos de JDK 9

### <a name="apksigner"></a>apksigner

Hay un problema conocido con apksigner y JDK 9 en el que el `apksigner.bat` archivo invoca el `apksigner.jar` con `-Djava.ext.dirs` en lugar de `-classpath` que espera JDK 9. Se recomienda usar JDK 8 (1.8). Para obtener información sobre cómo instalar JDK 8, vea [¿cómo se puede actualizar la versión de Java Development Kit (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Si ha instalado JDK 9, asegúrese de que la siguiente ruta de acceso no está establecido en su `PATH` variable de entorno tal como se seguirán apuntando hacia el JDK 9: `C:\ProgramData\Oracle\Java\javapath`. Después de quitarla, `java-version` en una línea de comandos debe mostrar JDK 8.
