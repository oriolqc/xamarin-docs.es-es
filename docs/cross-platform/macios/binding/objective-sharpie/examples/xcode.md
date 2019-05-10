---
title: Ejemplo del mundo real con un proyecto de Xcode
description: Este documento describe cómo usar un proyecto de Xcode como una entrada directa a Sharpie objetivo, lo que simplifica el proceso de creación C# enlaces a código de Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ccfc2f1760d8971e2d824cf65344fa2a5e158c12
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978369"
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

