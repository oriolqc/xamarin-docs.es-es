---
title: 'Xamarin.Essentials: Bloquear pantalla'
description: En este documento se describe la clase ScreenLock de Xamarin.Essentials, que puede solicitar que la pantalla no quede suspendida cuando se ejecuta la aplicación.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3bf8c949650cf9f039a5a516366a90e717dc944b
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675320"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: Bloquear pantalla

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **ScreenLock** puede solicitar que la pantalla no quede suspendida cuando se ejecuta la aplicación.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenlock"></a>Uso de ScreenLock

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para usar la funcionalidad de bloqueo de pantalla se llama a los métodos `RequestActive` y `RequestRelease` para solicitar que la pantalla no se desactive.

```csharp
public class ScreenLockTest
{
    public void ToggleScreenLock()
    {
        if (!ScreenLock.IsActive)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [Código fuente de ScreenLock](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentación de API para ScreenLock](xref:Xamarin.Essentials.ScreenLock)
