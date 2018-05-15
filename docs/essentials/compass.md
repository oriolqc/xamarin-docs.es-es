---
title: Xamarin.Essentials brújula
description: La clase brújula le permite supervisar el encabezado de Norte magnético del dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 39f141424ddd247458b9c8b35ae02ab29e2c2206
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials brújula

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

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android no proporciona una API para recuperar el encabezado brújula. Usamos el acelerómetro y magnetómetro para calcular el encabezado Norte magnético, lo que se recomienda por Google. 

En raras ocasiones, quizá verá resultados incoherentes porque los sensores deben ser calibrado, lo que implica mover el dispositivo en un movimiento de la figura 8. La mejor manera de hacerlo esto consiste en Abrir Google Maps, puntee en el punto de la ubicación y seleccionar **calibrar brújula**.

Tenga en cuenta que ejecuta varios sensores desde la aplicación al mismo tiempo puede ajustar la velocidad del sensor.

--------------

## <a name="api"></a>API

- [Código fuente brújula](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentación de API brújula](xref:Xamarin.Essentials.Compass)
