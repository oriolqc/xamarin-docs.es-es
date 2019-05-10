---
title: Enlace de Objective-C
description: Este documento proporciona vínculos a diversas guías que describen cómo crear C# enlaces a código de Objective-C, permitiendo a los desarrolladores consumir bibliotecas listos para usar en aplicaciones de Xamarin.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: bdf284d66539da3eca35e79d761712012674de0d
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978457"
---
# <a name="binding-objective-c"></a>Enlace de Objective-C

Esta sección incluye una serie de documentos que tratan la creación de enlaces a las bibliotecas de Objective-C, por lo que se puede llamar desde C# aplicaciones creadas con Xamarin.iOS o Xamarin.Mac.

##  <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Información general](~/cross-platform/macios/binding/overview.md)

Este documento contiene algunos de los aspectos internos de cómo realiza un enlace. Es un documento con algo de información técnica avanzado.

##  <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

Este documento describe el proceso usado para crear C# enlaces de Objective-C API y cómo se asignan las expresiones en Objective-C para las expresiones que se usan en. NET.
Si va a enlazar sólo las API de C, debe usar el mecanismo estándar de .NET para esto, el marco de trabajo de P/Invoke.

##  <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guía de referencia de definición de enlace](~/cross-platform/macios/binding/binding-types-reference.md)

Esta es la Guía de referencia que describe todos los atributos disponibles para los autores de enlace para controlar el proceso de generación de enlace.


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Sharpie objetivo es una herramienta de línea de comandos para ayudar a arrancar el primer paso de un enlace. Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar a la API pública en el [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md) (un proceso que también se puede realizar manualmente).

## <a name="ios"></a>iOS

El [página enlace de iOS](~/ios/platform/binding-objective-c/index.md) vínculos volver a estos recursos comunes de enlace, además a los ejemplos siguientes.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Tutorial: Enlace de una biblioteca de Objective-c.](~/ios/platform/binding-objective-c/walkthrough.md)

Este artículo proporciona un tutorial paso a paso de creación de un proyecto de enlace con el código abierto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) proyecto Objective-C como ejemplo. La biblioteca de InfColorPicker proporciona un controlador de vista reutilizables que permiten al usuario seleccionar un color basado en su representación HSB, lo más fácil de usar la selección de color. Objetivo Sharpie se usará para ayudar en el proceso de enlace.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Ejemplos de enlace](https://github.com/mono/monotouch-bindings)

Una colección de enlaces de terceros que puede usarse como referencia al crear nuevos proyectos de enlace.

## <a name="mac"></a>Mac

Históricamente [enlace Mac](~/mac/platform/binding.md) ha sido un proceso manual. Actualmente no hay un [preview descargable](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) de soporte técnico de proyecto de enlace de Mac para una versión futura de Visual Studio para Mac.

## <a name="related-links"></a>Vínculos relacionados

- [iOS enlace](~/ios/platform/binding-objective-c/index.md)
- [Enlace de Mac](~/mac/platform/binding.md)
