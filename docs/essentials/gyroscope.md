---
title: 'Xamarin.Essentials: giroscopio'
description: La clase giroscopio en Xamarin.Essentials le permite supervisar el sensor de giroscopio del dispositivo, que mide la rotación alrededor de tres ejes principal del dispositivo.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6b2df3b6c5061464a06730ad09cbbbfdbceeb247
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080572"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: giroscopio

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **giroscopio** clase le permite supervisar el sensor de giroscopio del dispositivo que es la rotación alrededor de tres ejes principal del dispositivo.

## <a name="using-gyroscope"></a>Usar giroscopio

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de giroscopio funciona mediante una llamada a la `Start` y `Stop` métodos para realizar escuchas para los cambios en el giroscopio. Los cambios se envían a través de la `ReadingChanged` eventos. Este es el ejemplo de uso:

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(GyroscopeChangedEventArgs e)
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

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Velocidad de sensor](xref:Xamarin.Essentials.SensorSpeed)

- **Más rápido** : obtener los datos del sensor de mayor velocidad posible (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Juego** : frecuencia adecuada para juegos (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Normal** : velocidad predeterminada adecuado para los cambios de orientación de pantalla.
- **Interfaz de usuario** : velocidad apropiada para la interfaz de usuario general.

Si no se garantiza que el controlador de eventos para ejecutar en el subproceso de interfaz de usuario y si el controlador de eventos necesita tener acceso a elementos de interfaz de usuario, use la [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) método para ejecutar ese código en el subproceso de interfaz de usuario.

## <a name="api"></a>API

- [Código fuente de giroscopio](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Documentación de giroscopio API](xref:Xamarin.Essentials.Gyroscope)
