---
title: Resumen de capítulo 28. Ubicación y mapas
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 40d2b67f1a1655ec1d731493446f320b8aef17ca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Resumen de capítulo 28. Ubicación y mapas

Xamarin.Forms es compatible con un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) elemento que se deriva de `View`. Debido a los requisitos de plataforma especial que implica el uso de mapas, se implementan en un ensamblado independiente, **Xamarin.Forms.Maps**e incluyen un espacio de nombres distinto: `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>El sistema de coordenadas geográficas

Un sistema de coordenadas geográficas identifica posiciones en un objeto esférica (o casi esférica) como la tierra. Una coordenada consta de un *latitud* y *longitud* expresado en ángulos.

Llama a un círculo muy bien la `equator` está comprendido entre los dos polos a través del cual se extiende conceptualmente eje de la tierra.

### <a name="parallels-and-latitude"></a>Parallels y latitud

Ángulo medido Norte o al sur del Ecuador desde el centro de las marcas de tierra líneas de latitud igual conocido como *parallels*. Estos van de 0 grados en el Ecuador a 90 grados en el norte y sur polos. Por convención, latitudes al norte del Ecuador son valores positivos y, al sur del Ecuador son valores negativos.

### <a name="longitude-and-meridians"></a>Latitud y meridianos

Mitades de círculos great desde el Polo Norte al Polo Sur son líneas de igual longitud, también conocido como *meridianos*. Estos son relativas del meridiano de Greenwich, Inglaterra. Por convención, longitudes al este del meridiano son valores positivos desde 0 grados 180 grados y longitudes oeste del meridiano son valores negativos desde 0 grados para &ndash;180 grados.

### <a name="the-equirectangular-projection"></a>La proyección equirectangular

Cualquier mapa de la tierra plana presenta distorsiones. Si todas las líneas de latitud y longitud son rectas y si las diferencias iguales en ángulos de latitud y longitud corresponden a distancias iguales en el mapa, el resultado es un *proyección equirectangular*. Esta asignación distorsiona áreas más cercano a los polos porque se estiran horizontalmente.

### <a name="the-mercator-projection"></a>La proyección de Mercator

El popular *proyección Mercator* intenta compensar la ampliación horizontal mediante también la ampliación vertical estas áreas. Esto da como resultado un mapa donde aparecen mucho mayores que son realmente, pero cualquier área local se ajusta de manera bastante estrecha con el área real áreas cerca de los polos.

### <a name="map-services-and-tiles"></a>Servicios de asignación y mosaicos

Servicios de mapa usan una variación de la proyección de Mercator denominada `Web Mercator`. Los servicios de mapa entregar mosaicos de mapa de bits a un cliente basado en el nivel de zoom y ubicación.

## <a name="getting-the-users-location"></a>Obtener la ubicación del usuario

El Xamarin.Forms `Map` clases no incluyen una instalación para obtener la ubicación geográfica del usuario, pero esto es a menudo deseable cuando se trabaja con mapas, por lo que un servicio de dependencia debe controlarla.

### <a name="the-location-tracker-api"></a>La herramienta de seguimiento de ubicación API

El [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solución contiene código para que un rastreador de ubicación API. El [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) estructura encapsula una latitud y longitud. El [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interfaz define dos métodos para iniciar y pausar el Rastreador de ubicación y un evento cuando está disponible una nueva ubicación.

#### <a name="the-ios-location-manager"></a>El Administrador de la ubicación de iOS

La implementación de iOS de `ILocationTracker` es un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) clase que hace uso de iOS [ `CLLocationManager` ](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/).

#### <a name="the-android-location-manager"></a>El Administrador de la ubicación de Android

La implementación de Android `ILocationTracker` es un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) clase que usa el Android [ `LocationManager` ](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/) clase.

#### <a name="the-windows-runtime-geo-locator"></a>El localizador de replicación geográfica en tiempo de ejecución de Windows

La implementación de Windows en tiempo de ejecución de `ILocationTracker` es un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) clase que usa el UWP [ `Geolocator` ](https://msdn.microsoft.com/library/windows/apps/br225534).

### <a name="display-the-phones-location"></a>Mostrar la ubicación del teléfono

El [ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) ejemplo usa la herramienta de seguimiento de la ubicación para mostrar la ubicación del teléfono tanto en el texto en un mapa de equirectangular.

### <a name="the-required-overhead"></a>El necesario sobrecarga

Se requiere para cierta sobrecarga **WhereAmI** para usar la herramienta de seguimiento de ubicación. Primero, todos los proyectos en el **WhereAmI** solución debe tener referencias a los proyectos correspondientes en **Xamarin.FormsBook.Platform**y cada **WhereAmI** proyecto debe llamar a la `Toolkit.Init` método.

Una sobrecarga específica de la plataforma adicional, en forma de permisos de ubicación, es necesaria.

#### <a name="location-permission-for-ios"></a>Permiso de ubicación para iOS

Para iOS, el **info.plist** archivo debe incluir elementos que contiene el texto de la pregunta que pregunta al usuario que permita obtener la ubicación del usuario.

#### <a name="location-permissions-for-android"></a>Permisos de ubicación para Android

Las aplicaciones Android que obtener la ubicación del usuario deben tener un permiso ACCESS_FILE_LOCATION en el archivo AndroidManifest.xml.

#### <a name="location-permissions-for-the-windows-runtime"></a>Permisos de ubicación para el tiempo de ejecución de Windows

Debe tener una aplicación de Windows o Windows Phone un `location` funcionalidad del dispositivo se marca en el archivo Package.appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Trabajar con Xamarin.Forms.Maps

Varios requisitos que intervienen en el uso de la `Map` clase.

### <a name="the-nuget-package"></a>El paquete de NuGet

El **Xamarin.Forms.Maps** NuGet biblioteca debe agregarse a la solución de aplicación. El número de versión debe ser el mismo que el **Xamarin.Forms** paquete instalado actualmente.

### <a name="initializing-the-maps-package"></a>Inicializar el paquete de mapas

Los proyectos de aplicación deben llamar a la `Xamarin.FormsMaps.Init` método después de realizar una llamada a `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Habilitar los servicios de asignación

