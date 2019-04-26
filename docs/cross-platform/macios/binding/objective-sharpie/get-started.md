---
title: Introducción a Sharpie objetivo
description: Este documento proporciona información general de Sharpie objetivo, la herramienta usada para automatizar la creación de C# enlaces a código de Objective-C.
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: c1831467ca0cbb4329a1e77fb355698f2d16cd6a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199785"
---
# <a name="getting-started-with-objective-sharpie"></a>Introducción a Sharpie objetivo

> [!IMPORTANT]
> Sharpie objetivo es una herramienta para desarrolladores con experiencia de Xamarin con conocimientos avanzados de Objective-C (y por extensión, C). Antes de intentar enlazar una biblioteca de Objective-C debe tener un conocimiento sólido de cómo compilar la biblioteca nativa en la línea de comandos (y una buena comprensión del funcionamiento de la biblioteca nativa).

<a name="installing" />

## <a name="installing-objective-sharpie"></a>Instalar Sharpie objetivo

Sharpie objetivo actualmente es una herramienta de línea de comandos independiente para Mac OS X 10.10 y versiones más recientes y es _no es un producto totalmente compatible con Xamarin_. Solo debe usarse por desarrolladores avanzados para ayudar a crear un proyecto de enlace a un 3rd party biblioteca de Objective-C.

Sharpie objetivo se puede descargar como un instalador de paquete estándar de OS X.
Ejecute el instalador y siga todas las indicaciones en pantalla desde el Asistente para instalación:

- **Versión actual: 3.4**
  - [Descargue la versión más reciente](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [Anuncio de foro](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> Use el `sharpie update` comando para actualizar a la versión más reciente.

## <a name="basic-walkthrough"></a>Tutorial básico

Sharpie objetivo es una herramienta de línea de comandos proporcionada por Xamarin que ayuda a crear las definiciones necesarias para enlazar una biblioteca de Objective-C parte 3ª a C#.
Incluso cuando se usa Sharpie objetivo, el desarrollador *le* que necesite modificar los archivos generados al finalizar Sharpie objetivo para solucionar cualquier problema que no se puede controlar automáticamente la herramienta.

Siempre que sea posible, objetivo Sharpie indicará las API con las que tiene alguna duda sobre cómo enlazar correctamente (muchas construcciones en el código nativo son ambiguas).
Estas anotaciones se mostrarán como [ `[Verify]` atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

El resultado de objetivo Sharpie es un par de archivos - [ `ApiDefinition.cs` y `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -que puede utilizarse para crear un proyecto de enlace que compila en una biblioteca que puede usar en aplicaciones de Xamarin.

> [!IMPORTANT]
> Objetivo Sharpie incluye una **principales** regla para el uso adecuado: absolutamente se debe pasar los argumentos de línea de comandos del compilador de clang correcta con el fin de garantizar el análisis correcto. Esto es porque el Sharpie objetivo de fase de análisis es simplemente una herramienta [implementado con la API de clang libtooling](http://clang.llvm.org/docs/LibTooling.html).

Esto significa que el objetivo Sharpie tiene toda la funcionalidad de Clang (el compilador de C/Objective-C/C++ que realmente se compila la biblioteca nativa que enlazaría) y todo su conocimiento interno de los archivos de encabezado para el enlace.
En lugar de convertir el objeto analizado [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) al código de objeto, objetivo Sharpie traduce el AST a un C# enlace "aplicar la técnica scaffolding" adecuado para la entrada a la `bmac` y `btouch` herramientas de enlace de Xamarin.

Si objetivo Sharpie errores durante el análisis, significa que clang cerradas durante su análisis de la fase tratando de construir el AST y necesita averiguar por qué.

**¡NUEVO!** versión 3.0 intentos abordar algunos de esta complejidad al admitir proyectos Xcode directamente. Si una biblioteca nativa tiene un proyecto de Xcode válido, objetivo Sharpie puede evaluar el proyecto para una configuración para deducir los archivos de encabezado de entrada necesarios y marcas de compilador y el destino especificado.

Si no hay ningún proyecto de Xcode está disponible, deberá estar más familiarizado con el proyecto por deducir de los archivos de encabezado de entrada correctos, las rutas de búsqueda del archivo de encabezado y otras marcas de compilador necesarias. Es importante tener en cuenta que las marcas de compilador utilizadas para generar la biblioteca nativa son los mismos que se debe pasar al objetivo Sharpie. Se trata de un proceso más manual y que requieren un poco de familiaridad con compilar el código nativo en la línea de comandos con la cadena de herramientas Clang.

**¡NUEVO!** versión 3.0 introduce también una herramienta para enlazar fácilmente [CocoaPods](https://cocoapods.org) a través de la `sharpie pod` comando.
Si la biblioteca que le interesa está disponible como un CocoaPod, se recomienda que empiece por intenta enlazarse el CocoaPod con objetivo Sharpie (en lugar de intentar enlazar directamente en el origen).

## <a name="related-links"></a>Vínculos relacionados

- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Generar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)