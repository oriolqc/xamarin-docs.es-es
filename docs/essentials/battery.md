---
title: 'Xamarin.Essentials: Batería'
description: En este documento se describe la clase Battery de Xamarin.Essentials, que permite comprobar la información de la batería del dispositivo y supervisar los cambios.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6a14c939064538a405a1fe64061e0bb2e903fedd
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675437"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: Batería

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Battery** permite comprobar la información de la batería del dispositivo y supervisar los cambios.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la funcionalidad de **Battery**, se requiere la siguiente configuración específica para la plataforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

El permiso `Battery` es necesario y se debe configurar en el proyecto Android. Se puede agregar de las siguientes maneras:

Abra el archivo **AssemblyInfo.cs** de la carpeta **Propiedades** y agregue lo siguiente:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

O BIEN, actualice el manifiesto de Android:

Abra el archivo **AndroidManifest.xml** de la carpeta **Propiedades** y agregue lo siguiente dentro del nodo **manifest**.

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **Manifiesto de Android**, busque el área **Permisos requeridos:** y active el permiso **Battery** (Batería). Esto actualizará automáticamente el archivo **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se requiere configuración adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se requiere configuración adicional.

-----

## <a name="using-battery"></a>Uso de Battery

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Compruebe la información actual de la batería:

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or -1.0 if unable to determine.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

Cada vez que se cambia cualquiera de las propiedades de la batería, se desencadena un evento:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(object sender, BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>Diferencias entre las plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

No hay diferencias entre las plataformas.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Se debe usar el dispositivo para probar las API. 
* Solo devolverá `AC` o `Battery` para `PowerSource`.
* No se puede cancelar la vibración.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* Solo devolverá `AC` o `Battery` para `PowerSource`.

-----

## <a name="api"></a>API

- [Código fuente de Battery](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentación de API para Battery](xref:Xamarin.Essentials.Battery)
