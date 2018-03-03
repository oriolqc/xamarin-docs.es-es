---
title: Enlace iOS bibliotecas
description: "Cómo crear bibliotecas nativas de iOS (y CocoaPods) accesible en aplicaciones de Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 3afe1a03299e600502d49b1db039af4c6642e131
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="binding-ios-libraries"></a>Enlace iOS bibliotecas

_Cómo crear bibliotecas nativas de iOS (y CocoaPods) accesible en aplicaciones de Xamarin._

Siga estos vínculos para obtener información sobre cómo enlazar bibliotecas Objective-C y CocoaPods para Xamarin.iOS y Xamarin.Mac:

- [**Información general sobre** ](~/cross-platform/macios/binding/overview.md) -
  describe cómo funciona el enlace.
- [**Enlace bibliotecas Objective-C** ](~/cross-platform/macios/binding/objective-c-libraries.md) -
  obtener instrucciones sobre cómo enlazar Objective-C bibliotecas para usarlas en los proyectos de Xamarin.
- [**Guía de referencia de la definición de tipo** ](~/cross-platform/macios/binding/binding-types-reference.md) -
  describe todos los atributos disponibles para los autores de enlaces para controlar el proceso de generación de enlace.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Sharpie objetivo](~/cross-platform/macios/binding/objective-sharpie/index.md)

Sharpie objetivo es una herramienta de línea de comandos para ayudar a arrancar el primer paso de un enlace.
Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar la API pública en la [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md) (un proceso que en caso contrario, se realiza manualmente). Sharpie objetivo no crea un enlace por sí mismo, pero puede ayudarle a empezar.

¡Objetivo 3.0 Sharpie introdujo la capacidad de enlazar directamente Cocoapods!

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Tutorial: enlace de una biblioteca de C de objetivo de iOS](walkthrough.md)

Esta página proporciona un tutorial paso a paso para crear un proyecto de enlace de iOS con el código abierto [ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) proyecto Objective-C como ejemplo. El **InfColorPicker** biblioteca proporciona un controlador de vista reutilizables que permiten al usuario seleccionar un color basado en su representación de HSB, lo más fácil de usar selección de color.
Sharpie objetivo usará para ayudar en el proceso de enlace.



## <a name="related-links"></a>Vínculos relacionados

- [Enlace Objective-c.](~/cross-platform/macios/binding/index.md)
- [Enlace de Mac](~/mac/platform/binding.md)
