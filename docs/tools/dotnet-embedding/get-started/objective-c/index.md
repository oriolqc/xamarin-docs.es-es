---
title: Introducción a Objective-c.
description: Este documento se describe cómo empezar a usar la inserción de .NET con Objective-C. Describe los requisitos, la instalación de inserción de .NET de NuGet y las plataformas compatibles.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 92b11db2ee566bcd9f3f8a3ee3771e163a47589b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61040900"
---
# <a name="getting-started-with-objective-c"></a>Introducción a Objective-c.

Se trata de la página de introducción para Objective-C, que cubre los aspectos básicos para todas las plataformas compatibles.

## <a name="requirements"></a>Requisitos

Para usar la inserción de .NET con Objective-C, necesitará un equipo Mac que ejecuta:

* macOS 10.12 (Sierra) o posterior
* Xcode 8.3.2 o posterior
* [Mono 5.0](https://www.mono-project.com/download/)

Puede instalar [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) para editar y compilar su C# código.

> [!NOTE]
> * Las versiones anteriores de macOS, Xcode y Mono _podría_ funciona, pero se ha comprobado y no admitido
> * Generación de código se puede realizar en Windows, pero solo es posible compilarlo en un equipo Mac que Xcode está instalado

## <a name="installing-net-embedding-from-nuget"></a>Instalación de inserción de .NET de NuGet

Siga estas [instrucciones](~/tools/dotnet-embedding/get-started/install/install.md) para instalar y configurar la inserción de .NET para el proyecto.

Una invocación de comando de ejemplo se muestra en el [macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) y [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) guías de introducción.

## <a name="platforms"></a>Plataformas

Objective-C es un lenguaje que se usa normalmente para escribir aplicaciones para macOS, iOS, tvOS y watchOS; inserción de .NET es compatible con todas esas plataformas. Trabajar con cada plataforma implica algunos [diferencias clave y estos se explican aquí](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Creación de una aplicación macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) es más fácil, ya que no implica tantos pasos adicionales, como la configuración de identidad, creando perfiles, simuladores y dispositivos. Se recomienda comenzar con el documento de macOS antes para iOS.

### <a name="ios--tvos"></a>iOS y tvOS

Asegúrese de que ya están configurados para desarrollar aplicaciones de iOS antes de intentar crear uno mediante la inserción de .NET. El [instrucciones](~/tools/dotnet-embedding/get-started/objective-c/ios.md) se supone que ya ha creado e implementado una aplicación iOS desde su equipo.

Compatibilidad con tvOS es análogo al funcionamiento de iOS, si solo se usa proyectos de tvOS en el IDE (Visual Studio y Xcode) en lugar de los proyectos de iOS.

> [!NOTE]
> La compatibilidad con watchOS estará disponible en una futura versión y será muy similar a iOS o tvOS.

## <a name="further-reading"></a>Información adicional

* [Inserción de .NET características específicas de Objective-c.](~/tools/dotnet-embedding/objective-c/index.md)
* [Procedimientos recomendados para Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Las descripciones y códigos de error](~/tools/dotnet-embedding/errors.md)
* [Plataformas de destino](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Weather (iOS y macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
