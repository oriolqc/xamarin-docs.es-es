---
title: Migración de Java a C#
description: Una tercera opción para usar Java en una aplicación de Xamarin.Android es portar el código fuente de Java para C#.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/05/2016
ms.openlocfilehash: 9beb6d59c9376a404c06af7f0cd1efd985929843
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075151"
---
# <a name="porting-java-to-c"></a>Migración de Java a C#

_Una tercera opción para usar Java en una aplicación de Xamarin.Android es portar el código fuente de Java para C#._

## <a name="overview"></a>Información general

Este enfoque puede ser de interés para las organizaciones que:

-  **Cambia las pilas de tecnología de Java a C#.**
-  **Debe mantener un C# y una versión de Java del mismo producto.**
-  **Desea disponer de una versión de .NET de una biblioteca de Java más popular.**


Hay dos formas de migrar código de Java a C#. La primera consiste en trasladar el código manualmente. Esto implica que los programadores cualificados que entender .NET y Java y están familiarizado con las expresiones adecuadas para cada idioma. Este enfoque es más recomendable para pequeñas cantidades de código, o para las organizaciones que deseen mover completamente fuera de Java para C#.

La segunda metodología de migración consiste en probar y automatizar el proceso con un convertidor de código, como [enfocar](https://github.com/mono/sharpen). [Enfocar](https://github.com/mono/sharpen) es un convertidor de código abierto de Versant que se utilizó originalmente para portar el código para *db4o* de Java a C#. db4o es una base de datos orientada a objetos que Versant desarrolla en Java y, a continuación, portar a .NET. Mediante un convertidor de código puede tener sentido para los proyectos que deben existir en ambos lenguajes y que requieren algunas paridad entre los dos.

Un ejemplo de cuándo tiene sentido una herramienta de conversión de código automatizado puede verse en la [ngit](https://github.com/mono/ngit) proyecto.
Ngit es un puerto del proyecto Java [jgit](http://eclipse.org/).
Jgit propio es una implementación de Java de la [Git](http://git-scm.com/) sistema de administración de código de origen. Para generar C# código de Java, el uso de los programadores ngit personalizada automatizadas del sistema para extraer el código de Java de jgit, se aplican algunas revisiones para alojar el proceso de conversión y, a continuación, ejecutar Dar nitidez, lo que genera el C# código. Esto permite que el proyecto ngit sacar provecho del trabajo en curso, continua que se realiza en jgit.

A menudo hay una cantidad no trivial de trabajo relacionados con una herramienta de conversión de código automatizado de arranque, y esto puede resultar un obstáculo para usar. En muchos casos, puede ser más sencillo y fácil de Java para puerto C# a mano.



## <a name="related-links"></a>Vínculos relacionados

- [Enfocar la herramienta de conversión](https://github.com/mono/sharpen)
