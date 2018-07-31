---
title: 'Xamarin.Essentials: acelerómetro'
description: La clase Accelerometer en Xamarin.Essentials le permite supervisar el sensor de acelerómetro del dispositivo, lo que indica la aceleración del dispositivo en tres espacio dimensional.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b5a24e214eb129b4d53b94586632791c8827447b
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353846"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: acelerómetro

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **acelerómetro** clase le permite supervisar el sensor de acelerómetro del dispositivo que indica la aceleración del dispositivo en tres espacio dimensional.

## <a name="using-accelerometer"></a>Usando el acelerómetro

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de acelerómetro funciona mediante una llamada a la `Start` y `Stop` métodos para realizar escuchas de los cambios realizados en la aceleración. Los cambios se enviarán a través de la `ReadingChanged` eventos. Este es el ejemplo de uso:

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(object sender, AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAcceleromter()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
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

Lecturas de acelerómetro se notifican en G. Una G es una unidad de y forzar igual que ejercida por campo de la tierra gravitatorio (9,81 m/s ^ 2).

El sistema de coordenadas se define con respecto a la pantalla del teléfono en su orientación predeterminada. No se intercambian los ejes cuando cambia la orientación de pantalla del dispositivo.

El eje X es horizontal y apunta a la derecha, el eje Y es vertical y que señala hacia arriba y el eje Z apunta hacia el exterior de la parte frontal de la pantalla. En este sistema, las coordenadas detrás de la pantalla tienen valores de Z negativo.

Ejemplos:

* Cuando el dispositivo se encuentra plana en una tabla y se inserta en su lado izquierdo hacia la derecha, el valor de x acceleration es positivo.

* Cuando se encuentra el dispositivo sin formato en una tabla, el valor de la aceleración es G + 1.00 o (+ m/s 9,81 ^ 2), que corresponden a la aceleración del dispositivo (0 m/s ^ 2) menos la fuerza de la gravedad (-m/s 9,81 ^ 2) y normalizada, como se muestra en G.

* Cuando el dispositivo se encuentra plana en una tabla y se desplazados hacia el cielo con una aceleración de m/s ^ 2, el valor de la aceleración es igual A + 9.81 que corresponden a la aceleración del dispositivo (+ m/s ^ 2) menos la fuerza de la gravedad (-m/s 9,81 ^ 2) y normalizada en G.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código fuente de acelerómetro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentación de API de acelerómetro](xref:Xamarin.Essentials.Accelerometer)
