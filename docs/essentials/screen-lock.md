---
title: Bloqueo de pantalla de Xamarin.Essentials
description: La clase ScreenLock puede solicitar para impedir que la pantalla del modo de suspensión cuando se ejecuta la aplicación.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7175362dcb7f85746ea85447936d7fe3e2fd026b
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-screen-lock"></a>Bloqueo de pantalla de Xamarin.Essentials

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
        if (ScreenLock.IsMonitoring)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease;
    }
}
```

## <a name="api"></a>API

- [Código de origen del bloqueo de pantalla](https://github.com/xamarin/Essentials/tree/master/Essentials/ScreenLock)
- [Documentación de la API de bloqueo de pantalla](xref:Xamarin.Essentials.ScreenLock)
