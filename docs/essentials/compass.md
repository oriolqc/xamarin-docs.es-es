---
title: 'Xamarin.Essentials: Compass'
description: Este documento describe la clase Compass en Xamarin.Essentials, que le permite supervisar el encabezado de Norte magnético del dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: cf41948c55c742140896bfb48d9bb4abf25c8d68
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947418"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: Compass

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **brújula** clase le permite supervisar el encabezado de Norte magnético del dispositivo.

## <a name="using-compass"></a>Uso de la brújula

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de la brújula funciona mediante una llamada a la `Start` y `Stop` métodos para realizar escuchas de los cambios realizados en la brújula. Los cambios se enviarán a través de la `ReadingChanged` eventos. A continuación se muestra un ejemplo:

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

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android no proporciona una API para recuperar el encabezado de brújula. Usamos el acelerómetro y magnetómetro para calcular la partida el norte magnético, se recomienda por Google. 

En raras ocasiones, quizás verá resultados incoherentes porque los sensores necesitan calibrarse, lo que implica mover el dispositivo en un movimiento de la figura 8. La mejor manera de hacerlo es abrir Google Maps, pulsa en el punto de su ubicación y seleccione **calibrar brújula**.

Tenga en cuenta que ejecuta varios sensores desde la aplicación al mismo tiempo puede ajustar la velocidad del sensor.

--------------

## <a name="api"></a>API

- [Código fuente de la brújula](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentación de API de brújula](xref:Xamarin.Essentials.Compass)
