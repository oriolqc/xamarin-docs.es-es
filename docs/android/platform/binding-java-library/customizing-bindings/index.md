---
title: Personalizar los enlaces
description: Puede personalizar un enlace Xamarin.Android mediante la edición de los metadatos que controla el proceso de enlace. Estas modificaciones manuales a menudo son necesarias para resolver errores de compilación y dar forma a la API resultante para que resulte más coherente con C# / .NET. Estas guías explican la estructura de estos metadatos, cómo modificar los metadatos y cómo usar JavaDoc para recuperar los nombres de parámetros de método.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 09/25/2017
ms.openlocfilehash: bb4f3b24be2072cb8b33893899a23951ace63607
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763142"
---
# <a name="customizing-bindings"></a>Personalizar los enlaces

_Puede personalizar un enlace Xamarin.Android mediante la edición de los metadatos que controla el proceso de enlace. Estas modificaciones manuales a menudo son necesarias para resolver errores de compilación y dar forma a la API resultante para que resulte más coherente con C# / .NET. Estas guías explican la estructura de estos metadatos, cómo modificar los metadatos y cómo usar JavaDoc para recuperar los nombres de parámetros de método._


## <a name="overview"></a>Información general
 
Xamarin.Android automatiza gran parte del proceso de enlace; Sin embargo, en algunos casos es necesario realizar modificaciones manuales para resolver los problemas siguientes:

-   Resolver errores causados por falta de tipos, tipos confusas, nombres duplicados, problemas de visibilidad de clase y otras situaciones que no se pueden resolver la compilación mediante las herramientas de Xamarin.Android. 

-   Cambiar la asignación que Xamarin.Android se utiliza para enlazar la API de Android a diferentes tipos en C# (por ejemplo, muchos desarrolladores prefieren Java se asignan `int` constantes en C# `enum` constantes).

-   Quitar los tipos que no deben estar enlazados. 

-   Adición de tipos que no tengan equivalente en la API de Java subyacente. 

Hacer que algunos o todos estos cambios mediante la modificación de los metadatos que controla el proceso de enlace.


## <a name="guides"></a>Guías

Las guías siguientes describen los metadatos que controla el proceso de enlace y explican cómo modificar estos metadatos para resolver estos problemas:

-   [Metadatos de enlaces de Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) proporciona información general de los metadatos que entra en un enlace de Java.
    Describe los distintos pasos manuales que a veces son necesarias para completar una biblioteca de enlace de Java y se explica cómo formar una API expuesta por un enlace a seguir las instrucciones de diseño de .NET de forma más estrecha.

-   [Nombres de parámetros con Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) explica cómo recuperar los nombres de parámetro en un proyecto de enlace de Java mediante el uso de Javadoc generado desde el proyecto dependiente de Java.


 

