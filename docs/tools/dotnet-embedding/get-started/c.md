---
title: introducción a C
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: topgenorth
ms.author: toopge
ms.date: 04/19/2018
ms.openlocfilehash: f3c238dc9805dafa922f8e32fb4b1935a3fa152c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="getting-started-with-c"></a>introducción a C

## <a name="requirements"></a>Requisitos

Para usar la incrustación de .NET con C, necesitará un equipo que ejecuta Mac o Windows:

### <a name="macos"></a>macOS

* macOS 10.12 (Sierra) o posterior
* Xcode 8.3.2 o posterior
* [Mono](http://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7, 8, 10 o posterior
* Visual Studio 2015 o versiones posterior

## <a name="installing-net-embedding-from-nuget"></a>Instalar .NET incrustación de NuGet

Siga estas [instrucciones](~/tools/dotnet-embedding/get-started/install/install.md) para instalar y configurar la incrustación de .NET para el proyecto.

Debe configurar la invocación del comando será similar (posiblemente con números de versión diferentes y las rutas de acceso):

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

Si todo funciona correctamente, aparecerá el siguiente resultado:

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

Puesto que la `--compile` marca se pasa a la herramienta, incrustación de .NET debe también se compila los archivos de salida en una biblioteca compartida, que encontrará junto a los archivos generados, un **libmanaged.dylib** archivo macOS y **managed.dll** en Windows.

Para utilizar la biblioteca compartida, puede incluir la **managed.h** biblioteca API administrada de archivo de encabezado de C, que proporciona las declaraciones de C correspondientes a los respectivos y vínculo con la mencionada anteriormente compilados biblioteca compartida.

## <a name="further-reading"></a>Información adicional

* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
