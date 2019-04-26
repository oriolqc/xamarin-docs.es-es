---
title: Inserción de .NET
description: Inserción de .NET permite que el código existente de .NET (C#, F#etc.) que será consumido por el código escrito en otros lenguajes de programación.
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 23233ea8b06e0db580ba99edf2705e3dae5b931f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363560"
---
# <a name="net-embedding"></a>Inserción de .NET

![Vista previa](~/media/shared/preview.png)

Inserción de .NET permite que el código existente de .NET (C#, F#etc.) a ser consumidos desde otros lenguajes de programación y en diversos entornos diferentes.

Esto significa que si tiene una biblioteca de .NET que desea utilizar desde su aplicación iOS existente, puede hacerlo.   O bien, si desea vincularla con una biblioteca nativa de C++, también puede hacerlo.   O usar código de .NET desde Java.

Inserción de .NET se basa en el [Embeddinator 4000](https://github.com/mono/Embeddinator-4000) proyecto de código abierto.

## <a name="environments-and-languages"></a>Entornos y lenguajes

La herramienta es tener en cuenta el entorno que se va a utilizar, así como el lenguaje que lo consumen.   Por ejemplo, la plataforma iOS no admite la compilación de just-in-time (JIT), por lo que la inserción de .NET estáticamente se compilará el código de .NET en código nativo que se puede usar en iOS.  Otros entornos de permitir la compilación JIT y, en esos entornos, decidimos compilación JIT.

Es compatible con varios consumidores de lenguaje, por lo que pone de manifiesto código .NET como código idiomático en el lenguaje de destino.   Esta es la lista de idiomas admitidos en la actualidad:

- [**Objective-C** ](objective-c/index.md) : asignación de .NET a las API de Objective-C idiomático
- [**Java** ](android/index.md) : asignación de .NET a las API de Java idiomático
- [**C** ](get-started/c.md) : asignación de .NET a orientada a objetos como las API de C

Más idiomas se agregará más adelante.

## <a name="getting-started"></a>Introducción

Para empezar, seleccione una de nuestras guías para cada uno de los idiomas admitidos actualmente:

- [**Objective-C** ](get-started/objective-c/index.md) : se trata de iOS y macOS
- [**Java** ](get-started/java/index.md) : se trata de macOS y Android
- [**C** ](get-started/c.md) – cubre el lenguaje de C en plataformas de escritorio

## <a name="related-links"></a>Vínculos relacionados

- [Embeddinator-4000 en GitHub](https://github.com/mono/Embeddinator-4000)
