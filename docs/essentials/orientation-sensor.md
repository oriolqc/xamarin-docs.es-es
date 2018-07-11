---
title: 'Xamarin.Essentials: OrientationSensor'
description: La clase OrientationSensor le permite supervisar la orientación de un dispositivo en un espacio tridimensional.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c01fa28e495eb3eceec62885060dce8f096c4086
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947395"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **OrientationSensor** clase le permite supervisar la orientación de un dispositivo en tres espacio dimensional.

> [!NOTE]
> Esta clase es para determinar la orientación de un dispositivo en el espacio 3D. Si necesita determinar si vídeo del dispositivo de la pantalla está en modo vertical u horizontal, use el `Orientation` propiedad de la `ScreenMetrics` objeto disponible desde el [ `DeviceDisplay` ](device-display.md) clase.

## <a name="using-orientationsensor"></a>Uso de OrientationSensor

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

El `OrientationSensor` está habilitada mediante una llamada a la `Start` método para supervisar los cambios de orientación del dispositivo y deshabilitado mediante una llamada a la `Stop` método. Los cambios se enviarán a través de la `ReadingChanged` eventos. Este es un ejemplo de uso:

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Orientation.X}, Y: {data.Orientation.Y}, Z: {data.Orientation.Z}, W: {data.Orientation.W}");
        // Process Orientation quaternion (X, Y, Z, and W)
    }

    public void ToggleOrientationSensor()
    {
        try
        {
            if (OrientationSensor.IsMonitoring)
                OrientationSensor.Stop();
            else
                OrientationSensor.Start(speed);
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

`OrientationSensor` las lecturas se notifican en forma de un [ `Quaternion` ](xref:System.Numerics.Quaternion) que describe la orientación del dispositivo en función de los dos sistemas de coordenadas 3D:

El dispositivo (normalmente un teléfono o tableta) tiene un sistema de coordenadas 3D con los ejes siguientes:

- El valor positivo X señala eje hacia la derecha de la pantalla en modo vertical.
- El eje Y positivo apunta a la parte superior del dispositivo en modo vertical.
- El eje Z positivo apunta fuera de la pantalla.

El sistema de coordenadas 3D de la tierra tiene los ejes siguientes:

- El valor positivo eje X tangente a la superficie de la tierra y puntos de este.
- El eje Y positivo también es tangente a la superficie de la tierra y puntos de Norte.
- El eje Z positivo es perpendicular a la superficie de la tierra y puntos de seguridad.

El `Quaternion` describe la rotación del sistema de coordenadas del dispositivo en relación con el sistema de coordenadas de la tierra.

Un `Quaternion` valor está muy estrechamente relacionada con rotación alrededor de un eje. Si un eje de rotación es el valor normalizado (un<sub>x</sub>, un<sub>y</sub>, un<sub>z</sub>), y el ángulo de giro es Θ, entonces la (X, Y, Z, W) los componentes del cuaternión son:

(un<sub>x</sub>·sin(Θ/2), un<sub>y</sub>·sin(Θ/2), un<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Estos son los sistemas de coordenadas derecho, por lo que con el control de posición de la mano derecha señala en la dirección positiva del eje de rotación, la curva de los dedos indican la dirección de rotación de ángulos positivos.

Ejemplos:

* Cuando se encuentra el dispositivo sin formato en una tabla con su pantalla hacia arriba, con la parte superior del dispositivo (en modo vertical), que señalan al norte, se alinean los dos sistemas de coordenadas. El `Quaternion` valor representa el cuaternión de identidad (0, 0, 0, 1). Todos los giros se pueden analizar con respecto a esta posición.

* Cuando se encuentra el dispositivo sin formato en una tabla con su pantalla hacia arriba y la parte superior del dispositivo (en modo vertical) que señala al oeste, el `Quaternion` valor es (0, 0, 0,707, 0,707). El dispositivo se ha girado 90 grados alrededor del eje Z de la tierra.

* Cuando el dispositivo se mantiene vertical para que la parte superior (en modo vertical) que señala hacia el cielo y Norte se enfrenta a la parte posterior del dispositivo, el dispositivo ha sido gira 90 grados alrededor del eje X. El `Quaternion` valor es (0,707, 0, 0, 0,707).

* Si se coloca el dispositivo para que su margen izquierdo se encuentra en una tabla y la parte superior, Norte señala el dispositivo se ha girado &ndash;90 grados alrededor del eje Y (o 90 grados alrededor del eje Y negativo). El `Quaternion` valor es (0,-0.707, 0, 0,707).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código fuente de OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentación de API OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
