---
title: Enlace iOS bibliotecas
description: Este documento describe cómo crear C# enlaces a código Objective-C, lo que permite utilizar bibliotecas nativas y CocoaPods en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: b054595568a34616a01f2c3f3c7d85f968c3f1fa
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787178"
---
# <a name="binding-ios-libraries"></a>Enlace iOS bibliotecas

Siga estos vínculos para obtener información sobre cómo enlazar bibliotecas Objective-C y CocoaPods para Xamarin.iOS y Xamarin.Mac:

- [**Información general sobre** ](~/cross-platform/macios/binding/overview.md) -
  describe cómo funciona el enlace.
- [**Enlace bibliotecas Objective-C** ](~/cross-platform/macios/binding/objective-c-libraries.md) -
  obtener instrucciones sobre cómo enlazar Objective-C bibliotecas para usarlas en los proyectos de Xamarin.
- [**Guía de referencia de la definición de tipo** ](~/cross-platform/macios/binding/binding-types-reference.md) -
  describe todos los atributos disponibles para los autores de enlaces para controlar el proceso de generación de enlace.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Sharpie objetivo es una herramienta de línea de comandos para ayudar a arrancar el primer paso de un enlace.
Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar la API pública en la [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md) (un proceso que en caso contrario, se realiza manualmente). Sharpie objetivo no crea un enlace por sí mismo, pero puede ayudarle a empezar.

¡Objetivo 3.0 Sharpie introdujo la capacidad de enlazar directamente Cocoapods!

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Tutorial: enlace de una biblioteca de C de objetivo de iOS](walkthrough.md)

Esta página proporciona un tutorial paso a paso para crear un proyecto de enlace de iOS con el código abierto [ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) proyecto Objective-C como ejemplo. El **InfColorPicker** biblioteca proporciona un controlador de vista reutilizables que permiten al usuario seleccionar un color basado en su representación de HSB, lo más fácil de usar selección de color.
Sharpie objetivo usará para ayudar en el proceso de enlace.

## <a name="xamarin-university-lightning-lecture"></a>Charla de rayos Universidad de Xamarin

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**iOS enlaces en C o C++, por [Universidad de Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Vínculos relacionados

- [Enlace de Objective-C](~/cross-platform/macios/binding/index.md)
- [Enlace de Mac](~/mac/platform/binding.md)
