---
title: 'Xamarin.Essentials: detección de agitaciones'
description: La clase Accelerometer de Xamarin.Essentials permite detectar movimientos de agitación en el dispositivo.
ms.assetid: 07513D32-120F-4F12-8757-A47802A8027B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 2a14d739806fef353472a5186a9dbedd8e218662
ms.sourcegitcommit: be51b459a0a148ae3adca31d7599f53f7b2c3a68
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2019
ms.locfileid: "59019313"
---
# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials: detección de agitaciones

La clase **[Accelerometer](accelerometer.md)** permite supervisar el sensor del acelerómetro del dispositivo, que indica la aceleración del dispositivo en un espacio tridimensional. Además, le permite registrar eventos que se realizarán cuando el usuario agite el dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>Uso de la detección de agitaciones

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Para detectar la agitación del dispositivo, debe usar la funcionalidad Accelerometer mediante una llamada a los métodos `Start` y `Stop` para realizar cambios en la aceleración y para detectar movimientos de agitación. Siempre que se detecte agitación, se desencadenará un evento `ShakeDetected `. Se recomienda usar `Game` o faster para `SensorSpeed`. A continuación le mostramos un ejemplo de uso:

```csharp

public class DetectShakeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Game;

    public DetectShakeTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ShakeDetected  += Accelerometer_ShakeDetected ;
    }

    void Accelerometer_ShakeDetected (object sender, EventArgs e)
    {
        // Process shake event
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

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="implementation-details"></a>Detalles de implementación

La API para la detección de agitaciones usa lecturas sin procesar del acelerómetro para calcular la aceleración. Utiliza un mecanismo de cola simple para detectar si tres cuartos de los eventos recientes del acelerómetro se han producido durante el último medio segundo. La aceleración se calcula añadiendo el cuadrado de las lecturas X, Y y Z del acelerómetro y comparándolo con un umbral determinado.

## <a name="api"></a>API

- [Código fuente de Accelerometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentación de API para Accelerometer](xref:Xamarin.Essentials.Accelerometer)
