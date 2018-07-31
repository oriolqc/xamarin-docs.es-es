---
title: 'Xamarin.Essentials: giroscopio'
description: La clase giroscopio en Xamarin.Essentials le permite supervisar el sensor de giroscopio del dispositivo, que mide la rotación alrededor de tres ejes principal del dispositivo.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f1e1199ae32158889ec569eb5f7e9742f37d45d4
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353631"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: giroscopio

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **giroscopio** clase le permite supervisar el sensor de giroscopio del dispositivo que es la rotación alrededor de tres ejes principal del dispositivo.

## <a name="using-gyroscope"></a>Uso de giroscopio

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de giroscopio funciona mediante una llamada a la `Start` y `Stop` métodos para realizar escuchas de los cambios realizados en el giroscopio. Los cambios se enviarán a través de la `ReadingChanged` eventos. Este es el ejemplo de uso:

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(object sender, GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z
        Console.WriteLine($"Reading: X: {data.AngularVelocity.X}, Y: {data.AngularVelocity.Y}, Z: {data.AngularVelocity.Z}");
    }

    public void ToggleGyroscope()
    {
        try
        {
            if (Gyroscope.IsMonitoring)
              Gyroscope.Stop();
            else
              Gyroscope.Start(speed);
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

## <a name="api"></a>API

- [Código fuente de giroscopio](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Documentación de API giroscopio](xref:Xamarin.Essentials.Gyroscope)
