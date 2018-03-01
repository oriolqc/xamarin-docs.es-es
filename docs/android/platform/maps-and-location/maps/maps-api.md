---
title: API de mapas
ms.topic: article
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: eddd723c07919db4749c63c5b4f1d05e9be81022
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="maps-api"></a>API de mapas

Mediante la aplicación de asignaciones es grande, pero a veces van a incluir mapas directamente en la aplicación. Además de los integrados asigna aplicaciones, Google también ofrece un [API de asignación nativo para Android](https://developers.google.com/maps/documentation/android/).
La API de mapas es adecuada para los casos donde desea mantener un mayor control sobre la experiencia de asignación. Cosas que son posibles con la API de Maps incluyen:

-  Cambiar mediante programación el punto de vista del mapa.
-  Agregar y personalizar los marcadores.
-  Anotar un mapa con superposiciones.

A diferencia de la v1 de API de Google Maps Android ahora desusado, API de Android de Google Maps v2 forma parte de [servicios de Google Play](http://developer.android.com/google/play-services/index.html).
Por lo tanto, es necesario cumplir algunos requisitos previos obligatorios antes de que se puede usar la API de Android de Google Maps en una aplicación Xamarin.Android.

<a name="Configuring_Maps_API_Prerequisites" />

## <a name="google-maps-api-prerequisites"></a>Requisitos previos de la API de mapas de Google

Varios elementos deben configurarse para poder usar la API de mapas, incluidos:

-  Instalar el SDK de servicios de Google Play
-  Crear un emulador con las APIs de Google
-  Obtener una clave de API de mapas
-  Especifique los permisos necesarios


<a name="Google_APIs_Add-On" />

### <a name="install-the-google-play-services-sdk"></a>Instalar el SDK de servicios de Google Play

Servicios de Google Play es una tecnología de Google que permite a las aplicaciones Android aprovechar las ventajas de las diversas características de Google como Google +, en la aplicación de facturación y mapas. Estas características son accesibles en dispositivos Android como servicios en segundo plano que se encuentran en el [APK de Google Play Services](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Las aplicaciones de Android interactúan con servicios de Google Play a través de la biblioteca de cliente de servicios de Google Play. Esta biblioteca contiene las interfaces y clases para los servicios individuales como las asignaciones. El siguiente diagrama muestra la relación entre una aplicación de Android y servicios de Google Play:

![Diagrama que ilustra el APK de servicios de Google reproducir la actualización de Google Play Store](maps-api-images/play-services-diagram.png)

La API de mapas de Android se proporciona como parte de servicios de Google Play.
Antes de que una aplicación de Xamarin.Android puede utilizar la API de mapas, el SDK de servicios de Google Play debe estar instalado y enlazado. El SDK de servicios de Google Play se instala a través del Administrador de SDK de Android. Captura de pantalla siguiente muestra dónde en el Administrador de Android SDK se encuentra el cliente de servicios de Google Play:

![Servicios de Google Play aparece bajo adicionales en el Administrador de SDK de Android](maps-api-images/image01.png)

> [!NOTE]
> **Nota:** los servicios de Google Play APK es un producto con licencia que no estén presenten en todos los dispositivos. Si no está instalado, Google Maps no funcionará en el dispositivo.

<a name="Binding_Google_Play_Services" />

#### <a name="binding-google-play-services"></a>Enlace de Google Play Services

Una vez que la biblioteca de cliente de servicios de Google Play está instalada, debe estar enlazado una biblioteca de enlace Xamarin.Android Java. Hay dos maneras para lograr esto:

-  **Usa el paquete NuGet de mapas de servicios de reproducción de Google** -éste es el enfoque más sencillo (disponible solo en 4.8 Xamarin.Android o superior).
   Instalar el [Xamarin Google Play Services mapas NuGet](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps); esta acción también instalará los paquetes de dependencia de servicios de Google Play.
   El resto de esta guía se centra en este enfoque.

-  **Enlazar manualmente la biblioteca de cliente de servicios de Google Play** -éste es un enfoque más complejo y es la única manera de Xamarin.Android 4.4 o Xamarin.Android 4.6 enlazar el SDK de servicios de Google Play.
   Enlazar manualmente la biblioteca de cliente de servicios de Google Play queda fuera del ámbito de este documento, pero pueden encontrar un ejemplo de cómo hacer esto en el [mapas y demostración de ubicación v3 ejemplo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3) en Github.

<a name="Adding_the_Google_Play_Services_Component" />

#### <a name="adding-the-google-play-services-map-package"></a>Agregar el paquete de mapa de Google Play Services

Para agregar el paquete de Google Play Services Map, haga clic con el **referencias** carpeta del proyecto en el Explorador de soluciones y haga clic en **administrar paquetes de NuGet...** :

![Elemento de menú contextual que muestra el Explorador de soluciones de administrar paquetes de NuGet en referencias](maps-api-images/image02.png)

Se abrirá la **Administrador de paquetes de NuGet**. Haga clic en **examinar** y escriba **Xamarin Google Play Services Maps** en el campo de búsqueda. Seleccione **Xamarin.GooglePlayServices.Maps** y haga clic en **instalar**. (Si este paquete se hubiera instalado previamente, haga clic en **actualización**.):

[![Administrador de paquetes de NuGet con paquete Xamarin.GooglePlayServices.Maps seleccionado](maps-api-images/image03-sml.png)](maps-api-images/image03.png)

Tenga en cuenta que también están instalados los siguientes paquetes de dependencia:

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**


<a name="Creating_an_Emulator_with_Google_APIs" />

### <a name="create-an-emulator-with-google-apis"></a>Crear un emulador con API de Google

Aunque no se recomienda, es posible configurar el emulador para admitir la API de mapas de Android. El emulador debe configurarse para tener como destino el 17 de nivel de API de Google (Android 4.2.2) o superior. En la captura de pantalla siguiente, se configura una imagen del emulador de nivel de API 19: 

![Administrador del emulador de Android con un AVD configurado para el nivel de API 19](maps-api-images/image04.png)


<a name="apikey" />

### <a name="obtain-a-google-maps-api-key"></a>Obtener una clave de API de mapas de Google

El último paso es obtener una clave de API de Google Maps (tenga en cuenta que no se puede volver a usar una clave de API de heredado v1 de Google Maps). Para obtener información acerca de cómo obtener y usar la clave de API con Xamarin.Android, consulte [obtener la clave de API de A Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
 

<a name="Specify_Permissions" />

### <a name="specify-the-required-permissions"></a>Especifique los permisos necesarios

Deben especificar los siguientes permisos en la **AndroidManifest.XML** para la API de Android de Google Maps:

-  **Acceso al estado de red** &ndash; la API de mapas debe ser capaz de comprobar si pueden descargar los mosaicos de mapa.

-  **Acceso a Internet** &ndash; acceso a Internet es necesario descargar los mosaicos de mapa y comunicarse con los servidores de Google Play para el acceso de API.

-  **OpenGL ES v2** &ndash; la aplicación debe declarar el requisito de OpenGL ES v2.

-  **Clave de API de Google Maps** &ndash; clave de la API se usa para confirmar que la aplicación está registrada y autorización para usar servicios de Google Play. Vea [obtener una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) para obtener más información acerca de esta clave.

-  **Escribir en un almacenamiento externo** &ndash; la API de Android de mapas de Google se almacenarán en caché descargados mosaicos en un almacenamiento externo.

-  **Acceso a los servicios basados en Web de Google** &ndash; la aplicación necesita permisos para tener acceso a los servicios web de Google que respaldan la API de mapas de Android.

-  **Permisos para las notificaciones de Google Play Services** &ndash; la aplicación debe tener permiso para recibir notificaciones remotas de servicios de Google Play.

-  **Acceso a los proveedores de ubicación** &ndash; se trata de permisos opcionales.
   Permitirá la `GoogleMap` clase para mostrar la ubicación del dispositivo en el mapa.


El fragmento de código siguiente es un ejemplo de la configuración que debe agregarse a **AndroidManifest.XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="14" android:targetSdkVersion="17" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- We need to be able to download map tiles and access Google Play Services-->
    <uses-permission android:name="android.permission.INTERNET" />

    <!-- Allow the application to access Google web-based services. -->
    <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />

    <!-- Google Maps for Android v2 will cache map tiles on external storage -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <!-- Google Maps for Android v2 needs this permission so that it may check the connection state as it must download data -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- Permission to receive remote notifications from Google Play Services -->
    <!-- Notice here that we have the package name of our application as a prefix on the permissions. -->
    <uses-permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" />
    <permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" android:protectionLevel="signature" />

    <!-- These are optional, but recommended. They will allow Maps to use the My Location provider. -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />


    <application android:label="@string/app_name">
        <!-- Put your Google Maps V2 API Key here. -->
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
    </application>
</manifest>
```

<a name="The_GoogleMap" />

## <a name="the-googlemap-class"></a>La clase GoogleMap

Una vez que se ocupa de los requisitos previos de, es el momento de empezar a desarrollar la aplicación y usar las API de mapas de Android. El [GoogleMap](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap) clase es la API principal que usará una aplicación Xamarin.Android para mostrar e interactuar con un Google Maps para Android. Esta clase tiene las siguientes responsabilidades:

-  Interactuar con los servicios de Google Play para autorizar la aplicación con el servicio web de Google.

-  Descargar, almacenamiento en caché y mostrar los iconos de mapa.

-  Mostrar controles de interfaz de usuario, como realizar una panorámica y zoom para el usuario.

-  Marcadores de dibujo y formas geométricas en mapas.

El `GoogleMap` se agrega a una actividad en uno de dos maneras:

-  **MapFragment** - la [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html) es un fragmento especializado que actúa como host para la `GoogleMap` objeto. El `MapFragment` requiere el nivel de API de Android 12 o superior.
   Pueden usar las versiones anteriores de Android el [SupportMapFragment](http://developer.android.com/reference/com/google/android/gms/maps/SupportMapFragment.html).

-  **MapView** - la [MapView](https://developer.xamarin.com/api/type/Android.GoogleMaps.MapView/) es una subclase especializada de vista que puede actuar como un host para un `GoogleMap` objeto. Los usuarios de esta clase deben reenviar todos los métodos del ciclo de vida de actividad a la `MapView` clase.

Cada uno de estos contenedores exponen una `Map` propiedad que devuelve una instancia de `GoogleMap`. Preferencia se debe conceder a la [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html) clase ya que es una API más sencilla que reduce el código reutilizable de cantidad que un desarrollador debe implementar de forma manual.

<a name="Adding_GoogleMap_To_An_Activity" />

### <a name="adding-a-mapfragment-to-an-activity"></a>Agregar una MapFragment a una actividad

Captura de pantalla siguiente es un ejemplo de una forma muy sencilla `MapFragment`:

[![Captura de pantalla de un dispositivo que se muestra un fragmento de asignación](maps-api-images/image05-sml.png)](maps-api-images/image05.png)

Similar a otras clases de fragmento, hay dos maneras de agregar esta `MapFragment` a una actividad:

-   **Mediante declaración** : el `MapFragment` se pueden agregar mediante el archivo de diseño XML para la actividad. El fragmento XML siguiente muestra un ejemplo de cómo usar el `fragment` elemento:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **Mediante programación** - la `MapFragment` pueden agregarse mediante programación tal como se describe a continuación.

Para agregar mediante programación un `MapFragment`, debe implementar la actividad de la `IOnMapReadyCallback` interfaz. Dado que la inicialización de un `GoogleMap` objeto puede tardar algún tiempo en completarse como la API se comunica con Google Play, debe proporcionar una devolución de llamada que notifica a la aplicación cuando el `GoogleMap` está listo.

En primer lugar, agregue `IOnMapReadyCallback` a la `Activity` declaración de clase.
Por ejemplo:

```csharp
public class MapWithMarkersActivity : Activity, IOnMapReadyCallback
```

Después, en la `OnCreate` método, agregar el `MapFragment` tal como se muestra en el ejemplo de código siguiente (el `GoogleMapOptions` clase se explica más adelante en esta guía):

```csharp
_mapFragment = FragmentManager.FindFragmentByTag("map") as MapFragment;
if (_mapFragment == null)
{
    GoogleMapOptions mapOptions = new GoogleMapOptions()
        .InvokeMapType(GoogleMap.MapTypeSatellite)
        .InvokeZoomControlsEnabled(false)
        .InvokeCompassEnabled(true);

    FragmentTransaction fragTx = FragmentManager.BeginTransaction();
    _mapFragment = MapFragment.NewInstance(mapOptions);
    fragTx.Add(Resource.Id.map, _mapFragment, "map");
    fragTx.Commit();
}
_mapFragment.GetMapAsync(this);
```

A `GoogleMap` deben adquirirse mediante `GetMapAsync`, tal y como se muestra al final del ejemplo de código anterior &ndash; Esto inicializa automáticamente el sistema de asignaciones y la vista. (Tenga en cuenta que este método no usa `await` / `async` semántica &ndash; la `Async` comportamiento se implementa por Android.) Cuando el `GoogleMap` objeto esté listo, la aplicación llama a Android `OnMapReady` método (que se debe implementar como parte de la `IOnMapReadyCallback` interfaz). Por ejemplo:

```csharp
public void OnMapReady (GoogleMap map)
{
    _map = map;
}
```

En el ejemplo de código anterior, el `OnMapReady` devolución de llamada inicializa el `_map` variable creado `GoogleMap` objeto.

Como ejemplo de cómo utilizar este resultado, cuando `OnResume` es llama, puede comprobar para ver si `_map` es distinto de null. Si `_map` está establecido en un `GoogleMap` objeto, `OnResume` puede llamar a métodos en ella para agregar marcadores y mover su cámara a una longitud especificada y la latitud. Para obtener un ejemplo de código completo, vea [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo).


<a name="Map_Types" />

### <a name="map-types"></a>Tipos de mapa

Hay cinco tipos diferentes de asignaciones disponibles a través de la API de Google asigna:

-  **Normal** -éste es el tipo de mapa de forma predeterminada. Muestra carreteras e importantes características naturales junto con algunos puntos artificiales de interés (por ejemplo, edificios y puentes).

-  **Satélite** -este mapa muestra la fotografía de satélite.

-  **Híbrido** : este mapa muestra la fotografía de satélite y asigna la carretera.

-  **Terreno** -principalmente muestra características topográficas con algunos carreteras.

-  **Ninguno** -esta asignación no carga los mosaicos, se representa como una cuadrícula vacía.


La imagen siguiente muestra tres de los distintos tipos de mapas, de izquierda a derecha (normal, híbrido, terreno):

[![Tres asignan capturas de pantalla de ejemplo: Normal, híbrida y el terreno](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png)

El `GoogleMap.MapType` propiedad se utiliza para establecer o cambiar se muestra qué tipo de mapa. El siguiente fragmento de código muestra cómo mostrar un mapa de satélite.

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MapType = GoogleMap.MapTypeSatellite;
}
```

<a name="GoogleMap_Properties" />

### <a name="googlemap-properties"></a>Propiedades de GoogleMap

`GoogleMap` define varias propiedades que pueden controlar la funcionalidad y la apariencia del mapa. Una manera de configurar el estado inicial de un `GoogleMap` consiste en pasar un [GoogleMapOptions](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMapOptions.html) objeto cuando se crea un `MapFragment`. El siguiente fragmento de código es un ejemplo del uso de un `GoogleMapOptions` objeto cuando se crea un `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
_mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, _mapFragment, "map");
fragTx.Commit();
```

La otra forma de configurar un `GoogleMap` objeto es estableciendo valores de la [UiSettings](http://developer.android.com/reference/com/google/android/gms/maps/UiSettings.html) propiedad del objeto map. El ejemplo de código siguiente muestra cómo configurar un `GoogleMap` para mostrar los controles de zoom y una brújula:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.UiSettings.ZoomControlsEnabled = true;
    _map.UiSettings.CompassEnabled = true;
}
```

<a name="Interacting_with_the_Map" />

## <a name="interacting-with-the-map"></a>Interactuar con la asignación

La API de mapas Android proporciona API que permite a una actividad cambiar el punto de vista, agregar marcadores, coloque superposiciones personalizadas o dibujar formas geométricas. En esta sección se describe cómo realizar algunas de estas tareas en Xamarin.Android.

### <a name="changing-the-viewpoint"></a>Cambiar el punto de vista

Mapas son modelados como un plano sin formato en la pantalla, en función de la proyección Mercator. La vista del mapa es el de un *cámara* aspecto hacia abajo en este plano. Cambiar la ubicación, zoom, inclinación y afecta puede controlar la posición de la cámara. El [CameraUpdate](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdate) clase se utiliza para mover la ubicación de la cámara. `CameraUpdate` objetos no haya instancias directamente, en su lugar la API de mapas proporciona los [CameraUpdateFactory](http://developer.android.com/reference/com/google/android/gms/maps/CameraUpdateFactory.html) clase.

Una vez un `CameraUpdate` ha creado un objeto, que se pasa como un parámetro a cualquiera la [GoogleMap.MoveCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#moveCamera(com.google.maps.CameraUpdate)) o [GoogleMap.AnimateCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#animateCamera(com.google.maps.CameraUpdate)) métodos. El `MoveCamera` método actualiza el mapa al instante durante la `AnimateCamera` método proporciona una transición suave y animada.

Este fragmento de código es un ejemplo sencillo de cómo usar el `CameraUpdateFactory` para crear un `CameraUpdate` el nivel de zoom del mapa que incrementará en uno:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

La API de mapas proporciona un [CameraPosition](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) que realizará la agregación de todos los valores posibles para la posición de la cámara. Una instancia de esta clase se puede proporcionar para la [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition(com.google.android.gms.maps.model.CameraPosition)) método que devolverá una `CameraUpdate` objeto. La API de mapas también incluye el [CameraPosition.Builder](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) clase que proporciona una API fluida para crear `CameraPosition` objetos.
El fragmento de código siguiente muestra un ejemplo de cómo crear un `CameraUpdate` desde una `CameraPosition` y que usa para cambiar la posición de la cámara en un `GoogleMap`:

```csharp
LatLng location = new LatLng(50.897778, 3.013333);
CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
builder.Target(location);
builder.Zoom(18);
builder.Bearing(155);
builder.Tilt(65);
CameraPosition cameraPosition = builder.Build();
CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(cameraUpdate);
}
```

En el fragmento de código anterior, una ubicación específica de la asignación se representa mediante el un [LatLng](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/LatLng) clase. El nivel de zoom se establece a 18. La relación es la medida brújula las agujas del reloj desde el norte. La inclinación propiedad controla el ángulo de visión y especifica un ángulo de 25 grados respecto a la vertical. La siguiente captura de pantalla muestra la `GoogleMap` después de ejecutar el código anterior:

[![Ángulo de visualización de mapa de Google de ejemplo que muestra una ubicación especificada con un superpuesto](maps-api-images/image06-sml.png)](maps-api-images/image06.png)

<a name="Adding_Overlays_to_a_Map" />

### <a name="drawing-on-the-map"></a>Dibujo del mapa

La API de mapas Android proporciona la API para dibujar los elementos siguientes en un mapa:

-  **Marcadores** : estos son los iconos especiales que se usan para identificar una sola ubicación en un mapa.

-  **Superposiciones** -se trata de una imagen que puede usarse para identificar una colección de ubicaciones o área en el mapa.

-  **Círculos, líneas y polígonos** -se trata de API que permiten a las actividades agregar formas a un mapa.

<a name="markers" />

#### <a name="markers"></a>Markers

La API de mapas proporciona un [marcador](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker) clase que encapsula todos los datos acerca de una única ubicación en un mapa. De forma predeterminada utiliza un icono estándar proporcionado por Google Maps. Es posible personalizar el aspecto de un marcador y responder a clics del usuario.

<a name="AddingAMarker" />

##### <a name="adding-a-marker"></a>Agregar un marcador

Para agregar un marcador a un mapa, es necesario crear un nuevo [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) objeto y, a continuación, llame a la [AddMarker](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker(com.google.android.gms.maps.model.MarkerOptions)) método en un `GoogleMap` instancia. Este método devolverá un [marcador](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker) objeto.

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    _map.AddMarker(marker1);
}
```

Se mostrará el título del marcador en un *ventana información* cuando el usuario puntea en el marcador. Captura de pantalla siguiente muestra el aspecto de este marcador:

[![Ejemplo de asignación de Google con un marcador y una ventana de información para Vimy Ridge](maps-api-images/image07-sml.png)](maps-api-images/image07.png)

<a name="Customizing_A_Marker" />

##### <a name="customizing-a-marker"></a>Personalizar un marcador

Es posible personalizar el icono utilizado por el marcador mediante una llamada a la `MarkerOptions.InvokeIcon` método al agregar el marcador al mapa.
Este método toma un [BitmapDescriptor](http://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptor.html) objeto que contiene los datos necesarios para representar el icono. El [BitmapDescriptorFactory](https://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory.html) clase proporciona algunos métodos auxiliares para simplificar la creación de un `BitmapDescriptor`. En la lista siguiente presenta algunos de estos métodos:

-   `DefaultMarker(float colour)` &ndash; Utilice el marcador de Google Maps predeterminado, pero cambie el color.

-   `FromAsset(string assetName)` &ndash; Utilizar un icono personalizado desde el archivo especificado en la carpeta de activos.

-   `FromBitmap(Bitmap image)` &ndash; Use el mapa de bits especificado como icono.

-   `FromFile(string fileName` &ndash; Crear el icono personalizado desde el archivo en la ruta de acceso especificada.

-   `FromResource(int resourceId)` &ndash; Crear un icono personalizado del recurso especificado.

El fragmento de código siguiente muestra un ejemplo de cómo crear un marcador de predeterminado coloreada cian:

```csharp
mapFrag.GetMapAsync(this);
...
if (_map != null)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    markerOpt1.InvokeIcon(BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan));
    _map.AddMarker(marker1);
}
```

<a name="Info_Windows" />

#### <a name="info-windows"></a>Ventanas de información

*Ventanas de información* son ventanas especiales que emergente para mostrar información al usuario cuando pulse un marcador específico. De forma predeterminada, la ventana de información mostrará el contenido del título del marcador. Si no se ha asignado el título, no se mostrará ninguna ventana de información. Solo una ventana de información se puede mostrar a la vez.

Es posible personalizar la ventana de información mediante la implementación de la [GoogleMap.IInfoWindowAdapter](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) interfaz. Hay dos métodos importantes de esta interfaz:

-  `public View GetInfoWindow(Marker marker)` &ndash; Se llama a este método para obtener una ventana de información personalizada sobre un marcador. Si devuelve `null` , se utilizará la representación de ventana predeterminado. Si este método devuelve una vista, esa vista se situará dentro del marco de ventana de información.

-  `public View GetInfoContents(Marker marker)` &ndash; Este método solo se llamará si GetInfoWindow devuelve `null` . Este método puede devolver un `null` valor si es la representación predeterminada de los contenidos de la ventana de información que se usará. De lo contrario, este método debe devolver una vista con el contenido de la ventana de información.

Una ventana de información no es una vista dinámica; en su lugar Android convertirá la vista de un mapa de bits estático y mostrar en la imagen. Esto significa que una ventana de información no puede responder a los eventos de toque o gestos, ni actualizará automáticamente propio. Para actualizar una ventana de información, es necesario llamar a la [GoogleMap.ShowInfoWindow](http://developer.android.com/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) método.

La imagen siguiente muestra algunos ejemplos de algunas ventanas de información personalizada. La imagen de la izquierda tiene su contenido personalizado, mientras que la imagen de la derecha tiene su ventana y el contenido personalizado:

![Ventanas de marcador de ejemplo para Melbourne, incluido el icono y el rellenado. La ventana derecha tiene esquinas redondeadas.](maps-api-images/marker-infowindows.png)

<a name="Adding_an_overlay" />

#### <a name="ground-overlays"></a>Superposiciones de terreno

A diferencia de los marcadores, que identifican una ubicación específica en un mapa, una [GroundOverlay](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlay.html) es una imagen que se usa para identificar una colección de ubicaciones o un área del mapa.

<a name="AddingAGroundOverlay" />

##### <a name="adding-a-groundoverlay"></a>Agregar un GroundOverlay

Agregar una superposición de principio a un mapa es muy similar a agregar un marcador a un mapa. En primer lugar, un [GroundOverlayOptions](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlayOptions.html) se crea el objeto. Este objeto, a continuación, se pasa como un parámetro a la `GoogleMap.AddGroundOverlay` método, que devolverá una `GroundOverlay` objeto. Este fragmento de código es un ejemplo de cómo agregar una superposición de principio a un mapa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = _map.AddGroundOverlay(groundOverlayOptions);
```

Captura de pantalla siguiente muestra esta superposición en un mapa:

[![Ejemplo de asignación con una imagen de un bajista polar superponer](maps-api-images/image09-sml.png)](maps-api-images/image09.png)

<a name="Lines_Circles_and_Polygons" />

#### <a name="lines-circles-and-polygons"></a>Las líneas, círculos y polígonos

Hay tres tipos simples de figuras geométricas que se pueden agregar a un mapa:

-  **Polilínea** -se trata de una serie de segmentos de línea conectados. Puede marcar una ruta de acceso en un mapa o crear cualquier forma necesario.

-  **Polígono** -se trata de una forma cerrada para marcar las áreas en un mapa.

-  **Círculo** -esto, dibujará un círculo en el mapa.


<a name="Polylines" />

##### <a name="polylines"></a>Polilíneas

A [polilínea](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Polyline) es una lista de consecutivos `LatLng` objetos que definen los vértices de cada segmento de línea. Se crea una polilínea creando primero una `PolylineOptions` objeto y agregando los puntos a él. El `PolylineOption` objeto, a continuación, se pasa a un `GoogleMap` objeto mediante una llamada a la `AddPolyline` método.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.
myMap.AddPolyline(rectOptions);
```

<a name="Polygons" />

##### <a name="polygons"></a>Polígonos

`Polygon`son muy similares a `Polyline`s, sin embargo, no estén abiertos ha finalizado. `Polygon`se un bucle cerrado y tienen su interior rellena.
`Polygon`se crean en la misma manera como un `Polyline`, excepto el [GoogleMap.AddPolygon](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) método invocado.

A diferencia de un `Polyline`, un `Polygon` se cierra automáticamente. Cuando `AddPolygon` llamar, el método se cierran automáticamente fuera del polígono debe dibujar una línea que conecta los puntos primero y último. El siguiente fragmento de código creará un rectángulo sólido a través de la misma área que el fragmento de código anterior en el `Polyline` ejemplo.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon
myMap.AddPolygon(rectOptions);
```

<a name="Circles" />

##### <a name="circles"></a>Círculos

Círculos se crean creando primero una [CircleOption](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/CircleOptions) objeto que especificará el centro y el radio del círculo en metros. El círculo se dibuja en el mapa mediante una llamada a [GoogleMap.AddCircle](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap#addCircle(com.google.android.gms.maps.model.CircleOptions)).
El fragmento de código siguiente muestra cómo dibujar un círculo:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);
_map.AddCircle (CircleOptions);
```

<a name="RespondingToClicks" />

## <a name="responding-to-events"></a>Responder a eventos

Hay tres tipos de interacciones que un usuario pueda tener con una asignación:

-  **Haga clic en de marcador** -el usuario hace clic en un marcador.

-  **Arrastre de marcador** -hizo clic el usuario long-en una mparger

-  **Haga clic en la ventana de información** -el usuario hizo clic en una ventana de información.

Cada uno de estos eventos se tratarán con más detalle a continuación.

<a name="Marker_Click_Events" />

### <a name="marker-click-events"></a>Marcador Click (eventos)

Cuando el usuario hace clic en un marcador de la `MarkerClick` se generará el evento y un `GoogleMap.MarkerClickEventArgs` pasa. Esta clase contiene dos propiedades:

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; Esta propiedad debe establecerse en `true` para indicar que el controlador de eventos ha consumido el evento. Si se establece en `false` , a continuación, se producirá el comportamiento predeterminado además el comportamiento personalizado del controlador de eventos.

-  `P0` &ndash; Este parámetro de nombre mal es una referencia al marcador que ha provocado la `MarkerClick` eventos.


Este fragmento de código muestra un ejemplo de un `MarkerClick` que cambiará la posición de la cámara a una nueva ubicación en el mapa:

```csharp
private void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;
    Marker marker = markerClickEventArgs.P0;
    if (marker.Id.Equals(MyMarkerId)) // The ID of a specific marker the user clicked on.
    {
        _map.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(new LatLng(20.72110, -156.44776), 13));
    }
    else
    {
        Toast.MakeText(this, String.Format("You clicked on Marker ID {0}", marker.Id), ToastLength.Short).Show();
    }
}
```

<a name="Marker_Drag_Events" />

### <a name="marker-drag-events"></a>Eventos de marcador de arrastre

Este evento se desencadena cuando el usuario desea arrastrar el marcador. De forma predeterminada, los marcadores no son arrastrables. Un marcador se puede establecer como arrastrable estableciendo la `Marker.Draggable` propiedad `true` o invocando la `MarkerOptions.Draggable` método con `true` como un parámetro.

Para en primer lugar, arrastre el marcador, el usuario debe larga, haga clic en él y mantener su dedo en el mapa. Cuando arrastra el dedo por la pantalla, se moverá el marcador. Cuando levante el dedo del usuario fuera de la pantalla, el marcador permanecerá en su lugar.

En la lista siguiente describe los diversos eventos que se generan para un marcador arrastrable:

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; Este evento se desencadena cuando el usuario arrastra primero el marcador.

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; Este evento se desencadena como se arrastra el marcador.

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; Este evento se desencadena cuando cuando finaliza el usuario arrastra el marcador.

Cada uno de los `EventArgs` contiene una propiedad única denominada `P0` que es una referencia a la `Marker` del objeto que se están arrastrando.

<a name="Info_Window_Click_Events" />

### <a name="info-window-click-events"></a>Ventana información Click (eventos)

Solo una ventana de información se puede mostrar a la vez. Cuando el usuario hace clic en una ventana de información en un mapa, el objeto de mapa, se producirá un `InfoWindowClick` eventos. El fragmento de código siguiente muestra cómo conectar un controlador para el evento:

```csharp
private bool SetupMapIfNeeded()
{
    if (_map == null)
    {
        _map = _mapFragment.Map;
        if (_map != null)
        {
            _map.InfoWindowClick += MapOnInfoWindowClick;
            return true;
        }
        return false;
    }
    return true;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.P0;
    // Do something with marker.
}
```

Recuerde que una ventana de información es una variable static `View` que se representa como una imagen en el mapa. Los widgets, como botones, casillas de verificación o vistas de texto que se sitúan dentro de la ventana de información serán inertes y no pueden responder a cualquiera de sus eventos de usuario integral.



## <a name="related-links"></a>Vínculos relacionados

- [Servicios de Google Play](http://developer.android.com/google/play-services/index.html)
- [Mapas de Google Android API v2](https://developers.google.com/maps/documentation/android/)
- [APK de servicios de Google Play](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Obtener una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Problema 57880: Servicios de Google Play actualizados pero AVD no](https://code.google.com/p/android/issues/detail?id=57880)
