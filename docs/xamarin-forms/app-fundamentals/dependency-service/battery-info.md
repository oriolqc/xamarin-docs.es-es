---
title: Comprobación de estado de la batería
description: En este artículo se explica cómo usar la clase Xamarin.Forms DependencyService para tener acceso a información de la batería forma nativa para cada plataforma.
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: cbb4a01ac2c6d933fe40a0b3c2571d1fe3ce75c0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998407"
---
# <a name="checking-battery-status"></a>Comprobación de estado de la batería

En este artículo le guía a través de la creación de una aplicación que comprueba el estado de la batería. En este artículo se basa en el complemento de la batería por James Montemagno. Para obtener más información, consulte el [repositorio de GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery).

Dado que Xamarin.Forms no incluye la funcionalidad para comprobar el estado actual de la batería, esta aplicación deberá usar [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) para aprovechar las API nativas.  En este artículo se describen los pasos siguientes para usar `DependencyService`:

- **[Creación de la interfaz](#Creating_the_Interface)**  &ndash; comprender cómo se crea la interfaz en código compartido.
- **[Implementación de iOS](#iOS_Implementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para Android.
- **[Implementación de la plataforma Windows universal](#UWPImplementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para la plataforma Universal de Windows (UWP).
- **[Implementar en código compartido](#Implementing_in_Shared_Code)**  &ndash; Aprenda a usar `DependencyService` para llamar a la implementación nativa desde código compartido.

Cuando haya completado, la aplicación mediante `DependencyService` tendrá la siguiente estructura:

![](battery-info-images/battery-diagram.png "Estructura de la aplicación de DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

En primer lugar, cree una interfaz en código compartido que expresa la funcionalidad deseada. En el caso de una batería de comprobación de la aplicación, la información relevante es el porcentaje de batería restante, si el dispositivo está cargando o no, y cómo el dispositivo está recibiendo alimentación:

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

Codificar esta interfaz en el código compartido permitirá que la aplicación de Xamarin.Forms tener acceso a las API de administración de energía en cada plataforma.

> [!NOTE]
> Las clases que implementan la interfaz deben tener un constructor sin parámetros para que funcione con el `DependencyService`. Los constructores no se puede definir las interfaces.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

El `IBattery` interfaz se debe implementar en cada proyecto de aplicación específico de la plataforma. La implementación de iOS usará nativo [ `UIDevice` ](https://developer.xamarin.com/api/type/UIKit.UIDevice/) API para tener acceso a información de la batería. Tenga en cuenta que la clase siguiente tiene un constructor sin parámetros para que el `DependencyService` puede crear nuevas instancias:

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

Por último, agregue esto `[assembly]` atributo por encima de la clase (y fuera de los espacios de nombres que se han definido), incluido cualquier necesario `using` instrucciones:

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

Este atributo registra la clase como una implementación de la `IBattery` interfaz, lo que significa que `DependencyService.Get<IBattery>` puede usarse en código compartido para crear una instancia de ella:

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

La implementación de Android utiliza la [ `Android.OS.BatteryManager` ](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/) API. Esta implementación es más compleja que la versión de iOS, que requieren comprobaciones para controlar la falta de permisos de la batería:

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

Agregar esto `[assembly]` atributo por encima de la clase (y fuera de los espacios de nombres que se han definido), incluido cualquier necesario `using` instrucciones:

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

Este atributo registra la clase como una implementación de la `IBattery` interfaz, lo que significa que `DependencyService.Get<IBattery>` se pueden usar en el código compartido puede crear una instancia de ella.

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementación de la plataforma universal de Windows

La implementación de UWP usa la `Windows.Devices.Power` API para obtener información de estado de la batería:

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

El `[assembly]` atributo encima de la declaración de espacio de nombres registra la clase como una implementación de la `IBattery` interfaz, lo que significa que `DependencyService.Get<IBattery>` puede usarse en código compartido para crear una instancia de ella.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementar en código compartido

Ahora que ha implementado la interfaz para cada plataforma, la aplicación compartida puede escribirse para aprovechar las ventajas de la misma. La aplicación constará de una página con un botón que, cuando puntea actualizaciones su texto con el estado actual de la batería. Usa el `DependencyService` para obtener una instancia de la `IBattery` interfaz. En tiempo de ejecución, esta instancia será la implementación específica de la plataforma que tiene acceso completo a lo SDK nativo.

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

Ejecutando esta aplicación en iOS, Android, o UWP y presionar el botón se producirán en el texto del botón Actualizar para reflejar el estado actual de energía del dispositivo.

![](battery-info-images/battery.png "Ejemplo de estado de la batería")


## <a name="related-links"></a>Vínculos relacionados

- [DependencyService (ejemplo)](https://developer.xamarin.com/samples/DependencyService)
- [Uso de DependencyService (ejemplo)](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
