---
title: Enlace de bibliotecas de iOS
description: Este documento describe cómo crear C# enlaces a código de Objective-C, lo que permite usar las bibliotecas nativas y CocoaPods en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: e599d4f99877e24e06de2c26ed2cafe48526f6f5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61346946"
---
# <a name="binding-ios-libraries"></a>Enlace de bibliotecas de iOS

Siga estos vínculos para obtener información sobre las bibliotecas de Objective-C y CocoaPods de enlace de Xamarin.iOS y Xamarin.Mac:

- [**Información general sobre** ](~/cross-platform/macios/binding/overview.md) -
  describe cómo funciona el enlace.
- [**Enlace de bibliotecas de Objective-C** ](~/cross-platform/macios/binding/objective-c-libraries.md) -
  obtener instrucciones sobre cómo enlazar las bibliotecas de Objective-C para su uso en proyectos de Xamarin.
- [**Guía de referencia de la definición de tipo** ](~/cross-platform/macios/binding/binding-types-reference.md) -
  describe todos los atributos disponibles para los autores de enlace para controlar el proceso de generación de enlace.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Sharpie objetivo es una herramienta de línea de comandos para ayudar a arrancar el primer paso de un enlace.
Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar a la API pública en el [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md) (un proceso que en caso contrario, se realiza manualmente). Sharpie objetivo no crea un enlace por sí mismo, pero puede ayudarle a empezar.

¡Objetivo 3.0 Sharpie introdujo la capacidad de enlazar directamente Cocoapods!

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Tutorial: enlace de una biblioteca de Objective-C de iOS](walkthrough.md)

Esta página proporciona un tutorial paso a paso de creación de un proyecto de enlace de iOS con el código abierto [ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) proyecto Objective-C como ejemplo. El **InfColorPicker** biblioteca proporciona un controlador de vista reutilizables que permiten al usuario seleccionar un color basado en su representación HSB, lo más fácil de usar la selección de color.
Objetivo Sharpie se usará para ayudar en el proceso de enlace.

## <a name="xamarin-university-lightning-lecture"></a>Xamarin University Lightning charla

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**iOS enlaces en C/C ++, por [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Vínculos relacionados

- [Enlace de Objective-C](~/cross-platform/macios/binding/index.md)
- [Enlace de Mac](~/mac/platform/binding.md)
- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Generar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)