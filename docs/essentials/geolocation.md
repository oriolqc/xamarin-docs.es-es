---
title: 'Xamarin.Essentials: ubicación geográfica'
description: Este documento describe la clase de ubicación geográfica en Xamarin.Essentials, que proporciona las API para recuperar las coordenadas de ubicación geográfica actual del dispositivo.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 11749107403fc99e1d49b63ee3b50ff105abaa57
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080292"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: ubicación geográfica

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Geolocalización** clase proporciona las API para recuperar las coordenadas de ubicación geográfica actual del dispositivo.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **Geolocalización** la siguiente configuración específica de la plataforma de función, se necesita:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Se requieren permisos de grueso y la ubicación de un problema y deben configurarse en el proyecto de Android. Además, si su aplicación está dirigida a Android 5.0 (API nivel 21) o versiones posteriores, debe declarar la aplicación usa las características de hardware en el archivo de manifiesto. Esto puede agregarse de las maneras siguientes:

Abra la **AssemblyInfo.cs** de archivos en el **propiedades** carpeta y agregar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

O actualizar el manifiesto de Android:

Abra la **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

O haga doble clic en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto Android** buscar el **los permisos necesarios:** área y compruebe el **ACCESS_COARSE_LOCATION** y **ACCESS_FINE_LOCATION**permisos. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

La aplicación **Info.plist** debe contener el `NSLocationWhenInUseUsageDescription` clave para tener acceso a la ubicación del dispositivo.

Abra el editor de plist y agregue el **privacidad - ubicación cuando Use uso descripción** propiedad y relleno en un valor que se mostrará al usuario.

Manualmente o edite el archivo y agregue lo siguiente:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Debe establecer el `Location` permiso para la aplicación. Esto puede hacerse abriendo el **Package.appxmanifest** y si selecciona el **capacidades** ficha y la comprobación de **ubicación**.

-----

## <a name="using-geolocation"></a>Utiliza ubicación geográfica

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La API de Geoloation también le pedirá al usuario permisos cuando sea necesario.

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

La altitud no siempre está disponible. Si no está disponible, el `Altitude` propiedad podría ser `null` o el valor puede ser cero. Si está disponible la altitud, el valor está en metros por encima por encima del nivel del mar. 

Para consultar el dispositivo actual [ubicación](xref:Xamarin.Essentials.Location) coordenadas, la `GetLocationAsync` puede utilizarse. Es mejor pasar por un completo `GeolocationRequest` y `CancellationToken` ya que puede tardar algún tiempo en obtener la ubicación del dispositivo.

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

El [ `Location` ](xref:Xamarin.Essentials.Location) y [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) definen clases `CalculateDistance` métodos que permiten calcular la distancia entre dos ubicaciones geográficas. Esto calcula distancia no tiene carreteras u otras rutas en la cuenta y es simplemente la distancia más corta entre los dos puntos a lo largo de la superficie de la tierra, también conocido como el _círculo gran distancia_ o coloquialmente, el distancia "como los archivos de gallo."

Por ejemplo:

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

El `Location` constructor tiene argumentos de latitud y longitud en ese orden. Los valores de latitud positivo son al norte del Ecuador y los valores de latitud positivo son al este del meridiano. Use el argumento final `CalculateDistance` para especificar millas o kilómetros. El `Location` también define la clase `KilometersToMiles` y `MilesToKilometers` métodos para convertir entre las dos unidades.

## <a name="api"></a>API

- [Código de origen de ubicación geográfica](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Documentación de la API de ubicación geográfica](xref:Xamarin.Essentials.Geolocation)
