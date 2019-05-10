---
title: Creación de enlaces con Sharpie objetivo
description: Esta sección proporciona una introducción al objetivo Sharpie, herramienta de línea de comandos de Xamarin usan para automatizar el proceso de creación de un enlace a una biblioteca de Objective-C
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: cbe0be19e38892d06a79831d7974fbddcb71a9ac
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978488"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Creación de enlaces con Sharpie objetivo

_Esta sección proporciona una introducción al objetivo Sharpie, herramienta de línea de comandos de Xamarin usan para automatizar el proceso de creación de un enlace a una biblioteca de Objective-C_

- [Información general sobre](#overview) & [historial](#history)
- [Introducción](get-started.md)
- [Herramientas y comandos](tools.md)
- [Características](platform/index.md)
- [Ejemplos](examples/index.md)
- [Tutorial completo](~/ios/platform/binding-objective-c/walkthrough.md)
- [Historial de versiones](releases.md)

## <a name="overview"></a>Información general

Sharpie objetivo es una herramienta de línea de comandos para ayudar a arrancar el primer paso de un enlace.
Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar a la API pública en el [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (un proceso que anteriormente se puede realizar manualmente).

Objetivo Sharpie usa archivos de encabezado de análisis de Clang, por lo que el enlace es tan completa como sea posible y exacto. Esto puede reducir considerablemente el tiempo y esfuerzo que se tarda en generar un enlace de calidad.

> [!IMPORTANT]
> Sharpie objetivo es una herramienta para desarrolladores con experiencia de Xamarin con conocimientos avanzados de Objective-C (y por extensión, C). Antes de intentar enlazar una biblioteca de Objective-C debe tener un conocimiento sólido de cómo compilar la biblioteca nativa en la línea de comandos (y una buena comprensión del funcionamiento de la biblioteca nativa).

## <a name="history"></a>Historial

Hemos ha evolucionando y utilizando el Sharpie objetivo internamente en Xamarin durante los últimos tres años. Como un testimonio de la eficacia de Sharpie objetivo, las API se introdujeron en Xamarin.iOS y Xamarin.Mac desde iOS 8, Mac OS X 10.10, y watchOS 2.0 se arranca completamente con Sharpie objetivo. Xamarin se basa principalmente en objetivo Sharpie internamente para crear sus propios productos.

Sin embargo, Sharpie objetivo es una herramienta muy avanzada que requiere conocimientos avanzados de Objective-C y C, cómo usar el compilador de clang en la línea de comandos y las bibliotecas nativas con carácter general de cómo se colocan juntos. Debido a esta barra alta, pensamos que tener una GUI asistente establece las expectativas incorrectas y, por lo tanto, objetivo Sharpie actualmente solo está disponible como una herramienta de línea de comandos.

## <a name="related-links"></a>Vínculos relacionados

- [Descarga de Sharpie objetivo](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [Tutorial: Enlace de una biblioteca de Objective-c.](~/ios/platform/binding-objective-c/walkthrough.md)
- [Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalles de enlace](~/cross-platform/macios/binding/overview.md)
- [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desarrolladores de Objective-C](~/ios/get-started/objective-c-developers/index.md)

