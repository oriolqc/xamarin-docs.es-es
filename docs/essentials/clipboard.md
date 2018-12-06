---
title: 'Xamarin.Essentials: Portapapeles'
description: En este documento se describe la clase Clipboard de Xamarin.Essentials, que permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 90ede9d0d0fbee9efabcce25c0ae7c3c439d9e69
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898708"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Portapapeles

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
await Clipboard.SetTextAsync("Hello World");
```

Para leer texto desde el **Portapapeles**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Código fuente de Clipboard](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentación de API de Clipboard](xref:Xamarin.Essentials.Clipboard)
