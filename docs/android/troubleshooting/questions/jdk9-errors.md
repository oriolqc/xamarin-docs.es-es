---
title: Xamarin.Android y JDK 9
description: "En este artículo se explica cómo resolver errores de Java Development Kit (JDK) 9 en Xamarin.Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 9e1dd2fac015783e06bad6656f09a687f3abba2c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinandroid-and-jdk-9"></a>Xamarin.Android y JDK 9

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

## <a name="known-issues-with-jdk-9"></a>Problemas conocidos con JDK 9

### <a name="apksigner"></a>apksigner

Hay un problema conocido con apksigner y 9 de JDK en el que el `apksigner.bat` archivo invoca el `apksigner.jar` con `-Djava.ext.dirs` en lugar de `-classpath` que espera el 9 de JDK. Se recomienda utilizar JDK 8 (1.8). ¿Para obtener información sobre cómo instalar JDK 8, vea [cómo actualizar la versión del Kit de desarrollo de Java (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Después de desinstalar JDK 9, asegúrese de que la ruta de acceso siguiente no se estableció en su `PATH` variable de entorno tal como se seguirán apuntando hacia JDK 9: `C:\ProgramData\Oracle\Java\javapath`. Después de quitarlo, `java -version` en una línea de comandos debe mostrar JDK 8.
