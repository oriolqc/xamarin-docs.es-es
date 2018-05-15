---
title: Bloqueo de pantalla de Xamarin.Essentials
description: La clase ScreenLock puede solicitar para impedir que la pantalla del modo de suspensión cuando se ejecuta la aplicación.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f7e6a3d6933ed1fce7522fdbb8102e5100bd1589
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
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
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [Código de origen del bloqueo de pantalla](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentación de la API de bloqueo de pantalla](xref:Xamarin.Essentials.ScreenLock)
