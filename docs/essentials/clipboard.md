---
title: 'Xamarin.Essentials: Portapapeles'
description: En este documento se describe la clase Clipboard de Xamarin.Essentials, que permite copiar y pegar texto en el Portapapeles del sistema entre aplicaciones.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: c186f5c61bd2fa3df305be92a03135e57e302d02
ms.sourcegitcommit: 6e04246207aa743820029e8c217a43cfdd24f991
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2019
ms.locfileid: "67352132"
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

> [!TIP]
> El acceso al Portapapeles debe realizarse en el subproceso de la interfaz de usuario principal. Consulte la API de [MainThread](~/essentials/main-thread.md) para ver cómo invocar métodos en el subproceso de la interfaz de usuario principal.

## <a name="api"></a>API

- [Código fuente de Clipboard](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentación de API de Clipboard](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
