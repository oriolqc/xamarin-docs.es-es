---
title: 'Xamarin.Essentials: Barometer'
description: La clase Barometer de Xamarin.Essentials permite supervisar el sensor del barómetro del dispositivo, que mide la presión.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 08/16/2018
ms.openlocfilehash: 0217b89bc3f45347acecbdb3b8cdccfeed751744
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130306"
---
# <a name="xamarinessentials-barometer"></a>Xamarin.Essentials: Barometer

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Barometer** permite supervisar el sensor del barómetro del dispositivo, que mide la presión.

## <a name="using-barometer"></a>Uso de Barometer

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad Barometer funciona mediante una llamada a los métodos `Start` y `Stop` para escuchar los cambios en la lectura de presión del barómetro en kilopascales. Los cambios se enviarán a través del evento `ReadingChanged`. A continuación le mostramos un ejemplo de uso:

```csharp

public class BarometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public BarometerTest()
    {
        // Register for reading changes.
        Barometer.ReadingChanged += Barometer_ReadingChanged;
    }

    void Barometer_ReadingChanged(object sender, BarometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Pressure
        Console.WriteLine($"Reading: Pressure: {data.Pressure} kilopascals");
    }

    public void ToggleBarometer()
    {
        try
        {
            if (Barometer.IsMonitoring)
              Barometer.Stop();
            else
              Barometer.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Sin detalles de implementación específicos para la plataforma.

# <a name="iostabios"></a>[iOS](#tab/ios)

Esta API usa [CMAltimeter](https://developer.apple.com/documentation/coremotion/cmaltimeter#//apple_ref/occ/cl/CMAltimeter) para supervisar los cambios de presión, una característica de hardware que se agregó en iPhone 6 y dispositivos más nuevos. Se producirá una excepción `FeatureNotSupportedException` en los dispositivos que no admiten el altímetro.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Sin detalles de implementación específicos para la plataforma.

-----

## <a name="api"></a>API

- [Código fuente de Barometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Barometer)
- [Documentación de API para Barometer](xref:Xamarin.Essentials.Barometer)
