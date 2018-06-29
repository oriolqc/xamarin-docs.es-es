---
title: 'Xamarin.Essentials: Información de pantalla del dispositivo'
description: Este documento describe la clase DeviceDisplay en Xamarin.Essentials, que proporciona las métricas de pantalla para el dispositivo en el que se ejecuta la aplicación.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3060d56e14fb0d3801a96ec0fe6e24c9efda4dac
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080318"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Información de pantalla del dispositivo

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

El **DeviceDisplay** clase también expone un evento que se puede suscribir a que se desencadena cuando cualquier pantalla cambios de métrica:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
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
- [Documentación de la API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
