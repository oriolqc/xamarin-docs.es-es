---
title: 'Xamarin.Essentials: Mostrar información de dispositivo'
description: Este documento describe la clase DeviceDisplay en Xamarin.Essentials, que proporciona las métricas de la pantalla del dispositivo en el que se ejecuta la aplicación.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: cb42da4c8c2d0e381a5b00f7e60da6f427d19c66
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353833"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Mostrar información de dispositivo

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **DeviceDisplay** clase proporciona información acerca de las métricas de pantalla del dispositivo que se ejecuta la aplicación.

## <a name="using-devicedisplay"></a>Uso de DeviceDisplay

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Métricas de la pantalla

Además de información básica del dispositivo la **DeviceDisplay** clase contiene información acerca de la pantalla y la orientación del dispositivo.

```csharp
// Get Metrics
var metrics = DeviceDisplay.ScreenMetrics;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = metrics.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = metrics.Rotation;

// Width (in pixels)
var width = metrics.Width;

// Height (in pixels)
var height = metrics.Height;

// Screen density
var density = metrics.Density;
```

El **DeviceDisplay** clase también expone un evento que se puede suscribir para que se desencadena siempre que cualquier pantalla de los cambios de métrica:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChangedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [Código fuente de DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentación de API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
