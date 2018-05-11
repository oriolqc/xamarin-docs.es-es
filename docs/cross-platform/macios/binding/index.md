---
title: Enlace de Objective-C
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: 721036993061d08dadf8b279e13981caaa51f91f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="binding-objective-c"></a>Enlace de Objective-C

Esta sección incluye una amplia variedad de documentos que abarcan crear enlaces a bibliotecas de Objective-C, por lo que se puede llamar desde las aplicaciones de C# creadas con Xamarin.iOS o Xamarin.Mac.

##  <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Información general](~/cross-platform/macios/binding/overview.md)

Este documento contiene algunos de los elementos internos de cómo realiza un enlace. Es un documento con cierta información técnica avanzado.

##  <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

Este documento describe el proceso usado para crear los enlaces de C# de API de C de objetivo y cómo se asignan las expresiones en Objective-C para las expresiones utilizados en .NET.
Si va a enlazar solo las API de C, debe usar el mecanismo estándar de .NET para esto, el marco de trabajo de P/Invoke.

##  <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guía de referencia de definición de enlace](~/cross-platform/macios/binding/binding-types-reference.md)

Se trata de la Guía de referencia que describe todos los atributos disponibles para los autores de enlaces para controlar el proceso de generación de enlace.


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Sharpie objetivo es una herramienta de línea de comandos para ayudar a arrancar el primer paso de un enlace. Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar la API pública en la [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md) (un proceso que también se puede realizar manualmente).

## <a name="ios"></a>iOS

El [una página de iOS enlace](~/ios/platform/binding-objective-c/index.md) vínculos volver a estos recursos comunes de enlace, además a los ejemplos siguientes.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Tutorial: Enlazar una biblioteca Objective-c.](~/ios/platform/binding-objective-c/walkthrough.md)

Este artículo proporciona un tutorial paso a paso para crear un proyecto de enlace con el código abierto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) proyecto Objective-C como ejemplo. La biblioteca de InfColorPicker proporciona un controlador de vista reutilizables que permiten al usuario seleccionar un color basado en su representación de HSB, lo más fácil de usar selección de color. Sharpie objetivo usará para ayudar en el proceso de enlace.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Ejemplos de enlace](https://github.com/mono/monotouch-bindings)

Una colección de enlaces de terceros que se puede usa una referencia al crear nuevos proyectos de enlace.

## <a name="mac"></a>Mac

Históricamente [enlace Mac](~/mac/platform/binding.md) ha sido un proceso muy manual. Actualmente no hay un [preview descargable](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) de soporte técnico de proyecto de enlace de Mac para una futura versión de Visual Studio para Mac.



## <a name="related-links"></a>Vínculos relacionados

- [iOS enlace](~/ios/platform/binding-objective-c/index.md)
- [Enlace de Mac](~/mac/platform/binding.md)
