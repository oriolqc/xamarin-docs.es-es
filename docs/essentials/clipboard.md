---
title: Portapapeles Xamarin.Essentials
description: La clase de Portapapeles le permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 44ba8090851b65327682b3d290d58fb23bb8aae4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
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

- [Código fuente de Portapapeles](https://github.com/xamarin/Essentials/tree/master/Essentials/Clipboard)
- [Documentación de la API del Portapapeles](xref:Xamarin.Essentials.Clipboard)
