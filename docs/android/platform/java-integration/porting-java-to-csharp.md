---
title: Migrar de Java a C#
description: Una tercera opción para usar Java en una aplicación de Xamarin.Android es migrar el código fuente de Java a C#.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/05/2016
ms.openlocfilehash: c2d05b101c627dab42dc1343eab2a408d1bd010f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762602"
---
# <a name="porting-java-to-c"></a>Migrar de Java a C#

_Una tercera opción para usar Java en una aplicación de Xamarin.Android es migrar el código fuente de Java a C#._

## <a name="overview"></a>Información general

Este planteamiento puede ser de interés para las organizaciones que:

-  **Cambia las pilas de tecnología de Java a C#.**
-  **Debe mantener un C# y una versión de Java del mismo producto.**
-  **Se desea que una versión de .NET de una biblioteca popular de Java.**


Hay dos formas de migrar código de Java a C#. La primera manera consiste en migrar el código manualmente. Esto implica que los desarrolladores con conocimientos que entender .NET y Java y están familiarizado con las expresiones adecuados para cada idioma. Este método hace que sea más conveniente para pequeñas cantidades de código, o para las organizaciones que desean mover el completamente fuera de Java a C#.

La segunda metodología de migración es intentar automatizar el proceso con un convertidor de código, como [enfocar](https://github.com/mono/sharpen). [Enfocar](https://github.com/mono/sharpen) es un convertidor de código abierto de Versant que se utilizó originalmente para trasladar el código para *db4o* de Java a C#. db4o es una base de datos orientado a objetos que Versant desarrollado en Java y, a continuación, pasar a .NET. Utilizando un convertidor de código puede tener sentido para los proyectos que debe existir en ambos lenguajes y que requieren algunas paridad entre los dos.

Se puede ver un ejemplo de cuándo una herramienta de conversión de código automatizado tiene sentido en el [ngit](https://github.com/mono/ngit) proyecto.
Ngit es un puerto del proyecto Java [jgit](http://eclipse.org/).
Jgit propio es una implementación de Java de la [Git](http://git-scm.com/) sistema de administración de código de origen. Para generar código de C# en Java, los programadores ngit utilizan un sistema automatizado personalizado para extraer el código Java de jgit, aplicar algunas revisiones para alojar el proceso de conversión y, a continuación, ejecutar nitidez, que genera el código de C#. Esto permite al proyecto ngit sacar provecho del trabajo en curso, continuado que se realice en jgit.

A menudo hay una cantidad no trivial de trabajo relacionados con una herramienta de conversión de código automatizado de arranque y esto puede resultar una barrera para usar. En muchos casos, puede ser más sencillo y fácil de puerto Java a C# manualmente.



## <a name="related-links"></a>Vínculos relacionados

- [Enfocar la herramienta de conversión](https://github.com/mono/sharpen)
