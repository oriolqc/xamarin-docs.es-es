---
title: Ejemplo del mundo real con un proyecto de Xcode
description: Este documento describe cómo usar un proyecto de Xcode como una entrada directa a Sharpie objetivo, lo que simplifica el proceso de creación C# enlaces a código de Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 05c55dc7cd20de2d216d1f267ea5a73631748a0a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61265262"
---
# <a name="real-world-example-using-an-xcode-project"></a>Ejemplo del mundo real con un proyecto de Xcode

**Este ejemplo se usa el [biblioteca POP desde Facebook](https://github.com/facebook/pop).**

Nuevo en la versión 3.0, objetivo Sharpie admite proyectos Xcode como entrada. Estos proyectos especifican los archivos de encabezado adecuados y las marcas de compilador necesarias para compilar la biblioteca nativa y, por tanto, es necesarias enlazarla demasiado. Objetivo Sharpie seleccionará la primera _destino_ y su configuración predeterminada de un proyecto si no dado otras indicaciones.

Antes de objetivo Sharpie intenta analizar los archivos de proyecto y de encabezado, se debe compilar. Los proyectos suelen tengan las fases de compilación que correctamente se estructuran los archivos de encabezado para su uso externo y la integración, por lo que conviene siempre se compile el proyecto completo antes de intentar enlazar.

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

## <a name="related-links"></a>Vínculos relacionados

- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Generar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)