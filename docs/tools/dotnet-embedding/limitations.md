---
title: "Limitaciones de incrustación de .NET"
ms.topic: article
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 110eb4169103f99c1538a1846fd231069863530c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="net-embedding-limitations"></a>Limitaciones de incrustación de .NET


Este documento explica las limitaciones de .NET incrustación (Embeddinator-4000) y, siempre que sea posible, proporciona soluciones para ellos.

## <a name="general"></a>General

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usar más de una biblioteca incrustada en un proyecto

No es posible tener dos tiempos de ejecución mono coexistan dentro de la misma aplicación. Esto significa que no puede utilizar dos bibliotecas diferentes embeddinator 4000 generado dentro de la misma aplicación.

**Solución alternativa:** puede usar el generador para crear una biblioteca única que incluye varios ensamblados (de proyectos diferentes).

### <a name="subclassing"></a>Creación de subclases

El embeddinator facilita la integración del tiempo de ejecución mono dentro de las aplicaciones mediante la exposición de un conjunto de API de listas para usar para la plataforma y el lenguaje de destino.

Sin embargo, esto no es una integración bidireccional, por ejemplo, no puede crear subclases de un tipo administrado y espera que el código administrado para volver a llamar dentro de su código nativo, ya que el código administrado no es consciente de este coexistencia.

Según sus necesidades, es posible que partes de la solución de esta limitación, p. ej.

* el código administrado puede p/invoke en el código nativo. Para ello, personalizar el código administrado para permitir la personalización desde código nativo;

* usar productos como Xamarin.iOS y exponer una biblioteca administrada que permitiría ObjC (en este caso) para crear una subclase algunos subclases NSObject administradas.


## <a name="objc-generated-code"></a>ObjC genera código

### <a name="nullability"></a>Aceptación de valores null

No hay ningún metadato en. NET, que decirnos si una referencia nula es aceptable o no de una API. La mayoría de las API producirán `ArgumentNullException` si no puede afrontar un `null` argumento. Esto es problemático como ObjC control de excepciones es mejor evitar.

Dado que se puede generar las anotaciones de nulabilidad precisa en los archivos de encabezado y desea minimizar las excepciones administradas se predeterminado para los argumentos no son null (`NS_ASSUME_NONNULL_BEGIN`) y agregue algunos específico, cuando es posible que la precisión, las anotaciones de nulabilidad.
