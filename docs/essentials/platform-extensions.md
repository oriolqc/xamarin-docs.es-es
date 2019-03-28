---
title: Extensiones de plataforma de Xamarin.Essentials
description: Xamarin.Essentials ofrece varios métodos de extensión de la plataforma al trabajar con tipos de plataforma como Rect, Size y Point.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 740f6f4658c3e840f73261f554eb330ee02edb5d
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329070"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials: extensiones de plataforma

Xamarin.Essentials ofrece varios métodos de extensión de plataforma al trabajar con tipos de plataforma como Rect, Size y Point. Esto significa que puede convertir la versión `System` de estos tipos a tipos específicos de iOS, Android y UWP. 

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>Uso de las extensiones de plataforma

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Todas las extensiones de plataforma solo se pueden llamar desde proyectos UWP, iOS o Android.

### <a name="point"></a>Punto

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="size"></a>Tamaño

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>Rectángulo

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

## <a name="api"></a>API

- [Código fuente de los convertidores](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [Documentación sobre la API de los convertidores de puntos](xref:Xamarin.Essentials.PointExtensions)
- [Documentación sobre la API de los convertidores de rectángulos](xref:Xamarin.Essentials.RectangleExtensions)
- [Documentación sobre la API de los convertidores de tamaño](xref:Xamarin.Essentials.SizeExtensions)
