---
title: 'Xamarin.Essentials: Información de pantalla del dispositivo'
description: Este documento describe la clase DeviceDisplay en Xamarin.Essentials, que proporciona las métricas de pantalla para el dispositivo en el que se ejecuta la aplicación.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 830b96bcc21397047cb5aaacb5c568bc2ee863c4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782377"
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

- [Código fuente de DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentación de la API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
