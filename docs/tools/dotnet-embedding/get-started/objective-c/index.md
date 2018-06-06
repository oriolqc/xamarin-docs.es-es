---
title: Introducción a Objective-c.
description: Este documento se describe cómo empezar a usar la incrustación de .NET con el objetivo de C. Se trata de requisitos, la instalación de .NET incrustación de NuGet y plataformas admitidas.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: c5db0a55cc1d2597837ae5feb2c5167a0a21b494
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792985"
---
# <a name="getting-started-with-objective-c"></a>Introducción a Objective-c.

Se trata de la página de introducción para Objective-C, que cubre los conceptos básicos de todas las plataformas admitidas.

## <a name="requirements"></a>Requisitos

Para usar la incrustación de .NET con Objective-C, necesitará un equipo Mac que ejecuta:

* macOS 10.12 (Sierra) o posterior
* Xcode 8.3.2 o posterior
* [Mono 5.0](http://www.mono-project.com/download/)

Puede instalar [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/) para editar y compilar el código de C#.

> [!NOTE]
> * Las versiones anteriores de macOS, Xcode y Mono _podría_ el trabajo, pero se ha comprobado y no compatibles
> * Generación de código puede realizarse en Windows, pero solo es posible compilarlo en un equipo Mac donde está instalado Xcode

## <a name="installing-net-embedding-from-nuget"></a>Instalar .NET incrustación de NuGet

Siga estas [instrucciones](~/tools/dotnet-embedding/get-started/install/install.md) para instalar y configurar la incrustación de .NET para el proyecto.

Una invocación de comando de ejemplo se muestra en el [macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) y [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) guías de introducción.

## <a name="platforms"></a>Plataformas

Objective-C es un lenguaje que se usa normalmente para escribir aplicaciones de Mac OS, iOS, tvOS y watchOS; incrustación de .NET admite todas esas plataformas. Trabajar con cada plataforma implica algunos [diferencias clave y las siguientes se explican aquí](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Crear una aplicación de macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) es más fácil, ya que no implica tantos pasos adicionales, como configurar la identidad, provisining perfiles, simuladores y dispositivos. Se recomienda comenzar con el documento de macOS anteriores a la de iOS.

### <a name="ios--tvos"></a>iOS / tvOS

Asegúrese de que ya están configurados para desarrollar aplicaciones de iOS antes de intentar crear uno con la incrustación. NET. El [siguiendo instrucciones](~/tools/dotnet-embedding/get-started/objective-c/ios.md) se supone que ya ha creado e implementa una aplicación de iOS desde su equipo.

Compatibilidad con tvOS es análogo al funcionamiento de iOS, usando solo tvOS proyectos en el IDE (Visual Studio y Xcode) en lugar de proyectos de iOS.

> [!NOTE]
> La compatibilidad con watchOS estará disponible en una versión futura y será muy similar a iOS/tvOS.

## <a name="further-reading"></a>Información adicional

* [Características de .NET incrustar específicas Objective-c.](~/tools/dotnet-embedding/objective-c/index.md)
* [Prácticas recomendadas para Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
* [Plataformas de destino](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de tiempo (iOS y Mac OS)](https://github.com/jamesmontemagno/embeddinator-weather)
