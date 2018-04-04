---
title: Inserción de .NET
description: 'Incrustación de .NET permite que el código existente de .NET (C#, F # y otros) que vayan a usar en otros lenguajes de programación'
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: f5e778ef9ba31c1a9e880b9fc66c2e48ddb2420c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="net-embedding"></a>Inserción de .NET

![Vista previa](~/media/shared/preview.png)

Incrustación de .NET permite que el código existente de .NET (C#, F # y otros) que vayan a usar en otros lenguajes de programación y en varios entornos diferentes.

Esto significa que si tiene una biblioteca de .NET que desea utilizar desde la aplicación iOS existente, puede hacerlo.   O bien, si desea vincular con una biblioteca de C++ nativo, también puede hacerlo.   O bien, use código .NET de Java.

## <a name="environments-and-languages"></a>Entornos e idiomas

La herramienta es consciente de que el entorno que se va a utilizar, así como el idioma que se va a utilizar.   Por ejemplo, la plataforma iOS no permite la compilación de just-in-time (JIT), por lo que el embeddinator estáticamente compilará el código .NET en código nativo que se puede usar en iOS.  Otros entornos de permitir la compilación JIT y, en esos entornos, decidimos a la compilación JIT.

Es compatible con algunos consumidores de lenguaje, por lo que pone de manifiesto código .NET como idiomática código en el lenguaje de destino.   Esta es la lista de los idiomas admitidos en la actualidad:

- [**Objective-C** ](objective-c/index.md) : asignación de .NET a las API de C objetivo idiomática.
- [**Java** ](android/index.md) : asignación de .NET a las API de Java idiomática.
- **C**: asignación de .NET a una orientación a objetos como las API de C.

Más idiomas se proceden posteriormente.

## <a name="getting-started"></a>Introducción

Para empezar, seleccione una de nuestras guías para cada uno de los idiomas admitidos actualmente:

- [**Objective-C** ](get-started/objective-c/index.md) – cubre iOS y Mac OS.
- [**Java** ](get-started/java/index.md) – cubre macOS y Android.
- [**C** ](get-started/c.md) – cubre el lenguaje C en plataformas de escritorio.


## <a name="related-links"></a>Vínculos relacionados

- [4000 Embeddinator en GitHub](https://github.com/mono/Embeddinator-4000)
