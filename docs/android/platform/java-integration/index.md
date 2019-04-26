---
title: Información general sobre la integración de Java
description: El ecosistema de Java incluye una diversidad y gran colección de componentes. Muchos de estos componentes pueden usarse para reducir el tiempo necesario para desarrollar una aplicación Android. Este documento se presentan y proporcionar una descripción general de algunas de las formas en que los desarrolladores pueden usar estos componentes de Java existentes para mejorar su experiencia de desarrollo de aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/18/2017
ms.openlocfilehash: 3ab31fb7cac97fbae3315f51daf3dd4b1edbcc1d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085297"
---
# <a name="java-integration-overview"></a>Información general sobre la integración de Java

_El ecosistema de Java incluye una diversidad y gran colección de componentes. Muchos de estos componentes pueden usarse para reducir el tiempo necesario para desarrollar una aplicación Android. Este documento se presentan y proporcionar una descripción general de algunas de las formas en que los desarrolladores pueden usar estos componentes de Java existentes para mejorar su experiencia de desarrollo de aplicaciones de Xamarin.Android._


## <a name="overview"></a>Información general

Debido al alcance del ecosistema de Java, es muy probable que cualquier determinada funcionalidad necesaria para una aplicación de Xamarin.Android ya se ha codificado en Java. Por este motivo, es atractiva para probar y volver a usar estas bibliotecas existentes al crear una aplicación de Xamarin.Android. 

Hay tres maneras posibles para volver a usar las bibliotecas de Java en una aplicación de Xamarin.Android: 

-   **Crear una biblioteca de enlaces Java** &ndash; con esta técnica, se usa un proyecto de Xamarin.Android para crear C# contenedores en torno a los tipos de Java. A continuación, puede hacer referencia a una aplicación de Xamarin.Android el C# contenedores creados por este proyecto y, a continuación, utilice el `.jar` archivo. 

-   **Java Native Interface** &ndash; el *Java nativo* *interfaz* (JNI) es un marco que permite que el código no son de Java (como C++ o C#) para llamar a o llamar a código de Java que se ejecuta dentro de una JVM. 

-   **El código de puerto** &ndash; este método implica tomar el código fuente Java y, a continuación, convertirlo en C#. Esto puede realizarse manualmente o mediante una herramienta automatizada como dar nitidez. 

En el núcleo de las primeras dos técnicas es el *Java Native Interface* (JNI). JNI es un marco que permite a las aplicaciones que no se escriben en Java interactuar con código de Java que se ejecuta en una máquina Virtual de Java. Xamarin.Android usa JNI crear *enlaces* para C# código. 

La primera técnica es un enfoque declarativo más automatizado para el enlace de bibliotecas de Java. Implica el uso de Visual Studio para Mac o un tipo de proyecto de Visual Studio que Xamarin.Android proporciona &ndash; la biblioteca de enlaces de Java. Para crear correctamente estos enlaces, una biblioteca de enlaces Java todavía pueden requerir que algunas modificaciones manuales, pero no tantas como haría un enfoque puro de JNI. Consulte [enlace a una biblioteca de Java](~/android/platform/binding-java-library/index.md) para obtener más información acerca de las bibliotecas de Java de enlace. 

La segunda técnica con JNI, funciona en un nivel inferior mucho, pero puede proporcionar para un control más preciso y tener acceso a los métodos de Java que no suelen estar accesibles a través de una biblioteca de enlaces de Java. 

La tercera técnica es totalmente diferente de los dos anteriores: trasladar el código de Java a C#. Portar el código de un idioma a otro puede ser un proceso muy laborioso, pero es posible reducir que esfuerzo con la Ayuda de una herramienta denominada *enfocar*. Enfocar es una herramienta de código abierto que es un Java-a-C# convertidor. 



## <a name="summary"></a>Resumen

Este documento proporciona una descripción general de algunas de las distintas formas en que se pueden reutilizar las bibliotecas de Java en una aplicación de Xamarin.Android. Introdujo los conceptos de enlaces y contenedores RCW administrados y describen las opciones para migrar código de Java a C#. 


## <a name="related-links"></a>Vínculos relacionados

- [Arquitectura](~/android/internals/architecture.md)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Trabajo con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Dar nitidez](https://github.com/slluis/sharpen)
- [Java Native Interface](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
