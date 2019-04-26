---
title: Servicios de ubicación
description: Esta guía presenta el reconocimiento de ubicación en las aplicaciones de Android y muestra cómo obtener la ubicación del usuario mediante la API de servicio de ubicación de Android, así como el proveedor de ubicación fusionadas disponible con la API de servicios de ubicación de Google.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: 05a2187a5e8dc010f04e89757b566eaf44cb5fd6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61181292"
---
# <a name="location-services"></a>Servicios de ubicación

_Esta guía presenta el reconocimiento de ubicación en las aplicaciones de Android y muestra cómo obtener la ubicación del usuario mediante la API de servicio de ubicación de Android, así como el proveedor de ubicación fusionadas disponible con la API de servicios de ubicación de Google._

## <a name="location-services-overview"></a>Información general de servicios de ubicación

Android proporciona acceso a diversas tecnologías de ubicación como ubicación de la torre de celda, Wi-Fi y GPS. Los detalles de cada tecnología de ubicación se abstraen mediante *proveedores de ubicación*, permitiendo a las aplicaciones obtener las ubicaciones de la misma manera independientemente del proveedor utilizado. Esta guía presenta al proveedor de ubicación de multiplicación, una parte de Google Play Services, que determina la mejor manera de obtener la ubicación de los dispositivos en función de qué proveedores están disponibles y cómo se usa el dispositivo de manera inteligente. API de servicio de ubicación de Android y muestra cómo comunicarse con la ubicación del sistema de servicio mediante un `LocationManager`. La segunda parte de la guía explora el uso de la API de servicios de ubicación de Android el `LocationManager`.
 
Como regla general, las aplicaciones deben prefiere usar el proveedor de ubicación fusionadas, revirtiendo a la API de servicios de ubicación de Android más antiguos solo cuando sea necesario.

## <a name="location-fundamentals"></a>Aspectos básicos de la ubicación

En Android, con independencia de qué API para trabajar con datos de ubicación, elige varios conceptos siguen siendo los mismos. Esta sección presentan los proveedores de ubicación y permisos relacionados con la ubicación.

### <a name="location-providers"></a>Proveedores de ubicación

Varias tecnologías se utilizan internamente para identificar la ubicación del usuario. El hardware utilizado depende del tipo de *proveedor de ubicación* seleccionado para el trabajo de recopilación de datos. Android usa tres proveedores de ubicación:

