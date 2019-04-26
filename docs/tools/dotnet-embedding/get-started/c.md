---
title: introducción a C
description: Este documento describe cómo usar la inserción de .NET para incrustar código .NET en una aplicación C. Describe cómo usar la inserción de .NET en 2019 de Visual Studio y Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: lobrien
ms.author: laobri
ms.date: 04/19/2018
ms.openlocfilehash: 342ba2a6b51483983df7bd04034a4cef62fd57ff
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61213598"
---
# <a name="getting-started-with-c"></a>introducción a C

## <a name="requirements"></a>Requisitos

Para usar la inserción de .NET con c#, necesitará un equipo que ejecuta Mac o Windows:

### <a name="macos"></a>macOS

* macOS 10.12 (Sierra) o posterior
* Xcode 8.3.2 o posterior
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7, 8, 10 o posterior
* Visual Studio 2015 o posterior

## <a name="installing-net-embedding-from-nuget"></a>Instalación de inserción de .NET de NuGet

Siga estas [instrucciones](~/tools/dotnet-embedding/get-started/install/install.md) para instalar y configurar la inserción de .NET para el proyecto.

Debe configurar la invocación del comando será similar a (posiblemente con números de versión diferentes y las rutas de acceso):

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>Generación

### <a name="output-files"></a>Archivos de salida

Si todo va bien, aparecerá el siguiente resultado:

```shell
Parsing assemblies...
    Parsed 'managed.dll'
Processing assemblies...
Generating binding code...
    Generated: managed.h
    Generated: managed.c
    Generated: mscorlib.h
    Generated: mscorlib.c
    Generated: embeddinator.h
    Generated: glib.c
    Generated: glib.h
    Generated: mono-support.h
    Generated: mono_embeddinator.c
    Generated: mono_embeddinator.h
```

Puesto que la `--compile` marca se pasa a la herramienta, inserción de .NET debe también han compilado los archivos de salida en una biblioteca compartida, que se encuentra junto a los archivos generados, un **libmanaged.dylib** archivo en macOS y **managed.dll** en Windows.

Para consumir la biblioteca compartida, puede incluir el **managed.h** archivo de encabezado de C, que proporciona las declaraciones de C correspondientes a los respectivos managed API de biblioteca y vínculo con los mencionados anteriormente compilados biblioteca compartida.

## <a name="further-reading"></a>Información adicional

* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Las descripciones y códigos de error](~/tools/dotnet-embedding/errors.md)
