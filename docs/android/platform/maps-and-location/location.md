---
title: "Servicios de ubicación"
description: "Esta guía presenta reconocimiento de ubicación en aplicaciones de Android y muestra cómo obtener la ubicación del usuario mediante la API del servicio de ubicación Android, así como el proveedor de ubicación fusionadas disponible con la API de servicios de ubicación de Google."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 28cfb4cda27446b914c23a2ce4e8e9f845876107
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="location-services"></a>Servicios de ubicación

_Esta guía presenta reconocimiento de ubicación en aplicaciones de Android y muestra cómo obtener la ubicación del usuario mediante la API del servicio de ubicación Android, así como el proveedor de ubicación fusionadas disponible con la API de servicios de ubicación de Google._

## <a name="location-services-overview"></a>Información general de servicios de ubicación

Android proporciona acceso a diversas tecnologías de ubicación como la ubicación de la torre de celda, Wi-Fi y GPS. Los detalles de cada tecnología de ubicación se extraen a través de *proveedores de ubicación*, lo que permite a las aplicaciones obtener ubicaciones en la misma forma independientemente del proveedor usando. Esta guía presenta al proveedor de ubicación fusionadas, una parte de los servicios de Google Play, que determina la mejor manera de obtener la ubicación de los dispositivos en función de qué proveedores están disponibles y cómo se usa el dispositivo de manera inteligente. API de servicio de ubicación de Android y muestra cómo comunicarse con la ubicación del sistema de servicio que utiliza un `LocationManager`. La segunda parte de la guía explora la API de servicios de ubicación Android mediante el `LocationManager`.
 
Como regla general, deberían optar por las aplicaciones utilizar el proveedor de ubicación fusionadas, revirtiendo a la API de servicios de ubicación Android anterior solo cuando sea necesario.

## <a name="location-fundamentals"></a>Conceptos básicos de ubicación

En Android, no importa qué API que elija para trabajar con datos de ubicación, varios conceptos siguen siendo los mismos. Esta sección presentan los proveedores de ubicación y permisos relacionados con la ubicación.

### <a name="location-providers"></a>Proveedores de ubicación

Hay varias tecnologías se usan internamente para identificar la ubicación del usuario. El hardware utilizado depende del tipo de *proveedor de la ubicación* seleccionado para el trabajo de recopilación de datos. Android usa tres proveedores de ubicación:

-   **Proveedor de GPS** &ndash; GPS proporciona la ubicación más precisa, utiliza la máxima potencia y funciona mejor en el exterior. Este proveedor utiliza una combinación de GPS y GPS asistida ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS)), que devuelve datos GPS recopilados por torres de telefonía móviles.

-   **Proveedor de red** &ndash; proporciona una combinación de datos de Wi-Fi y red de telefonía móvil, incluidos aGPS recopilados por torres de telefonía móvil. Utiliza menos energía que el proveedor de GPS, pero devuelve datos de ubicación de precisión variable.

-   **Proveedor pasivo** &ndash; una opción "superpuesta" utilizar proveedores solicitados por otras aplicaciones o servicios para generar datos de ubicación en una aplicación. Esto es un menos confiable pero ideal de la opción de ahorro de energía para aplicaciones que no requieren actualizaciones de constante de la ubicación para que funcione.

Proveedores de ubicación no están siempre disponibles. Por ejemplo, que tengamos que deseamos utilizar GPS para nuestra aplicación, pero GPS podría estar desactivada en la configuración o el dispositivo no tenga GPS en absoluto. Si un proveedor concreto no está disponible, si se elige dicho proveedor podría devolver `null`.

### <a name="location-permissions"></a>Permisos de ubicación

Una aplicación con reconocimiento de ubicación debe tener acceso a los sensores de hardware del dispositivo para recibir datos móviles, Wi-Fi y GPS. El acceso se controla a través de los permisos adecuados en Android del manifiesto la aplicación.
Existen dos permisos &ndash; según los requisitos de la aplicación y la elección de API, desea permitir que uno:

