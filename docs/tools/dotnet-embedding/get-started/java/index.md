---
title: Introducción a Java
description: Este documento describe cómo empezar a usar la inserción de .NET con Java. Describe los requisitos del sistema, instalación y las plataformas compatibles.
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: 79a483743946c4f7509833867f2afe4b1e055183
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61198947"
---
# <a name="getting-started-with-java"></a>Introducción a Java

Se trata de la página de introducción para Java, que cubre los aspectos básicos para todas las plataformas compatibles.

## <a name="requirements"></a>Requisitos

Para usar la inserción de .NET con Java, necesitará:

* Java 1.8 o posterior
* [Mono 5.0](https://www.mono-project.com/download/)

Para Mac:

* Xcode 8.3.2 o posterior

Para Windows:

* Visual Studio 2017 con compatibilidad con C++
* Windows 10 SDK

Para Android:

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) o posterior
* [Android Studio 3.x](https://developer.android.com/studio/index.html) con Java 1.8

Puede usar [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) para editar y compilar su C# código.

> [!NOTE]
> Las versiones anteriores de Xcode, Visual Studio, Xamarin.Android, Android Studio y Mono _podría_ funciona, pero se ha comprobado y no admitido.

## <a name="installation"></a>Instalación

Inserción de .NET está actualmente disponible en [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

Esto colocará **Embeddinator 4000.exe** en el **paquetes/Embeddinator-4000/tools** directory.

Además, puede compilar la inserción de .NET de código fuente, consulte nuestra [repositorio de git](https://github.com/mono/Embeddinator-4000/) y [que contribuyen](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) documento para obtener instrucciones.

## <a name="platforms"></a>Plataformas

Java está actualmente en estado de vista previa para macOS, Windows y Android.

La plataforma está seleccionada, pasando el `--platform=<platform>` argumento de línea de comandos de la herramienta de inserción de .NET. Actualmente `macOS`, `Windows`, y `Android` son compatibles.

### <a name="macos-and-windows"></a>macOS y Windows

Para el desarrollo, debe ser capaz de usar un IDE de Java que admite Java 1.8. Incluso puede usar Android Studio para este si lo desea, [ver aquí](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Puede usar la salida del archivo JAR como lo haría con cualquier archivo jar de Java estándar.

### <a name="android"></a>Android

Asegúrese de que ya están configurados para desarrollar aplicaciones de Android antes de intentar crear uno mediante la inserción de .NET. El [instrucciones](~/tools/dotnet-embedding/get-started/java/android.md) se supone que ya ha creado e implementado una aplicación Android desde su equipo.

Se recomienda Android Studio para el desarrollo, pero otros IDE debería funcionar siempre y cuando no hay compatibilidad para la [formato de archivo AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Información adicional

* [Introducción a Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Devoluciones de llamada en Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Investigación preliminar de Android](~/tools/dotnet-embedding/android/index.md)
* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Las descripciones y códigos de error](~/tools/dotnet-embedding/errors.md)
