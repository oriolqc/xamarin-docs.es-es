---
title: Sharpie objetivo
description: Esta sección proporciona una introducción al objetivo Sharpie, herramienta de línea de comandos de Xamarin utilizado para automatizar el proceso de creación de un enlace a una biblioteca Objective-c.
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 4341aa7d2ea0bec88e9c36c1804c5a909fd6fd5c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="objective-sharpie"></a>Sharpie objetivo

_Esta sección proporciona una introducción al objetivo Sharpie, herramienta de línea de comandos de Xamarin utilizado para automatizar el proceso de creación de un enlace a una biblioteca Objective-c._

- [Información general sobre](#overview) & [historial](#history)
- [Introducción](get-started.md)
- [Herramientas y comandos](tools.md)
- [Características](platform/index.md)
- [Ejemplos](examples/index.md)
- [Tutorial completo](~/ios/platform/binding-objective-c/walkthrough.md)
- [Historial de versiones](releases.md)

## <a name="overview"></a>Información general

Sharpie objetivo es una herramienta de línea de comandos para ayudar a arrancar el primer paso de un enlace.
Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar la API pública en la [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (un proceso que anteriormente se puede realizar manualmente).

Objetivo Sharpie utiliza archivos de encabezado de análisis de Clang, por lo que el enlace sea lo más exacta y completa como sea posible. Esto puede reducir considerablemente el tiempo y el esfuerzo necesario para generar un enlace de calidad.

> [!IMPORTANT]
> Sharpie objetivo es una herramienta para desarrolladores de Xamarin con experiencia con conocimientos avanzados de Objective-C (y por extensión, C). Antes de intentar enlazar una biblioteca C objetivo debe tener un conocimiento sólido de cómo compilar la biblioteca nativa en la línea de comandos (y una buena comprensión del funcionamiento de la biblioteca nativa).

## <a name="history"></a>Historial

Hemos ha evolucionado y utilizando el Sharpie objetivo internamente en Xamarin durante los últimos tres años. Como fe a la potencia de objetivo Sharpie, API introdujeron en Xamarin.iOS y Xamarin.Mac desde iOS 8, Mac OS X 10.10 y watchOS 2.0 se iniciar completamente con Sharpie de objetivo. Xamarin depende en gran medida objetivo Sharpie internamente para la creación de sus propios productos.

Sin embargo, Sharpie objetivo es una herramienta muy avanzada que requiere conocimientos avanzados de Objective-C y C, cómo usar el compilador de clang en la línea de comandos y bibliotecas nativas normalmente cómo se colocan juntos. Debido a esta barra de alta, creímos que con una GUI asistente establece las expectativas incorrectas y, por lo tanto, objetivo Sharpie actualmente solo está disponible como una herramienta de línea de comandos.

## <a name="related-links"></a>Vínculos relacionados

- [Descarga de Sharpie objetivo](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [Tutorial: Enlazar una biblioteca Objective-c.](~/ios/platform/binding-objective-c/walkthrough.md)
- [Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalles de enlace](~/cross-platform/macios/binding/overview.md)
- [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desarrolladores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
