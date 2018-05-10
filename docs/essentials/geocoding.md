---
title: Geocodificación Xamarin.Essentials
description: La clase de codificación geográfica proporciona las API para código geográfico un placemark a coordenadas de posición y a un placemark invertir coordincates código geográfico.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0bfc463ca22d4137eadc35c02375fe41b0b99b8a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-geocoding"></a>Geocodificación Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Geocodificación** clase proporciona las API para código geográfico un placemark a coordenadas de posición y a un placemark invertir coordincates código geográfico.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **Geocodificación** se requiere la siguiente configuración específica de plataforma de funcionalidad.

# <a name="androidtabandroid"></a>[Android](#tab/android)

No se necesita ninguna instalación adicional.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se necesita ninguna instalación adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Una clave de API de mapas de Bing tiene que usar funcationality de codificación geográfica. Registrarse para obtener una segunda [mapas de Bing](https://www.bingmapsportal.com/) cuenta. En **mi cuenta > Mis claves** crear una clave nueva y rellene la información según el tipo de aplicación.

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
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
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
    // Handle exception that may have occured in geocoding
}
```

## <a name="api"></a>API

- [Código fuente de codificación geográfica](https://github.com/xamarin/Essentials/tree/master/Essentials/Geocoding)
- [Documentación de API de codificación geográfica](xref:Xamarin.Essentials.Geocoding)
