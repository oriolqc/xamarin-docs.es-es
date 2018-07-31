---
title: 'Xamarin.Essentials: Compass'
description: Este documento describe la clase Compass en Xamarin.Essentials, que le permite supervisar el encabezado de Norte magnético del dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c3fe98c384a87bdc08ce94e7537d1a6343767561
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353888"
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
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
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
            // Some other exception has occurred
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

## <a name="low-pass-filter"></a>Filtro de paso bajo

Debido a cómo la brújula Android están actualizados y se calcula que es posible que sea necesario para suavizar los valores de los valores. Un _baja pasar filtro_ pueden aplicarse que calcula el promedio de los valores de seno y coseno de los ángulos y puede activarse estableciendo el `ApplyLowPassFilter` propiedad en el `Compass` clase:

```csharp
Compass.ApplyLowPassFilter = true;
```

Esto se aplica solo a la plataforma Android. Puede leer más información [aquí](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Código fuente de la brújula](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentación de API de brújula](xref:Xamarin.Essentials.Compass)
