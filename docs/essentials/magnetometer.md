---
title: 'Xamarin.Essentials: Magnetómetro'
description: La clase Magnetómetro en Xamarin.Essentials le permite supervisar el sensor de magnetómetro del dispositivo, lo que indica la orientación del dispositivo en relación con el campo magnético de la tierra.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 52790f78c2d78347a35f111b3c4db63900c24ec7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947366"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: Magnetómetro

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Magnetómetro** clase le permite supervisar el sensor de magnetómetro del dispositivo que indica la orientación del dispositivo en relación con el campo magnético de la tierra.

## <a name="using-magnetometer"></a>Uso de Magnetómetro

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de Magnetómetro funciona mediante una llamada a la `Start` y `Stop` métodos para realizar escuchas de los cambios realizados en el magnetómetro. Los cambios se enviarán a través de la `ReadingChanged` eventos. Este es el ejemplo de uso:

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometerr_ReadingChanged(MagnetometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process MagneticField X, Y, and Z
        Console.WriteLine($"Reading: X: {data.MagneticField.X}, Y: {data.MagneticField.Y}, Z: {data.MagneticField.Z}");
    }

    public void ToggleMagnetometer()
    {
        try
        {
            if (Magnetometer.IsMonitoring)
              Magnetometer.Stop();
            else
              Magnetometer.Start(speed);
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

Todos los datos se devuelven en microteslas.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código fuente de magnetómetro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [Documentación de API magnetómetro](xref:Xamarin.Essentials.Magnetometer)
