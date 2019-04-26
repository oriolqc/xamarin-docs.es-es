---
title: Uso de la API de Google Maps en su aplicación
description: Cómo implementar características de API de Google Maps v2 en la aplicación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 1889154a12a701fb4ce57ef8644699dd978f768e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61186324"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Uso de la API de Google Maps en su aplicación

El uso de la aplicación mapas es excelente, pero a veces van a incluir asignaciones directamente en la aplicación. Además de los integrados se asigna la aplicación, Google también ofrece un [API nativas de asignación para Android](https://developers.google.com/maps/documentation/android-sdk/intro).
La API de mapas es adecuada para los casos donde desee mantienen más control sobre la experiencia de asignación. Cosas que son posibles con la API de Maps incluyen:

-  Cambiar mediante programación el punto de vista del mapa.
-  Cómo agregar y personalizar los marcadores.
-  Anotar un mapa con superposiciones.

A diferencia de la versión 1 ahora está en desuso API de Android de Google Maps, API de Android de Google Maps v2 es parte de [Google Play Services](https://developers.google.com/android/guides/overview).
Una aplicación de Xamarin.Android debe cumplir algunos requisitos previos obligatorios antes de que se puede usar la API de Android de Google Maps.


## <a name="google-maps-api-prerequisites"></a>Requisitos previos de la API de Google Maps

Varios pasos deben realizarse antes de poder usar la API de Maps, incluidos:

-  [Obtener una clave de API de mapas](#obtain-maps-key)
-  [Instale el SDK de Google Play Services](#install-gps-sdk)
-  [Instale el paquete de Xamarin.GooglePlayServices.Maps de NuGet](#install-gpsmaps-nuget)
-  [Especifique los permisos necesarios](#declare-permissions)
-  [Opcionalmente, puede crear un emulador con las APIs de Google](#create-emulator-with-google-api)


### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />Obtener una clave de API de Google Maps

El primer paso es obtener una clave de API de Google Maps (tenga en cuenta que no se puede reutilizar una clave de API de la API heredada de v1 de Google Maps). Para obtener información acerca de cómo obtener y usar la clave de API con Xamarin.Android, consulte [clave de API de obtención A Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).


### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> Instale el SDK de Google Play Services

Google Play Services es una tecnología de Google que permite a las aplicaciones Android aprovechar las ventajas de las distintas características de Google como Google +, de facturación en la aplicación y asignaciones. Estas características son accesibles en dispositivos Android como servicios en segundo plano, que se encuentran en el [APK de Google Play Services](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Las aplicaciones de Android interactúan con Google Play Services a través de la biblioteca de cliente de Google Play Services. Esta biblioteca contiene las interfaces y clases de los servicios individuales, como los mapas. El diagrama siguiente muestra la relación entre una aplicación de Android y Google Play Services:

![Diagrama que ilustra el Store Play de Google actualizando el APK de servicios de Google Play](maps-api-images/play-services-diagram.png)

La API de mapas de Android se proporciona como parte de Google Play Services.
Antes de que una aplicación de Xamarin.Android puede usar la API de mapas, el SDK de Google Play Services debe instalarse con la [Android SDK Manager](~/android/get-started/installation/android-sdk.md). Captura de pantalla siguiente muestra dónde en el cliente de servicios de Google Play se puede encontrar Android SDK Manager:

![Google Play Services aparece en los datos adicionales en el Administrador de SDK de Android](maps-api-images/image01.png)

> [!NOTE]
> Los servicios de Google Play APK es un producto con licencia que no puede estar presente en todos los dispositivos. Si no está instalado, Google Maps no funcionará en el dispositivo.

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> Instale el paquete de Xamarin.GooglePlayServices.Maps de NuGet

El [Xamarin.GooglePlayServices.Maps paquete](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contiene los enlaces de Xamarin.Android para la API de mapas de Google Play Services.
Para agregar el paquete de mapa de Google Play Services, haga clic en el **referencias** carpeta del proyecto en el Explorador de soluciones y haga clic en **administrar paquetes NuGet...** :

![Elemento del menú contextual Explorador de soluciones muestra administrar paquetes de NuGet en referencias](maps-api-images/image02.png)

Se abrirá el **Administrador de paquetes de NuGet**. Haga clic en **examinar** y escriba **Xamarin Google Play Services Maps** en el campo de búsqueda. Seleccione **Xamarin.GooglePlayServices.Maps** y haga clic en **instalar**. (Si hubiera instalado previamente este paquete, haga clic en **Update**.):

[![Administrador de paquetes de NuGet con el paquete Xamarin.GooglePlayServices.Maps seleccionado](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Tenga en cuenta que también están instalados los siguientes paquetes de dependencia:

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> Especifique los permisos necesarios

Las aplicaciones deben identificar los requisitos de hardware y el permiso para usar la API de Google Maps.  Algunos permisos se conceden automáticamente mediante el SDK de Google Play Services, y no es necesario agregarlos de forma explícita a un desarrollador **AndroidManfest.XML**:

-  **Acceso al estado de la red** &ndash; la API de mapas debe ser capaz de comprobar si pueden descargar los mosaicos de mapa.

-  **Acceso a Internet** &ndash; acceso a Internet es necesario descargar los mosaicos de mapa y comunicarse con los servidores de Google Play para el acceso de API.

Se deben especificar los siguientes permisos y las características en el **AndroidManifest.XML** para la API de Android de Google Maps:

-  **OpenGL ES v2** &ndash; la aplicación debe declarar el requisito para la versión de OpenGL ES 2.

-  **Clave de API de Google Maps** &ndash; la clave de API se usa para confirmar que la aplicación se registra y autorización para usar Google Play Services. Consulte [obtener una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) para obtener más información acerca de esta clave.

- **El cliente heredado de Apache HTTP de solicitud** &ndash; las aplicaciones destinadas a Android 9.0 (API nivel 28) o posterior debe especificar que el cliente heredado de Apache HTTP es una biblioteca opcional que se usará.

-  **Acceso a los servicios basados en Web de Google** &ndash; la aplicación necesita permisos para tener acceso a los servicios web de Google que realizar una copia de la API de mapas de Android.

-  **Permisos para las notificaciones de Google Play Services** &ndash; la aplicación debe tener permiso para recibir notificaciones remotas de Google Play Services.

-  **Acceso a los proveedores de ubicación** &ndash; son permisos opcionales.
   Permitirá la `GoogleMap` clase para mostrar la ubicación del dispositivo en el mapa.

Además, 9 Android quitó la biblioteca de cliente Apache HTTP desde el bootclasspath, y por lo que no está disponible para las aplicaciones destinadas a API 28 o superior. Se debe agregar la siguiente línea a la `application` nodo de su **AndroidManifest.xml** archivo seguir usando el cliente HTTP de Apache en las aplicaciones destinadas a API 28 o superior:

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Las versiones muy anteriores de SDK de Google Play requieren una aplicación para solicitar el `WRITE_EXTERNAL_STORAGE` permiso. Este requisito ya no es necesario con los enlaces de Xamarin recientes de Google Play Services.

El fragmento de código siguiente es un ejemplo de la configuración que debe agregarse a **AndroidManifest.XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="23" android:targetSdkVersion="28" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- Necessary for apps that target Android 9.0 or higher -->
    <uses-library android:name="org.apache.http.legacy" android:required="false" />


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
        <!-- Necessary for apps that target Android 9.0 or higher -->
        <uses-library android:name="org.apache.http.legacy" android:required="false" />
    </application>
</manifest>
```

Además de solicitar los permisos **AndroidManifest.XML**, una aplicación también debe realizar comprobaciones de permisos en tiempo de ejecución para el `ACCESS_COARSE_LOCATION` y `ACCESS_FINE_LOCATION` permisos. Consulte la [Xamarin.Android permisos](~/android/app-fundamentals/permissions.md) guía para obtener más información sobre la realización de comprobaciones de permiso de tiempo de ejecución.


### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />Creación de un emulador con API de Google

En caso de que un dispositivo Android físico con Google Play services no está instalado, es posible crear una imagen del emulador para el desarrollo. Para obtener más información, consulte el [Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).


## <a name="the-googlemap-class"></a>La clase GoogleMap

Una vez que se cumplen los requisitos previos, es el momento de empezar a desarrollar la aplicación y usar la API de mapas de Android. El [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) clase es la API principal que usará una aplicación de Xamarin.Android para mostrar e interactuar con un Google Maps para Android. Esta clase tiene las siguientes responsabilidades:

-  Interactuar con los servicios de Google Play y autorizar la aplicación con el servicio web de Google.

-  Descargar, almacenamiento en caché y mostrar los iconos de mapa.

-  Mostrar controles de interfaz de usuario, como realizar una panorámica y zoom para el usuario.

-  Marcadores de dibujo y formas geométricas en mapas.

El `GoogleMap` se agrega a una actividad en uno de dos maneras:

-  **MapFragment** : la [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) es un fragmento especializado que actúa como host para el `GoogleMap` objeto. El `MapFragment` requiere el nivel de API de Android 12 o superior.
   Pueden usar las versiones anteriores de Android el [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Esta guía se centra en usar la `MapFragment` clase.

-  **MapView** : la [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) es una subclase especializada de la vista, que puede actuar como un host para un `GoogleMap` objeto. Los usuarios de esta clase deben reenviar todos los métodos de ciclo de vida de actividad a la `MapView` clase.

Cada uno de estos contenedores expone un `Map` propiedad que devuelve una instancia de `GoogleMap`. Preferencia debería proporcionarse a los [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) clase ya que es una API más sencilla que reduce el código reutilizable de cantidad que un desarrollador deberá implementar manualmente.

### <a name="adding-a-mapfragment-to-an-activity"></a>Agregar un MapFragment a una actividad

Captura de pantalla siguiente es un ejemplo de una sencilla `MapFragment`:

[![Captura de pantalla de un dispositivo que se muestra un fragmento de asignación de Google](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Al igual que otras clases de fragmentos, hay dos maneras de agregar un `MapFragment` a una actividad:

-   **Mediante declaración** : la `MapFragment` se pueden agregar mediante el archivo de diseño XML para la actividad. El fragmento XML siguiente muestra un ejemplo de cómo usar el `fragment` elemento:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **Mediante programación** : la `MapFragment` puede crearse mediante programación utilizando la [ `MapFragment.NewInstance` ](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) método y, a continuación, se agrega a una actividad. Este fragmento de código muestra la manera más sencilla de crear una instancia de un `MapFragment` de objeto y agregar a una actividad:

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    Es posible configurar el `MapFragment` pasando un [ `GoogleMapOptions` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) objeto `NewInstance`. Esto se explica en la sección [GoogleMap propiedades](#googlemap_object) que aparece más adelante en esta guía.

El `MapFragment.GetMapAsync` método se usa para inicializar el [ `GoogleMap` ](#googlemap_object) que está hospedado en el fragmento y obtener una referencia al objeto de mapa que está hospedado por el `MapFragment`. Este método toma un objeto que implementa el `IOnMapReadyCallback` interfaz.

Esta interfaz tiene un único método, `IMapReadyCallback.OnMapReady(MapFragment map)` que se invocará cuando es posible que la aplicación interactuar con el `GoogleMap` objeto. El siguiente fragmento de código muestra cómo se puede inicializar una actividad de Android un `MapFragment` e implemente el `IOnMapReadyCallback` interfaz:
```csharp
public class MapWithMarkersActivity : AppCompatActivity, IOnMapReadyCallback
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.MapLayout);

        var mapFragment = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.map);
        mapFragment.GetMapAsync(this);

        // remainder of code omitted
    }

    public void OnMapReady(GoogleMap map)
    {
        // Do something with the map, i.e. add markers, move to a specific location, etc.
    }
}
```

### <a name="map-types"></a>Tipos de mapa

Hay cinco tipos diferentes de asignaciones de la API de mapas de Google:

-  **Normal** -éste es el tipo de asignación predeterminado. Muestra las carreteras y características naturales importante junto con algunos puntos artificiales de interés (por ejemplo, edificios y puentes).

-  **Satélite** -esta asignación muestra la fotografía de satélite.

-  **Híbrido** : esta asignación muestra la fotografía satélite y mapas de carreteras.

-  **Terreno** -principalmente muestra características topográficas con algunas carreteras.

-  **Ninguno** -esta asignación no carga ningún mosaico, se representa como una cuadrícula vacía.


La siguiente imagen muestra tres de los diferentes tipos de mapas, de izquierda a derecha (normal, híbrido, terreno):

[![Tres capturas de pantalla de ejemplo de mapa: Normal, híbridas y el terreno](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

El `GoogleMap.MapType` propiedad se utiliza para establecer o cambiar el tipo de asignación se muestra. El fragmento de código siguiente muestra cómo mostrar un mapa de satélite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```


### <a name="a-namegooglemapobject-googlemap-properties"></a><a name="googlemap_object" />Propiedades de GoogleMap

`GoogleMap` define varias propiedades que pueden controlar la funcionalidad y la apariencia del mapa. Una manera de configurar el estado inicial de un `GoogleMap` consiste en pasar un [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) objeto cuando se crea un `MapFragment`. El fragmento de código siguiente es un ejemplo del uso de un `GoogleMapOptions` objeto cuando se crea un `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, mapFragment, "map");
fragTx.Commit();
```

La otra forma de configurar un `GoogleMap` está manipulando las propiedades en el [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) del objeto map. El ejemplo de código siguiente muestra cómo configurar un `GoogleMap` para mostrar los controles de zoom y una brújula:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Interactuar con el GoogleMap

La API de mapas de Android proporciona API que permiten que una actividad para cambiar el punto de vista, agregar marcadores, coloque las superposiciones personalizadas o dibujar formas geométricas. En esta sección se describe cómo realizar algunas de estas tareas en Xamarin.Android.

### <a name="changing-the-viewpoint"></a>Cambiar el punto de vista

Mapas se modelan como un plano sin formato en la pantalla, en función de la proyección Mercator. La vista del mapa es el de un *cámara* atractivo hacia abajo en este plano. La posición de la cámara puede controlarse mediante la modificación de la ubicación, zoom, inclinación y afecta. El [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) clase se utiliza para mover la ubicación de la cámara. `CameraUpdate` no son instancias de los objetos directamente, en su lugar, proporciona la API de Maps la [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory) clase.

Una vez un `CameraUpdate` se ha creado el objeto, se pasa como parámetro a cualquiera la [GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) o [GoogleMap.AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) métodos. El `MoveCamera` método actualiza el mapa al instante durante la `AnimateCamera` método proporciona una transición suave y animada.

Este fragmento de código es un ejemplo sencillo de cómo usar el `CameraUpdateFactory` para crear un `CameraUpdate` que aumentará el nivel de zoom del mapa por nivel de uno zoom:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

La API de Maps proporciona un [CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) que realizará la agregación de todos los valores posibles para la posición de la cámara. Una instancia de esta clase se puede proporcionar a los [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) método que devolverá un `CameraUpdate` objeto. La API de Maps incluye también la [CameraPosition.Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) clase que proporciona una API fluida para crear `CameraPosition` objetos.
El fragmento de código siguiente muestra un ejemplo de creación de un `CameraUpdate` desde un `CameraPosition` y usarlo para cambiar la posición de la cámara en una `GoogleMap`:

```csharp
public void OnMapReady(GoogleMap map)
{
    LatLng location = new LatLng(50.897778, 3.013333);

    CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
    builder.Target(location);
    builder.Zoom(18);
    builder.Bearing(155);
    builder.Tilt(65);

    CameraPosition cameraPosition = builder.Build();

    CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

    map.MoveCamera(cameraUpdate);
}
```

En el fragmento de código anterior, una ubicación específica en el mapa se representa mediante el [LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) clase. El nivel de zoom se establece a 18, que es una medida arbitraria de zoom utilizado por Google Maps. La relación es la medida brújula las agujas del reloj desde el norte. La propiedad inclinación controla el ángulo de visión y especifica un ángulo de 25 grados desde la vertical. La siguiente captura de pantalla muestra la `GoogleMap` después de ejecutar el código anterior:

[![Ángulo de visualización de mapa de Google de ejemplo que muestra una ubicación especificada con un superpuesto](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>Dibujo del mapa

La API de mapas de Android proporciona la API para dibujar los siguientes elementos en un mapa:

-  **Marcadores** : estos son los iconos especiales que se usan para identificar una sola ubicación en un mapa.

-  **Las superposiciones** -se trata de una imagen que puede usarse para identificar una colección de ubicaciones o área en el mapa.

-  **Líneas, polígonos y círculos** -se trata de API que permiten a las actividades agregar formas a un mapa.


#### <a name="markers"></a>Markers

La API de Maps proporciona un [marcador](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) clase que encapsula todos los datos sobre una sola ubicación en un mapa. De forma predeterminada, el marcador de clase utiliza un icono estándar proporcionado por Google Maps. Es posible personalizar la apariencia de un marcador y responder a clics de usuario.


##### <a name="adding-a-marker"></a>Agregar un marcador

Para agregar un marcador a un mapa, es necesario crear un nuevo [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) objeto y, a continuación, llame a la [AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) método en un `GoogleMap` instancia. Este método devolverá un [marcador](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) objeto.

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

Se mostrará el título del marcador en un *ventana información* cuando el usuario puntea en el marcador. Captura de pantalla siguiente muestra el aspecto de este marcador:

[![Ejemplo de mapa de Google con un marcador y una ventana de información para Vimy Ridge](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>Personalización de un marcador

Es posible personalizar el icono utilizado por el marcador mediante una llamada a la `MarkerOptions.InvokeIcon` método al agregar el marcador al mapa.
Este método toma un [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) objeto que contiene los datos necesarios para representar el icono. El [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) clase proporciona algunos métodos auxiliares para simplificar la creación de un `BitmapDescriptor`. En la lista siguiente presenta algunos de estos métodos:

-   `DefaultMarker(float colour)` &ndash; Utilice el marcador de Google Maps predeterminado, pero cambie el color.

-   `FromAsset(string assetName)` &ndash; Usar un icono personalizado desde el archivo especificado en la carpeta de recursos.

-   `FromBitmap(Bitmap image)` &ndash; Use el mapa de bits especificado como el icono.

-   `FromFile(string fileName)` &ndash; Crear el icono personalizado desde el archivo en la ruta de acceso especificada.

-   `FromResource(int resourceId)` &ndash; Crear un icono personalizado desde el recurso especificado.

El fragmento de código siguiente muestra un ejemplo de cómo crear un marcador predeterminado de color aguamarina:

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    var bmDescriptor = BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan);
    markerOpt1.InvokeIcon(bmDescriptor);

    map.AddMarker(markerOpt1);
}
```

#### <a name="info-windows"></a>Ventanas de información

*Windows info* son ventanas especiales ese cuadro emergente para mostrar información al usuario cuando pulse un marcador concreto. De forma predeterminada, la ventana de información mostrará el contenido del título del marcador. Si no se ha asignado el título, no aparecerá ninguna ventana de información. Sólo una ventana de información puede mostrarse a la vez.

Es posible personalizar la ventana de información mediante la implementación de la [GoogleMap.IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) interfaz. Hay dos métodos importantes de esta interfaz:

-  `public View GetInfoWindow(Marker marker)` &ndash; Este método se llama para obtener una ventana de información personalizada para un marcador. Si devuelve `null` , entonces se usará la representación predeterminada de la ventana. Si este método devuelve una vista, esa vista se colocarán dentro del marco de ventana de información.

-  `public View GetInfoContents(Marker marker)` &ndash; Este método solo se llamará si GetInfoWindow devuelve `null` . Este método puede devolver un `null` valor si es la representación predeterminada, el contenido de la ventana de información que se usará. En caso contrario, este método debe devolver una vista con el contenido de la ventana de información.

Una ventana de información no es una vista dinámica: en su lugar Android convertirá la vista de un mapa de bits estático y mostrar en la imagen. Esto significa que una ventana de información no puede responder a los eventos de toque o gestos, ni actualizará automáticamente propio. Para actualizar una ventana de información, es necesario llamar a la [GoogleMap.ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) método.

La siguiente imagen muestra algunos ejemplos de algunas ventanas de información personalizada. La imagen de la izquierda tiene su contenido personalizado, mientras que la imagen de la derecha tiene su ventana y el contenido personalizado con esquinas redondeadas:

![Ventanas de marcador de ejemplo de Melbourne, incluido el icono y rellenado. La ventana de la derecha tiene esquinas redondeadas.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

A diferencia de los marcadores, que identifican una ubicación específica en un mapa, una [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) es una imagen que se usa para identificar una colección de ubicaciones o a un área en el mapa.

##### <a name="adding-a-groundoverlay"></a>Agregar un GroundOverlay

Agregar una superposición de cero a una asignación es similar a agregar un marcador a un mapa. En primer lugar, un [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) se crea el objeto. Este objeto, a continuación, se pasa como parámetro a la [ `GoogleMap.AddGroundOverlay` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) método, que devolverá un `GroundOverlay` objeto. Este fragmento de código es un ejemplo de cómo agregar una superposición de cero a un mapa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

Captura de pantalla siguiente muestra esta superposición en un mapa:

[![Mapa de ejemplo con una imagen de un osito polar superpuesta](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>Líneas, círculos y polígonos

Hay tres tipos simples de figuras geométricas que pueden agregarse a un mapa:

-  **Polyline** -se trata de una serie de segmentos de línea conectados. Puede marcar una ruta de acceso en un mapa o crear una forma geométrica.

-  **Círculo** -dibujará un círculo en el mapa.

-  **Polígono** -se trata de una forma cerrada para marcar las áreas en un mapa.


##### <a name="polylines"></a>Polilíneas

Un [Polyline](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) es una lista de consecutivos `LatLng` objetos que especifican los vértices de cada segmento de línea. Se crea una polilínea creando primero un `PolylineOptions` objeto y agregarle los puntos. El `PolylineOption` objeto, a continuación, se pasa a un `GoogleMap` objeto mediante una llamada a la `AddPolyline` método.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>Círculos

Se crean los círculos creando primero un [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) objeto que se especificará el centro y el radio del círculo en metros. El círculo se dibuja en el mapa mediante una llamada a [GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
El fragmento de código siguiente muestra cómo dibujar un círculo:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```


##### <a name="polygons"></a>Polígonos

`Polygon`son similares a `Polyline`s, sin embargo, no estén abiertos finalizó. `Polygon`s son un bucle cerrado y su interior rellena.
`Polygon`se crean en la misma manera como un `Polyline`, excepto el [GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) método invocado.

A diferencia de un `Polyline`, un `Polygon` es de autocierre. El polígono se cerrarán desactivada por el `AddPolygon` método al dibujar una línea que conecta los puntos primeros y últimos. El siguiente fragmento de código creará un rectángulo sólido a través de la misma área que el fragmento de código anterior en el `Polyline` ejemplo.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```


## <a name="responding-to-user-events"></a>Responder a eventos de usuario

Hay tres tipos de interacciones que un usuario pudiera haber suscrito con un mapa:

-  **Haga clic en de marcador** -el usuario hace clic en un marcador.

-  **Arrastre marcador** -hizo clic el usuario long-en una mparger

-  **Ventana de información haga clic en** -el usuario hace clic en una ventana de información.

Cada uno de estos eventos se explicará más detalladamente a continuación.


### <a name="marker-click-events"></a>Marcador de eventos de clic

El `MarkerClicked` evento se desencadena cuando el usuario puntea en un marcador. Este evento acepta un `GoogleMap.MarkerClickEventArgs` objeto como parámetro. Esta clase contiene dos propiedades:

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; Esta propiedad debe establecerse en `true` para indicar que el controlador de eventos ha consumido el evento. Si se establece en `false` , a continuación, se producirá el comportamiento predeterminado además el comportamiento del controlador de eventos personalizado.

-  `Marker` &ndash; Esta propiedad es una referencia al marcador de que ha provocado la `MarkerClick` eventos.


Este fragmento de código muestra un ejemplo de un `MarkerClick` que cambiará la posición de la cámara a una nueva ubicación en el mapa:

```csharp
void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;

    var marker = markerClickEventArgs.Marker;
    if (marker.Id.Equals(gotMauiMarkerId))
    {
        LatLng InMaui = new LatLng(20.72110, -156.44776);

        // Move the camera to look at Maui.
        PositionPolarBearGroundOverlay(InMaui);
        googleMap.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(InMaui, 13));
        gotMauiMarkerId = null;
        polarBearMarker.Remove();
        polarBearMarker = null;
    }
    else
    {
        Toast.MakeText(this, $"You clicked on Marker ID {marker.Id}", ToastLength.Short).Show();
    }
}
```


### <a name="marker-drag-events"></a>Eventos de arrastre de marcador

Este evento se desencadena cuando el usuario desea arrastrar el marcador. De forma predeterminada, los marcadores no son arrastrables. Un marcador se puede establecer como arrastrable estableciendo el `Marker.Draggable` propiedad `true` o invocando la `MarkerOptions.Draggable` método con `true` como un parámetro.

Para arrastrar el marcador, el usuario debe primero larga, haga clic en el marcador y, a continuación, su dedo debe permanecer en el mapa. Cuando los dedos del usuario se arrastran alrededor de la pantalla, se moverá el marcador. Cuando se levanta el dedo del usuario fuera de la pantalla, el marcador permanecerá en su lugar.

En la lista siguiente se describe los distintos eventos que se generan para un marcador arrastrable:

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; Este evento se desencadena cuando el usuario arrastra primero el marcador.

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; Este evento se desencadena como el marcador se está arrastrando.

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; Este evento se desencadena cuando finaliza el usuario arrastra el marcador.

Cada uno de los `EventArgs` contiene una propiedad única denominada `P0` que es una referencia a la `Marker` de objeto que se está arrastrando.


### <a name="info-window-click-events"></a>Eventos de ventana, haga clic en información

Sólo una ventana de información se puede mostrar a la vez. Cuando el usuario hace clic en una ventana de información en un mapa, el objeto de mapa, se producirá un `InfoWindowClick` eventos. El fragmento de código siguiente muestra cómo enlazar un controlador al evento:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.InfoWindowClick += MapOnInfoWindowClick;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.Marker;
    // Do something with marker.
}
```

Recuerde que una ventana de información es estático `View` que se representa como una imagen en el mapa. Los widgets, como botones, casillas de verificación o las vistas de texto que se colocan dentro de la ventana de información serán inertes y no pueden responder a cualquiera de sus eventos de usuario integral.


## <a name="related-links"></a>Vínculos relacionados

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [Mapas de Google Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Obtención de una clave de API de Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element)
