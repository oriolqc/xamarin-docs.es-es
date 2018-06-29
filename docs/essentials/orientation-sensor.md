---
title: 'Xamarin.Essentials: OrientationSensor'
description: La clase OrientationSensor le permite supervisar la orientación de un dispositivo en un espacio tridimensional.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c7bbc849e5fa0b901f5b54e77d548b28bc2a72c6
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080556"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **OrientationSensor** clase le permite supervisar la orientación de un dispositivo en tres espacio dimensiones.

> [!NOTE]
> Esta clase es para determinar la orientación de un dispositivo en un espacio 3D. Si necesita determinar si vídeo del dispositivo de la pantalla está en modo vertical u horizontal, use la `Orientation` propiedad de la `ScreenMetrics` objeto disponible desde el [ `DeviceDisplay` ](device-display.md) clase.

## <a name="using-orientationsensor"></a>Usar OrientationSensor

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

El `OrientationSensor` está habilitada mediante una llamada a la `Start` método para supervisar los cambios en la orientación del dispositivo y deshabilitado mediante una llamada a la `Stop` método. Los cambios se envían a través de la `ReadingChanged` eventos. Este es un ejemplo de uso:

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

El dispositivo (normalmente un teléfono o una tableta) tiene un sistema de coordenadas 3D con los ejes siguientes:

- El valor positivo X puntos de eje a la derecha de la pantalla en modo vertical.
- El eje Y positivo señala a la parte superior del dispositivo en modo vertical.
- El eje Z positivo apunta fuera de la pantalla.

El sistema de coordenadas 3D de la tierra tiene los ejes siguientes:

- El valor positivo eje X es tangente a la superficie de la tierra y puntos de este.
- El eje Y positivo también es tangente a la superficie de la tierra y puntos Norte.
- El eje Z positivo es perpendicular a la superficie de la tierra y señala hacia arriba.

El `Quaternion` describe la rotación del dispositivo sistema de coordenadas en relación con el sistema de coordenadas de la tierra.

Un `Quaternion` valor está muy estrechamente relacionado con rotación alrededor de un eje. Si un eje de rotación es el valor normalizado (un<sub>x</sub>,<sub>y</sub>,<sub>z</sub>), y el ángulo de giro es Θ, a continuación, la (X, Y, Z, W) componentes del cuaternión son:

(un<sub>x</sub>·sin(Θ/2), un<sub>y</sub>·sin(Θ/2), un<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Se trata de sistemas de coordenadas derecho, por lo que con el control de la derecha que apunta en la dirección del eje de rotación positiva, la curva de los dedos indicar la dirección de rotación de los ángulos positivos.

Ejemplos:

* Cuando el dispositivo se encuentra sobre una tabla con su pantalla hacia arriba, con la parte superior del dispositivo (en modo vertical) que señalan al norte, se alinean los dos sistemas de coordenadas. El `Quaternion` valor representa el cuaternión de identidad (0, 0, 0, 1). Todas las rotaciones se pueden analizar con respecto a esta posición.

* Cuando el dispositivo se encuentra sobre una tabla con su pantalla hacia arriba y la parte superior del dispositivo (en modo vertical) que señala al oeste, la `Quaternion` valor es (0, 0, 0,707, 0,707). El dispositivo se ha girado 90 grados alrededor del eje Z de la tierra.

* Cuando el dispositivo se mantiene en vertical para que la parte superior (en modo vertical) señala hacia el cielo y Norte se enfrenta a la parte posterior del dispositivo, el dispositivo ha sido gira 90 grados alrededor del eje X. El `Quaternion` valor es (0,707, 0, 0, 0,707).

* Si se coloca el dispositivo para que su borde izquierdo está en una tabla y la parte superior Norte, señala el dispositivo se ha girado &ndash;90 grados alrededor del eje Y (o 90 grados alrededor del eje Y negativo). El `Quaternion` valor es (0,-0.707, 0, 0,707).

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Velocidad de sensor](xref:Xamarin.Essentials.SensorSpeed)

- **Más rápido** : obtener los datos del sensor de mayor velocidad posible (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Juego** : frecuencia adecuada para juegos (no se garantiza para devolver en el subproceso de interfaz de usuario).
- **Normal** : velocidad predeterminada adecuado para los cambios de orientación de pantalla.
- **Interfaz de usuario** : velocidad apropiada para la interfaz de usuario general.

Si no se garantiza que el controlador de eventos para ejecutar en el subproceso de interfaz de usuario y si el controlador de eventos necesita tener acceso a elementos de interfaz de usuario, use la [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) método para ejecutar ese código en el subproceso de interfaz de usuario.

## <a name="api"></a>API

- [Código fuente de OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentación de la API de OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
