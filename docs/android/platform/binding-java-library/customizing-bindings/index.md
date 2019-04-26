---
title: Personalización de enlaces
description: Puede personalizar un enlace de Xamarin.Android mediante la edición de los metadatos que controla el proceso de enlace. Estas modificaciones manuales a menudo son necesarias para resolver errores de compilación y para dar forma a la API resultante para que sea más coherente con C#/.NET. Estas guías explican la estructura de estos metadatos, cómo modificar los metadatos y cómo usar JavaDoc para recuperar los nombres de parámetros de método.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: 44bff372225ee1bf555eb3eeb34da918830980b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957569"
---
# <a name="customizing-bindings"></a>Personalización de enlaces

_Puede personalizar un enlace de Xamarin.Android mediante la edición de los metadatos que controla el proceso de enlace. Estas modificaciones manuales a menudo son necesarias para resolver errores de compilación y para dar forma a la API resultante para que sea más coherente con C#/.NET. Estas guías explican la estructura de estos metadatos, cómo modificar los metadatos y cómo usar JavaDoc para recuperar los nombres de parámetros de método._


## <a name="overview"></a>Información general
 
Xamarin.Android automatiza gran parte del proceso de enlace; Sin embargo, en algunos casos es necesario realizar modificaciones manuales para resolver los problemas siguientes:

-   Resolver errores causados por falta de tipos, tipos de ofuscación, nombres duplicados, problemas de visibilidad de clase y otras situaciones que no se puede resolver la compilación mediante las herramientas de Xamarin.Android. 

-   Cambiar la asignación de Xamarin.Android usa para enlazar la API de Android con diferentes tipos en C# (por ejemplo, muchos desarrolladores prefieren asignar Java `int` constantes a C# `enum` constantes).

-   Eliminación de tipos no usados que no deben estar enlazados. 

-   Adición de tipos que no tienen ningún homólogo en la API de Java subyacente. 

Puede realizar algunos o todos estos cambios mediante la modificación de los metadatos que controla el proceso de enlace.


## <a name="guides"></a>Guías

Las siguientes guías describen los metadatos que controla el proceso de enlace y explican cómo modificar estos metadatos para resolver estos problemas:

-   [Metadatos de enlaces Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) proporciona información general de los metadatos que entrarán en un enlace de Java.
    Describe los distintos pasos manuales que a veces son necesarias para completar una biblioteca de enlaces de Java y se explica cómo dar forma a una API expuesta por un enlace mejor seguir las instrucciones de diseño. NET.

-   [Nomenclatura de parámetros con Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) se explica cómo recuperar los nombres de parámetro en un proyecto de enlace de Java mediante el uso de Javadoc producido desde el proyecto dependiente de Java.


 