-   **Proveedor de GPS** &ndash; GPS ofrece la ubicación más precisa, usa la máxima potencia y funciona mejor en el exterior. Este proveedor utiliza una combinación de GPS y GPS asistido ([aGPS](https://en.wikipedia.org/wiki/Assisted_GPS)), que devuelve datos GPS recopilados por torre de telefonía móvil.

-   **Proveedor de red** &ndash; ofrece una combinación de datos de Wi-Fi y móvil, incluidos aGPS recopilados por la celda towers. Utiliza menos energía que el proveedor de GPS, pero devuelve los datos de ubicación de precisión variable.

-   **Proveedor pasivo** &ndash; una opción "superpuesta" mediante proveedores solicitados por otras aplicaciones o servicios para generar datos de ubicación en una aplicación. Esto es menos confiable, pero la opción de ahorro de energía ideal para las aplicaciones que no requieren actualizaciones de ubicación constante para que funcione.

Los proveedores de ubicación no están siempre disponibles. Por ejemplo, podríamos usar GPS para nuestra aplicación, pero GPS podría estar desactivada en la configuración o el dispositivo podría no tener GPS en absoluto. Si un proveedor concreto no está disponible, al elegir ese proveedor podría devolver `null`.

### <a name="location-permissions"></a>Permisos de ubicación

Una aplicación con reconocimiento de ubicación necesita tener acceso a los sensores de hardware de un dispositivo para recibir datos de telefonía móvil, Wi-Fi y GPS. Acceso se controla a través de los permisos adecuados en el manifiesto de Android en la aplicación.
Hay dos permisos &ndash; según los requisitos de la aplicación y las API que prefiera, desea permitir uno:

-   `ACCESS_FINE_LOCATION` &ndash; Permite que una aplicación acceso a GPS.
    Necesario para la *proveedor GPS* y *proveedor pasivo* opciones (*proveedor pasivo necesita permiso para tener acceso a datos GPS recopilados por otra aplicación o servicio*). Permiso opcional para el *proveedor de red*.

-   `ACCESS_COARSE_LOCATION` &ndash; Permite el acceso a una aplicación a la ubicación de red de telefonía móvil y Wi-Fi. Necesario para *proveedor de red* si `ACCESS_FINE_LOCATION` no está establecida.

Para las aplicaciones destinadas a API versión 21 (Android 5.0 Lollipop) o versiones posteriores, puede habilitar `ACCESS_FINE_LOCATION` y todavía se ejecutan en dispositivos que no tengan hardware GPS. Si su aplicación requiere hardware GPS, debe agregar explícitamente un `android.hardware.location.gps` `uses-feature` elemento para el manifiesto de Android. Para obtener más información, vea el Android [usa la característica](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) referencia del elemento.

Para establecer los permisos, expanda el **propiedades** carpeta en el **panel de solución** y haga doble clic en **AndroidManifest.xml**. Los permisos se enumerará en **permisos necesarios**:

[![Captura de pantalla de la configuración de Android Manifest permisos necesarios](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

Al establecer alguno de estos permisos, se indica a Android que la aplicación necesita permiso para tener acceso a los proveedores de ubicación del usuario. Dispositivos que ejecutan el nivel de API 22 (Android 5.1) o inferior le preguntará al usuario que conceda estos permisos cada vez que se instala la aplicación. En los dispositivos con la API de nivel de 23 (Android 6.0) o versiones posteriores, la aplicación debe realizar una comprobación de permiso de tiempo de ejecución antes de realizar una solicitud del proveedor de ubicación. 

> [!NOTE]
>Nota: Establecer `ACCESS_FINE_LOCATION` implica el acceso a los datos de ubicación gruesa y fina. Nunca debería tener que establecer los permisos, solo el *mínimo* permiso la aplicación necesita para funcionar.

Este fragmento de código es un ejemplo de cómo comprobar que una aplicación tiene permiso para el `ACCESS_FINE_LOCATION` permiso:

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

Las aplicaciones deben ser tolerante a errores de la situación donde el usuario no conceder el permiso (o ha revocado el permiso) y tener una manera de tratar esta situación correctamente. Consulte la [Guía de permisos](~/android/app-fundamentals/permissions.md) para obtener más detalles sobre la implementación de permiso de tiempo de ejecución comprueba en Xamarin.Android.


## <a name="using-the-fused-location-provider"></a>Mediante el proveedor de ubicación fusionadas

El proveedor de ubicación fusionadas es la forma preferida para las aplicaciones de Android recibir actualizaciones de la ubicación del dispositivo porque eficazmente seleccionará el proveedor de ubicación durante el tiempo de ejecución para proporcionar la mejor información de ubicación de un modo eficaz de la batería. Por ejemplo, un usuario desplazarse al aire libre Obtiene la mejor ubicación leer con GPS. Si el usuario, a continuación, se describe en el interior, donde el GPS funciona mal (si en absoluto), el proveedor de ubicación fusionadas puede cambiar automáticamente a la red Wi-Fi, que funciona mejor en el interior.
 
El proveedor de ubicación fusionadas API proporciona una variedad de otras herramientas para impulsar aplicaciones con reconocimiento de ubicación, como el perímetro y supervisión de la actividad. En esta sección, nos vamos a centrar en los aspectos básicos de la configuración de la `LocationClient`, proveedores de establecer y obtener la ubicación del usuario.

El proveedor de ubicación fusionadas forma parte de [Google Play Services](https://developer.android.com/google/play-services/index.html).
El paquete de Google Play Services debe estar instalado y configurado correctamente en la aplicación para el proveedor de ubicación fusionadas API para trabajar, y el dispositivo debe tener el APK de Google Play Services instalado.

Antes de Xamarin.Android aplicación puede utilizar el proveedor de ubicación combinados, debe agregar el **Xamarin.GooglePlayServices.Maps** paquete al proyecto. Además, los siguientes `using` instrucciones deben agregarse a los archivos de origen que hacen referencia a las clases se describen a continuación:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Comprobando si Google Play Services está instalado

Se produciría un testamento Xamarin.Android bloqueo si intenta utilizar el proveedor de ubicación fusionadas cuando Google Play Services no está instalado (o actualizado), a continuación, una excepción en tiempo de ejecución.  Si Google Play Services no está instalado, a continuación, la aplicación debe retroceder al servicio de ubicación de Android se ha explicado anteriormente. Si Google Play Services no está actualizada, la aplicación podría mostrar un mensaje al usuario que se les solicita actualizar la versión instalada de Google Play Services.

Este fragmento de código es un ejemplo de cómo una actividad de Android puede comprobar mediante programación si Google Play Services está instalado:

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);
                  
        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

Para interactuar con el proveedor de ubicación de multiplicación, una aplicación de Xamarin.Android debe tener una instancia de la `FusedLocationProviderClient`. Esta clase expone los métodos necesarios para suscribirse a las actualizaciones de ubicación y recuperar la última ubicación conocida del dispositivo.

El `OnCreate` método de una actividad es un lugar adecuado para obtener una referencia a la `FusedLocationProviderClient`, tal y como se muestra en el siguiente fragmento de código:

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>Introducción a la última ubicación conocida

El `FusedLocationProviderClient.GetLastLocationAsync()` método proporciona una manera sencilla y sin bloqueo para una aplicación de Xamarin.Android obtener rápidamente la última ubicación conocida del dispositivo con la mínima sobrecarga de codificación.

Este fragmento de código muestra cómo usar el `GetLastLocationAsync` método para recuperar la ubicación del dispositivo:

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>Suscribirse a la ubicación de actualizaciones

Una aplicación de Xamarin.Android también puede suscribirse a las actualizaciones de la ubicación del proveedor de ubicación fusionadas utilizando el `FusedLocationProviderClient.RequestLocationUpdatesAsync` método, como se muestra en este fragmento de código:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
Este método toma dos parámetros:

-   **`Android.Gms.Location.LocationRequest`** &ndash; Un `LocationRequest` objeto es la forma en que una aplicación de Xamarin.Android pasa los parámetros en cómo debe funcionar el proveedor de ubicación fusionadas. El `LocationRequest` contiene información tal como la que se deben realizar solicitudes frecuentes o ¿qué tan importante que debe ser una actualización de la ubicación exacta. Por ejemplo, una solicitud de ubicación importante hará que el dispositivo para usar el GPS y por consiguiente más potencia, al determinar la ubicación. Este fragmento de código muestra cómo crear un `LocationRequest` para una ubicación con una gran precisión, comprobación aproximadamente cada cinco minutos durante una actualización de la ubicación (pero no antes de dos minutos entre las solicitudes). El proveedor de ubicación fusionadas usará un `LocationRequest` como orientación para qué proveedor de ubicación que se usará al intentar determinar la ubicación del dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; Para recibir actualizaciones de ubicación, una aplicación de Xamarin.Android debe subclase la `LocationProvider` clase abstracta. Esta clase expone dos métodos que quizás invocan por el proveedor de ubicación fusionadas para actualizar la aplicación con información de ubicación. Esto se explicará más detalladamente a continuación.

Para notificar a una aplicación de Xamarin.Android de una actualización de la ubicación, el proveedor de ubicación fusionadas invocará el `LocationCallBack.OnLocationResult(LocationResult result)`. El `Android.Gms.Location.LocationResult` parámetro contendrá la información de ubicación de actualización.

Cuando el proveedor de ubicación fusionadas detecta un cambio en la disponibilidad de datos de ubicación, llamará el `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` método. Si el `LocationAvailability.IsLocationAvailable` propiedad devuelve `true`, a continuación, se puede suponer que los resultados de la ubicación de dispositivo notifican por `OnLocationResult` son tan precisa y tan actualizado como lo requiera la `LocationRequest`. Si `IsLocationAvailable` es false, no hay resultados de la ubicación será devueltos por `OnLocationResult`.

Este fragmento de código es un ejemplo de implementación de la `LocationCallback` objeto:

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>Mediante la API de servicios de ubicación de Android

El servicio de ubicación de Android es una API más antigua con información de ubicación de Android. Datos de ubicación se recopilan por los sensores de hardware y recopilados por un servicio del sistema, que se obtiene acceso en la aplicación con un `LocationManager` clase y un `ILocationListener`.

El servicio de ubicación es ideal para las aplicaciones que deben ejecutarse en dispositivos que no tienen instalado Google Play Services.

El servicio de ubicación es un tipo especial de [servicio](https://developer.android.com/guide/components/services.html) administrados por el sistema. Un servicio de sistema interactúa con el hardware del dispositivo y está siempre en ejecución. Para aprovechar las actualizaciones de ubicación en nuestra aplicación, se suscribirá a actualizaciones de la ubicación del servicio de ubicación de sistema con un `LocationManager` y un `RequestLocationUpdates` llamar.

Para obtener la ubicación del usuario mediante el servicio de ubicación de Android implica varios pasos:

1.  Obtener una referencia a la `LocationManager` service.
2.  Implemente el `ILocationListener` interfaz y el controlador de eventos cuando cambia la ubicación.
3.  Use el `LocationManager` para actualizaciones de solicitud de ubicación de un proveedor especificado. El `ILocationListener` desde el paso anterior se usarán para recibir devoluciones de llamada de la `LocationManager`.
4.  Detener las actualizaciones de ubicación cuando la aplicación ya no es adecuada recibir actualizaciones.

### <a name="location-manager"></a>Administrador de ubicación

Podemos acceder al servicio de ubicación del sistema con una instancia de la `LocationManager` clase. `LocationManager` es una clase especial que nos permite interactuar con el servicio de ubicación del sistema y llamar a métodos en ella. Una aplicación puede obtener una referencia a la `LocationManager` mediante una llamada a `GetSystemService` y se pasa un tipo de servicio, tal como se muestra a continuación:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` es un buen lugar para obtener una referencia a la `LocationManager`.
Es una buena idea para mantener la `LocationManager` como una variable de clase, por lo que podemos llamarlo en varios puntos del ciclo de vida de actividad.

### <a name="request-location-updates-from-the-locationmanager"></a>Actualizaciones de solicitud de ubicación de la LocationManager

Una vez que la aplicación tiene una referencia a la `LocationManager`, debe indicar el `LocationManager` qué tipo de información de ubicación que son necesarios, y con qué frecuencia es esa información para actualizarse. Esto se realiza una llamada a `RequestLocationUpdates` en el `LocationManager` objeto y pasando algunos criterios para las actualizaciones y una devolución de llamada que recibirá las actualizaciones de ubicación. Esta devolución de llamada es un tipo que debe implementar la `ILocationListener` interfaz (que se describe con más detalle más adelante en esta guía).

El `RequestLocationUpdates` método indica la ubicación del sistema de servicio que desea que la aplicación empezar a recibir actualizaciones de ubicación. Este método permite especificar el proveedor, así como los umbrales de tiempo y la distancia para controlar la frecuencia de actualización. Por ejemplo, el método siguiente solicita la ubicación actualiza cada 2000 milisegundos, desde el proveedor de ubicación de GPS y solo cuando cambia la ubicación más de 1 metro:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Una aplicación debe solicitar actualizaciones de ubicación sólo tan a menudo como sea necesario para la aplicación funcionar bien. Esto conserva la duración de la batería y crea una mejor experiencia del usuario.

### <a name="responding-to-updates-from-the-locationmanager"></a>Responder a las actualizaciones desde el LocationManager

Una vez que una aplicación solicita actualizaciones desde el `LocationManager`, puede recibir información desde el servicio implementando la [ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/) interfaz. Esta interfaz proporciona cuatro métodos para realizar escuchas para el servicio de ubicación y el proveedor de ubicación, `OnLocationChanged`. El sistema llamará `OnLocationChanged` cuando la ubicación del usuario cambia suficiente para ser calificadas como un cambio de ubicación según los criterios establecidos cuando se solicitan actualizaciones de ubicación. 

El código siguiente muestra los métodos de la `ILocationListener` interfaz:

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>Cancelar la suscripción a las actualizaciones de LocationManager

Para ahorrar recursos del sistema, una aplicación debe cancelar la suscripción a las actualizaciones de ubicación tan pronto como sea posible. El `RemoveUpdates` método indica la `LocationManager` para dejar de enviar actualizaciones a nuestra aplicación.  Por ejemplo, puede llamar una actividad `RemoveUpdates` en el `OnPause` método para que podemos ahorrar energía si una aplicación no necesita actualizaciones de ubicación mientras su actividad no está en la pantalla:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Si la aplicación necesita obtener actualizaciones de ubicación mientras está en segundo plano, desea crear un servicio personalizado que se suscribe para el servicio de ubicación del sistema. Hacer referencia a la [procesamiento en segundo plano con servicios de Android](~/android/app-fundamentals/services/index.md) guía para obtener más información.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinar el mejor proveedor de ubicación para el LocationManager

La aplicación anterior establece GPS como el proveedor de ubicación. Sin embargo, GPS no estén disponibles en todos los casos, por ejemplo, si el dispositivo está en el interior o no tiene un receptor GPS. Si este es el caso, el resultado es un `null` devuelto para el proveedor.

Para obtener la aplicación funcione al GPS no está disponible, se utiliza el `GetBestProvider` método para pedir el proveedor de ubicación (compatible con el dispositivo y usuario habilitado) disponible mejor al iniciar la aplicación. En lugar de pasar de un proveedor específico, puede indicar a `GetBestProvider` los requisitos para el proveedor, como la precisión y power - con un [ `Criteria` objeto](https://developer.xamarin.com/api/type/Android.Locations.Criteria/). `GetBestProvider` Devuelve el proveedor mejor para los criterios proporcionados.

El código siguiente muestra cómo obtener el mejor proveedor disponible y usarlo cuando se solicitan actualizaciones de ubicación:

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
>  Si el usuario ha deshabilitado todos los proveedores de ubicación, `GetBestProvider` devolverá `null`. Para ver cómo funciona este código en un dispositivo real, asegúrese de habilitar el GPS, Wi-Fi y redes celulares en **Google configuración > ubicación > modo** tal como se muestra en esta captura de pantalla:

[![Pantalla de configuración de modo de ubicación en un teléfono Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

La captura de pantalla siguiente muestra la ubicación de aplicaciones está ejecutando mediante `GetBestProvider`:

[![Aplicación GetBestProvider mostrar latitud, longitud y proveedor](location-images/location-03.png)](location-images/location-03.png#lightbox)

Tenga en cuenta que `GetBestProvider` no cambia dinámicamente el proveedor. En su lugar, determina el mejor proveedor disponible una vez durante el ciclo de vida de actividad. Si el estado de proveedor cambia después de que se ha establecido, la aplicación requerirá código adicional en el `ILocationListener` métodos &ndash; `OnProviderEnabled`, `OnProviderDisabled`, y `OnStatusChanged` &ndash; para controlar todas las posibilidades relacionados con el conmutador de proveedor.

## <a name="summary"></a>Resumen

Esta guía trata sobre cómo obtener la ubicación del usuario con el servicio de ubicación de Android y el proveedor de ubicación fusionadas de API de servicios de ubicación de Google.


## <a name="related-links"></a>Vínculos relacionados

- [Ubicación (ejemplo)](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider (sample)](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Clase de criterios](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [Clase LocationManager](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [Clase LocationListener](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
