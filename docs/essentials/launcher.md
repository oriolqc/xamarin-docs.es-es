---
title: Selector de Xamarin.Essentials
description: La clase del iniciador en Xamarin.Essentials permite que una aplicación abrir un identificador URI por el sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 3ab820ece127558c1a5ca8b13c05fc4d6f20646e
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353956"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: iniciador

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **iniciador** clase permite que una aplicación abrir un identificador URI por el sistema. A menudo se utiliza cuando una vinculación en esquemas de URI personalizados de otra aplicación en profundidad. Si desea para abrir el explorador en un sitio Web, a continuación, se debe hacer referencia a la **[explorador](open-browser.md)** API.

## <a name="using-launcher"></a>Uso del iniciador

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para usar la llamada a la funcionalidad de iniciador el `OpenAsync` método y pase un `string` o `Uri` para abrir. Opcionalmente, el `CanOpenAsync` método puede usarse para comprobar si el esquema URI puede controlarse mediante una aplicación en el dispositivo.

```csharp
public class LauncherTest
{
    public async Task OpenRideShare()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="api"></a>API

- [Código fuente de iniciador](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentación de API de iniciador](xref:Xamarin.Essentials.Launcher)
