---
title: 'Xamarin.Essentials: Geocodificación'
description: La clase de Geocodificación en Xamarin.Essentials, proporciona API para ambas código geográfico un placemark a una posición coordenadas e invertir las coordenadas de código geográfico para un placemark.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 063adba82d96e7fcc64d7ec49a0c0133e1cef8ef
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831453"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: Geocodificación

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Geocodificación** clase proporciona las API para obtener un placemark a una posición coordenadas e invertir coordincates código geográfico para un placemark.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **Geocodificación** se requiere la funcionalidad de la siguiente configuración específica de plataforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

No se requiere ninguna configuración adicional.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se requiere ninguna configuración adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Se requiere una clave de API de Bing maps usar funcationality geocodificación. Regístrese para obtener una [mapas de Bing](https://www.bingmapsportal.com/) cuenta. En **mi cuenta > Mis claves** crear una nueva clave y rellene la información en función de su tipo de aplicación (que debe ser **aplicación pública de Windows (UWP, 8.x y versiones anteriores)** para aplicaciones UWP).

Desde el principio en la vida de la aplicación antes de llamar a cualquier **Geocodificación** métodos establecen la clave de API:

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Uso de Geocodificación

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Introducción [ubicación](xref:Xamarin.Essentials.Location) coordenadas para una dirección:

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occured in geocoding
}
```

Altitud no siempre está disponible. Si no está disponible, el `Altitude` propiedad podría ser `null` o el valor puede ser cero. Si está disponible la altitud, el valor es en metros por encima por encima del nivel del mar. 

Introducción [placemarks](xref:Xamarin.Essentials.Placemark) para un conjunto de coordenadas existente:

```csharp
try
{
    var lat = 47.673988;
    var lon = -122.121513;

    var placemarks = await Geocoding.GetPlacemarksAsync(lat, lon);

    var placemark = placemarks?.FirstOrDefault();
    if (placemark != null)
    {
        var geocodeAddress =
            $"AdminArea:       {placemark.AdminArea}\n" +
            $"CountryCode:     {placemark.CountryCode}\n" +
            $"CountryName:     {placemark.CountryName}\n" +
            $"FeatureName:     {placemark.FeatureName}\n" +
            $"Locality:        {placemark.Locality}\n" +
            $"PostalCode:      {placemark.PostalCode}\n" +
            $"SubAdminArea:    {placemark.SubAdminArea}\n" +
            $"SubLocality:     {placemark.SubLocality}\n" +
            $"SubThoroughfare: {placemark.SubThoroughfare}\n" +
            $"Thoroughfare:    {placemark.Thoroughfare}\n";

        Console.WriteLine(geocodeAddress);
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

## <a name="distance-between-two-locations"></a>Distancia entre dos ubicaciones

El [ `Location` ](xref:Xamarin.Essentials.Location) y [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) clases definen métodos para calcular la distancia entre dos ubicaciones. Consulte el artículo [ **Xamarin.Essentials: Geolocalización** ](geolocation.md#calculate-distance) para obtener un ejemplo.

## <a name="api"></a>API

- [Código fuente de geocodificación](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Documentación de API de geocodificación](xref:Xamarin.Essentials.Geocoding)
