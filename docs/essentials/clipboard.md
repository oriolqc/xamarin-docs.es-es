---
title: Portapapeles Xamarin.Essentials
description: La clase de Portapapeles le permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 218f90746f130f02c47040a9191b1001fa80c203
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
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
