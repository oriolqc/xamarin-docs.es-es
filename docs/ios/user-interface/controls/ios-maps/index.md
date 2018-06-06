---
title: Mapas de Xamarin.iOS
description: Este documento describe el marco de trabajo de MapKit de iOS y cómo se utiliza con Xamarin.iOS. Describe cómo agregar un mapa, estilo, realizar una panorámica y zoom, trabajar con la ubicación del usuario, agregar anotaciones, trabajar con llamadas y superposiciones y realizar la búsqueda local.
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 3649c8eb9c8c1a82940b8e2eece7d2bfd005d024
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790235"
---
# <a name="maps-in-xamarinios"></a>Mapas de Xamarin.iOS

Los mapas son una característica común en todos los sistemas operativos de dispositivos móviles modernos. iOS ofrece compatibilidad de asignación forma nativa mediante el marco del Kit de mapa. Con el Kit de mapa, las aplicaciones pueden agregar fácilmente mapas enriquecidas e interactivas. Estas asignaciones se pueden personalizar de varias maneras, como agregar anotaciones para marcar las ubicaciones en un mapa y superponer los gráficos de formas arbitrarias. Kit de mapa incluso tiene compatibilidad integrada para mostrar la ubicación actual de un dispositivo.

## <a name="adding-a-map"></a>Agregar un mapa

Agregue una asignación a una aplicación se consigue agregando un `MKMapView` instancia a la jerarquía de la vista, tal y como se muestra a continuación:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` es un `UIView` subclase que muestra una asignación. Basta con agregar la asignación mediante el código anterior genera un mapa interactivo:

 ![](images/00-map.png "Una asignación de ejemplo")

## <a name="map-style"></a>Estilo de mapa

 `MKMapView` es compatible con 3 diferentes estilos de mapas. Para aplicar un estilo de mapa, basta con establecer la `MapType` propiedad en un valor de la `MKMapType` enumeración:
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  Captura de pantalla siguiente se muestran los diferentes estilos de mapa que están disponibles:

 ![](images/01-mapstyles.png "Esta captura de pantalla muestra los diferentes estilos de mapa que están disponibles")

## <a name="panning-and-zooming"></a>Panorámica y zoom

 `MKMapView` incluye compatibilidad con características de interactividad de asignación como:

-  Zoom a través de un gesto de alejar
-  Panorámica a través de un movimiento panorámico


Estas características se pueden habilitar o deshabilitar estableciendo simplemente la `ZoomEnabled` y `ScrollEnabled` propiedades de la `MKMapView` instancia, donde el valor predeterminado es true para ambos. Por ejemplo, para mostrar un mapa estático, basta con establecer las propiedades adecuadas en false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Ubicación del usuario

Además de la interacción del usuario, `MKMapView` también tiene compatibilidad integrada para mostrar la ubicación del dispositivo. Esto consigue utilizando la *Core ubicación* framework. Antes de poder acceder a la ubicación del usuario, debe pedir al usuario. Para ello, cree una instancia de `CLLocationManager` y llame a `RequestWhenInUseAuthorization`.

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Tenga en cuenta que en las versiones de iOS antes 8.0, intentos de llamar a `RequestWhenInUseAuthorization` se producirá un error. Asegúrese de comprobar la versión de iOS antes de realizar esa llamada si piensa admitir las versiones anteriores a 8.

Obtener acceso a la ubicación del usuario también es necesario realizar modificaciones a **Info.plist**. Se deben establecer las siguientes claves relacionadas con los datos de ubicación:

- **NSLocationWhenInUseUsageDescription**: Al obtener acceso a la ubicación del usuario mientras interactúa con la aplicación.
- **NSLocationAlwaysUsageDescription**: Al obtener acceso la aplicación a la ubicación del usuario en segundo plano.

Puede agregar dichas claves abriendo **Info.plist** y seleccionando *origen* en la parte inferior del editor.

Una vez que haya actualizado **Info.plist** y se le solicita al usuario permiso tener acceso a su ubicación, puede mostrar la ubicación del usuario en el mapa estableciendo la `ShowsUserLocation` propiedad en true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "La alerta de acceso de ubicación de permitir")
 
## <a name="annotations"></a>Anotaciones

 `MKMapView` también admite la visualización de imágenes, conocido como anotaciones en un mapa. Puede tratarse de imágenes personalizadas o PIN definidos por el sistema de distintos colores. Por ejemplo, la captura de pantalla siguiente muestra una asignación con un tanto un pin y una imagen personalizada:

 ![](images/03-annotations.png "Esta captura de pantalla muestra una asignación con un tanto un pin y una imagen personalizada")

### <a name="adding-an-annotation"></a>Agregar una anotación

Una anotación propio tiene dos partes:

-  La `MKAnnotation` objeto, que incluye datos acerca de la anotación, como el título y la ubicación de la anotación del modelo.
-  El `MKAnnotationView` , que contiene la imagen para mostrar y, opcionalmente, una llamada que se muestra cuando el usuario puntee en la anotación.


Kit de asignación usa el patrón de delegación de iOS para agregar anotaciones a un mapa, donde el `Delegate` propiedad de la `MKMapView` está establecida en una instancia de un `MKMapViewDelegate`. Se trata de implementación de este delegado que es responsable de devolver el `MKAnnotationView` de una anotación.

Para agregar una anotación, la anotación se agrega por primera vez mediante una llamada a `AddAnnotations` en el `MKMapView` instancia:

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

Cuando la ubicación de la anotación se vuelve visible en el mapa, el `MKMapView` llamará a su delegado `GetViewForAnnotation` método para obtener el `MKAnnotationView` para mostrar.

Por ejemplo, el código siguiente devuelve proporcionada por el sistema `MKPinAnnotationView`:

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>Reutilizar anotaciones

Para ahorrar memoria, `MKMapView` permite ver de anotación 's para que se pueden agrupar para su reutilización, similar a la manera en que se reutilizan las celdas de tabla. Obtener una vista de anotación del grupo se realiza con una llamada a `DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Mostrar llamadas

