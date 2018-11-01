---
title: 'Xamarin.Essentials: Launcher'
description: La clase Launcher de Xamarin.Essentials permite que una aplicación abra un URI por el sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: b9dc211a8b8b007e6975448a6c6c35f870021cc6
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674801"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Launcher

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

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

## <a name="platform-differences"></a>Diferencias entre las plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

La tarea devuelta desde `CanOpenAsync` se completa de inmediato.

# <a name="iostabios"></a>[iOS](#tab/ios)

Si la aplicación de destino en este dispositivo nunca antes se abrió por `OpenAsync` desde la aplicación, iOS le pedirá una vez al usuario que permita que la aplicación la abra.

La tarea devuelta desde `CanOpenAsync` se completa de inmediato.

[Aquí](https://developer.xamarin.com/api/member/UIKit.UIApplication.CanOpenUrl/p/Foundation.NSUrl/) encontrará más información sobre la implementación de iOS.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No hay diferencias entre las plataformas.

-----

## <a name="api"></a>API

- [Código fuente de Launcher](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentación de API para Launcher](xref:Xamarin.Essentials.Launcher)
