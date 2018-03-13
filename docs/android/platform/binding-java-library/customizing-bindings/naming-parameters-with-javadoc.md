---
title: "Nombres de parámetros con Javadoc"
description: "Este artículo explica cómo recuperar los nombres de parámetro en un proyecto de enlace de Java mediante el uso de Javadoc generado a partir del proyecto de Java."
ms.topic: article
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/20/2017
ms.openlocfilehash: d83135aa9c101e06a680b458cce8c12dcdddd947
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="naming-parameters-with-javadoc"></a>Nombres de parámetros con Javadoc

_Este artículo explica cómo recuperar los nombres de parámetro en un proyecto de enlace de Java mediante el uso de Javadoc generado a partir del proyecto de Java._


## <a name="overview"></a>Información general

Al enlazar una biblioteca Java, algunos metadatos sobre la API de enlace se pierde. En concreto los nombres de parámetros a métodos. Nombres de parámetro aparecerá como `p0`, `p1`, etcetera. Esto es porque el Java `.class` archivos no conservan los nombres de parámetro que se usaron en el código fuente de Java. 

Un proyecto de enlace Xamarin.Android Java puede proporcionar los nombres de parámetro, si tiene acceso al elemento HTML Javadoc de la biblioteca original. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integración de Javadoc HTML en un proyecto de enlace de Java

Integrar el código HTML de Javadoc en un proyecto de Java de enlace es un proceso manual que consta de los siguientes pasos: 

1.  Descargar el Javadoc para la biblioteca
2.  Editar la `.csproj` de archivos y agregar un `<JavaDocPaths>` propiedad:
3.  Limpie y recompile el proyecto

Una vez hecho esto, los nombres de parámetro de Java originales deben estar presentes en las API enlazadas por un proyecto de enlace de Java. 


> [!NOTE]
> Hay una gran variedad de en la salida de JavaDoc. El archivo. Cadena de herramientas JAR enlace no admite cada permutación posibles único y, por consiguiente, algún parámetro puede no ser correctamente con el nombre.


## <a name="summary"></a>Resumen

Este artículo trata cómo usar Javadoc en un proyecto de enlace de Java para proporcionar nombres de parámetro de significado a las API de enlace. 