Como se mencionó anteriormente, una anotación puede mostrarse una llamada. Para mostrar una llamada simplemente establezca `CanShowCallout` en true en la `MKAnnotationView`. En el título de la anotación que se muestra cuando la anotación que se derivan, tal como se muestra el resultado:

 ![](images/04-callout.png "El título de las anotaciones que se va a mostrar")

### <a name="customizing-the-callout"></a>Personalización de la llamada

La llamada puede personalizarse para mostrar vistas de accesorios derecha e izquierdas, tal y como se muestra a continuación:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Este código genera la siguiente llamada:

 ![](images/05-callout-accessories.png "Una llamada de ejemplo")

Para controlar el usuario al puntear en el descriptor de acceso correcta, simplemente se implementa el `CalloutAccessoryControlTapped` método en el `MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Superposiciones

Otra forma de gráficos de la capa en un mapa es con las superposiciones. Superposiciones admiten el dibujo contenido gráfica que escale con la asignación a medida que se aplica el zoom. iOS proporciona compatibilidad para varios tipos de gráficos superpuestos, incluidos:

-  Polígonos - se usa normalmente para resaltar algunas región en un mapa.
-  Polilíneas - visto a menudo al mostrar una ruta.
-  Círculos - usados para resaltar un área circular de un mapa.


Además, pueden crearse superposiciones personalizadas para mostrar las geometrías arbitrarias con el código de dibujo granular y personalizado. Por ejemplo, radial tiempo sería un buen candidato para una superposición personalizado.

#### <a name="adding-an-overlay"></a>Agregar una superposición

De forma similar a las anotaciones, agregar una superposición implica 2 partes:

-  Crear un objeto de modelo en la superposición y agregarlo a la `MKMapView` .
-  Crear una vista para la superposición en los `MKMapViewDelegate` .


El modelo de la superposición puede ser cualquier `MKShape` subclase. Incluye Xamarin.iOS `MKShape` subclases de polígonos, polilíneas y círculos; a través de la `MKPolygon`, `MKPolyline` y `MKCircle` clases respectivamente.

Por ejemplo, el código siguiente se utiliza para agregar una `MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

La vista de superposición es un `MKOverlayView` instancia devuelta por la `GetViewForOverlay` en el `MKMapViewDelegate`. Cada `MKShape` le corresponde una `MKOverlayView` que sabe cómo mostrar la forma especificada. Para `MKPolygon` hay `MKPolygonView`. De forma similar, `MKPolyline` corresponde a `MKPolylineView`y para `MKCircle` hay `MKCircleView`.

