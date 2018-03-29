---
title: Ejemplo del mundo real con CocoaPods
description: Este documento muestra cómo utilizar Sharpie objetivo para generar automáticamente las definiciones de enlace de C# desde un CocoaPod.
ms.topic: article
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/28/2018
ms.openlocfilehash: 24c796cb258578fdfc68c5b4aa1079d3c589da0f
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="real-world-example-using-cocoapods"></a>Ejemplo del mundo real con CocoaPods

> [!NOTE]
> Este ejemplo se utiliza la [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).

Novedad en la versión 3.0, admite el enlace de CocoaPods Sharpie de objetivo y también incluye un comando (`sharpie pod`) para realizar la descarga, configurar y compilar CocoaPods muy fácil. Debería [familiarizarse con CocoaPods](https://cocoapods.org) en general antes de utilizar esta característica.

## <a name="creating-a-binding-for-a-cocoapod"></a>Crear un enlace para un CocoaPod

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

El `init` subcomando también tiene cierta ayuda útil:

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

Se pueden proporcionar varios nombres de CocoaPod y subspec para `init`.

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

El resultado será el proyecto que se compila y, a continuación, evalúa y analizar Sharpie objetivo de CocoaPod Xcode. Un lote de salida de la consola se generarán, pero debe tener como resultado la definición de enlace al final:

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Pasos siguientes

Después de generar el **ApiDefinitions.cs** y **StructsAndEnums.cs** archivos, eche un vistazo a la siguiente documentación para generar un ensamblado que se va a usar en sus aplicaciones:

- [Información general de enlace Objective-c.](~/cross-platform/macios/binding/overview.md)
- [Bibliotecas de enlace Objective-c.](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Tutorial: Enlazar una biblioteca de C de objetivo de iOS](~/ios/platform/binding-objective-c/walkthrough.md)

