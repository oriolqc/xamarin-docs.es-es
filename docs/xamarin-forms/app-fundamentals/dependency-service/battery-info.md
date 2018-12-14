---
title: Comprobar el estado de la batería
description: En este artículo, se explica cómo usar la clase DependencyService de Xamarin.Forms para acceder a información de la batería de forma nativa para cada plataforma.
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: cbb4a01ac2c6d933fe40a0b3c2571d1fe3ce75c0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998407"
---
# <a name="checking-battery-status"></a>Comprobar el estado de la batería

En este artículo, se explica cómo crear una aplicación que comprueba el estado de la batería. Este artículo se basa en la aplicación Battery Plugin desarrollada por James Montemagno. Para obtener más información, vea el [repositorio de GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery).

Como en Xamarin.Forms no se incluyen funciones para comprobar el estado actual de la batería, esta aplicación tendrá que usar [`DependencyService`](xref:Xamarin.Forms.DependencyService) para aprovechar las API nativas.  En este artículo, se explican los pasos siguientes con el fin de usar `DependencyService`:

- **[Crear la interfaz](#Creating_the_Interface)**: obtenga información sobre cómo se crea la interfaz en código compartido.
- **[Implementación de iOS](#iOS_Implementation)**: obtenga información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**: obtenga información sobre cómo implementar la interfaz en código nativo para Android.
- **[Implementación de UWP](#UWPImplementation)**: obtenga información sobre cómo implementar la interfaz en código nativo para la Plataforma universal de Windows (UWP).
- **[Implementación en código compartido](#Implementing_in_Shared_Code)**: obtenga información sobre cómo usar `DependencyService` para llamar a la implementación nativa desde código compartido.

Una vez completada, la aplicación `DependencyService` que use tendrá la estructura siguiente:

![](battery-info-images/battery-diagram.png "Estructura de la aplicación de DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

Primero, cree una interfaz en el código compartido que exprese la función deseada. En el caso de una aplicación que comprueba el estado de la batería, la información relevante es el porcentaje de la batería restante (independientemente de si el dispositivo está cargándose o no) y cómo recibe energía el dispositivo:

```csharp
namespace DependencyServiceSample
{
  public enum BatteryStatus
  {
    Charging,
    Discharging,
    Full,
    NotCharging,
    Unknown
  }

  public enum PowerSource
  {
    Battery,
    Ac,
    Usb,
    Wireless,
    Other
  }

  public interface IBattery
  {
    int RemainingChargePercent { get; }
    BatteryStatus Status { get; }
    PowerSource PowerSource { get; }
  }
}
```

Al codificar para esta interfaz en el código compartido, se permitirá que la aplicación de Xamarin.Forms acceda a las API de administración de energía en cada plataforma.

> [!NOTE]
> Las clases que implementen la interfaz necesitan tener un constructor sin parámetros para funcionar con el elemento `DependencyService`. Los constructores no se pueden definir mediante interfaces.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

La interfaz `IBattery` tiene que implementarse en cada proyecto de aplicación específico de la plataforma. La implementación de iOS usará las API de [`UIDevice`](https://developer.xamarin.com/api/type/UIKit.UIDevice/) nativas para acceder a información de la batería. Tenga en cuenta que la clase siguiente tiene un constructor sin parámetros para que el elemento `DependencyService` pueda crear instancias:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;

namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery
  {
    public BatteryImplementation()
    {
      UIDevice.CurrentDevice.BatteryMonitoringEnabled = true;
    }

    public int RemainingChargePercent
    {
      get
      {
        return (int)(UIDevice.CurrentDevice.BatteryLevel * 100F);
      }
    }

    public BatteryStatus Status
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return BatteryStatus.Charging;
          case UIDeviceBatteryState.Full:
            return BatteryStatus.Full;
          case UIDeviceBatteryState.Unplugged:
            return BatteryStatus.Discharging;
          default:
            return BatteryStatus.Unknown;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Full:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Unplugged:
            return PowerSource.Battery;
          default:
            return PowerSource.Other;
        }
      }
    }
  }
}
```

Por último, agregue este atributo `[assembly]` encima de la clase (y fuera de cualquier espacio de nombres definido), incluidas las instrucciones `using` necesarias:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;//necessary for registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery {
    ...
```

Este atributo registra la clase como una implementación de la interfaz `IBattery`, lo que significa que `DependencyService.Get<IBattery>` puede usarse en código compartido para crear una instancia de este:

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

La implementación de Android usa la API de [`Android.OS.BatteryManager`](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/). Esta implementación es más compleja que la versión de iOS, que necesita comprobaciones para controlar la falta de permisos de la batería:

```csharp
using System;
using Android;
using Android.Content;
using Android.App;
using Android.OS;
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid;

namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery
  {
    private BatteryBroadcastReceiver batteryReceiver;
    public BatteryImplementation() { }

    public int RemainingChargePercent
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              var level = battery.GetIntExtra(BatteryManager.ExtraLevel, -1);
              var scale = battery.GetIntExtra(BatteryManager.ExtraScale, -1);

              return (int)Math.Floor(level * 100D / scale);
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }

      }
    }

    public DependencyServiceSample.BatteryStatus Status
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;
              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);
              if (isCharging)
                return DependencyServiceSample.BatteryStatus.Charging;

              switch(status)
              {
                case (int)BatteryStatus.Charging:
                  return DependencyServiceSample.BatteryStatus.Charging;
                case (int)BatteryStatus.Discharging:
                  return DependencyServiceSample.BatteryStatus.Discharging;
                case (int)BatteryStatus.Full:
                  return DependencyServiceSample.BatteryStatus.Full;
                case (int)BatteryStatus.NotCharging:
                  return DependencyServiceSample.BatteryStatus.NotCharging;
                default:
                  return DependencyServiceSample.BatteryStatus.Unknown;
              }
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;

              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);

              if (!isCharging)
                return DependencyServiceSample.PowerSource.Battery;
              else if (usbCharge)
                return DependencyServiceSample.PowerSource.Usb;
              else if (acCharge)
                return DependencyServiceSample.PowerSource.Ac;
              else if (wirelessCharge)
                return DependencyServiceSample.PowerSource.Wireless;
              else
                return DependencyServiceSample.PowerSource.Other;
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }
  }
}
```

Agregue este atributo `[assembly]` encima de la clase (y fuera de cualquier espacio de nombres definido), incluidas las instrucciones `using` necesarias:

```csharp
...
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery {
    ...
```

Este atributo registra la clase como una implementación de la interfaz `IBattery`, lo que significa que `DependencyService.Get<IBattery>` puede usarse en el código compartido para crear una instancia de este.

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementación de Plataforma universal de Windows

La implementación de UWP usa las API de `Windows.Devices.Power` para obtener información del estado de la batería:

```csharp
using DependencyServiceSample.UWP;
using Xamarin.Forms;

[assembly: Dependency(typeof(BatteryImplementation))]
namespace DependencyServiceSample.UWP
{
    public class BatteryImplementation : IBattery
    {
        private BatteryStatus status = BatteryStatus.Unknown;
        Windows.Devices.Power.Battery battery;

        public BatteryImplementation()
        {
        }

        private Windows.Devices.Power.Battery DefaultBattery
        {
            get
            {
                return battery ?? (battery = Windows.Devices.Power.Battery.AggregateBattery);
            }
        }

        public int RemainingChargePercent
        {
            get
            {
                var finalReport = DefaultBattery.GetReport();
                var finalPercent = -1;

                if (finalReport.RemainingCapacityInMilliwattHours.HasValue && finalReport.FullChargeCapacityInMilliwattHours.HasValue)
                {
                    finalPercent = (int)((finalReport.RemainingCapacityInMilliwattHours.Value /
                        (double)finalReport.FullChargeCapacityInMilliwattHours.Value) * 100);
                }
                return finalPercent;
            }
        }

        public BatteryStatus Status
        {
            get
            {
                var report = DefaultBattery.GetReport();
                var percentage = RemainingChargePercent;

                if (percentage >= 1.0)
                {
                    status = BatteryStatus.Full;
                }
                else if (percentage < 0)
                {
                    status = BatteryStatus.Unknown;
                }
                else
                {
                    switch (report.Status)
                    {
                        case Windows.System.Power.BatteryStatus.Charging:
                            status = BatteryStatus.Charging;
                            break;
                        case Windows.System.Power.BatteryStatus.Discharging:
                            status = BatteryStatus.Discharging;
                            break;
                        case Windows.System.Power.BatteryStatus.Idle:
                            status = BatteryStatus.NotCharging;
                            break;
                        case Windows.System.Power.BatteryStatus.NotPresent:
                            status = BatteryStatus.Unknown;
                            break;
                    }
                }
                return status;
            }
        }

        public PowerSource PowerSource
        {
            get
            {
                if (status == BatteryStatus.Full || status == BatteryStatus.Charging)
                {
                    return PowerSource.Ac;
                }
                return PowerSource.Battery;
            }
        }
    }
}
```

El atributo `[assembly]` por encima de la declaración del espacio de nombres registra la clase como una implementación de la interfaz `IBattery`, lo que quiere decir que puede usarse `DependencyService.Get<IBattery>` en código compartido para crear una instancia de este.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementación en código compartido

Después de implementar la interfaz para cada plataforma, puede escribirse la aplicación compartida para aprovechar sus funciones. La aplicación estará compuesta por una página con un botón que, al pulsarlo, actualiza el texto por el estado actual de la batería. Usa el elemento `DependencyService` para obtener una instancia de la interfaz de `IBattery`. En tiempo de ejecución, esta instancia será la implementación específica de la plataforma que tenga acceso total al SDK nativo.

```csharp
public MainPage ()
{
    var button = new Button {
        Text = "Click for battery info",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    button.Clicked += (sender, e) => {
        var bat = DependencyService.Get<IBattery>();

        switch (bat.PowerSource){
          case PowerSource.Battery:
            button.Text = "Battery - ";
            break;
          case PowerSource.Ac:
            button.Text = "AC - ";
            break;
          case PowerSource.Usb:
            button.Text = "USB - ";
            break;
          case PowerSource.Wireless:
            button.Text = "Wireless - ";
            break;
          case PowerSource.Other:
          default:
            button.Text = "Other - ";
            break;
        }
        switch (bat.Status){
          case BatteryStatus.Charging:
            button.Text += "Charging";
            break;
          case BatteryStatus.Discharging:
            button.Text += "Discharging";
            break;
          case BatteryStatus.NotCharging:
            button.Text += "Not Charging";
            break;
          case BatteryStatus.Full:
            button.Text += "Full";
            break;
          case BatteryStatus.Unknown:
          default:
            button.Text += "Unknown";
            break;
        }
    };
    Content = button;
}
```

Al ejecutar esta aplicación en iOS, Android o UWP y pulsar el botón, se actualizará el texto del botón para reflejar el estado actual de la batería del dispositivo.

![](battery-info-images/battery.png "Ejemplo de estado de la batería")


## <a name="related-links"></a>Vínculos relacionados

- [DependencyService (ejemplo)](https://developer.xamarin.com/samples/DependencyService)
- [Uso de DependencyService (ejemplo)](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
