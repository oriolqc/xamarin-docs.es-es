---
title: Trasladar Java a C# para Xamarin. Android
description: Una tercera opción para usar Java en una aplicación de Xamarin. Android es trasladar el código fuente de C#Java a.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/05/2016
ms.openlocfilehash: c6627f585326848c5221729ca94071b00651c59e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511174"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>Trasladar Java a C# para Xamarin. Android

Este enfoque puede ser de interés para organizaciones que:

- **Está cambiando las pilas de tecnología de Java C#a.**
- **Debe mantener una C# y una versión de Java del mismo producto.**
- **Desea tener una versión de .NET de una biblioteca de Java popular.**

Hay dos maneras de trasladar código de Java C#a. La primera forma es migrar el código manualmente. Esto implica a desarrolladores experimentados que entienden .NET y Java y que están familiarizados con las expresiones adecuadas para cada idioma. Este enfoque tiene más sentido para pequeñas cantidades de código o para organizaciones que quieren salir completamente de Java a C#.

La segunda metodología de migración consiste en probar y automatizar el proceso mediante el uso de un convertidor de código, como el [enfoque](https://github.com/mono/sharpen). [Sharp](https://github.com/mono/sharpen) es un convertidor de código abierto desde el que se usó originalmente para portar el código de *DB4O* de Java C#a. DB4O es una base de datos orientada a objetos que se ha desarrollado en Java y, a continuación, se ha migrado a .NET. El uso de un convertidor de código puede tener sentido para los proyectos que deben existir en ambos lenguajes y que requieran cierta paridad entre los dos.

Un ejemplo de Cuándo tiene sentido una herramienta de conversión de código automatizada puede verse en el proyecto [ngit](https://github.com/mono/ngit) .
Ngit es un puerto del proyecto de Java [las jgit](http://eclipse.org/).
Las jgit es una implementación de Java del sistema de administración de código fuente de [git](http://git-scm.com/) . Para generar C# código desde Java, los programadores de ngit usan un sistema automatizado personalizado para extraer el código Java de las jgit, aplicar algunas revisiones para acomodar el proceso de conversión y, a continuación, C# ejecutar enfoque, lo que genera el código. Esto permite que el proyecto ngit se beneficie del trabajo continuo continuo realizado en las jgit.

A menudo, hay una cantidad de trabajo que no es trivial con el arranque de una herramienta de conversión de código automatizada, lo que puede resultar una barrera para su uso. En muchos casos, puede ser más sencillo y más fácil trasladar Java a C# mano.

## <a name="related-links"></a>Vínculos relacionados

- [Herramienta de conversión de enfoque](https://github.com/mono/sharpen)
