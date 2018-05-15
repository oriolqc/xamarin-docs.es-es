---
title: Portapapeles Xamarin.Essentials
description: La clase de Portapapeles le permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 67a0218325918b57e5ed2618b57d52d3fe3ee820
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-clipboard"></a>Portapapeles Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Portapapeles** clase le permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.

## <a name="using-clipboard"></a>Usar el Portapapeles

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Para comprobar si la **Portapapeles** tiene actualmente listo para pegar texto:

```csharp
var hasText = Clipboard.HasText;
```

Para establecer el texto en el **Portapapeles**:

```csharp
ClipBoard.SetText("Hello World");
```

Leer texto desde el **Portapapeles**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Código fuente de Portapapeles](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentación de la API del Portapapeles](xref:Xamarin.Essentials.Clipboard)