Por ejemplo, el código siguiente devuelve una `MKCircleView` para un `MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

Se muestra un círculo en el mapa tal como se muestra:

 ![](images/06-circle-overlay.png "Un círculo que aparece en el mapa")

## <a name="local-search"></a>Búsqueda local

iOS incluye una API con el Kit de mapa, lo que permite búsquedas asincrónicas para los puntos de interés en una región geográfica determinada de búsqueda local.

Para llevar a cabo una búsqueda local, una aplicación debe seguir estos pasos:

1.  Crear `MKLocalSearchRequest` objeto.
1.  Crear un `MKLocalSearch` objeto desde el `MKLocalSearchRequest` .
1.  Llame a la `Start` método en la `MKLocalSearch` objeto.
1.  Recuperar la `MKLocalSearchResponse` objeto en una devolución de llamada.


La propia API de búsqueda local proporciona ninguna interfaz de usuario. Incluso que no requiere una asignación que se usará. Sin embargo, para hacer un uso práctico de búsqueda local, una aplicación debe proporcionar una manera de especificar una consulta de búsqueda y mostrar los resultados. Además, puesto que los resultados contendrá los datos de ubicación, hará que a menudo sentido para mostrarlos en un mapa.

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>Agregar una interfaz de usuario de búsqueda Local

Es una manera de aceptar la entrada de búsqueda con un `UISearchBar`, que proporciona un `UISearchController` y mostrará los resultados en una tabla.

El código siguiente agrega el `UISearchController` (que tiene una propiedad de la barra de búsqueda) en el `ViewDidLoad` método `MapViewController`:

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;
            
//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller 
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;

```csharp
Note that you are responsible for incorporating the search bar object into the user interface. In this example, we assigned it to the TitleView of the navigation bar, but if you do not use a navigation controller in your application you will have to find another place to display it.

In this code snippet, we created another custom view controller – `searchResultsController` –  that displays the search results and then we used this object to create our search controller object. We also created a new search updater, which becomes active when the user interacts with the search bar. It receives notifications about searches with each keystroke and is responsible for updating the UI.
We will take a look at how to implement both the `searchResultsController` and the `searchResultsUpdater` later in this guide.

This results in a search bar displayed over the map as shown below:

 ![](images/07-searchbar.png "A search bar displayed over the map")
 


### Displaying the Search Results

To display search results, we need to create a custom View Controller; normally a `UITableViewController`. As shown above, the `searchResultsController` is passed to the constructor of the `searchController` when it is being created.
The following code is an example of how to create this custom View Controller:

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });


    }
}
```

### <a name="updating-the-search-results"></a>Actualizar los resultados de búsqueda

El `SearchResultsUpdater` actúa como intermediario entre el `searchController`de barra de búsqueda y los resultados de búsqueda. 

En este ejemplo hemos tenido que crear en primer lugar el método de búsqueda en el `SearchResultsViewController`. Para ello que debemos crear un `MKLocalSearch` objeto y usarlo para emitir una búsqueda de un `MKLocalSearchRequest`, se recuperan los resultados en una devolución de llamada que se pasa a la `Start` método de la `MKLocalSearch` objeto. A continuación, se devuelven los resultados en un `MKLocalSearchResponse` objeto que contiene una matriz de `MKMapItem` objetos:

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });


}
```

A continuación, en nuestro `MapViewController` vamos a crear una implementación personalizada de `UISearchResultsUpdating`, que se asigna a la `SearchResultsUpdater` propiedad de nuestro `searchController` en el [agregar una interfaz de usuario de búsqueda Local](#Adding_a_Local_Search_UI) sección:

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

La implementación anterior agrega una anotación al mapa cuando se selecciona un elemento de los resultados, tal y como se muestra a continuación:

 ![](images/08-search-results.png "Una anotación que se agrega al mapa cuando se selecciona un elemento de los resultados")
 
> [!IMPORTANT]
> `UISearchController` se ha implementado en iOS 8. Si desea admitir dispositivos anteriores a esto, debe usar `UISearchDisplayController`.



## <a name="summary"></a>Resumen

Este artículo examinada la *mapa* *Kit* framework para iOS. En primer lugar, examinando cómo la `MKMapView` clase permite mapas interactivos que se incluirá en una aplicación. A continuación, muestra cómo personalizar aún más los mapas mediante anotaciones y superposiciones. Por último, examinan las capacidades de búsqueda local que se agregaron al Kit de mapa con iOS 6.1, que muestra cómo usar realizar consultas en función de ubicación para los puntos de interés y agregarlas a un mapa.

## <a name="related-links"></a>Vínculos relacionados

- [SearchController](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
- [MapDemo (ejemplo)](https://developer.xamarin.com/samples/monotouch/MapDemo)
