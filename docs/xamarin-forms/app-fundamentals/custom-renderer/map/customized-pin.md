---
title: Personalizar un Pin de mapa
description: "Este artículo demuestra cómo crear a un representador personalizado para el control de mapa que muestra una asignación nativo con un NIP personalizado y una vista personalizada de los datos de pin en cada plataforma."
ms.topic: article
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: c0846e959b4a4cfec9417de59125a8665b648a76
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="customizing-a-map-pin"></a>Personalizar un Pin de mapa

_Este artículo demuestra cómo crear a un representador personalizado para el control de mapa que muestra una asignación nativo con un NIP personalizado y una vista personalizada de los datos de pin en cada plataforma._

Cada vista Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Cuando un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) se representa por una aplicación de Xamarin.Forms en iOS, el `MapRenderer` se crea la instancia de clase, que a su vez crea una instancia nativo `MKMapView` control. En la plataforma Android, el `MapRenderer` clase crea instancias nativo `MapView` control. En la plataforma Universal de Windows (UWP), el `MapRenderer` nativo crea una instancia de clase `MapControl`. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y representador, consulte [clases del representador Base y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

En el diagrama siguiente ilustra la relación entre el [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) y los controles nativos correspondientes que lo implementan:

![](customized-pin-images/map-classes.png "Relación entre el Control de mapa y los controles nativos de implementación")

El proceso de representación se puede utilizar para implementar las personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) el mapa personalizado de Xamarin.Forms.
1. [Crear](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para el mapa en cada plataforma.

Cada elemento ahora se tratarán a su vez, para implementar un `CustomMap` representador que muestra una asignación nativo con un NIP personalizado y una vista personalizada de los datos de pin en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) debe inicializarse y configurado antes de su uso. Para obtener más información, consulte [ `Maps Control` ](~/xamarin-forms/user-interface/map.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Creación del mapa personalizado

Se puede crear un control de mapa personalizado subclases el [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) de la clase, como se muestra en el ejemplo de código siguiente:

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

El `CustomMap` control se crea en el proyecto de biblioteca (PCL) de clases portables y define la API para la asignación personalizada. El mapa personalizado expone la `CustomPins` propiedad que representa la colección de `CustomPin` objetos que se representará el control de mapa nativos en cada plataforma. La `CustomPin` clase se muestra en el ejemplo de código siguiente:

```csharp
public class CustomPin : Pin
{
  public string Id { get; set; }
  public string Url { get; set; }
}
```

Esta clase define un `CustomPin` como hereda las propiedades de la [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/) clase y la adición de `Id`, y `Url` propiedades.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Consumir el mapa personalizado

El `CustomMap` control puede hacer referencia en XAML en el proyecto PCL declarar un espacio de nombres para su ubicación y usando el prefijo de espacio de nombres en el control de mapa personalizado. El siguiente ejemplo de código muestra cómo el `CustomMap` control puede utilizarse en una página XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <ContentPage.Content>
        <local:CustomMap x:Name="myMap" MapType="Street"
          WidthRequest="{x:Static local:App.ScreenWidth}"
          HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

El `local` prefijo de espacio de nombres puede tener cualquier nombre. Sin embargo, el `clr-namespace` y `assembly` valores deben coincidir con los detalles de la asignación personalizada. Una vez que se declara el espacio de nombres, el prefijo se utiliza para hacer referencia a la asignación personalizada.

El siguiente ejemplo de código muestra cómo el `CustomMap` control puede utilizarse en una página de C#:

```csharp
public class MapPageCS : ContentPage
{
  public MapPageCS ()
  {
    var customMap = new CustomMap {
      MapType = MapType.Street,
      WidthRequest = App.ScreenWidth,
      HeightRequest = App.ScreenHeight
    };
    ...

    Content = customMap;
  }
}
```

El `CustomMap` instancia se utilizará para mostrar el mapa nativo en cada plataforma. Tiene [ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/) propiedad establece el estilo de presentación de la [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/), con los valores posibles que se define en el [ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/) enumeración. Para iOS y Android, el ancho y alto del mapa se establece a través de propiedades de la `App` clase que se inicializan en los proyectos específicos de la plataforma.

La ubicación del mapa y el PIN contiene, se inicializan tal y como se muestra en el ejemplo de código siguiente:

```csharp
public MapPage ()
{
  ...
  var pin = new CustomPin {
    Type = PinType.Place,
    Position = new Position (37.79752, -122.40183),
    Label = "Xamarin San Francisco Office",
    Address = "394 Pacific Ave, San Francisco CA",
    Id = "Xamarin",
    Url = "http://xamarin.com/about/"
  };

  customMap.CustomPins = new List<CustomPin> { pin };
  customMap.Pins.Add (pin);
  customMap.MoveToRegion (MapSpan.FromCenterAndRadius (
    new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
}
```

Esta inicialización agrega un NIP personalizado y coloca la vista del mapa con la [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) método, que cambia la posición y el nivel de zoom del mapa mediante la creación de un [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) desde un [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) y un [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

Ahora puede agregarse un representador personalizado a cada proyecto de aplicación para personalizar los controles nativos de mapa.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Crear al representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `MapRenderer` clase que representa el mapa personalizado.
1. Invalidar el `OnElementChanged` método que representa el mapa personalizado y lógica de escritura para personalizarlo. Se llama a este método cuando se crea la asignación personalizada de Xamarin.Forms correspondiente.
1. Agregar un `ExportRenderer` atributo a la clase de representador personalizado para especificar que se utilizará para representar el mapa personalizado de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no se ha registrado un representador personalizado, se usará el representador predeterminado para la clase base del control.

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](customized-pin-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado CustomMap")

El `CustomMap` control se representa mediante clases de representador específico de la plataforma, que se derivan la `MapRenderer` clase para cada plataforma. Esto da como resultado de cada `CustomMap` de control se represente con controles específicos de la plataforma, como se muestra en las capturas de pantalla siguiente:

![](customized-pin-images/screenshots.png "CustomMap en cada plataforma")

El `MapRenderer` clase expone el `OnElementChanged` método, que se llama cuando se crea la asignación personalizada de Xamarin.Forms para representar el control nativo correspondiente. Este método toma una `ElementChangedEventArgs` parámetro que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento Xamarin.Forms que el representador *era* adjunta y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo la `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `CustomMap` instancia.

Una versión reemplazada de la `OnElementChanged` método en cada clase de representador específico de la plataforma, es el lugar donde realice la personalización de control nativo. Una referencia con tipo para el control nativo que se va a usar en la plataforma puede obtenerse a través del `Control` propiedad. Además, una referencia al control Xamarin.Forms que se esté representando puede obtenerse a través de la `Element` propiedad.

Se debe tener cuidado al suscribirse a los controladores de eventos en el `OnElementChanged` método, como se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

Se debe configurar el control nativo y controladores de eventos suscribirse a solo cuando el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms. Del mismo modo, los controladores de eventos que se han suscrito debe cancelar su suscripción solo cuando se cambia el elemento que está vinculado el representador. Este enfoque le ayudará a crear a un representador personalizado que no sufren pérdidas de memoria.

Cada clase de representador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control personalizado Xamarin.Forms que se procesa y el nombre del tipo de representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes tratan la implementación de cada clase de representador personalizado específico de la plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado de iOS

Las capturas de pantalla siguientes muestran la asignación, antes y después de la personalización:

![](customized-pin-images/map-layout-ios.png "Control de mapa antes y después de la personalización")

En iOS se denomina el pin un *anotación*, y puede ser una imagen personalizada o un pin de distintos colores definidos por el sistema. Opcionalmente pueden mostrar anotaciones un *llamada*, que se muestra en respuesta a la que el usuario seleccione la anotación. Muestra la llamada la `Label` y `Address` propiedades de la `Pin` instancia opcional izquierda y derecha accesorios vistas. En la captura de pantalla anterior, la vista de accesorio izquierda es la imagen de un mono, con la vista derecha accesorio que se va a la *información* botón.

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma de iOS:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        UIView customPinView;
        List<CustomPin> customPins;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        ...
    }
}
```

El `OnElementChanged` método realiza la siguiente configuración de la [ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/) instancia, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:

- El [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/) propiedad está establecida en el `GetViewForAnnotation` método. Este método se llama cuando el [ubicación de la anotación se vuelve visible en el mapa](#Displaying_the_Annotation)y se utiliza para personalizar la antes de anotación para mostrar.
- Controladores de eventos para el `CalloutAccessoryControlTapped`, `DidSelectAnnotationView`, y `DidDeselectAnnotationView` se registran eventos. Estos eventos activan cuando el usuario [puntea el accesorio derecho de la llamada](#Tapping_on_the_Right_Callout_Accessory_View)y cuando el usuario [selecciona](#Selecting_the_Annotation) y [anula la selección de](#Deselecting_the_Annotation) la anotación, respectivamente. Los eventos son cancelar su suscripción cuando se asocia el elemento el representador de cambios.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Mostrar la anotación

El `GetViewForAnnotation` método se llama cuando la ubicación de la anotación se vuelve visible en el mapa y se utiliza para personalizar la antes de anotación para mostrar. Una anotación tiene dos partes:

- `MkAnnotation` : incluye el título, subtítulo y ubicación de la anotación.
- `MkAnnotationView` : contiene la imagen para representar la anotación y, opcionalmente, una llamada que se muestra cuando el usuario puntee en la anotación.

El `GetViewForAnnotation` método acepta un `IMKAnnotation` que contiene los datos de la anotación y devuelve un `MKAnnotationView` para su presentación en el mapa y se muestra en el ejemplo de código siguiente:

```csharp
MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null) {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Id);
    if (annotationView == null) {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Id);
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Id = customPin.Id;
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Este método garantiza que la anotación se mostrará como una imagen personalizada, en lugar de como pin definidos por el sistema y que cuando la anotación que se derivan una llamada se mostrará que incluye contenido adicional a la izquierda y derecha del título de la anotación y dirección . Esto se consigue como sigue:

1. El `GetCustomPin` método se llama para devolver los datos de NIP personalizado para la anotación.
1. Para ahorrar memoria, la vista de la anotación está agrupada para volver a usar con la llamada a [ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/).
1. El `CustomMKAnnotationView` clase extiende la `MKAnnotationView` clase con `Id` y `Url` propiedades que corresponden a unas propiedades idénticas en el `CustomPin` instancia. Una nueva instancia de la `CustomMKAnnotationView` se crea, siempre que la anotación está `null`:
  - El `CustomMKAnnotationView.Image` propiedad se establece en la imagen que representará la anotación en el mapa.
  - El `CustomMKAnnotationView.CalloutOffset` propiedad está establecida en un `CGPoint` que especifica que la llamada se centrará por encima de la anotación.
  - El `CustomMKAnnotationView.LeftCalloutAccessoryView` propiedad está establecida en una imagen de un mono que va a aparecer a la izquierda del título de la anotación y dirección.
  - El `CustomMKAnnotationView.RightCalloutAccessoryView` propiedad está establecida en un *información* botón que va a aparecer a la derecha del título de la anotación y dirección.
  - El `CustomMKAnnotationView.Id` propiedad está establecida en el `CustomPin.Id` propiedad devuelta por la `GetCustomPin` método. Esto permite que la anotación que se va a ser identificado para que ocupen [llamada puede personalizarse aún más](#Selecting_the_Annotation), si lo desea.
  - El `CustomMKAnnotationView.Url` propiedad está establecida en el `CustomPin.Url` propiedad devuelta por la `GetCustomPin` método. La dirección URL se abrirá cuando el usuario [puntee en el botón que se muestra en la vista de accesorios de llamada derecho](#Tapping_on_the_Right_Callout_Accessory_View).
1. El [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/) propiedad está establecida en `true` para que se muestre la llamada al que se derivan la anotación.
1. La anotación se devuelve para su visualización en el mapa.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Al seleccionar la anotación

Cuando el usuario puntea en la anotación, la `DidSelectAnnotationView` desencadena el evento, que a su vez ejecuta la `OnDidSelectAnnotationView` método:

```csharp
void OnDidSelectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  customPinView = new UIView ();

  if (customView.Id == "Xamarin") {
    customPinView.Frame = new CGRect (0, 0, 200, 84);
    var image = new UIImageView (new CGRect (0, 0, 200, 84));
    image.Image = UIImage.FromFile ("xamarin.png");
    customPinView.AddSubview (image);
    customPinView.Center = new CGPoint (0, -(e.View.Frame.Height + 75));
    e.View.AddSubview (customPinView);
  }
}
```

Este método extiende la llamada existente (que contiene vistas izquierdas y derecha accesorios) mediante la adición de un `UIView` instancia a él que contiene una imagen del logotipo de Xamarin, siempre que la anotación seleccionada tiene su `Id` propiedad establecida en `Xamarin`. Esto permite escenarios donde se pueden mostrar llamadas diferentes para distintas anotaciones. El `UIView` instancia se mostrarán centrado por encima de la llamada existente.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Puntee en la vista derecha accesorio de llamada

Cuando el usuario puntea en el *información* botón en la vista de accesorios de llamada correcta, el `CalloutAccessoryControlTapped` desencadena el evento, que a su vez ejecuta la `OnCalloutAccessoryControlTapped` método:

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

Este método abre un explorador web y se desplaza a la dirección almacenada en la `CustomMKAnnotationView.Url` propiedad. Tenga en cuenta que la dirección se ha definido al crear el `CustomPin` colección en el proyecto PCL.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Anulando la selección de la anotación

Cuando la anotación se muestra y el usuario puntea en el mapa, el `DidDeselectAnnotationView` desencadena el evento, que a su vez ejecuta la `OnDidDeselectAnnotationView` método:

```csharp
void OnDidDeselectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  if (!e.View.Selected) {
    customPinView.RemoveFromSuperview ();
    customPinView.Dispose ();
    customPinView = null;
  }
}
```

Este método garantiza cuando la llamada existente no está seleccionada, la parte ampliada de la llamada (la imagen del logotipo de Xamarin) también dejará de mostrarse y publicará sus recursos.

Para obtener más información acerca de cómo personalizar un `MKMapView` de la instancia, consulte [iOS mapas](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

Las capturas de pantalla siguientes muestran la asignación, antes y después de la personalización:

![](customized-pin-images/map-layout-android.png "Control de mapa antes y después de la personalización")

En Android se denomina el pin un *marcador*, y puede ser una imagen personalizada o un marcador definido por el sistema de distintos colores. Pueden mostrar marcadores un *ventana información*, que se muestra en respuesta al usuario puntee en el marcador. La ventana de información muestra la `Label` y `Address` propiedades de la `Pin` una instancia y se pueden personalizar para incluir otro tipo de contenido. Sin embargo, solo una ventana de información se puede mostrar a la vez.

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.Droid
{
    public class CustomMapRenderer : MapRenderer, GoogleMap.IInfoWindowAdapter
    {
        List<CustomPin> customPins;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                NativeMap.InfoWindowClick -= OnInfoWindowClick;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                customPins = formsMap.CustomPins;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(GoogleMap map)
        {
            base.OnMapReady(map);

            NativeMap.InfoWindowClick += OnInfoWindowClick;
            NativeMap.SetInfoWindowAdapter(this);
        }
        ...
    }
}
```

Siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms, la `OnElementChanged` llamadas al método el `MapView.GetMapAsync` método, que obtiene subyacente `GoogleMap` que está enlazado a la vista. Una vez el `GoogleMap` instancia está disponible, el `OnMapReady` se invocará la invalidación. Este método registra un controlador de eventos para el `InfoWindowClick` evento, que se desencadena cuando el [se hace clic en la ventana de información](#Clicking_on_the_Info_Window)y se canceló la suscripción de sólo cuando el elemento el representador se adjunta a los cambios. El `OnMapReady` invalidar también llamadas el `SetInfoWindowAdapter` método para especificar que el `CustomMapRenderer` instancia de la clase proporcionará los métodos para personalizar la ventana de información.

El `CustomMapRenderer` la clase implementa la `GoogleMap.IInfoWindowAdapter` a la interfaz [personalizar la ventana de información](#Customizing_the_Info_Window). Esta interfaz especifica que se deben implementar los métodos siguientes:

- `public Android.Views.View GetInfoWindow(Marker marker)` : Este método se llama para devolver una ventana de información personalizada para un marcador. Si devuelve `null`, se utilizará la representación de ventana predeterminado. Si devuelve un `View`, a continuación, que `View` se colocarán dentro del marco de ventana de información.
- `public Android.Views.View GetInfoContents(Marker marker)` : Este método se llama para devolver un `View` que contiene el contenido de la ventana de información y solo se llamará si la `GetInfoWindow` método `null`. Si devuelve `null`, se utilizará la representación predeterminada del contenido de la ventana de información.

En la aplicación de ejemplo, solo el contenido de la ventana de información se ha personalizado, lo cual la `GetInfoWindow` método `null` para habilitar esta opción.

#### <a name="customizing-the-marker"></a>Personalizar el marcador

El icono utilizado para representar un marcador puede personalizarse mediante una llamada a la `MarkerOptions.SetIcon` método. Esto puede realizarse al invalidar la `CreateMarker` método, que se invoca para cada `Pin` que se agrega al mapa:

```csharp
protected override MarkerOptions CreateMarker(Pin pin)
{
    var marker = new MarkerOptions();
    marker.SetPosition(new LatLng(pin.Position.Latitude, pin.Position.Longitude));
    marker.SetTitle(pin.Label);
    marker.SetSnippet(pin.Address);
    marker.SetIcon(BitmapDescriptorFactory.FromResource(Resource.Drawable.pin));
    return marker;
}
```

Este método crea un nuevo `MarkerOption` instancia para cada `Pin` instancia. Después de establecer la posición, la etiqueta y la dirección del marcador, su icono está establecido con el `SetIcon` método. Este método toma un `BitmapDescriptor` objeto que contiene los datos necesarios para representar el icono, con el `BitmapDescriptorFactory` clase proporciona métodos auxiliares para simplificar la creación de la `BitmapDescriptor`.

Para obtener más información sobre el uso de la `BitmapDescriptorFactory` clase para personalizar un marcador, vea [personalizar un marcador](~/android/platform/maps-and-location/maps/maps-api.md).

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Personalizar la ventana de información

Cuando un usuario puntea en el marcador, el `GetInfoContents` se ejecuta el método, siempre que el `GetInfoWindow` método `null`. El siguiente ejemplo de código muestra la `GetInfoContents` método:

```csharp
public Android.Views.View GetInfoContents (Marker marker)
{
  var inflater = Android.App.Application.Context.GetSystemService (Context.LayoutInflaterService) as Android.Views.LayoutInflater;
  if (inflater != null) {
    Android.Views.View view;

    var customPin = GetCustomPin (marker);
    if (customPin == null) {
      throw new Exception ("Custom pin not found");
    }

    if (customPin.Id == "Xamarin") {
      view = inflater.Inflate (Resource.Layout.XamarinMapInfoWindow, null);
    } else {
      view = inflater.Inflate (Resource.Layout.MapInfoWindow, null);
    }

    var infoTitle = view.FindViewById<TextView> (Resource.Id.InfoWindowTitle);
    var infoSubtitle = view.FindViewById<TextView> (Resource.Id.InfoWindowSubtitle);

    if (infoTitle != null) {
      infoTitle.Text = marker.Title;
    }
    if (infoSubtitle != null) {
      infoSubtitle.Text = marker.Snippet;
    }

    return view;
  }
  return null;
}
```

Este método devuelve un `View` que contiene el contenido de la ventana de información. Esto se consigue como sigue:

- Un `LayoutInflater` se recupera la instancia. Se utiliza para crear una instancia de un archivo XML de diseño en su correspondiente `View`.
- El `GetCustomPin` método se llama para devolver los datos de NIP personalizado para la ventana de información.
- El `XamarinMapInfoWindow` diseño se aumenta si el `CustomPin.Id` propiedad es igual a `Xamarin`. En caso contrario, el `MapInfoWindow` se aumenta el diseño. Esto permite escenarios donde se pueden mostrar los diseños de ventana de información diferente para distintos marcadores.
- El `InfoWindowTitle` y `InfoWindowSubtitle` recursos se recuperan del diseño aumentada y sus `Text` propiedades se establecen en los datos correspondientes de la `Marker` instancia, siempre que los recursos no están `null`.
- El `View` instancia se devuelve para su visualización en el mapa.

> [!NOTE]
> Una ventana de información no está activo `View`. En su lugar, Android, se convertirá la `View` a una variable static de mapa de bits y que mostrar como una imagen. Esto significa que, durante una ventana de información, puede responder a un evento click, no puede responder a los eventos de toque o gestos, y los controles individuales en la ventana de información no pueden responder a sus propios eventos de clic.

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>Al hacer clic en la ventana de información

Cuando el usuario hace clic en la ventana de información, el `InfoWindowClick` desencadena el evento, que a su vez ejecuta la `OnInfoWindowClick` método:

```csharp
void OnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
  var customPin = GetCustomPin (e.Marker);
  if (customPin == null) {
    throw new Exception ("Custom pin not found");
  }

  if (!string.IsNullOrWhiteSpace (customPin.Url)) {
    var url = Android.Net.Uri.Parse (customPin.Url);
    var intent = new Intent (Intent.ActionView, url);
    intent.AddFlags (ActivityFlags.NewTask);
    Android.App.Application.Context.StartActivity (intent);
  }
}
```

Este método abre un explorador web y se desplaza a la dirección almacenada en la `Url` propiedad de los recuperados `CustomPin` de instancia para el `Marker`. Tenga en cuenta que la dirección se ha definido al crear el `CustomPin` colección en el proyecto PCL.

Para obtener más información acerca de cómo personalizar un `MapView` de la instancia, consulte [API de mapas de](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Crear al representador personalizado en la plataforma Universal de Windows

Las capturas de pantalla siguientes muestran la asignación, antes y después de la personalización:

![](customized-pin-images/map-layout-uwp.png "Control de mapa antes y después de la personalización")

En UWP se denomina el pin un *icono de mapa*, y puede ser una imagen personalizada o la imagen predeterminada definida por el sistema. Puede mostrar un icono de mapa de un `UserControl`, que se muestra en respuesta al usuario punteando en el icono de mapa. El `UserControl` puede mostrar cualquier contenido, incluido el `Label` y `Address` propiedades de la `Pin` instancia.

En el ejemplo de código siguiente se muestra al representador personalizado UWP:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        MapControl nativeMap;
        List<CustomPin> customPins;
        XamarinMapOverlay mapOverlay;
        bool xamarinOverlayShown = false;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                nativeMap.MapElementClick -= OnMapElementClick;
                nativeMap.Children.Clear();
                mapOverlay = null;
                nativeMap = null;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                nativeMap = Control as MapControl;
                customPins = formsMap.CustomPins;

                nativeMap.Children.Clear();
                nativeMap.MapElementClick += OnMapElementClick;

                foreach (var pin in customPins)
                {
                    var snPosition = new BasicGeoposition { Latitude = pin.Pin.Position.Latitude, Longitude = pin.Pin.Position.Longitude };
                    var snPoint = new Geopoint(snPosition);

                    var mapIcon = new MapIcon();
                    mapIcon.Image = RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///pin.png"));
                    mapIcon.CollisionBehaviorDesired = MapElementCollisionBehavior.RemainVisible;
                    mapIcon.Location = snPoint;
                    mapIcon.NormalizedAnchorPoint = new Windows.Foundation.Point(0.5, 1.0);

                    nativeMap.MapElements.Add(mapIcon);                    
                }
            }
        }
        ...
    }
}
```

El `OnElementChanged` método realiza las operaciones siguientes, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:

- Borra la `MapControl.Children` colección para quitar los elementos de interfaz de usuario existentes del mapa, antes de registrar un controlador de eventos para el `MapElementClick` eventos. Este evento se desencadena cuando el usuario puntea o hace clic en un `MapElement` en el `MapControl`y se canceló la suscripción de sólo cuando el elemento el representador se adjunta a los cambios.
- Cada código pin en el `customPins` recopilación se muestra en la ubicación geográfica correcta en el mapa como sigue:
  - La ubicación para el pin se crea como un `Geopoint` instancia.
  - Un `MapIcon` instancia se crea para representar el pin.
  - La imagen utilizada para representar el `MapIcon` se especifica estableciendo el `MapIcon.Image` propiedad. Sin embargo, imagen del icono de mapa no se siempre garantiza que se mostrará, como pueden estar ocultos por otros elementos en el mapa. Por lo tanto, el icono de mapa `CollisionBehaviorDesired` propiedad está establecida en `MapElementCollisionBehavior.RemainVisible`, para asegurarse de que permanece visible.
  - La ubicación de la `MapIcon` se especifica estableciendo el `MapIcon.Location` propiedad.
  - El `MapIcon.NormalizedAnchorPoint` propiedad se establece en la ubicación aproximada del puntero en la imagen. Si esta propiedad mantiene su valor predeterminado de (0,0), que representa la esquina superior izquierda de la imagen, pueden dar lugar a cambios en el nivel de zoom del mapa en la imagen que apunta a una ubicación diferente.
  - El `MapIcon` instancia se agrega a la `MapControl.MapElements` colección. Esto da como resultado el icono de mapa que se muestra en el `MapControl`.

> [!NOTE]
> Cuando se utiliza la misma imagen para varios iconos de mapa, la `RandomAccessStreamReference` instancia debe declararse en el nivel de página o la aplicación para obtener el mejor rendimiento.

#### <a name="displaying-the-usercontrol"></a>Mostrar el control de usuario

Cuando un usuario puntee en el icono de mapa, el `OnMapElementClick` se ejecuta el método. En el ejemplo de código siguiente se muestra este método:

```csharp
private void OnMapElementClick(MapControl sender, MapElementClickEventArgs args)
{
    var mapIcon = args.MapElements.FirstOrDefault(x => x is MapIcon) as MapIcon;
    if (mapIcon != null)
    {
        if (!xamarinOverlayShown)
        {
            var customPin = GetCustomPin(mapIcon.Location.Position);
            if (customPin == null)
            {
                throw new Exception("Custom pin not found");
            }

            if (customPin.Id == "Xamarin")
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Pin.Position.Latitude, Longitude = customPin.Pin.Position.Longitude };
                var snPoint = new Geopoint(snPosition);

                nativeMap.Children.Add(mapOverlay);
                MapControl.SetLocation(mapOverlay, snPoint);
                MapControl.SetNormalizedAnchorPoint(mapOverlay, new Windows.Foundation.Point(0.5, 1.0));
                xamarinOverlayShown = true;
            }
        }
        else
        {
            nativeMap.Children.Remove(mapOverlay);
            xamarinOverlayShown = false;
        }
    }
}
```

Este método crea un `UserControl` instancia que muestra información sobre el código pin. Esto se consigue como sigue:

- El `MapIcon` se recupera la instancia.
- El `GetCustomPin` método se llama para devolver los datos de NIP personalizado que se mostrará.
- Un `XamarinMapOverlay` se crea una instancia para mostrar los datos de NIP personalizado. Esta clase es un control de usuario.
- La ubicación geográfica en la que se va a mostrar el `XamarinMapOverlay` a la instancia en la `MapControl` se crea como un `Geopoint` instancia.
- El `XamarinMapOverlay` instancia se agrega a la `MapControl.Children` colección. Esta colección contiene elementos de interfaz de usuario XAML que se mostrará en el mapa.
- La ubicación geográfica de la `XamarinMapOverlay` instancia en el mapa se establece mediante una llamada a la `SetLocation` método.
- La ubicación relativa en el `XamarinMapOverlay` instancia que corresponde a la ubicación especificada, se establece mediante una llamada a la `SetNormalizedAnchorPoint` método. Esto garantiza que los cambios en el nivel de zoom del resultado de la asignación en el `XamarinMapOverlay` siempre que se muestran en la ubicación correcta de la instancia.

O bien, si ya se muestra información sobre el código pin en el mapa, puntee en el mapa quita el `XamarinMapOverlay` instancia desde el `MapControl.Children` colección.

#### <a name="tapping-on-the-information-button"></a>Puntee en el botón de información

Cuando el usuario puntea en el *información* botón en el `XamarinMapOverlay` control de usuario, el `Tapped` desencadena el evento, que a su vez ejecuta la `OnInfoButtonTapped` método:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Este método abre un explorador web y se desplaza a la dirección almacenada en la `Url` propiedad de la `CustomPin` instancia. Tenga en cuenta que la dirección se ha definido al crear el `CustomPin` colección en el proyecto PCL.

Para obtener más información acerca de cómo personalizar un `MapControl` de la instancia, consulte [mapas y visión general de la ubicación](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) en MSDN.

## <a name="summary"></a>Resumen

Este artículo muestra cómo crear un representador personalizado para el `Map` control, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propios personalización específica de la plataforma. Xamarin.Forms.Maps proporciona una abstracción de multiplataforma para mostrar los mapas que usan la asignación nativo experimentan de API en cada plataforma para proporcionar una asignación rápida y familiar para los usuarios.


## <a name="related-links"></a>Vínculos relacionados

- [Control de mapas](~/xamarin-forms/user-interface/map.md)
- [Mapas de iOS](~/ios/user-interface/controls/ios-maps/index.md)
- [API de Maps](~/android/platform/maps-and-location/maps/maps-api.md)
- [Pin personalizada (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
