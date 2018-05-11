---
title: Ejemplo del mundo real utilizando un proyecto de Xcode
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 4e30b07c10de439df8e1ec1706845150e8c54a41
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="real-world-example-using-an-xcode-project"></a>Ejemplo del mundo real utilizando un proyecto de Xcode


**Este ejemplo se utiliza la [biblioteca POP desde Facebook](https://github.com/facebook/pop).**

Nuevo en la versión 3.0, objetivo Sharpie admite proyectos de Xcode como entrada. Estos proyectos especifican los archivos de encabezado adecuados y marcas de compilador necesarias para compilar la biblioteca nativa y, por tanto, es necesarias enlazarla demasiado. Objetivo Sharpie seleccionará la primera _destino_ y su configuración predeterminada de un proyecto si no se le solicita que hiciera lo contrario.

Antes de objetivo Sharpie intenta analizar los archivos de proyecto y de encabezado, se debe compilar. Los proyectos tienen a menudo fases de compilación que correctamente se estructura de archivos de encabezado de consumo externo y la integración, por lo que es mejor siempre se compile el proyecto completo antes de intentar enlazarla.

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

