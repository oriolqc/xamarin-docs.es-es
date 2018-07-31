---
title: Mapas de Xamarin.Essentials
description: La clase de mapas en Xamarin.Essentials permite que una aplicación abrir la aplicación instalada se asigna a una ubicación específica o placemark.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 445e2da84e9a9aaf1ce4d836af11cfba963b8cbb
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353957"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials: asignaciones

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **asigna** clase permite que una aplicación abrir la aplicación instalada se asigna a una ubicación específica o placemark.

## <a name="using-maps"></a>Uso de mapas

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de mapas funciona mediante una llamada a la `OpenAsync` método con el `Location` o `Placemark` abrir con opcional `MapsLaunchOptions`.

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(location, options);
    }
}
```

Cuando se abre con un `Placemark` se requiere la siguiente información:

* `CountryName`
* `AdminArea`
* `Thoroughfare`
* `Locality`

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>Métodos de extensión.

Si ya tiene una referencia a un `Location` o `Placemark` puede usar el método de extensión integrada `OpenMapsAsync` con opcional `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="platform-differences"></a>Diferencias entre las plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `MapDirectionsMode` no se admite y no tiene ningún efecto.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `MapDirectionsMode` se admite para establecer el modo de dirección de forma predeterminada cuando se abre la aplicación de mapas.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `MapDirectionsMode` no se admite y no tiene ningún efecto.

--------------

## <a name="platform-implementation-specifics"></a>Detalles de implementación de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android usa la `geo:` esquema Uri para iniciar la aplicación se asigna en el dispositivo. Esto puede pedirle al usuario seleccionar de una aplicación existente que admita este esquema de Uri.  Xamarin.Essentials se ha probado con Google Maps, que es compatible con este esquema.

# <a name="iostabios"></a>[iOS](#tab/ios)

No hay detalles de implementación específica de plataforma.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No hay detalles de implementación específica de plataforma.

--------------

## <a name="api"></a>API

- [Mapas de código fuente](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Documentación de API de MAPS](xref:Xamarin.Essentials.Maps)
