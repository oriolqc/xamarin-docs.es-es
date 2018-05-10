---
title: Limitaciones de incrustación de .NET
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 8ea3e7c6ff2fc28700c46109a814fc5da6958500
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="net-embedding-limitations"></a>Limitaciones de incrustación de .NET

Este documento explica las limitaciones de incrustación de .NET y, siempre que sea posible, proporciona soluciones para ellos.

## <a name="general"></a>General

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usar más de una biblioteca incrustada en un proyecto

No es posible tener dos tiempos de ejecución Mono coexistan dentro de la misma aplicación. Esto significa que no puede utilizar dos bibliotecas distintas generado .NET incrustar dentro de la misma aplicación.

**Solución alternativa:** puede usar el generador para crear una biblioteca única que incluye varios ensamblados (de proyectos diferentes).

### <a name="subclassing"></a>Creación de subclases

Incrustar .NET facilita la integración del tiempo de ejecución Mono dentro de las aplicaciones mediante la exposición de un conjunto de API de listas para usar para la plataforma y el lenguaje de destino.

Sin embargo, esto no es una integración bidireccional, por ejemplo, no puede crear subclases de un tipo administrado y espera que el código administrado para volver a llamar dentro de su código nativo, ya que el código administrado no es consciente de este coexistencia.

Según sus necesidades, es posible que partes de la solución de esta limitación, p. ej.

* el código administrado puede p/invoke en el código nativo. Para ello, personalizar el código administrado para permitir la personalización desde código nativo;

* usar productos como Xamarin.iOS y exponer una biblioteca administrada que permitiría que Objective-C (en este caso) para crear una subclase algunas administradas NSObject subclases.

## <a name="objective-c-generated-code"></a>Código generado Objective-C

### <a name="nullability"></a>Aceptación de valores null

No hay ningún metadato en .NET que proporcione información si es aceptable una referencia nula o no de una API. La mayoría de las API producirán `ArgumentNullException` si no puede afrontar un `null` argumento. Esto es problemático como Objective-C de control de excepciones es mejor evitar.

Dado que se puede generar las anotaciones de nulabilidad precisa en los archivos de encabezado y desea minimizar las excepciones administradas se predeterminado para los argumentos no son null (`NS_ASSUME_NONNULL_BEGIN`) y agregue algunos específico, cuando es posible que la precisión, las anotaciones de nulabilidad.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Incrustación de .NET no admite actualmente bitcode en iOS, que está habilitada para algunas plantillas de proyecto de Xcode. Esto tendrá que ser deshabilitada para marcos de vínculo generado correctamente.

![Opción Bitcode](images/ios-bitcode-option.png)
