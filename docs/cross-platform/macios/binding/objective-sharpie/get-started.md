---
title: "Introducción"
ms.topic: article
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 53e64acd8e64c9a8151b2c55045db4e308f97531
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="getting-started"></a>Introducción

> [!IMPORTANT]
> Sharpie objetivo es una herramienta para desarrolladores de Xamarin con experiencia con conocimientos avanzados de Objective-C (y por extensión, C). Antes de intentar enlazar una biblioteca C objetivo debe tener un conocimiento sólido de cómo compilar la biblioteca nativa en la línea de comandos (y una buena comprensión del funcionamiento de la biblioteca nativa).

<a name="installing" />

## <a name="installing-objective-sharpie"></a>Instalar Sharpie objetivo

Objetivo Sharpie actualmente es una herramienta de línea de comandos independiente para Mac OS X 10.10 y versiones más recientes y es _no es un producto totalmente compatible Xamarin_. Solo debe usarse por los desarrolladores avanzados para ayudar a crear un proyecto de enlace para un 3rd party Objective-C biblioteca.

Puede descargar Sharpie objetivo como un instalador de paquetes de OS X estándar.
Ejecute el programa de instalación y siga todas las indicaciones en pantalla del Asistente para la instalación:

- **Versión actual: 3.4**
  - [Descargar la versión más reciente](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [Anuncio de foro](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> Use la `sharpie update` comando para actualizar a la versión más reciente.

## <a name="basic-walkthrough"></a>Tutorial básico

Sharpie objetivo es una herramienta de línea de comandos proporcionados por Xamarin que ayuda a crear las definiciones necesarios para enlazar una biblioteca de Objective-C parte 3ª con C#.
Aunque se use Sharpie objetivo, el desarrollador *le* necesite modificar los archivos generados al finalizar Sharpie objetivo para solucionar cualquier problema que no se pudo controlar automáticamente por la herramienta.

Siempre que sea posible, objetivo Sharpie anotará API con las que tiene dudas sobre cómo enlazar correctamente (muchas construcciones en el código nativo son ambiguas).
Estas anotaciones se mostrarán como [ `[Verify]` atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

El resultado del objetivo Sharpie es un par de archivos - [ `ApiDefinition.cs` y `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -que se puede utilizar para crear un proyecto de enlace que compila en una biblioteca se puede utilizar en aplicaciones de Xamarin.

> [!IMPORTANT]
> Objetivo Sharpie viene con una **principal** regla para el uso correcto: sin duda debe pasar los argumentos de línea de comandos del compilador de clang correcto con el fin de garantizar el análisis correcto. Esto es porque el Sharpie objetivo de fase de análisis es simplemente una herramienta [implementa con la API de clang libtooling](http://clang.llvm.org/docs/LibTooling.html).

Esto significa que el objetivo Sharpie tiene toda la funcionalidad de Clang (el compilador de C/objetivo-C o C++ que realmente se compila la biblioteca nativa que enlazaría) y todo su conocimiento interno de los archivos de encabezado para el enlace.
En lugar de convertir el objeto analizado [AST](http://en.wikipedia.org/wiki/Abstract_syntax_tree) al código de objeto, objetivo Sharpie traduce el AST para C# enlace "scaffolding" adecuado para la entrada a la `bmac` y `btouch` herramientas de enlace de Xamarin.

Si objetivo Sharpie errores durante el análisis, significa que clang out con errores durante su análisis fase intentar construir el AST y necesita averiguar por qué.

**¡NUEVO!** versión 3.0 los intentos de solucionar algunas de esta complejidad admitiendo Xcode proyectos directamente. Si una biblioteca nativa tiene un proyecto de Xcode válido, objetivo Sharpie puede evaluar el proyecto para un destino especificado y la configuración para deducir los archivos de encabezado de entrada necesarios y marcas de compilador.

Si no hay ningún proyecto de Xcode está disponible, debe estar más familiarizado con el proyecto, deducir los archivos de encabezado de entrada correctos, las rutas de búsqueda de archivo de encabezado y otras marcas de compilador necesarias. Es importante tener en cuenta que las marcas de compilador utilizadas para generar la biblioteca nativa son los mismos que debe pasarse al objetivo Sharpie. Se trata de un proceso más manual y que requieren cierta familiaridad con la compilación de código nativo en la línea de comandos con la cadena de herramientas Clang.

**¡NUEVO!** versión 3.0 también incorpora una herramienta para enlazar fácilmente [CocoaPods](https://cocoapods.org) a través de la `sharpie pod` comando.
Si la biblioteca que le interesa está disponible como un CocoaPod, se recomienda que primero debe intentar enlazar la CocoaPod con Sharpie objetivo (en lugar de intentar enlazar directamente en el origen).
