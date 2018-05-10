---
title: Mostrar información de dispositivo Xamarin.Essentials
description: La clase DeviceDisplay proporciona información sobre métricas de pantalla del dispositivo la aplicación se ejecuta en.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 570fb6cf3eadfbbc7e189f875498d3cea0bc1514
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-device-display-information"></a>Mostrar información de dispositivo Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **DeviceDisplay** clase proporciona información acerca de las métricas de pantalla del dispositivo está ejecutando la aplicación.

## <a name="using-devicedisplay"></a>Usar DeviceDisplay

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Métricas de pantalla

Además de información básica del dispositivo la **DeviceDisplay** clase contiene información sobre la pantalla y la orientación del dispositivo.

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

El **DeviceDisplay** clase también expone y eventos que se pueden suscribir que desencadena un evento cada vez que cualquier pantalla cambios de métrica:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChanagedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [Código fuente de DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Essentials/DeviceDisplay)
- [Documentación de la API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
