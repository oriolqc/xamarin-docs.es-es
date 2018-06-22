---
title: Descripción de la integración de Java
description: El ecosistema de Java incluye una colección de diferentes y gran de componentes. Muchos de estos componentes pueden utilizarse para reducir el tiempo necesario para desarrollar una aplicación Android. Este documento se presentan y proporcionar una descripción general de algunas de las formas en que los desarrolladores pueden usar estos componentes existentes de Java para mejorar su experiencia de desarrollo de aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/18/2017
ms.openlocfilehash: dbaf17479ae077fced425df5ac31bdbbc4e06b64
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766951"
---
# <a name="java-integration-overview"></a>Descripción de la integración de Java

_El ecosistema de Java incluye una colección de diferentes y gran de componentes. Muchos de estos componentes pueden utilizarse para reducir el tiempo necesario para desarrollar una aplicación Android. Este documento se presentan y proporcionar una descripción general de algunas de las formas en que los desarrolladores pueden usar estos componentes existentes de Java para mejorar su experiencia de desarrollo de aplicaciones de Xamarin.Android._


## <a name="overview"></a>Información general

Dada la extensión del ecosistema de Java, es muy probable que cualquier funcionalidad determinado necesaria para una aplicación Xamarin.Android ya se ha codificado en Java. Por este motivo, resulta atractivo para probar y volver a usar estas bibliotecas existentes al crear una aplicación de Xamarin.Android. 

Hay tres formas posibles de volver a usar las bibliotecas de Java en una aplicación Xamarin.Android: 

-   **Crear una biblioteca de enlaces de Java** &ndash; con esta técnica, un proyecto Xamarin.Android se usa para crear contenedores de C# alrededor de los tipos de Java. Una aplicación de Xamarin.Android puede, a continuación, hacer referencia a los contenedores de C# creados por este proyecto y, a continuación, usar el `.jar` archivo. 

-   **Java Native Interface** &ndash; el *Java Native* *interfaz* (JNI) es un marco que permite que el código Java no (por ejemplo, C++ o C#) para llamar a o se llama a código de Java que se ejecuta dentro de una JVM . 

-   **Trasladar el código** &ndash; este método implica tomar el código fuente de Java y, a continuación, convertirlo en C#. Esto puede hacerse manualmente o mediante una herramienta automatizada como perfilar. 

En el núcleo de las primeras dos técnicas es el *Java Native Interface* (JNI). JNI es un marco que permite a las aplicaciones que no se escriben en Java interactuar con código de Java que se ejecuta en una máquina Virtual de Java. Xamarin.Android usa JNI para crear *enlaces* para código de C#. 

La primera técnica es un enfoque más automatizado y declarativo en el enlace de las bibliotecas de Java. Implica el uso de Visual Studio para Mac o un tipo de proyecto de Visual Studio que proporciona Xamarin.Android &ndash; la biblioteca de enlaces de Java. Para crear correctamente estos enlaces, una biblioteca de enlaces de Java todavía pueden requerir que algunas modificaciones manuales, pero no tantos como haría un enfoque JNI puro. Vea [enlace una biblioteca de Java](~/android/platform/binding-java-library/index.md) para obtener más información acerca de las bibliotecas de enlace de Java. 

La segunda técnica mediante JNI, funciona en un nivel inferior mucho, pero pueden proporcionar para un control más preciso y tener acceso a los métodos de Java que no suele ser accesibles a través de una biblioteca de enlace de Java. 

La tercera técnica es totalmente diferente de los dos anteriores: trasladar el código de Java a C#. Trasladar código de un idioma a otro puede ser un proceso muy laborioso, pero es posible reducir que esfuerzo con la Ayuda de una herramienta denominada *enfocar*. Enfocar es una herramienta de código abierto que es un Java-a-C# convertidor. 



## <a name="summary"></a>Resumen

Este documento proporciona una descripción general de algunas de las distintas maneras en que se pueden reutilizar las bibliotecas de Java en una aplicación Xamarin.Android. Introdujo los conceptos de enlaces y administrar contenedores CCW y tratan opciones para migrar el código de Java a C#. 


## <a name="related-links"></a>Vínculos relacionados

- [Arquitectura](~/android/internals/architecture.md)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Trabajar con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Dar nitidez](https://github.com/slluis/sharpen)
- [Interfaz Java Native Interface](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
