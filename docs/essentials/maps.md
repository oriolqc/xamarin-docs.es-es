---
title: 'Xamarin.Essentials: Maps'
description: La clase Maps de Xamarin.Essentials permite que una aplicación abra la aplicación de mapas instalada en una ubicación o marca de posición específica.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: fb4cbc2fd334d574abc57a3359fa346bc6795408
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674787"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials: Maps

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **Maps** permite que una aplicación abra la aplicación de mapas instalada en una ubicación o marca de posición específica.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-maps"></a>Uso de Maps

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad Maps funciona mediante una llamada al método `OpenAsync` con `Location` o `Placemark` abierto con `MapsLaunchOptions` opcional.

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

Cuando se abre con `Placemark`, se requiere la siguiente información:

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

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

Si ya tiene una referencia a `Location` o a `Placemark`, puede usar el método de extensión integrado `OpenMapsAsync` con `MapsLaunchOptions` opcional:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="directions-mode"></a>Modo de instrucciones

Si llama a `OpenMapsAsync` sin `MapsLaunchOptions`, el mapa se iniciará en la ubicación especificada. Si quiere, puede hacer que se calcule una ruta de navegación desde la posición actual del dispositivo. Para ello, establezca `MapDirectionsMode` en `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { MapDirectionsMode = MapDirectionsMode.Driving };

        await Maps.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>Diferencias entre las plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

- `MapDirectionsMode` admite Bicycling (Bicicleta), Driving (Automóvil) y Walking (A pie).

# <a name="iostabios"></a>[iOS](#tab/ios)

- `MapDirectionsMode` admite Driving (Automóvil), Transit (Transporte público) y Walking (A pie).

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- `MapDirectionsMode` admite Driving (Automóvil), Transit (Transporte público) y Walking (A pie).

--------------

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android usa el esquema `geo:` de URI para iniciar la aplicación de mapas en el dispositivo. Esto podría pedirle al usuario que seleccione de una aplicación existente que admite este esquema de URI.  Xamarin.Essentials se probó con Google Maps, que admite este esquema.

# <a name="iostabios"></a>[iOS](#tab/ios)

Sin detalles de implementación específicos para la plataforma.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Sin detalles de implementación específicos para la plataforma.

--------------

## <a name="api"></a>API

- [Código fuente de Maps](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Documentación de API para Maps](xref:Xamarin.Essentials.Maps)
