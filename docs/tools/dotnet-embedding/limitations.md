---
title: Limitaciones de incrustación de .NET
description: Este documento describe las limitaciones de incrustación. NET, la herramienta que permite utilizar código .NET en otros lenguajes de programación.
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 7a162d632c98b4e412fa1b7b0c0c40ac945ff09f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114358"
---
# <a name="net-embedding-limitations"></a>Limitaciones de incrustación de .NET

Este documento explica las limitaciones de inserción de .NET y, siempre que sea posible, proporciona soluciones para ellos.

## <a name="general"></a>General

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usar más de una biblioteca incrustada en un proyecto

No es posible tener dos tiempos de ejecución Mono coexistan dentro de la misma aplicación. Esto significa que no puede usar dos bibliotecas diferentes generados por inserción de .NET dentro de la misma aplicación.

**Solución alternativa:** puede usar el generador para crear una biblioteca única que incluye varios ensamblados (de proyectos diferentes).

### <a name="subclassing"></a>Creación de subclases

Inserción de .NET facilita la integración del tiempo de ejecución Mono dentro de las aplicaciones mediante la exposición de un conjunto de API listos para usar para el idioma de destino y plataforma.

Sin embargo, esto no es una integración bidireccional, por ejemplo, no puede crear subclases de un tipo administrado y código administrado para devolver la llamada dentro del código nativo, ya que el código administrado no es consciente de esta coexistencia de espera.

Según sus necesidades, puede que sea posible a las partes de la solución de esta limitación, p. ej.

* el código administrado puede p/invoke en el código nativo. Para ello, personalizar el código administrado para permitir la personalización desde código nativo;

* Use productos como Xamarin.iOS y exponer una biblioteca administrada que permitiría que Objective-C (en este caso) para crear una subclase administra algunos NSObject subclases.

## <a name="objective-c-generated-code"></a>Código generado de Objective-C

### <a name="nullability"></a>Nulabilidad

No hay ningún metadato de .NET que proporcione información si es aceptable una referencia nula o no para una API. La mayoría de las API producirán `ArgumentNullException` si no se puede hacer frente a con un `null` argumento. Esto es problemático como Objective-C de control de excepciones es algo mejor evitar.

Puesto que no podemos generar anotaciones precisas de nulabilidad en los archivos de encabezado y desea minimizar las excepciones administradas se predeterminado para los argumentos que no son null (`NS_ASSUME_NONNULL_BEGIN`) y agregue algunas específicas, cuando la precisión es posible, las anotaciones de nulabilidad.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Es la incrustación de objetos de .NET no compatible con bitcode en iOS, que está habilitado para algunas plantillas de proyecto de Xcode. Esto debe deshabilitarse para marcos de trabajo en el vínculo que se generó correctamente.

![Opción Bitcode](images/ios-bitcode-option.png)
