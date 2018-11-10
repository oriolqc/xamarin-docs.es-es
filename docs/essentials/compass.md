---
title: 'Xamarin.Essentials: Brújula'
description: En este documento se describe la clase Compass de Xamarin.Essentials, que permite supervisar la dirección del norte magnético del dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 51812f9b4f88d77bf553a26ef3a6802239e338e0
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675502"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: Brújula

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Compass** permite supervisar la dirección del norte magnético del dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>Uso de Compass

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad Compass funciona mediante una llamada a los métodos `Start` y `Stop` para realizar escuchas de los cambios realizados en la brújula. Los cambios se enviarán a través del evento `ReadingChanged`. A continuación se muestra un ejemplo:

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

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android no proporciona una API para recuperar la dirección de la brújula. El acelerómetro y el magnetómetro se usan para calcular la dirección del norte magnético, lo que recomienda Google.

En casos poco habituales, es posible que vea resultados incoherentes porque sea necesario calibrar los sensores, lo que implica mover el dispositivo describiendo un ocho. La mejor manera de hacerlo es abrir Google Maps, pulsar en el punto de la ubicación y seleccionar **Calibrar la brújula**.

Tenga en cuenta que la ejecución simultánea de varios sensores desde la aplicación puede ajustar la velocidad del sensor.

## <a name="low-pass-filter"></a>Filtro de paso bajo

Debido a la forma de calcular y actualizar los valores de la brújula de Android, es posible que sea necesario suavizarlos. Se puede aplicar un _Filtro de paso bajo_ que calcula el promedio de los valores de seno y coseno de los ángulos, y se puede activar si se establece la propiedad `ApplyLowPassFilter` de la clase `Compass`:

```csharp
Compass.ApplyLowPassFilter = true;
```

Esto solo se aplica a la plataforma Android. Puede leer más información [aquí](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Código fuente de Compass](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentación de API para Compass](xref:Xamarin.Essentials.Compass)
