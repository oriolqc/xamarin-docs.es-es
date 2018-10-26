---
title: Nomenclatura de parámetros con Javadoc
description: En este artículo se explica cómo recuperar los nombres de parámetro en un proyecto de enlace de Java mediante el uso de Javadoc generado a partir del proyecto de Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/20/2017
ms.openlocfilehash: e394377043953a297afed36a3ce0747a3e6d1512
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104419"
---
# <a name="naming-parameters-with-javadoc"></a>Nomenclatura de parámetros con Javadoc

_En este artículo se explica cómo recuperar los nombres de parámetro en un proyecto de enlace de Java mediante el uso de Javadoc generado a partir del proyecto de Java._


## <a name="overview"></a>Información general

Al enlazar una biblioteca de Java existente, se pierde algunos metadatos sobre la API de enlazado. En concreto los nombres de parámetros a métodos. Los nombres de parámetro aparecerá como `p0`, `p1`, etcetera. Esto es porque el Java `.class` archivos no conservan los nombres de parámetro que se usaron en el código fuente de Java. 

Un proyecto de enlace de Xamarin.Android Java puede proporcionar los nombres de parámetro, si tiene acceso al código HTML de Javadoc de la biblioteca original. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integración de Javadoc HTML en un proyecto de enlace de Java

Integrar el código HTML de Javadoc en un proyecto de Java de enlace es un proceso manual que consta de los pasos siguientes: 

1.  Descargue el Javadoc para la biblioteca
2.  Editar el `.csproj` archivo y agregue un `<JavaDocPaths>` propiedad:
3.  Limpie y recompile el proyecto

Una vez hecho esto, los nombres de parámetro Java originales deben estar presentes en las API de enlazar un proyecto de enlace de Java. 


> [!NOTE]
> Hay una gran cantidad de varianza en la salida de JavaDoc. El archivo. JAR cadena de herramientas de enlace no admite todas las permutaciones posibles única y por lo tanto algún parámetro puede no estar correctamente con nombre.


## <a name="summary"></a>Resumen

En este artículo tratan cómo usar Javadoc en un proyecto de enlace de Java para proporcionar nombres de parámetro de significado para las API de enlazado. 

