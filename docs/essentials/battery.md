---
title: 'Xamarin.Essentials: la batería'
description: Este documento describe la clase de la batería en Xamarin.Essentials, lo que le permite comprobar información sobre la batería del dispositivo y supervise los cambios realizados.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1ed0ef5e013967545e739733c887702325f60c3f
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855060"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: la batería

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **batería** clase le permite comprobar el dispositivo de la información de la batería y supervise los cambios realizados.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **batería** se requiere la funcionalidad de la siguiente configuración específica de plataforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

El `Battery` permiso es necesario y debe configurarse en el proyecto de Android. Esto se puede agregar de las maneras siguientes:

Abra el **AssemblyInfo.cs** archivo bajo el **propiedades** carpeta y agregue:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Battery)]
```

O actualice el manifiesto de Android:

Abra el **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo.

```xml
<uses-permission android:name="android.permission.BATTERY" />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto de Android** encontrar el **los permisos necesarios:** área y compruebe el **batería** permiso. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se requiere ninguna configuración adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se requiere ninguna configuración adicional.

-----

## <a name="using-battery"></a>Uso de la batería

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

Cada vez que cambie cualquiera de las propiedades de la batería se desencadena un evento:

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

No hay diferencias entre las plataformas.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Dispositivo debe usarse para probar las API. 
* Solo devolverá `Ac` o `Battery` para `PowerSource`. 
* No se puede cancelar la vibración.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* Solo devolverá `Ac` o `Battery` para `PowerSource`. 

-----

## <a name="api"></a>API

- [Código fuente de la batería](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentación de la API de la batería](xref:Xamarin.Essentials.Battery)
