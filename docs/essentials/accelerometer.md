---
title: 'Xamarin.Essentials: acelerómetro'
description: La clase acelerómetro en Xamarin.Essentials le permite supervisar el sensor de acelerómetro del dispositivo, lo que indica la aceleración del dispositivo en tres espacio dimensiones.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 8229a372659e7918457a9d2f358b871e1a3f5978
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080551"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: acelerómetro

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **acelerómetro** clase le permite supervisar el sensor de acelerómetro del dispositivo que indica la aceleración del dispositivo en tres espacio dimensiones.

## <a name="using-accelerometer"></a>Usar acelerómetro

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de acelerómetro funciona mediante una llamada a la `Start` y `Stop` métodos para realizar escuchas para los cambios en la aceleración. Los cambios se envían a través de la `ReadingChanged` eventos. Este es el ejemplo de uso:

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(AccelerometerChangedEventArgs e)
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

Lecturas de acelerómetro se notifican en G. Una G es una unidad de gravitation forzar igual que ejercida por campo gravitatorio de la tierra (9.81 m/s ^ 2).

El sistema de coordenadas se define con respecto a la pantalla del teléfono en su orientación predeterminada. No se intercambian los ejes cuando cambia la orientación de la pantalla del dispositivo.

El eje X es horizontal y apunta a la derecha, el eje Y es vertical y apunta hacia arriba y el eje Z señala hacia el exterior de la cara frontal de la pantalla. En este sistema, coordenadas detrás de la pantalla tienen los valores Z negativos.

Ejemplos:

* Cuando el dispositivo se encuentra sin formato en una tabla y se inserta en su lado izquierdo hacia la derecha, el valor de la aceleración de x es positivo.

* Cuando el dispositivo se encuentra sobre una tabla, el valor de aceleración es +1.00 G o (+ 9.81 m/s ^ 2), que corresponden a la aceleración del dispositivo (0 m/s ^ 2) menos la fuerza de la gravedad (-9.81 m/s ^ 2) y normalizado como en G.

* Cuando el dispositivo se encuentra sin formato en una tabla y se inserta hacia el cielo con una aceleración de un m/s ^ 2, el valor de aceleración es igual A + 9.81 que corresponden a la aceleración del dispositivo (+ m/s ^ 2) menos la fuerza de la gravedad (-9.81 m/s ^ 2) y normalizado en G. 

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Velocidad de sensor](xref:Xamarin.Essentials.SensorSpeed)

- **Más rápido** : obtener los datos del sensor de mayor velocidad posible (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Juego** : frecuencia adecuada para juegos (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Normal** : velocidad predeterminada adecuado para los cambios de orientación de pantalla.
- **Interfaz de usuario** : velocidad apropiada para la interfaz de usuario general.

Si no se garantiza que el controlador de eventos para ejecutar en el subproceso de interfaz de usuario y si el controlador de eventos necesita tener acceso a elementos de interfaz de usuario, use la [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) método para ejecutar ese código en el subproceso de interfaz de usuario.

## <a name="api"></a>API

- [Código fuente de acelerómetro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentación de acelerómetro API](xref:Xamarin.Essentials.Accelerometer)
