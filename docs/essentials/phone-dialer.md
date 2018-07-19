---
title: 'Xamarin.Essentials: Marcador de teléfono'
description: La clase PhoneDialer en Xamarin.Essentials permite que una aplicación abrir un número de teléfono en el marcador
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 34a6c80836d8cb42b1f8fd95718fe248d4701c0f
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130798"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Marcador de teléfono

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **PhoneDialer** clase permite que una aplicación abrir un número de teléfono en el marcador.

## <a name="using-phone-dialer"></a>Mediante el marcador de teléfono

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de marcador telefónico funciona mediante una llamada a la `Open` método con un número de teléfono para abrir el marcador con. Cuando `Open` se solicita la API intentarán volver a dar formato al número basándose en el código de país, si se especifica.

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

- [Código fuente de marcador de teléfono](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentación de la API de marcador de teléfono](xref:Xamarin.Essentials.PhoneDialer)
