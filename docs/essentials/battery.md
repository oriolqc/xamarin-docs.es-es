---
title: 'Xamarin.Essentials: batería'
description: Este documento describe la clase de la batería en Xamarin.Essentials, que le permite comprobar información sobre la batería del dispositivo y supervise los cambios realizados.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 35764b4c2270359a7c010e1186f882e236e17fd5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782091"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: batería

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **batería** clase le permite comprobar el dispositivo información sobre la batería y supervise los cambios realizados.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **batería** se requiere la siguiente configuración específica de plataforma de funcionalidad.

# <a name="androidtabandroid"></a>[Android](#tab/android)

El `Battery` permiso es necesario y debe configurarse en el proyecto de Android. Esto puede agregarse de las maneras siguientes:

Abra la **AssemblyInfo.cs** de archivos en el **propiedades** carpeta y agregar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Battery)]
```

O bien, actualice el manifiesto de Android:

Abra la **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo.

```xml
<uses-permission android:name="android.permission.BATTERY" />
```

O haga clic con el botón secundario en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto Android** buscar el **los permisos necesarios:** área y compruebe el **batería** permiso. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se necesita ninguna instalación adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se necesita ninguna instalación adicional.

-----

## <a name="using-battery"></a>Uso de la batería

Agregue una referencia a Xamarin.Essentials en la clase:

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
    case BatteryPowerSource.Ac:
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

Cada vez que cualquiera de las propiedades de la batería cambia se desencadena un evento:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>Diferencias entre las plataformas

| Plataforma | Diferencia |
| --- | --- |
| iOS | Dispositivo debe utilizarse para probar las API. |
| iOS | Solo devolverá corriente alterna o batería para PowerSource. |
| UWP | Solo devolverá corriente alterna o batería para PowerSource. |

## <a name="api"></a>API

- [Código fuente de batería](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentación de la API de batería](xref:Xamarin.Essentials.Battery)
