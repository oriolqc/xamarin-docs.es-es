---
title: 'Xamarin.Essentials: ubicación geográfica'
description: Este documento describe la clase de la ubicación geográfica en Xamarin.Essentials, que proporciona las API para recuperar las coordenadas de geolocalización actual del dispositivo.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 11749107403fc99e1d49b63ee3b50ff105abaa57
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848756"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: ubicación geográfica

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Geolocalización** clase proporciona las API para recuperar las coordenadas de geolocalización actual del dispositivo.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **Geolocalización** funcionalidad, se requiere la siguiente configuración específica de la plataforma:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Grueso y una ubicación bien permisos son necesarios y deben configurarse en el proyecto de Android. Además, si la aplicación tiene como destino Android 5.0 (API nivel 21) o versiones posteriores, debe declarar la aplicación usa las características de hardware en el archivo de manifiesto. Esto se puede agregar de las maneras siguientes:

Abra el **AssemblyInfo.cs** archivo bajo el **propiedades** carpeta y agregue:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

O bien, puede actualizar el manifiesto de Android:

Abra el **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

O haga doble clic en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto de Android** encontrar el **los permisos necesarios:** área y compruebe el **ACCESS_COARSE_LOCATION** y **ACCESS_FINE_LOCATION**permisos. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

La aplicación **Info.plist** debe contener el `NSLocationWhenInUseUsageDescription` clave para tener acceso a la ubicación del dispositivo.

Abra el editor de plist y agregue el **privacidad - ubicación cuando en el uso de la descripción de uso** propiedad y relleno en un valor para mostrar el usuario.

O editar el archivo manualmente y agregue lo siguiente:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Debe establecer el `Location` permiso para la aplicación. Esto puede hacerse abriendo el **Package.appxmanifest** y si selecciona el **capacidades** ficha y la comprobación **ubicación**.

-----

## <a name="using-geolocation"></a>Uso de Geolocalización

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La API Geoloation también le pedirá al usuario permisos cuando sea necesario.

Puede obtener el último conocido [ubicación](xref:Xamarin.Essentials.Location) del dispositivo mediante una llamada a la `GetLastKnownLocationAsync` método. Esto suele ser más rápido, a continuación, realiza una consulta completa, pero puede ser menos precisa.

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

Altitud no siempre está disponible. Si no está disponible, el `Altitude` propiedad podría ser `null` o el valor puede ser cero. Si está disponible la altitud, el valor es en metros por encima por encima del nivel del mar. 

Para consultar el dispositivo actual [ubicación](xref:Xamarin.Essentials.Location) coordenadas, la `GetLocationAsync` se puede usar. Es mejor pasar un completo `GeolocationRequest` y `CancellationToken` ya que puede tardar algún tiempo en obtener la ubicación del dispositivo.

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>Precisión de la ubicación geográfica

En la tabla siguiente se describe la precisión por plataforma:

### <a name="lowest"></a>Mínima

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000 - 5000 |

### <a name="low"></a>Bajo

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300 - 3000 |

### <a name="medium-default"></a>Media (predeterminada)

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>Alto

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 0 - 100 |
| iOS | 10 |
| UWP | < = 10 |

### <a name="best"></a>Mejor

| Plataforma | Distancia (en metros) |
| --- | --- |
| Android | 0 - 100 |
| iOS | ~0 |
| UWP | < = 10 |

<a name="calculate-distance" />

## <a name="distance-between-two-locations"></a>Distancia entre dos ubicaciones

El [ `Location` ](xref:Xamarin.Essentials.Location) y [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) definen clases `CalculateDistance` métodos que permiten calcular la distancia entre dos ubicaciones geográficas. Esto calcula la distancia no tiene otras pathways o carreteras en cuenta y es simplemente la distancia más corta entre los dos puntos a lo largo de la superficie de la tierra, también conocido como el _distancia del círculo_ o coloquialmente, el distancia "como los archivos de gallo."

Por ejemplo:

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

El `Location` constructor tiene argumentos de latitud y longitud en ese orden. Los valores de latitud positivos están al norte del Ecuador y son los valores de longitud positivo al este del meridiano. Use el argumento final `CalculateDistance` especificar millas o kilómetros. El `Location` también define la clase `KilometersToMiles` y `MilesToKilometers` métodos de conversión entre las dos unidades.

## <a name="api"></a>API

- [Código fuente de Geolocalización](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Documentación de API de ubicación geográfica](xref:Xamarin.Essentials.Geolocation)
