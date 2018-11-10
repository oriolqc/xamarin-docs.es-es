---
title: 'Xamarin.Essentials: Portapapeles'
description: En este documento se describe la clase Clipboard de Xamarin.Essentials, que permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 8dd238da678dfb5773801137d313b286590aa463
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675541"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Portapapeles

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Clipboard** permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>Uso de Clipboard

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para comprobar si actualmente el **Portapapeles** tiene texto listo para pegar:

```csharp
var hasText = Clipboard.HasText;
```

Para establecer texto en el **Portapapeles**:

```csharp
Clipboard.SetText("Hello World");
```

Para leer texto desde el **Portapapeles**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Código fuente de Clipboard](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentación de API de Clipboard](xref:Xamarin.Essentials.Clipboard)
