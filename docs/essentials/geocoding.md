---
title: 'Xamarin.Essentials: codificación geográfica'
description: La clase de codificación geográfica en Xamarin.Essentials proporciona las API para ambos código geográfico un placemark a coordenadas de posición y a un placemark invertir coordenadas código geográfico.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 063adba82d96e7fcc64d7ec49a0c0133e1cef8ef
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080331"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: codificación geográfica

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Geocodificación** clase proporciona las API para código geográfico un placemark a coordenadas de posición y a un placemark invertir coordincates código geográfico.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **Geocodificación** se requiere la siguiente configuración específica de plataforma de funcionalidad.

# <a name="androidtabandroid"></a>[Android](#tab/android)

No se necesita ninguna instalación adicional.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se necesita ninguna instalación adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Una clave de API de mapas de Bing tiene que usar funcationality de codificación geográfica. Registrarse para obtener una segunda [mapas de Bing](https://www.bingmapsportal.com/) cuenta. En **mi cuenta > Mis claves** crear una clave nueva y rellene la información según el tipo de aplicación (que debe ser **público de aplicación de Windows (UWP, 8.x y versiones anteriores)** para aplicaciones UWP).

Desde el principio de la vida de la aplicación antes de llamar a cualquiera **Geocodificación** métodos establecen la clave de API:

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Uso de codificación geográfica

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Obtener [ubicación](xref:Xamarin.Essentials.Location) coordenadas para una dirección:

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

La altitud no siempre está disponible. Si no está disponible, el `Altitude` propiedad podría ser `null` o el valor puede ser cero. Si está disponible la altitud, el valor está en metros por encima por encima del nivel del mar. 

Obtener [placemarks](xref:Xamarin.Essentials.Placemark) para un conjunto de coordenadas existente:

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

El [ `Location` ](xref:Xamarin.Essentials.Location) y [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) clases definen métodos para calcular la distancia entre dos ubicaciones. Vea el artículo [ **Xamarin.Essentials: Geolocalización** ](geolocation.md#calculate-distance) para obtener un ejemplo.

## <a name="api"></a>API

- [Código fuente de codificación geográfica](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Documentación de API de codificación geográfica](xref:Xamarin.Essentials.Geocoding)
