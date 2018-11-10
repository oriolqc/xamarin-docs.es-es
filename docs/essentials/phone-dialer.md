---
title: 'Xamarin.Essentials: Marcador telefónico'
description: La clase PhoneDialer de Xamarin.Essentials permite que una aplicación abra un número de teléfono en el marcador telefónico.
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: bccc76e1602c475a5e4cc9a95d498d11f9a379b1
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675424"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Marcador telefónico

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **PhoneDialer** permite que una aplicación abra un número de teléfono en el marcador telefónico.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>Uso del marcador telefónico

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de marcador telefónico funciona mediante una llamada al método `Open` con un número de teléfono con el que abrir el marcador. Cuando se solicita `Open`, la API intentará automáticamente dar formato al número en función del código de país, si se especifica.

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

- [Código fuente del marcador telefónico](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentación de API para PhoneDialer](xref:Xamarin.Essentials.PhoneDialer)
