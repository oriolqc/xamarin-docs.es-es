---
title: Mapa de Xamarin.Forms
description: En este artículo se explica cómo usar la clase Xamarin.Forms Map para usar la API de mapa de nativa en cada plataforma para proporcionar la que experiencia para los usuarios asigna un familiar.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/13/2019
ms.openlocfilehash: 60d78797406f2e69c435fb597e36775d906852f9
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513108"
---
# <a name="xamarinforms-map"></a>Mapa de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/)

_Xamarin.Forms utiliza la asignación nativa API en cada plataforma._

Xamarin.Forms.Maps para utiliza la asignación nativa API en cada plataforma. Esto proporciona una experiencia rápida, familiar mapas para los usuarios, pero significa que algunos pasos de configuración son necesarias para cumplir con requisitos de cada API de plataformas.
Una vez configurado, el `Map` funciona igual que cualquier otro elemento de Xamarin.Forms en el código común de control.

Se ha utilizado el control de mapa en el [MapsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/) ejemplo, que se muestra a continuación.

 [![Se asigna en el ejemplo MobileCRM](map-images/maps-zoom-sml.png "ejemplo de Control de mapa")](map-images/maps-zoom.png#lightbox "ejemplo de Control de mapa")

Funcionalidad de asignación se puede mejorar aún más mediante la creación de un [asignar un representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>Inicialización del mapa

Al agregar mapas a una aplicación de Xamarin.Forms, **xamarin.Forms.Maps para** es un paquete NuGet independiente que se debe agregar a cada proyecto en la solución.
En Android, esto también tiene una dependencia en GooglePlayServices (NuGet otro) que se descarga automáticamente cuando se agrega xamarin.Forms.Maps para.

Después de instalar el paquete de NuGet, es necesario algún código de inicialización en cada proyecto de aplicación, *después* el `Xamarin.Forms.Forms.Init` llamada al método. Para iOS, use el código siguiente:

```csharp
Xamarin.FormsMaps.Init();
```

En Android debe pasar los mismos parámetros que `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Para la plataforma Universal de Windows (UWP) use el código siguiente:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Agregue esta llamada en los siguientes archivos para cada plataforma:

-  **iOS** -archivo AppDelegate.cs en el `FinishedLaunching` método.
-  **Android** -archivo MainActivity.cs, en el `OnCreate` método.
-  **UWP** -archivo MainPage.xaml.cs, en el `MainPage` constructor.

Una vez que se ha agregado el paquete de NuGet y llama al método de inicialización dentro de cada aplicación, `Xamarin.Forms.Maps` API pueden usarse en el proyecto de biblioteca estándar de .NET común o el código de proyecto compartido.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configuración de la plataforma

Se requieren pasos de configuración adicional en algunas plataformas antes de que se mostrará el mapa.

### <a name="ios"></a>iOS

Para obtener acceso a servicios de ubicación en iOS, debe establecer las siguientes claves **Info.plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : para usar los servicios de ubicación cuando la aplicación está en uso
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) : para usar los servicios de ubicación en todo momento
- iOS 10 y versiones anteriores
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : para usar los servicios de ubicación cuando la aplicación está en uso
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) : para usar los servicios de ubicación en todo momento    

Para admitir iOS 11 y versiones anteriores, puede incluir estas tres claves: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, y `NSLocationAlwaysUsageDescription`.

La representación XML de estas claves en **Info.plist** se muestra a continuación. Debe actualizar el `string` valores para que reflejen cómo la aplicación está utilizando la información de ubicación:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

El **Info.plist** también se pueden agregar entradas en **origen** vista mientras edita el **Info.plist** archivo:

![Info.plist para iOS 8](map-images/ios8-map-permissions.png "iOS 8 entradas de Info.plist necesaria")

### <a name="android"></a>Android

Para usar el [v2 de la API de Google Maps](https://developers.google.com/maps/documentation/android/) en Android debe generar una clave de API y agregarlo al proyecto Android.
Siga las instrucciones de la documentación de Xamarin [obtención de una clave de API de Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Después de seguir estas instrucciones, pegue la clave de API en el **Properties/Androidmanifest.XML** archivo (ver código fuente y buscar o actualizar el elemento siguiente):

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Sin una clave de API válida, el control de mapas se mostrará como un cuadro gris en Android.

> [!NOTE]
> Tenga en cuenta que, en orden para el APK tener acceso a Google Maps, debe incluir las huellas digitales de SHA-1 y empaquetar los nombres para cada almacén de claves (debug y release) que usó para firmar el APK. Por ejemplo, si usa un equipo para depuración y otro equipo para generar el APK de lanzamiento, debe incluir la huella digital de certificado de SHA-1 desde el almacén de claves de depuración del primer equipo y la huella digital de certificado de SHA-1 desde el almacén de claves de la versión de el segundo equipo. Recuerde también que modificar las credenciales de clave si la aplicación **nombre del paquete** cambios. Consulte [obtención de una clave de API de Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

También deberá habilitar los permisos adecuados, con el botón secundario en el proyecto de Android y seleccione **Opciones > compilar > aplicación de Android** y funciona a la perfección lo siguiente:

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

Algunos de ellos se muestran en la captura de pantalla siguiente:

![Permisos necesarios para Android](map-images/android-map-permissions.png "permisos necesarios para Android")

Los dos últimos son necesarios porque las aplicaciones requieren una conexión de red para descargar datos del mapa. Obtenga información sobre Android [permisos](https://developer.android.com/reference/android/Manifest.permission.html) para obtener más información.

Además, 9 Android quitó la biblioteca de cliente Apache HTTP desde el bootclasspath, y por lo que no está disponible para las aplicaciones destinadas a API 28 o superior. Se debe agregar la siguiente línea a la `application` nodo de su **AndroidManifest.xml** archivo seguir usando el cliente HTTP de Apache en las aplicaciones destinadas a API 28 o superior:

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Para utilizar asignaciones en la plataforma Universal de Windows debe generar un token de autorización. Para obtener más información, consulte [solicitar una clave de autenticación de asignaciones](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) en MSDN.

El token de autenticación, a continuación, se debe especificar en el `FormsMaps.Init("AUTHORIZATION_TOKEN")` llamada al método, para autenticar la aplicación con Bing Maps.

<a name="Using_Maps" />

## <a name="map-configuration"></a>Configuración de asignación

Consulte la [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) en el ejemplo MobileCRM para obtener un ejemplo de cómo se puede usar el control de mapa en el código. Un simple `MapPage` clase podría parecerse a este: tenga en cuenta que un nuevo `MapSpan` se crea para colocar la vista del mapa:

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

También se puede cambiar el contenido del mapa estableciendo el `MapType` propiedad para mostrar un mapa de calle normal (predeterminado), imágenes de satélite o una combinación de ambos.

```csharp
map.MapType == MapType.Street;
```

Válido `MapType` los valores son:

-  Híbrido
-  Satélite
-  Calle (predeterminado)

### <a name="map-region-and-mapspan"></a>Región del mapa y MapSpan

Como se muestra en el fragmento de código anterior, proporcionando un `MapSpan` instancia a un constructor de asignación establece la vista inicial (punto central y el nivel de zoom) de la asignación cuando se cargue. El `MoveToRegion` método en la clase map, a continuación, puede utilizarse para cambiar el nivel de posición o zoom del mapa. Hay dos maneras de crear un nuevo `MapSpan` instancia:

-  **MapSpan.FromCenterAndRadius()** -método estático para crear un intervalo de un `Position` y especificando un `Distance` .
-  **New () de MapSpan** -constructor que usa un `Position` y los grados de latitud y longitud para mostrar.


Para cambiar el nivel de zoom del mapa sin modificar la ubicación, cree un nuevo `MapSpan` utilizando la ubicación actual de la `VisibleRegion.Center` propiedad del control de mapa. Un `Slider` podría usarse para controlar el zoom del mapa similar al siguiente (sin embargo, hacer zoom directamente en el control de mapa, actualmente no puede actualizar el valor del control deslizante):

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![Mapas con zoom](map-images/maps-zoom-sml.png "Zoom del Control de mapa")](map-images/maps-zoom.png#lightbox "Zoom del Control de mapa")

### <a name="map-pins"></a>Asignar PIN

Se pueden marcar las ubicaciones en el mapa con `Pin` objetos.

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

`PinType` puede establecerse en uno de los valores siguientes, que pueden afectar a la manera en que se procesa el pin (según la plataforma):

-  Genérico
-  Lugar
-  SavedPin
-  SearchResult

### <a name="map-clicks"></a>Clics de mapa

`Map` define un `MapClicked` evento que se desencadena cuando se pulsa el mapa. El `MapClickedEventArgs` objeto que acompaña a la `MapClicked` el evento tiene una propiedad única denominada `Position`, del tipo `Position`. Cuando se desencadena el evento, el valor de la `Position` propiedad está establecida en la ubicación del mapa que se ha punteado.

En el ejemplo de código siguiente se muestra un controlador de eventos para el `MapClicked` eventos:

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

En este ejemplo, el `OnMapClicked` controlador de eventos genera la latitud y longitud que representa la ubicación del mapa derivados.

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>Crear un mapa en XAML

MAPS también se pueden crear en XAML, como se muestra en este ejemplo:

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map x:Name="MyMap"
                  Clicked="OnMapClicked"
                  WidthRequest="320"
                  HeightRequest="200"                  
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Más `xmlns` definición de espacio de nombres es necesario para hacer referencia a los controles de xamarin.Forms.Maps para.

El `MapRegion` y `Pins` se puede establecer en el código mediante la referencia con nombre para el `Map`:

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="populate-a-map-with-data-using-data-binding"></a>Rellenar un mapa con datos mediante el enlace de datos

El [ `Map` ](xref:Xamarin.Forms.Maps.Map) clase también expone las siguientes propiedades:

- `ItemsSource` : especifica la colección de `IEnumerable` elementos para mostrar.
- `ItemTemplate` : especifica el [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para aplicar a cada elemento de la colección de elementos mostrados.
- `ItemTemplateSelector`: especifica el [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir una [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para un elemento en tiempo de ejecución.

> [!NOTE]
> La propiedad `ItemTemplate` tiene prioridad cuando están establecidas ambas propiedades `ItemTemplate` e `ItemTemplateSelector`.

Un [ `Map` ](xref:Xamarin.Forms.Maps.Map) se pueden rellenar con datos mediante el uso de enlace de datos para enlazar su `ItemsSource` propiedad a un `IEnumerable` colección:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

El `ItemsSource` datos de la propiedad se enlaza a la `Locations` propiedad del modelo de vista conectada, que devuelve un `ObservableCollection` de `Location` objetos, que es un tipo personalizado. Cada `Location` objeto define `Address` y `Description` propiedades de tipo `string`y un `Position` propiedad de tipo [ `Position` ](xref:Xamarin.Forms.Maps.Position).

La apariencia de cada elemento de la `IEnumerable` colección se define estableciendo la `ItemTemplate` propiedad a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que contiene un [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) objeto que se enlaza a datos propiedades adecuadas.

El siguientes capturas de pantalla se muestra un [ `Map` ](xref:Xamarin.Forms.Maps.Map) mostrando un [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) colección utilizando el enlace de datos:

[![Captura de pantalla de mapa con datos enlazados PIN, en iOS y Android](map-images/pins-itemssource.png "chinchetas con datos enlazados")](map-images/pins-itemssource-large.png#lightbox "chinchetas con datos enlazados")

### <a name="choose-item-appearance-at-runtime"></a>Elija la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento en el `IEnumerable` se puede elegir la colección en tiempo de ejecución, según el valor del elemento, estableciendo el `ItemTemplateSelector` propiedad a un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:WorkingWithMaps"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <ContentPage.Resources>
        <local:MapItemTemplateSelector x:Key="MapItemTemplateSelector">
            <local:MapItemTemplateSelector.DefaultTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </local:MapItemTemplateSelector.DefaultTemplate>
            <local:MapItemTemplateSelector.XamarinTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="Xamarin!" />
                </DataTemplate>
            </local:MapItemTemplateSelector.XamarinTemplate>    
        </local:MapItemTemplateSelector>
    </ContentPage.Resources>

    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}"
                  ItemTemplateSelector="{StaticResource MapItemTemplateSelector}" />
        ...
    </Grid>
</ContentPage>
```

El ejemplo siguiente se muestra la `MapItemTemplateSelector` clase:

```csharp
public class MapItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Location)item).Address.Contains("San Francisco") ? XamarinTemplate : DefaultTemplate;
    }
}
```

La clase `MapItemTemplateSelector` define las propiedades `DefaultTemplate` y `XamarinTemplate` de tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se establecen para diferentes plantillas de datos. El `OnSelectTemplate` método devuelve el `XamarinTemplate`, que muestra "Xamarin" como una etiqueta cuando un `Pin` se pulsa, cuando el elemento tiene una dirección que contenga "San Francisco". Cuando el elemento no tiene una dirección que contenga "San Francisco", la `OnSelectTemplate` método devuelve el `DefaultTemplate`.

Para obtener más información acerca de los selectores de plantilla de datos, vea [creando un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [MapsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/)
- [Asignar a un representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Creación de un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
