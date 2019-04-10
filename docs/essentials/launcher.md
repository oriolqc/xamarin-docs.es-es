---
title: 'Xamarin.Essentials: Launcher'
description: La clase Launcher de Xamarin.Essentials permite que una aplicación abra un URI por el sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 26ab3687e9bcfecbd003ddd0a97f3e46a0cd92b2
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870058"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Selector

La clase **Launcher** permite que una aplicación abra un URI por el sistema. A menudo se usa al vincular en profundidad en los esquemas de URI personalizados de otra aplicación. Si quiere abrir el explorador en un sitio web, debe hacer referencia a la API **[Browser](open-browser.md)**.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-launcher"></a>Uso de Launcher

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para usar la funcionalidad Launcher, llame al método `OpenAsync` y pase un `string` o `Uri` para abrirla. Si quiere, el método `CanOpenAsync` se puede usar para comprobar si el esquema de URI se puede administrar desde una aplicación del dispositivo.

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="platform-differences"></a>Diferencias entre plataformas

# [<a name="android"></a>Android](#tab/android)

La tarea devuelta desde `CanOpenAsync` se completa de inmediato.

# [<a name="ios"></a>iOS](#tab/ios)

Si la aplicación de destino en este dispositivo nunca antes se abrió por `OpenAsync` desde la aplicación, iOS le pedirá una vez al usuario que permita que la aplicación la abra.

La tarea devuelta desde `CanOpenAsync` se completa de inmediato.

[Aquí](xref:UIKit.UIApplication.CanOpenUrl*) encontrará más información sobre la implementación de iOS.

# [<a name="uwp"></a>UWP](#tab/uwp)

No hay diferencias entre las plataformas.

-----

## <a name="api"></a>API

- [Código fuente de Launcher](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentación de API para Launcher](xref:Xamarin.Essentials.Launcher)
