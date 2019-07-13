---
title: Conceptos avanzados y funcionamiento interno
description: Arquitectura subyacente detrás de Xamarin.Android y diseño de API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/21/2018
ms.openlocfilehash: ba804e9757fb1354c39177e6f7cb9c97ea1b8188
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864133"
---
# <a name="advanced-concepts-and-internals"></a>Conceptos avanzados y funcionamiento interno

_Esta sección contiene temas que explican la arquitectura, diseño de API y las limitaciones de Xamarin.Android. Además, incluye temas que explican su implementación de la colección de elementos no utilizados y los ensamblados que están disponibles en Xamarin.Android. Dado que es Xamarin.Android [abierto](https://github.com/xamarin/xamarin-android), también es posible comprender el funcionamiento interno de Xamarin.Android mediante el examen de su código fuente._


## <a name="architectureandroidinternalsarchitecturemd"></a>[Arquitectura](~/android/internals/architecture.md)

En este artículo se explica la arquitectura subyacente de una aplicación de Xamarin.Android. Explica cómo ejecutan las aplicaciones de Xamarin.Android dentro de un entorno de ejecución Mono junto con el tiempo de ejecución Android Máquina Virtual y se explican conceptos claves como Android contenedores RCW y contenedores administrados. 



## <a name="api-designandroidinternalsapi-designmd"></a>[Diseño de API](~/android/internals/api-design.md)

El núcleo de bibliotecas de clases Base que forman parte de Mono, además de Xamarin.Android se suministra con enlaces para distintas API de Android permitir a los desarrolladores crear aplicaciones nativas de Android con Mono.

En el núcleo de Xamarin.Android existe es un motor de interoperabilidad ese mundo puentes de C# con el mundo de Java y ofrece a los desarrolladores con acceso a las API de Java desde C# o en otros lenguajes. NET.



## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Ensamblados](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android se suministra con varios ensamblados. Como Silverlight es un subconjunto de los ensamblados de .NET de escritorio extendido, Xamarin.Android también es un subconjunto de las distintas Silverlight y ensamblados de .NET de escritorio extendido. 

