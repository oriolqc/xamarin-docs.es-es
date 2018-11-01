---
title: 'Xamarin.Essentials: Accelerometer'
description: La clase Accelerometer en Xamarin.Essentials le permite supervisar el sensor de acelerómetro del dispositivo, que indica la aceleración del dispositivo en un espacio tridimensional.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 70f41e6242b62577b6aa42cc207802ead020499e
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674780"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: Accelerometer

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Accelerometer** permite supervisar el sensor del acelerómetro del dispositivo, que indica la aceleración del dispositivo en un espacio tridimensional.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-accelerometer"></a>Uso de Accelerometer

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad Accelerometer funciona mediante una llamada a los métodos `Start` y `Stop` para realizar escuchas de los cambios realizados en la aceleración. Los cambios se enviarán a través del evento `ReadingChanged`. A continuación le mostramos un ejemplo de uso:

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

    public void ToggleAccelerometer()
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

Las lecturas del acelerómetro se notifican en G. La G es una unidad de fuerza gravitacional igual a la ejercida por el campo gravitatorio de la Tierra (9,81 m/s^2).

El sistema de coordenadas se define con respecto a la pantalla del teléfono en orientación predeterminada. Cuando cambia la orientación de pantalla del dispositivo no se intercambian los ejes.

El eje X es horizontal y apunta a la derecha, el eje Y es vertical y apunta hacia arriba, y el eje Z apunta hacia el exterior de la parte frontal de la pantalla. En este sistema, las coordenadas de detrás de la pantalla tienen valores de Z negativo.

Ejemplos:

- Cuando el dispositivo se encuentra plano sobre una mesa y se mueve desde el lado izquierdo hacia la derecha, el valor de aceleración de X es positivo.

- Cuando el dispositivo se encuentra plano sobre una mesa, el valor de aceleración es + 1,00 G o (+ 9,81 m/s^2), que corresponden a la aceleración del dispositivo (0 m/s^2) menos la fuerza de la gravedad (- 9,81 m/s^2) y normalizada en G.

- Cuando el dispositivo se encuentra plano sobre una mesa y se mueve hacia el cielo con una aceleración de A m/s^2, el valor de la aceleración es igual A + 9,81, que corresponde a la aceleración del dispositivo (+ A m/s^2) menos la fuerza de la gravedad (- 9,81 m/s^2) y normalizada en G.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código fuente de Accelerometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentación de API de Accelerometer](xref:Xamarin.Essentials.Accelerometer)
