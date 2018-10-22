---
title: Kit de desarrollo de Java 9 y Xamarin.Android
description: En este artículo se explica cómo resolver errores de Java Development Kit (JDK) 9 en Xamarin.Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/18/2018
ms.openlocfilehash: 529062f820cd682dc6a9c22f706dbceecef1c836
ms.sourcegitcommit: 57f9a9ba2f199697cb75e7be67f1a372c35a861b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2018
ms.locfileid: "36269678"
---
# <a name="xamarinandroid-and-java-development-kit-9"></a>Kit de desarrollo de Java 9 y Xamarin.Android

_En este artículo se explica cómo resolver errores de Java Development Kit (JDK) 9 en Xamarin.Android._


## <a name="overview"></a>Información general

Xamarin.Android usa el Kit de desarrollo de Java (JDK) para integrar con el SDK de Android para compilar aplicaciones de Android y ejecutar el Diseñador de Android. Las versiones más recientes del SDK de Android (API de 24 y versiones posterior) requieren JDK 8 (1.8). **Dado que las herramientas de SDK de Android disponibles en Google no son totalmente compatibles con JDK 9, Xamarin.Android no funciona con JDK 9.**

## <a name="jdk-9-errors"></a>JDK 9 errores

Si intenta compilar un proyecto Xamarin.Android con 9 JDK instalado, obtendrá un error explícito que indica que no se admite el 9 de JDK.

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

Para resolver estos errores, debe instalar JDK 8 (1.8) como se explica en [cómo actualizar la versión del Kit de desarrollo de Java (JDK)?](~/android/troubleshooting/questions/update-jdk.md).


## <a name="checking-the-jdk-version"></a>¡¡Comprobando la versión JDK

También puede comprobar qué versión de Java que se ha instalado, escriba el comando siguiente (el JDK `bin` directorio debe estar en su `PATH`):

```shell
java -version
```

Si instala el 9 de JDK, verá un mensaje similar al siguiente:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Si está instalado el 9 de JDK, debe instalar Java JDK 8 (1.8). ¿Para obtener información sobre cómo instalar JDK 8, vea [cómo actualizar la versión del Kit de desarrollo de Java (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Tenga en cuenta que no es necesario desinstalar JDK 9; Sin embargo, debe asegurarse de que está utilizando Xamarin JDK 8 en lugar de JDK 9. En Visual Studio, haga clic en **Herramientas > Opciones > Xamarin > configuración de Android**. Si **ubicación de Kit de desarrollo de Java** no está establecido en una ubicación de JDK 8 (como **C:\\archivos de programa\\Java\\jdk1.8.0_111**), haga clic en **cambio**  y establézcalo en la ubicación donde está instalado el JDK 8. En Visual Studio para Mac, vaya a **Preferencias > proyectos > ubicaciones de SDK > Android > SDK de Java (JDK)** y haga clic en **examinar** para actualizar esta ruta de acceso.

## <a name="known-issues-with-jdk-9"></a>Problemas conocidos con JDK 9

### <a name="apksigner"></a>apksigner

Hay un problema conocido con apksigner y 9 de JDK en el que el `apksigner.bat` archivo invoca el `apksigner.jar` con `-Djava.ext.dirs` en lugar de `-classpath` que espera el 9 de JDK. Se recomienda utilizar JDK 8 (1.8). ¿Para obtener información sobre cómo instalar JDK 8, vea [cómo actualizar la versión del Kit de desarrollo de Java (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Si tiene instalado el 9 de JDK, asegúrese de que la ruta de acceso siguiente no se estableció en su `PATH` variable de entorno tal como se seguirán apuntando hacia JDK 9: `C:\ProgramData\Oracle\Java\javapath`. Después de quitarlo, `java-version` en una línea de comandos debe mostrar JDK 8.