Dado que la `Map` puede obtener la ubicación del usuario, la aplicación debe obtener el permiso para el usuario de la manera descrita anteriormente en este capítulo:

#### <a name="enabling-ios-maps"></a>Habilitar iOS asigna

Una aplicación de iOS con `Map` necesita dos líneas en el archivo info.plist.

#### <a name="enabling-android-maps"></a>Habilitar Android asigna

Una clave de autorización es necesaria para poder usar servicios de mapa de Google. Esta clave se inserta en la **AndroidManifest.xml** archivo. Además, el **AndroidManifest.xml** archivo requiere `manifest` etiquetas implicados en la obtención de la ubicación del usuario.

#### <a name="enabling-windows-runtime-maps"></a>Habilitar el tiempo de ejecución de Windows asigna

Una aplicación de Windows en tiempo de ejecución requiere una clave de autorización para el uso de mapas de Bing. Esta clave se pasa como argumento a la `Xamarin.FormsMaps.Init` método. La aplicación también debe habilitarse para los servicios de ubicación.

### <a name="the-unadorned-map"></a>La asignación sin adornar

El [ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) ejemplo está compuesto de un [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) archivo y [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) archivo de código subyacente que permite navegar a varios programas de demostración.

El [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) archivo muestra cómo mostrar la [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) vista. De forma predeterminada muestra la ciudad de Roma, pero se puede manipular el mapa con el usuario.

Para deshabilitar el desplazamiento horizontal y vertical, establecer el [ `HasScrollEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasScrollEnabled/) propiedad `false`. Para deshabilitar el zoom, establezca [ `HasZoomEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasZoomEnabled/) a `false`. Estas propiedades podrían no funcionar en todas las plataformas.

