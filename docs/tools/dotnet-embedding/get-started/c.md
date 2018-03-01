---
title: "introducción a C"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: ea8335348416dc00074d83e09b74521da7abcb66
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-c"></a>introducción a C


## <a name="requirements"></a>Requisitos

Para usar la incrustación de .NET con C necesitará un equipo que ejecuta Mac o Windows:

* macOS 10.12 (Sierra) o posterior
* Xcode 8.3.2 o posterior

* Windows 7, 8, 10 o posterior
* Visual Studio 2013 o versiones posterior

* [Mono](http://www.mono-project.com/download/)


## <a name="installation"></a>Instalación

El siguiente paso es descargar e instalar las herramientas de incrustación de .NET en su equipo.

Compilaciones binarias para los generadores de C y Java todavía no están disponibles, pero pronto estarán disponibles.

Como alternativa es posible compilar desde nuestro repositorio de git, consulte el [que han contribuido](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md) documento para obtener instrucciones.


## <a name="generation"></a>Generación

Para generar el código de C, invocar la herramienta de incrustación de .NET pasando los indicadores que tenga como destino el lenguaje c. derecho:

### <a name="windows"></a>Windows:

```csharp
$ build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=windows --compile managed.dll
```

Asegúrese de que a la llamada desde un shell de comandos de Visual Studio específica a la versión de Visual Studio está como destino.

### <a name="macos"></a>macOS

```csharp
$ mono build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=macos --compile managed.dll
```

### <a name="output-files"></a>Archivos de salida

Si todo funciona correctamente, aparecerá el siguiente resultado:

```csharp
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

Puesto que la `--compile` marca se pasa a la herramienta, incrustación de .NET debe también se compila los archivos de salida en una biblioteca compartida, que encontrará junto a los archivos generados, un `libmanaged.dylib` archivo macOS, y `managed.dll` en Windows.

Para utilizar la biblioteca compartida puede incluir el `managed.h` biblioteca API administrada de archivo de encabezado de C, que proporciona las declaraciones de C correspondientes a los respectivos y vínculo con la mencionada anteriormente compilados biblioteca compartida.

## <a name="further-reading"></a>Información adicional

* [Limitaciones de Embeddinator](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
