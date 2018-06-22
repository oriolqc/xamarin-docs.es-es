---
title: 'Xamarin.Essentials: Portapapeles'
description: Este documento describe la clase de Portapapeles en Xamarin.Essentials, que le permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 41b15b480fa23bd49667b68e904043e4f1a95732
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782364"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Portapapeles

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
Clipboard.SetText("Hello World");
```

Leer texto desde el **Portapapeles**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Código fuente de Portapapeles](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentación de la API del Portapapeles](xref:Xamarin.Essentials.Clipboard)