### <a name="streets-and-terrain"></a>Calles y terreno

También puede mostrar diferentes tipos de mapas estableciendo la `Map` propiedad [ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/) de tipo [ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/), una enumeración con tres miembros:

- [`Street`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Street/), el valor predeterminado
- [`Satellite`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Satellite/)
- [`Hybrid`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Hybrid/)

El [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) archivo muestra cómo usar un botón de opción para seleccionar el tipo de mapa. Hace uso de la [ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca y una clase basándose en el [MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) archivo.

### <a name="map-coordinates"></a>Coordenadas de mapa

Un programa puede obtener el área actual que el `Map` se están mostrando a través de la [ `VisibleRegion` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.VisibleRegion/) propiedad. Esta propiedad es *no* respaldado por una propiedad enlazable, y no hay ningún mecanismo de notificación para indicar cuándo ha cambiado, por lo que un programa que desea supervisar la propiedad probablemente tendrá que usar un temporizador para ese fin.

`VisibleRegion` es de tipo [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/), una clase con cuatro propiedades de solo lectura:

- [`Center`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Center/) de tipo [`Position`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)
- [`LatitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LatitudeDegrees/) de tipo `double`, que indica el alto del área de muestra de la asignación
- [`LongitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LongitudeDegrees/) de tipo `double`, que indica el ancho del área de muestra de la asignación
- [`Radius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Radius/) de tipo [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/), que indica el tamaño del mayor área circular visible en el mapa

`Position` y `Distance` son ambas estructuras. `Position` define dos propiedades de solo lectura establecidos por medio de la [ `Position` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Position.Position/p/System.Double/System.Double/):

- [`Latitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Latitude/)
- [`Longitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Longitude/)

`Distance` está diseñado para proporcionar una distancia independiente de la unidad mediante la conversión entre unidades métricas como inglesas. Un `Distance` valor puede crearse de varias maneras:

- [`Distance` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Distance.Distance/p/System.Double/) con una distancia en metros
- [`Distance.FromMeters`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMeters/p/System.Double/) método estático
- [`Distance.FromKilometers`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromKilometers/p/System.Double/) método estático
- [`Distance.FromMiles`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMiles/p/System.Double/) método estático

El valor está disponible en tres propiedades:

- [`Meters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Meters/) de tipo `double`
- [`Kilometers`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Kilometers/) de tipo `double`
- [`Miles`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Miles/) de tipo `double`

El [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) archivo contiene varios `Label` elementos para mostrar la `MapSpan` información. El [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) archivo de código subyacente utiliza un temporizador para mantener la información que se actualiza a medida que el usuario manipula el mapa.

### <a name="position-extensions"></a>Extensiones de posición

Una nueva biblioteca para este libro denominado [ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contiene tipos específicos de mapa pero independiente de la plataforma. El [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) clase tiene un `ToString` método para `Position`y un método para calcular la distancia entre dos `Position` valores.

### <a name="setting-an-initial-location"></a>Configurar una ubicación inicial

Puede llamar a la [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion/p/Xamarin.Forms.Maps.MapSpan/) método `Map` establecer mediante programación un nivel de zoom y la ubicación en el mapa. El argumento es de tipo `MapSpan`. Puede crear un `MapSpan` objeto mediante cualquiera de las siguientes acciones:

- [`MapSpan` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.MapSpan.MapSpan/p/Xamarin.Forms.Maps.Position/System.Double/System.Double/) con un `Position`y el intervalo de latitud y longitud
- [`MapSpan.FromCenterAndRadius`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius/p/Xamarin.Forms.Maps.Position/Xamarin.Forms.Maps.Distance/) con un `Position` y radio

También es posible crear un nuevo `MapSpan` desde otra existente utilizando los métodos [ `ClampLatitude` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.ClampLatitude/p/System.Double/System.Double/) o [ `WithZoom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.WithZoom/p/System.Double/).

El [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) archivo y [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) archivo de código subyacente muestra cómo utilizar el `MoveToRegion` método para mostrar el estado de Wyoming.

Como alternativa, puede usar el [ `Map` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Map.Map/p/Xamarin.Forms.Maps.MapSpan/) con un `MapSpan` objeto para inicializar la ubicación del mapa. El [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) archivo muestra cómo hacerlo completamente en XAML que se debe mostrar la oficina central de Xamarin en San Francisco.

### <a name="dynamic-zooming"></a>Zoom dinámico

Puede usar un `Slider` para acercar o alejar dinámicamente un mapa. El [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) archivo y [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) archivo de código subyacente se muestra cómo cambiar el radio de un mapa en función de la `Slider` valor.

El [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) archivo y [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) archivo de código subyacente se muestran un método alternativo que funciona mejor en Android, pero ningún enfoque funciona bien en las ventanas plataformas.

### <a name="the-phones-location"></a>Ubicación del teléfono

El [ `IsShowingUser` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.IsShowingUser/) propiedad de `Map` funciona un poco distinto en las tres plataformas como el [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) archivo de muestra:

- En iOS, un punto azul indica la ubicación del teléfono pero se debe desplazar manualmente no existe
- En Android, se muestra un icono que, cuando inserta mueve el mapa de la ubicación del teléfono
- UWP es similar a iOS, pero a veces, automáticamente se desplaza a la ubicación

El **MapDemos** proyecto intenta imitar el enfoque Android mediante la primera definición de un botón de icono basándose en la [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) archivo y [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) archivo de código subyacente.

El [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) archivo y [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) archivo de código subyacente utilizar este botón para navegar hasta la ubicación del teléfono.

### <a name="pins-and-science-museums"></a>PIN y museos ciencia

Por último, el `Map` clase define un [ `Pins` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.Pins/) propiedad de tipo `IList<Pin>`. El [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/) clase define cuatro propiedades:

- [`Label`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Label/) de tipo `string`, una propiedad necesaria
- [`Address`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Address/) de tipo `string`, una dirección de lenguaje natural opcional
- [`Position`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Position/) de tipo `Position`, que indica que el pin se muestra en el mapa
- [`Type`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Type/) de tipo [ `PinType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.PinType/), una enumeración, que no se utiliza

El **MapDemos** proyecto contiene el archivo [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), que enumera los museos ciencia en Estados Unidos, y [ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) y [ `Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) clases para deserializar estos datos.

El [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) archivo y [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) PIN de pantalla de archivo de código subyacente de estos museos ciencia en el mapa. Cuando el usuario puntea un pin, muestra la dirección y un sitio Web para los museos.

### <a name="the-distance-between-two-points"></a>La distancia entre dos puntos.

El [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) clase contiene un [ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) método con un cálculo simplificado de la distancia entre dos ubicaciones geográficas.

Se utiliza en el [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) archivo y [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) archivo de código subyacente para mostrar la distancia a los museos desde la ubicación del usuario:

[![Captura de pantalla triple de página museos Local](images/ch28fg28-small.png "distancia a una ubicación")](images/ch28fg28-large.png#lightbox "distancia a una ubicación")

El programa también muestra cómo restringir dinámicamente el número de puntos de conexión basándose en la ubicación del mapa.

## <a name="geocoding-and-back-again"></a>Codificación geográfica y volver

El [ **Xamarin.Forms.Maps** ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) ensamblado también contiene un [ `Geocoder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Geocoder/) clase con un [ `GetPositionsForAddressAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync/p/System.String/) método que convierte una dirección de texto en cero o más posibles posiciones geográficas y otro método [ `GetAddressesForPositionAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync/p/Xamarin.Forms.Maps.Position/) que se convierte en la otra dirección.

El [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) archivo y [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) archivo de código subyacente se muestra esta función.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 28 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Ejemplos de capítulo 28](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Control de mapa](~/xamarin-forms/user-interface/map.md)
