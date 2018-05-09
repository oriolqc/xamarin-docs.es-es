---
title: Ubicación geográfica Xamarin.Essentials
description: La clase de ubicación geográfica proporciona las API para recuperar las coordenadas de ubicación geográfica actual del dispositivo.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 51e63245cd6959f650a0aa078cc4632bc825de5b
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-geocoding"></a>Geocodificación Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Geolocalización** clase proporciona las API para recuperar las coordenadas de ubicación geográfica actual del dispositivo.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **Geolocalización** se requiere la siguiente configuración específica de plataforma de funcionalidad.

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

O bien, actualice el manifiesto de Android:

Abra la **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo.

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

O haga clic con el botón secundario en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto Android** buscar el **los permisos necesarios:** área y compruebe el **ACCESS_COARSE_LOCATION** y **ACCESS_FINE_LOCATION**permisos. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

La aplicación es necesario tener claves su **Info.plist** para NSLocationWhenInUseUsageDescription para tener acceso a la ubicación del dispositivo.

Abra el editor de plist y agregue el **privacidad - ubicación cuando Use uso descripción** propiedad y relleno en un valor que se mostrará al usuario.

Manualmente o edite el archivo y agregue lo siguiente:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Debe establecer el `Location` permiso para la aplicación. Esto puede hacerse por abrir la **Package.appxmanifest** y si selecciona el **capacidades** ficha y la comprobación de **ubicación**.

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
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
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

Para consultar el dispositivo actual [ubicación](xref:Xamarin.Essentials.Location) coordenadas el `GetLocationAsync` puede utilizarse. Se recomienda pasar por un completo `GeolocationRequest` y `CancellationToken` ya que puede tardar algún tiempo en obtener la ubicación del dispositivo.

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
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

En la tabla siguiente se describe precisión por plataforma

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

## <a name="api"></a>API

- [Código de origen de ubicación geográfica](https://github.com/xamarin/Essentials/tree/master/Essentials/Geolocation)
- [Documentación de la API de ubicación geográfica](xref:Xamarin.Essentials.Geolocation)
