---
title: Xamarin.Essentials telefónico
description: La clase PhoneDialer permite a una aplicación abrir un vínculo web en el explorador preferida de sistema optimizado o un explorador externo.
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6bea1b26e43459be12272f9dd2ecc6fafba2b8b4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials telefónico

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **PhoneDialer** clase permite a una aplicación abrir un vínculo web en el explorador preferida de sistema optimizado o un explorador externo.

## <a name="using-phone-dialer"></a>Mediante el marcador de teléfono

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de telefónico funciona mediante una llamada a la `Open` método con un número de teléfono para abrir el marcador con. Cuando `Open` se solicita la API intentarán volver a dar formato al número basándose en el código de país, si se especifica.

```csharp
public class PhoneDialerTest
{
    public async Task PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Código fuente de marcador de teléfono](https://github.com/xamarin/Essentials/tree/master/Essentials/PhoneDialer)
- [Documentación de la API de marcador de teléfono](xref:Xamarin.Essentials.PhoneDialer)
