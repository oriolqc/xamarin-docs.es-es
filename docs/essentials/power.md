---
title: 'Xamarin.Essentials: Estado de protector de energía de alimentación'
description: La clase de energía permite que un programa para obtener el estado de ahorro de energía para determinar si el dispositivo está funcionando en un modo de bajo consumo de energía.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 6d8ccb5be69eb1ea7ea63d3f5c373d9284089679
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831525"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Estado de protector de energía de alimentación

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Power** clase proporciona información sobre el estado de ahorro de energía del dispositivo, que indica si el dispositivo se está ejecutando en un modo de bajo consumo de energía. Las aplicaciones deben evitar el procesamiento en segundo plano si se encuentra en estado de ahorro de energía del dispositivo.

## <a name="background"></a>Fondo

Los dispositivos que funcionan con baterías pueden colocarse en un modo de ahorro de energía de bajo consumo de energía. A veces, los dispositivos se cambian en este modo automáticamente, por ejemplo, cuando la batería cae por debajo del 20% de su capacidad. El sistema operativo se responde a modo de ahorro de energía reduciendo las actividades que tienden a agotar la batería. Pueden ayudar las aplicaciones al evitar el procesamiento en segundo plano u otras actividades alta al modo de ahorro de energía está activado.

Para dispositivos Android, el **Power** clase devuelve información significativa solo para Android versión 5.0 (Lollipop) y versiones posteriores.

## <a name="using-the-power-class"></a>Uso de la clase de energía

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Obtener el estado de ahorro de energía actual del dispositivo mediante estático `Power.EnergySaverStatus` propiedad:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Esta propiedad devuelve un miembro de la `EnergySaverStatus` enumeración, que puede ser `On`, `Off`, o `Unknown`. Si la propiedad devuelve `On`, la aplicación debe evitar el procesamiento en segundo plano u otras actividades que podrían consumir una gran cantidad de energía.

La aplicación también debe instalar a un controlador de eventos. El **Power** clase expone un evento que se desencadena cuando cambia el estado de ahorro de energía:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanaged += OnEnergySaverStatusChanaged;
    }

    private void OnEnergySaverStatusChanaged(EnergySaverStatusChanagedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Si cambia el estado de ahorro de energía a `On`, la aplicación debe dejar de realizar el procesamiento en segundo plano. Si el estado cambia a `Unknown` o `Off`, la aplicación puede reanudar el procesamiento en segundo plano.

## <a name="api"></a>API

- [Código fuente de alimentación](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Documentación de la API de energía](xref:Xamarin.Essentials.Power)
