---
title: 'Xamarin.Essentials: brújula'
description: Este documento describe la clase brújula en Xamarin.Essentials, que le permite supervisar el encabezado de Norte magnético del dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 63818014a9b3bdbef479055cbbcfbf8d348080fc
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080555"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: brújula

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **brújula** clase le permite supervisar el encabezado de Norte magnético del dispositivo.

## <a name="using-compass"></a>Uso de brújula

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de brújula funciona mediante una llamada a la `Start` y `Stop` métodos para realizar escuchas para los cambios en la brújula. Los cambios se envían a través de la `ReadingChanged` eventos. A continuación se muestra un ejemplo:

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(CompassChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: {data.HeadingMagneticNorth} degrees");
        // Process Heading Magnetic North
    }

    public void ToggleCompass()
    {
        try
        {
            if (Compass.IsMonitoring)
              Compass.Stop();
            else
              Compass.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Some other exception has occured
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

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android no proporciona una API para recuperar el encabezado brújula. Usamos el acelerómetro y magnetómetro para calcular el encabezado Norte magnético, lo que se recomienda por Google. 

En raras ocasiones, quizá verá resultados incoherentes porque los sensores deben ser calibrado, lo que implica mover el dispositivo en un movimiento de la figura 8. La mejor manera de hacerlo esto consiste en Abrir Google Maps, puntee en el punto de la ubicación y seleccionar **calibrar brújula**.

Tenga en cuenta que ejecuta varios sensores desde la aplicación al mismo tiempo puede ajustar la velocidad del sensor.

--------------

## <a name="api"></a>API

- [Código fuente brújula](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentación de API brújula](xref:Xamarin.Essentials.Compass)