-   `ACCESS_FINE_LOCATION` &ndash; Permite el acceso de una aplicación a GPS.
    Necesario para la *proveedor GPS* y *proveedor pasivo* opciones (*proveedor pasivo necesita permiso para tener acceso a datos GPS recopilados por otra aplicación o servicio*). Permiso opcional para la *proveedor de red*.

-   `ACCESS_COARSE_LOCATION` &ndash; Permite el acceso a una aplicación a la ubicación de red de telefonía móvil y Wi-Fi. Necesario para *proveedor de red* si `ACCESS_FINE_LOCATION` no se ha establecido.

Para las aplicaciones que tienen como destino la API versión 21 (Android 5.0 círculo) o versiones posteriores, puede habilitar `ACCESS_FINE_LOCATION` y todavía ejecutar en dispositivos que no tengan hardware GPS. Si su aplicación requiere hardware GPS, debe agregar explícitamente un `android.hardware.location.gps` `uses-feature` elemento para el manifiesto de Android. Para obtener más información, vea el Android [usa la característica](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) referencia del elemento.

Para establecer los permisos, expanda la **propiedades** carpeta en el **solución Pad** y haga doble clic en **AndroidManifest.xml**. Los permisos se enumerará en **permisos necesarios**:

[![Captura de pantalla de la configuración de Android manifiesto permisos necesarios](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

Al establecer alguno de estos permisos, se indica a Android que la aplicación necesita permiso para tener acceso a los proveedores de ubicación del usuario. Dispositivos que ejecutar API nivel 22 (Android 5.1) o inferior, pide al usuario se conceden estos permisos cada vez que se instala la aplicación. En dispositivos que ejecutan API nivel 23 (Android 6.0) o versiones posteriores, la aplicación debe realizar una comprobación de permiso de tiempo de ejecución antes de realizar una solicitud del proveedor de ubicación. 

> [!NOTE]
>Nota: Al establecer `ACCESS_FINE_LOCATION` implica el acceso tanto a ubicación general y correctamente los datos. Nunca debería ser necesario establecer los permisos, solo el *mínimo* permiso la aplicación necesita para funcionar.

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

Aplicaciones deben tolerante a errores de la situación donde el usuario no concederá permiso (o revoca el permiso) y tienen una manera tratar correctamente con esa situación. Vea la [Guía de permisos](~/android/app-fundamentals/permissions.md) para obtener más detalles sobre la implementación de permiso en tiempo de ejecución comprueba en Xamarin.Android.


## <a name="using-the-fused-location-provider"></a>Mediante el proveedor de ubicación fusionadas

El proveedor de ubicación fusionadas es la mejor manera para aplicaciones Android recibir actualizaciones de la ubicación del dispositivo porque eficazmente seleccionará el proveedor de la ubicación en tiempo de ejecución para proporcionar la mejor información de ubicación de un modo eficaz de batería. Por ejemplo, un usuario desplazamientos al aire libre Obtiene la mejor ubicación leyendo con GPS. Si el usuario, a continuación, se explica en el interior, donde GPS funciona mal (si en absoluto), el proveedor de ubicación fusionadas puede cambiar automáticamente a la red Wi-Fi, que funciona mejor en el interior.
 
La API del proveedor de ubicación fusionadas proporciona una variedad de otras herramientas para permitir que las aplicaciones con reconocimiento de ubicación, incluidos el perímetro y la supervisión de la actividad. En esta sección, vamos a foco los conceptos básicos de la configuración de la `LocationClient`, establecimiento de proveedores y se obtiene la ubicación del usuario.

El proveedor de ubicación fusionadas forma parte de [servicios de Google Play](http://developer.android.com/google/play-services/index.html). El paquete de servicios de Google Play debe estar instalado y configurado correctamente en la aplicación para el proveedor de ubicación fusionadas API para trabajar y el dispositivo debe tener el APK de Google Play Services instalado.

Antes de un Xamarin.Android aplicación puede utilizar el proveedor de ubicación combinados, debe agregar el **Xamarin.GooglePlayServices.Maps** al proyecto.

### <a name="checking-if-google-play-services-is-installed"></a>Comprobando si está instalado servicios de Google Play

Se produciría una obtendrá Xamarin.Android un bloqueo si intenta usar el proveedor de ubicación fusionadas cuando no está instalado servicios de Google Play (o no está actualizada), a continuación, una excepción en tiempo de ejecución.  Si no está instalado servicios de Google Play, a continuación, la aplicación debe recurrir al servicio de ubicación Android descritos anteriormente. Si Servicios de Google Play no está actualizado, la aplicación podría mostrar un mensaje al usuario que se les solicita que actualice la versión instalada de servicios de Google Play.

Este fragmento de código es un ejemplo de cómo una actividad Android puede comprobar mediante programación si está instalado servicios de Google Play:

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

Para interactuar con el proveedor de ubicación fusionadas, una aplicación de Xamarin.Android debe tener una instancia de la `FusedLocationProviderClient`. Esta clase expone los métodos necesarios para suscribirse a las actualizaciones de la ubicación y recuperar la última ubicación conocida del dispositivo.

El `OnCreate` método de una actividad es un lugar adecuado para obtener una referencia a la `FusedLocationProviderClient`, como se muestra en el fragmento de código siguiente:

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

### <a name="getting-the-last-known-location"></a>Obtener la última ubicación conocida

El `FusedLocationProviderClient.GetLastLocationAsync()` método proporciona una manera sencilla y sin bloqueo para una aplicación Xamarin.Android obtener rápidamente la última ubicación conocida del dispositivo con la mínima sobrecarga de codificación.

Este fragmento de código muestra cómo utilizar el `GetLastLocationAsync` método para recuperar la ubicación del dispositivo:

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

-   **`Android.Gms.Location.LocationRequest`** &ndash; Un `LocationRequest` objeto es la forma en que una aplicación Xamarin.Android pasa los parámetros en funcionamiento del proveedor de ubicación combinados. El `LocationRequest` contiene información tal como cómo deben realizarse solicitudes frecuentes o la importancia que debe ser una actualización de la ubicación exacta. Por ejemplo, una solicitud de ubicación importante hará que el dispositivo que se usará el GPS y, por consiguiente, más potencia, al determinar la ubicación. Este fragmento de código muestra cómo crear un `LocationRequest` para una ubicación con una gran precisión, comprobación aproximadamente cada cinco minutos para una actualización de la ubicación (pero no antes de dos minutos entre las solicitudes). El proveedor de ubicación fusionadas usará un `LocationRequest` como guía para qué proveedor de ubicación que se utilizará al intentar para determinar la ubicación del dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; Para recibir actualizaciones de ubicación, una aplicación de Xamarin.Android debe subclase la `LocationProvider` clase abstracta. Esta clase expone dos métodos que puede ser invocan por el proveedor de ubicación fusionadas para actualizar la aplicación con información de ubicación. Esto se tratarán con más detalle a continuación.

Para notificar a una aplicación Xamarin.Android de una actualización de la ubicación, el proveedor de ubicación fusionadas invocará la `LocationCallBack.OnLocationResult(LocationResult result)`. El `Android.Gms.Location.LocationResult` parámetro contendrá la información de ubicación de actualización.

Cuando el proveedor de ubicación fusionadas detecta un cambio en la disponibilidad de los datos de ubicación, llamará a la `LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)` método. Si el `LocationAvailability.IsLocationAvailable` propiedad devuelve `true`, a continuación, se puede suponer que los resultados de la ubicación de dispositivo notifican por `OnLocationResult` son lo más preciso y como actualizado según sea necesario por la `LocationRequest`. Si `IsLocationAvailable` es false, no hay resultados de ubicación será devueltos por `OnLocationResult`.

Este fragmento de código es una implementación de ejemplo de la `LocationCallback` objeto:

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

El servicio de ubicación Android es una API anterior para usar la información de ubicación en Android. Datos de ubicación se recopilan por los sensores de hardware y recopilados por un servicio del sistema, que se obtiene acceso en la aplicación con un `LocationManager` clase y un `ILocationListener`.

El servicio de ubicación es ideal para las aplicaciones que se deben ejecutar en los dispositivos que no tiene instalados los servicios de Google Play.

El servicio de ubicación es un tipo especial de [servicio](http://developer.android.com/guide/components/services.html) administrados por el sistema. Un servicio de sistema interactúa con el hardware del dispositivo y está siempre se está ejecutando. Para aprovechar las actualizaciones de la ubicación en nuestra aplicación, se suscribirá a actualizaciones de la ubicación desde el servicio de ubicación de sistema mediante un `LocationManager` y un `RequestLocationUpdates` llamar.

Para obtener la ubicación del usuario mediante el servicio de ubicación Android implica varios pasos:

1.  Obtener una referencia a la `LocationManager` servicio.
2.  Implemente el `ILocationListener` interfaz y controlar eventos cuando cambia la ubicación.
3.  Use el `LocationManager` a las actualizaciones de la ubicación de solicitud para un proveedor especificado. El `ILocationListener` en el paso anterior se usarán para recibir devoluciones de llamada desde el `LocationManager`.
4.  Detenga las actualizaciones de ubicación cuando la aplicación ya no es adecuado recibir actualizaciones.

### <a name="location-manager"></a>Administrador de ubicación

Se puede tener acceso al servicio de ubicación de sistema con una instancia de la `LocationManager` clase. `LocationManager` es una clase especial que nos permite interactuar con el servicio de ubicación del sistema y llamar a métodos de ella. Una aplicación puede obtener una referencia a la `LocationManager` mediante una llamada a `GetSystemService` y pasar un tipo de servicio, tal y como se muestra a continuación:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` es un buen lugar para obtener una referencia a la `LocationManager`.
Es una buena idea para mantener la `LocationManager` como una variable de clase, por lo que podemos llamarlo en diversos puntos en el ciclo de vida de actividad.

### <a name="request-location-updates-from-the-locationmanager"></a>Actualizaciones de la ubicación de solicitud desde el LocationManager

Una vez que la aplicación tiene una referencia a la `LocationManager`, debe indicar el `LocationManager` qué tipo de información de ubicación que se necesitan, y la frecuencia con esa información para actualizarse. Esto se realiza una llamada a `RequestionLocationUpdates` en la `LocationManager` objeto y pasando algunos criterios para las actualizaciones y una devolución de llamada que va a recibir las actualizaciones de ubicación. Esta devolución de llamada es un tipo que debe implementar la `ILocationListener` interfaz (que se describe con más detalle más adelante en esta guía).

El `RequestionLocationUpdates` método indica la ubicación del sistema de servicio que desea que la aplicación empezar a recibir actualizaciones de ubicación. Este método permite especificar el proveedor, así como los umbrales de tiempo y distancia para controlar la frecuencia de actualización. Por ejemplo, el método siguiente a continuación de la ubicación de las solicitudes actualiza desde el proveedor de ubicación GPS cada 2000 milisegundos, y sólo cuando la ubicación cambia más de 1 metro:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Una aplicación debe solicitar actualizaciones de ubicación sólo con tanta frecuencia como sea necesario para que la aplicación funciona correctamente. Esto conserva la duración de la batería y crea una mejor experiencia para el usuario.

### <a name="responding-to-updates-from-the-locationmanager"></a>Responder a las actualizaciones desde el LocationManager

Una vez que una aplicación solicita actualizaciones desde la `LocationManager`, pueda recibir información desde el servicio implementando la [ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/) interfaz. Esta interfaz proporciona cuatro métodos para realizar escuchas para la ubicación del servicio y el proveedor de la ubicación, `OnLocationChanged`. El sistema llamará `OnLocationChanged` cuando la ubicación del usuario cambie lo suficiente como para ser calificada como un cambio de ubicación según los criterios establecidos cuando se solicitan las actualizaciones de ubicación. 

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

Para ahorrar recursos del sistema, una aplicación debe cancelar la suscripción a las actualizaciones de ubicación tan pronto como sea posible. El `RemoveUpdates` método indica la `LocationManager` para dejar de enviar actualizaciones a la aplicación.  Por ejemplo, puede llamar una actividad `RemoveUpdates` en el `OnPause` método para que se pueden conservar la energía si una aplicación no necesita actualizaciones de ubicación mientras su actividad no está en la pantalla:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Si la aplicación necesita obtener actualizaciones de ubicación mientras está en segundo plano, desea crear un servicio personalizado que se suscribe para el servicio de ubicación del sistema. Hacer referencia a la [Backgrounding con servicios Android](~/android/app-fundamentals/services/index.md) guía para obtener más información.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinar el mejor proveedor de ubicación para el LocationManager

La aplicación anterior establece GPS como el proveedor de ubicación. Sin embargo, GPS no estén disponibles en todos los casos, como por ejemplo, si el dispositivo está en el interior o no tiene un receptor GPS. Si este es el caso, el resultado es un `null` devuelto para el proveedor.

Para obtener la aplicación para que funcione cuando GPS no está disponible, se utiliza el `GetBestProvider` método para solicitar el mejor proveedor de ubicación (dispositivo compatible y usuario habilitado) disponible al iniciar la aplicación. En lugar de pasar de un proveedor concreto, puede indicar `GetBestProvider` los requisitos para el proveedor, como precisión y power - con un [ `Criteria` objeto](https://developer.xamarin.com/api/type/Android.Locations.Criteria/). `GetBestProvider` Devuelve el proveedor recomendado para los criterios proporcionados.

El código siguiente muestra cómo obtener el mejor proveedor disponible y usarlo cuando se solicitan las actualizaciones de ubicación:

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
>  Si el usuario ha deshabilitado todos los proveedores de ubicación, `GetBestProvider` devolverá `null`. Para ver cómo funciona este código en un dispositivo real, no olvide habilitar GPS, Wi-Fi y redes de telefonía móviles en **configuración de Google > ubicación > modo** tal y como se muestra en esta captura de pantalla:

[![Pantalla de configuración de modo de ubicación en un teléfono Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

La captura de pantalla siguiente muestra la aplicación ubicación en ejecución mediante `GetBestProvider`:

[![Aplicación de GetBestProvider mostrar latitud, longitud y proveedor](location-images/location-03.png)](location-images/location-03.png#lightbox)

Tenga en cuenta que `GetBestProvider` no cambia el proveedor de forma dinámica. En su lugar, determina el mejor proveedor disponible una vez durante el ciclo de vida de actividad. Si el estado de proveedor cambia después de que se ha establecido, la aplicación requerirá código adicional en el `ILocationListener` métodos &ndash; `OnProviderEnabled`, `OnProviderDisabled`, y `OnStatusChanged` &ndash; para controlar todas las posibilidades relacionadas con la conmutador de proveedor.

## <a name="summary"></a>Resumen

Esta guía trata cómo obtener la ubicación del usuario con el servicio de ubicación de Android y el proveedor de ubicación fusionadas a través de API de servicios de ubicación de Google.


## <a name="related-links"></a>Vínculos relacionados

- [Ubicación (ejemplo)](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider (ejemplo)](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Servicios de Google Play](http://developer.android.com/google/play-services/index.html)
- [Clase de criterios](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [Clase LocationManager](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [Clase LocationListener](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](http://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](http://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
