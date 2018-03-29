---
title: Introducción a Java
ms.topic: article
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: 06df925525c7bc01963274e9e0d5edfe7bb70613
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="getting-started-with-java"></a>Introducción a Java


Se trata de la página de introducción para Java, que cubre los conceptos básicos de todas las plataformas admitidas.

## <a name="requirements"></a>Requisitos

Para usar la incrustación de .NET con Java, necesitará:

* Java 1.8 o posterior
* [Mono 5.0](http://www.mono-project.com/download/)

Para Mac:
* Xcode 8.3.2 o posterior

Para Windows:
* Visual Studio de 2017 con compatibilidad de C++
* Windows 10 SDK

Para Android:
* [Xamarin.Android 7.5](https://www.visualstudio.com/xamarin/) o posterior
* [Android Studio 3.x](https://developer.android.com/studio/index.html) con Java 1.8

Puede usar [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/) para editar y compilar el código de C#.

> [!NOTE]
> Las versiones anteriores de Xcode, Visual Studio, Xamarin.Android, Android Studio y Mono _podría_ el trabajo, pero se ha comprobado y no compatibles.

## <a name="installation"></a>Instalación

Incrustación de .NET esté disponible en [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```csharp
nuget install Embeddinator-4000
```
Esto colocará `Embeddinator-4000.exe` en el `packages/Embeddinator-4000/tools` directory.

Además, puede compilar Embeddinator de origen, consulte nuestro [repositorio de git](https://github.com/mono/Embeddinator-4000/) y [que han contribuido](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md) documento para obtener instrucciones.

## <a name="platforms"></a>Plataformas

Java está actualmente en estado de vista previa para macOS, Windows y Android.

Se selecciona la plataforma pasando el `--platform=<platform>` argumento de línea de comandos para el embeddinator. Actualmente `macOS`, `Windows`, y `Android` son compatibles.

### <a name="macos-and-windows"></a>macOS y Windows

Para el desarrollo, debe poder utilizar cualquier IDE de Java que admite Java 1.8. Incluso puede utilizar Android Studio para esto si lo desea, [ve aquí](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Puede usar la salida del archivo JAR tal y como lo haría con cualquier archivo jar de Java estándar.

### <a name="android"></a>Android

Asegúrese de que ya están configurados para desarrollar aplicaciones de Android antes de intentar crear uno mediante Embeddinator. El [siguiendo instrucciones](~/tools/dotnet-embedding/get-started/java/android.md) se supone que ya correctamente ha creado e implementado una aplicación Android desde su equipo.

Android Studio se recomienda para el desarrollo, pero otros IDE debe funcionar como es compatible con la [formato de archivo AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Información adicional

* [Introducción de Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Devoluciones de llamada en Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Investigación de Android preliminar](~/tools/dotnet-embedding/android/index.md)
* [Limitaciones de Embeddinator](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
