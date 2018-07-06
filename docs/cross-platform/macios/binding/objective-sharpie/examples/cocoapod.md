---
title: Ejemplo del mundo real mediante CocoaPods
description: Este documento muestra cómo usar Sharpie objetivo para generar automáticamente las definiciones de enlace de C# desde un CocoaPod.
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: asb3993
ms.author: amburns
ms.date: 03/28/2018
ms.openlocfilehash: bac34f662e24c6b08a67cd8da1f41b37b43b3faf
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855213"
---
# <a name="real-world-example-using-cocoapods"></a>Ejemplo del mundo real mediante CocoaPods

> [!NOTE]
> Este ejemplo se usa el [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).

Novedad en la versión 3.0, admite el enlace de CocoaPods Sharpie objetivo e incluso incluye un comando (`sharpie pod`) para realizar la descarga, configuración y creación de CocoaPods muy fácil. Debería [familiarizarse con CocoaPods](https://cocoapods.org) en general antes de usar esta característica.

## <a name="creating-a-binding-for-a-cocoapod"></a>Creación de un enlace para un CocoaPod

El `sharpie pod` comando tiene una opción global y dos subcomandos:

```bash
$ sharpie pod -help
usage: sharpie pod [OPTIONS] COMMAND [COMMAND_OPTIONS]

Pod Options:
  -d, -dir DIR     Use DIR as the CocoaPods binding directory,
                   defaulting to the current directory

Available Commands:
  init         Initialize a new Xamarin C# CocoaPods binding project
  bind         Bind an existing Xamarin C# CocoaPods project
```

El `init` subcomando también tiene algo de ayuda útil:

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

Se pueden proporcionar varios nombres de CocoaPod y subspec a `init`.

```bash
$ sharpie pod init ios AFNetworking
** Setting up CocoaPods master repo ...
   (this may take a while the first time)
** Searching for requested CocoaPods ...
** Working directory:
**   - Writing Podfile ...
**   - Installing CocoaPods ...
**     (running `pod install --no-integrate --no-repo-update`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
** 🍻 Success! You can now use other `sharpie podn`  commands.
```

Una vez que se ha configurado su CocoaPod, ahora puede crear el enlace:

```bash
$ sharpie pod bind
```

Esto dará como resultado del proyecto que se compila y, a continuación, evalúa y analizadas por objetivo Sharpie de CocoaPod Xcode. Un lote de salida de la consola se generarán, pero debe tener como resultado la definición de enlace al final:

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Pasos siguientes

Después de generar el **ApiDefinitions.cs** y **StructsAndEnums.cs** archivos, eche un vistazo a la siguiente documentación para generar un ensamblado que se utiliza en las aplicaciones:

- [Introducción al enlace de Objective-c.](~/cross-platform/macios/binding/overview.md)
- [Enlace de bibliotecas de Objective-c.](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Tutorial: Enlazar a una biblioteca de Objective-C de iOS](~/ios/platform/binding-objective-c/walkthrough.md)
- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Compilar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
