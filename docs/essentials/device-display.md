---
title: 'Xamarin.Essentials: Información de pantalla del dispositivo'
description: En este documento se describe la clase DeviceDisplay de Xamarin.Essentials, que proporciona las métricas de pantalla del dispositivo en el que se ejecuta la aplicación.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ebe97cf7fbb78bff17196110e835bd35ff76b826
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674891"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Información de pantalla del dispositivo

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **DeviceDisplay** proporciona las métricas de pantalla del dispositivo en el que se ejecuta la aplicación.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Uso de DeviceDisplay

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Métricas de pantalla

Además de información básica del dispositivo, la clase **DeviceDisplay** contiene información sobre la pantalla y la orientación del dispositivo.

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

La clase **DeviceDisplay** también expone un evento al que se puede suscribir para que se desencadene siempre que cambie cualquier métrica de pantalla:

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

## <a name="platform-differences"></a>Diferencias entre las plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

No hay diferencias.

# <a name="iostabios"></a>[iOS](#tab/ios)

* El acceso a `ScreenMetrics` se debe realizar en el subproceso de la interfaz de usuario o se iniciará una excepción. Puede usar el método [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) para ejecutar ese código en el subproceso de la interfaz de usuario.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No hay diferencias.

--------------


## <a name="api"></a>API

- [Código fuente de DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentación de API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
