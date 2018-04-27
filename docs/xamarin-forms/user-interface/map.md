---
title: Asignación
description: Xamarin.Forms utiliza la API de asignación nativo en cada plataforma.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: e296ca79ee03e7fc61532758219b65946a8d4381
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="map"></a>Asignación

_Xamarin.Forms utiliza la API de asignación nativo en cada plataforma._

Xamarin.Forms.Maps utiliza la API de asignación nativo en cada plataforma. Esto proporciona una experiencia de mapas rápido y familiar para los usuarios, pero significa que se necesitan unos pasos de configuración para cumplir con cada requisitos de API específicos de plataformas.
Una vez configurado, el `Map` funciona igual que cualquier otro elemento de Xamarin.Forms en el código común de control.

* [Asigna la inicialización](#Maps_Initialization) - utilizando `Map` requiere código de inicialización durante el inicio.
* [Configuración de la plataforma](#Platform_Configuration) -cada plataforma requiere cierta configuración asigna a trabajar.
* [Usar mapas en C#](#Using_Maps) -mostrar asigna y ancla con C#.
* [Usar mapas en XAML](#Using_Xaml) -mostrar un mapa con XAML.

Se ha utilizado el control de mapa en el [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/) ejemplo, que se muestra a continuación.

 [![Se asigna en el ejemplo de MobileCRM](map-images/maps-zoom-sml.png "ejemplo de Control de mapa")](map-images/maps-zoom.png#lightbox "ejemplo de Control de mapa")

Se puede mejorar aún más la funcionalidad de mapas mediante la creación de un [asignar un representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="maps-initialization"></a>Inicialización de mapas

Al agregar mapas a una aplicación de Xamarin.Forms, **Xamarin.Forms.Maps** es un un paquete de NuGet independiente que debe agregar a todos los proyectos de la solución.
En Android, esto también tiene una dependencia en GooglePlayServices (NuGet otro) que se descarga automáticamente cuando se agrega Xamarin.Forms.Maps.

Después de instalar el paquete de NuGet, es necesario algún código de inicialización en cada proyecto de aplicación, *después* el `Xamarin.Forms.Forms.Init` llamada al método. Para iOS, utilice el código siguiente:

```csharp
Xamarin.FormsMaps.Init();
```

En Android debe pasar los mismos parámetros como `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Para la plataforma Universal de Windows (UWP) Utilice el código siguiente:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Agregue esta llamada en los siguientes archivos para cada plataforma:

-  **iOS** -AppDelegate.cs de archivos, en la `FinishedLaunching` método.
-  **Android** -MainActivity.cs de archivos, en la `OnCreate` método.
-  **UWP** -archivo MainPage.xaml.cs, en la `MainPage` constructor.

Una vez que se ha agregado el paquete de NuGet y llama al método de inicialización dentro de cada aplicaciones `Xamarin.Forms.Maps` API pueden utilizarse en el código común de PCL o proyecto compartido.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configuración de la plataforma

Pasos de configuración adicionales necesarios en algunas plataformas antes de que se mostrará el mapa.

### <a name="ios"></a>iOS

Para obtener acceso a los servicios de ubicación en iOS, debe establecer las siguientes claves **Info.plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : para usar los servicios de ubicación cuando la aplicación está en uso
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) : para usar los servicios de ubicación en todo momento
- iOS 10 y versiones anteriores
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : para usar los servicios de ubicación cuando la aplicación está en uso
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) : para usar los servicios de ubicación en todo momento    
    
Para admitir iOS 11 y versiones anteriores, puede incluir todas las claves de tres: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, y `NSLocationAlwaysUsageDescription`.

La representación XML de estas claves en **Info.plist** se muestra a continuación. Debe actualizar el `string` valores para reflejar la forma en que la aplicación está utilizando la información de ubicación:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

El **Info.plist** también se pueden agregar entradas en **origen** vista mientras se edita la **Info.plist** archivo:

![Info.plist para iOS 8](map-images/ios8-map-permissions.png "entradas Info.plist requeridas de iOS 8")


### <a name="android"></a>Android

Para usar el [v2 de API de Google Maps](https://developers.google.com/maps/documentation/android/) en Android debe generar una clave de API y agregarlo al proyecto de Android.
Siga las instrucciones que aparecen en la documentación de Xamarin en [obtener una clave de API de Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Después de seguir estas instrucciones, pegue la clave de API en el **Properties/AndroidManifest.xml** archivo (ver código fuente y buscar o actualizar el elemento siguiente):

```xml
<meta-data
        android:name="com.google.android.geo.API_KEY"
        android:value="YOUR_API_KEY"/>
```

Sin una clave de API válida el control de mapas se mostrará como un cuadro gris en Android.

> [!NOTE]
> Recuerde que debe generar otra clave usando el archivo de almacén de claves que se usa para firmar la versión de lanzamiento de cualquier aplicación que se haya cargado en la tienda Google Play. La clave genera para el desarrollo y depuración no funcionará y la aplicación descargada de Google Play habrá divide la presentación del mapa. También recuerde volver a generar la clave si la aplicación **nombre del paquete** cambios.

También deberá habilitar los permisos adecuados, el botón secundario en el proyecto de Android y seleccione **Opciones > compilar > aplicación Android** y cuenta atrás a lo siguiente:

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

Algunos de ellos se muestran en la captura de pantalla siguiente:

![Los permisos necesarios para Android](map-images/android-map-permissions.png "permisos necesarios para Android")

Las dos últimas son necesarias porque las aplicaciones requieren una conexión de red para descargar los datos de mapa. Obtenga información sobre Android [permisos](http://developer.android.com/reference/android/Manifest.permission.html) para obtener más información.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Para utilizar asignaciones en la plataforma Universal de Windows debe generar un token de autorización. Para obtener más información, consulte [solicitar una clave de autenticación de asignaciones](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) en MSDN.

El token de autenticación, a continuación, debe especificarse en el `FormsMaps.Init("AUTHORIZATION_TOKEN")` llamada al método, para autenticar la aplicación con Bing Maps.

<a name="Using_Maps" />

## <a name="using-maps"></a>Uso de asignaciones

Consulte la [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) en el ejemplo de MobileCRM para obtener un ejemplo de cómo puede utilizarse el control de mapa en el código. Un sencillo `MapPage` clase podría ser similar a este: aviso de que un nuevo `MapSpan` se crea para colocar la vista del mapa:

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>Tipo de mapa

El contenido de la asignación también puede cambiarse estableciendo la `MapType` propiedad para mostrar un mapa de calle normal (predeterminado), imágenes de satélite o una combinación de ambos.

```csharp
map.MapType == MapType.Street;
```

Válido `MapType` valores son:

-  Híbrido
-  Satélite
-  Calle (valor predeterminado)


### <a name="map-region-and-mapspan"></a>MapSpan y región del mapa

Como se muestra en el fragmento de código anterior, proporcionando un `MapSpan` instancia a un constructor de asignación establece la vista inicial (punto del centro y el nivel de zoom) de la asignación cuando se cargue. El `MoveToRegion` método en la clase map, a continuación, se puede utilizar para cambiar el nivel de posición o zoom del mapa. Hay dos maneras de crear un nuevo `MapSpan` instancia:

-  **MapSpan.FromCenterAndRadius()** -método estático para crear un intervalo de un `Position` y especificando un `Distance` .
-  **nueva MapSpan ()** -constructor que utiliza un `Position` y el degress de latitud y longitud para mostrar.


Para cambiar el nivel de zoom del mapa sin modificar la ubicación, cree un nuevo `MapSpan` con la ubicación actual de la `VisibleRegion.Center` propiedad del control de mapa. Un `Slider` podría usarse para controlar el zoom del mapa similar al siguiente (sin embargo, si el zoom directamente en el control de mapa, actualmente no puede actualizar el valor del control deslizante):

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![Mapas con zoom](map-images/maps-zoom-sml.png "mapa Control Zoom")](map-images/maps-zoom.png#lightbox "Zoom de Control de mapa")

### <a name="map-pins"></a>Asignar PIN

Se pueden marcar ubicaciones en el mapa con `Pin` objetos.

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

 `PinType` puede establecerse en uno de los valores siguientes, que pueden afectar a la manera en el pin en representa (en función de la plataforma):

-  Genérico
-  Lugar
-  SavedPin
-  SearchResult


<a name="Using_Xaml" />

## <a name="using-xaml"></a>Uso de Xaml

También se pueden colocar una mapas en los diseños de Xaml como se muestra en este fragmento de código.

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
    x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map WidthRequest="320" HeightRequest="200"
            x:Name="MyMap"
            IsShowingUser="true"
            MapType="Hybrid"
        />
    </StackLayout>
</ContentPage>
```

El `MapRegion` y `Pins` se puede establecer en el código mediante el `MyMap` referencia (o lo que se denomina el mapa). Tenga en cuenta que otro `xmlns` definición de espacio de nombres es necesario para hacer referencia a los controles de Xamarin.Forms.Maps.

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

<a name="Summary" />

## <a name="summary"></a>Resumen

El Xamarin.Forms.Maps es un NuGet independiente que se debe agregar a cada proyecto en una solución de Xamarin.Forms. Código de inicialización adicional es necesario, ya que así como algunos pasos de configuración para UWP, iOS y Android.

Una vez configurada la API asigna puede usarse para representar mapas con marcadores de pin en unas pocas líneas de código. Mapas se pueden mejorar con una [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).


## <a name="related-links"></a>Vínculos relacionados

- [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)
- [Asignar a un representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
