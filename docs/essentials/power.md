---
title: 'Xamarin.Essentials: estado de ahorro de energía'
description: La clase Power permite que un programa obtenga el estado de ahorro de energía para determinar si el dispositivo funciona en modo de bajo consumo energético.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: jamesmontemagno
ms.author: jamont
ms.date: 06/27/2018
ms.openlocfilehash: 5a89dba16a93b007c5d7312221d8d33e00c7404a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110009"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: estado de ahorro de energía

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Power** proporciona información sobre el estado de ahorro de energía del dispositivo, que indica si este se ejecuta en modo de bajo consumo energético. Las aplicaciones deben evitar el procesamiento en segundo plano si el estado de ahorro de energía del dispositivo está activado.

## <a name="background"></a>Fondo

Es posible poner los dispositivos que usan baterías en el modo de ahorro en caso de baja energía. A veces, los dispositivos cambian automáticamente a este modo, por ejemplo, cuando la batería cae por debajo del 20 % de su capacidad. El sistema operativo responde al modo de ahorro de energía reduciendo las actividades que tienden a agotar la batería. Para ayudar, las aplicaciones pueden evitar el procesamiento en segundo plano u otras actividades de alta potencia cuando el modo de ahorro de energía está activado.

En el caso de los dispositivos Android, la clase **Power**devuelve información importante solo para la versión 5.0 (Lollipop) y posteriores de Android.

## <a name="using-the-power-class"></a>Uso de la clase Power

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Conozca el estado de ahorro de energía actual del dispositivo con la propiedad estática `Power.EnergySaverStatus`:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Esta propiedad devuelve un miembro de la enumeración `EnergySaverStatus`, que es `On`, `Off` o `Unknown`. Si la propiedad devuelve `On`, la aplicación debe evitar el procesamiento en segundo plano o cualquier otra actividad que pueda tener un consumo energético grande.

La aplicación también debe instalar un controlador de eventos. La clase **Power** expone un evento que se desencadena cuando cambia el estado de ahorro de energía:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Si el estado de ahorro de energía cambia a `On`, la aplicación debe detener el procesamiento en segundo plano. Si el estado cambia a `Unknown` o `Off`, la aplicación puede reanudar el procesamiento en segundo plano.

## <a name="api"></a>API

- [Código fuente de Power](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Documentación de API para Power](xref:Xamarin.Essentials.Power)
