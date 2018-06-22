---
title: 'Xamarin.Essentials: Bloqueo de pantalla'
description: Este documento describe la clase ScreenLock en Xamarin.Essentials, que puede solicitar para impedir que la pantalla del modo de suspensión cuando se ejecuta la aplicación.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782913"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: Bloqueo de pantalla

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **ScreenLock** clase puede solicitar para impedir que la pantalla del modo de suspensión cuando se ejecuta la aplicación.

## <a name="using-screenlock"></a>Usar ScreenLock

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de bloqueo de pantalla funciona mediante una llamada a la `RequestActive` y `RequestRelease` métodos para solicitar la pantalla de desactivación.

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

- [Código de origen del bloqueo de pantalla](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentación de la API de bloqueo de pantalla](xref:Xamarin.Essentials.ScreenLock)
