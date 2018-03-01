---
title: Conceptos avanzados y elementos internos
description: "Arquitectura subyacente detrás de Xamarin.Android y su diseño de la API."
ms.topic: article
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/13/2017
ms.openlocfilehash: d120398d4c59e51cee8da5e8ed2fbe0994ceca76
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="advanced-concepts-and-internals"></a>Conceptos avanzados y elementos internos


##  <a name="architectureandroidinternalsarchitecturemd"></a>[Arquitectura](~/android/internals/architecture.md)

Este artículo explica la arquitectura subyacente de una aplicación Xamarin.Android. Explica cómo ejecutan aplicaciones Xamarin.Android dentro de un entorno de ejecución Mono junto con el tiempo de ejecución Android Máquina Virtual y se explican conceptos claves como Android contenedores CCW y administrar contenedores CCW. 



##  <a name="api-designandroidinternalsapi-designmd"></a>[Diseño de la API](~/android/internals/api-design.md)

Además del bibliotecas de clases Base que forman parte de Mono de núcleo, Xamarin.Android incluye enlaces para varias API de Android permitir a los desarrolladores crear aplicaciones Android nativas con Mono.

En el núcleo de Xamarin.Android existe es un motor de interoperabilidad que world puentes C# con el mundo de Java y ofrece a los desarrolladores con acceso a las API de Java de C# u otros lenguajes. NET.



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Ensamblados](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android se suministra con varios ensamblados. Igual que Silverlight es un subconjunto de los ensamblados .NET escritorio extendido, Xamarin.Android también es un subconjunto extendido de varios ensamblados de .NET de escritorio y Silverlight. 

