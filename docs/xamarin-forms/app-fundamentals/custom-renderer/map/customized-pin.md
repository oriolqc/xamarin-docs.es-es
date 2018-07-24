---
title: Personalización de un Pin de mapa
description: En este artículo se muestra cómo crear a un representador personalizado para el control de mapa que muestra una asignación nativa con un NIP personalizado y una vista personalizada de los datos de pin en cada plataforma.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 351119a8b0089f78d4ce98729a1516c3cd7bae7b
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203090"
---
# <a name="customizing-a-map-pin"></a>Personalización de un Pin de mapa

_En este artículo se muestra cómo crear a un representador personalizado para el control de mapa que muestra una asignación nativa con un NIP personalizado y una vista personalizada de los datos de pin en cada plataforma._

Cada vista Xamarin.Forms tiene un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo. Cuando un [ `Map` ](xref:Xamarin.Forms.Maps.Map) se representa mediante una aplicación de Xamarin.Forms en iOS, el `MapRenderer` se crea la instancia de clase, que a su vez crea una instancia nativa `MKMapView` control. En la plataforma Android, el `MapRenderer` nativo crea una instancia de clase `MapView` control. En la plataforma Universal de Windows (UWP), el `MapRenderer` nativo crea una instancia de clase `MapControl`. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y el representador, consulte [clases Base del representador y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama ilustra la relación entre el [ `Map` ](xref:Xamarin.Forms.Maps.Map) y los controles nativos correspondientes que lo implementan:

![](customized-pin-images/map-classes.png "Relación entre el Control de mapa y los controles nativos de implementación")

El proceso de representación puede utilizarse para implementar las personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para un [ `Map` ](xref:Xamarin.Forms.Maps.Map) en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) mapa personalizado de Xamarin.Forms.
1. [Crear](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para el mapa en cada plataforma.

Cada elemento ahora se explicará a su vez, para implementar un `CustomMap` representador que muestra un mapa nativo con un NIP personalizado y una vista personalizada de los datos de pin en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) se deben inicializar y configurar antes de su uso. Para obtener más información, vea [`Maps Control`](~/xamarin-forms/user-interface/map.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Crear el mapa personalizado

Se puede crear un control de mapa personalizado mediante la creación de subclases del [ `Map` ](xref:Xamarin.Forms.Maps.Map) clase, como se muestra en el ejemplo de código siguiente:

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

El `CustomMap` control se crea en el proyecto de biblioteca estándar de .NET y define la API para el mapa personalizado. Mapa personalizado expone el `CustomPins` propiedad que representa la colección de `CustomPin` objetos que se va a representar el control de mapa nativa en cada plataforma. La `CustomPin` clase se muestra en el ejemplo de código siguiente:

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

Esta clase define un `CustomPin` que la herencia de las propiedades de la [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) clase y la adición de un `Url` propiedad.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Consumo de mapa personalizado

El `CustomMap` control puede hacer referencia en XAML en el proyecto de biblioteca estándar de .NET declarando un espacio de nombres para su ubicación y utilizando el prefijo de espacio de nombres en el control de mapa personalizado. El siguiente ejemplo de código muestra cómo el `CustomMap` control puede utilizarse en una página XAML:

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

El `CustomMap` instancia se usará para mostrar el mapa nativo en cada plataforma. Tiene [ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType) propiedad establece el estilo de presentación de la [ `Map` ](xref:Xamarin.Forms.Maps.Map), con los valores posibles que se define en el [ `MapType` ](xref:Xamarin.Forms.Maps.MapType) enumeración. Para iOS y Android, el ancho y alto del mapa se establece a través de las propiedades de la `App` clase que se inicializan en los proyectos específicos de la plataforma.

La ubicación del mapa y los bolos contiene, se inicializan como se muestra en el ejemplo de código siguiente:

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

Esta inicialización se agrega un NIP personalizado y se coloca la vista del mapa con el [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método, que cambia la posición y el nivel de zoom del mapa mediante la creación de un [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) desde un [ `Position` ](xref:Xamarin.Forms.Maps.Position) y un [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

Ahora se puede agregar a un representador personalizado a cada proyecto de aplicación para personalizar los controles nativos de mapa.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Crear al representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `MapRenderer` clase que representa el mapa personalizado.
1. Invalidar el `OnElementChanged` método que representa el mapa personalizado y escribir lógica para personalizarlo. Este método se llama cuando se crea la asignación personalizada de Xamarin.Forms correspondiente.
1. Agregar un `ExportRenderer` a la clase de representador personalizado para especificar que se utilizará para representar el mapa personalizado de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no está registrado un representador personalizado, se usará el representador predeterminado de la clase base del control.

El siguiente diagrama ilustra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](customized-pin-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado CustomMap")

El `CustomMap` control se representa mediante las clases de representador específica de la plataforma, que se derivan de la `MapRenderer` clase para cada plataforma. Esto da como resultado de cada `CustomMap` controlar que se representa con controles específicos de la plataforma, como se muestra en las capturas de pantalla siguiente:

![](customized-pin-images/screenshots.png "CustomMap en cada plataforma")

El `MapRenderer` clase expone la `OnElementChanged` método, que se llama cuando se crea el mapa personalizado de Xamarin.Forms para representar el control nativo correspondiente. Este método toma un `ElementChangedEventArgs` parámetro que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento de Xamarin.Forms que el representador *era* adjunto y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo la `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `CustomMap` instancia.

Una versión reemplazada de la `OnElementChanged` método en cada clase de presentador específica de la plataforma, es el lugar para realizar la personalización de controles nativos. Una referencia con tipo para el control nativo que se va a usar en la plataforma puede obtenerse a través del `Control` propiedad. Además, se puede obtener una referencia al control de Xamarin.Forms que se representa mediante el `Element` propiedad.

Debe tener cuidado al suscribirse a los controladores de eventos en el `OnElementChanged` método, como se muestra en el ejemplo de código siguiente:

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

Se debe configurar el control nativo y suscribir los controladores de eventos sólo cuando se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. De forma similar, los controladores de eventos que se han suscrito a debe cancelar la suscripción solo cuando se cambia el elemento que está conectado el representador. Adoptar este enfoque le ayudará a crear a un representador personalizado que no sufra pérdidas de memoria.

Cada clase de presentador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control personalizado de Xamarin.Forms que se procesa y el nombre de tipo del representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes describen la implementación de cada clase de representador personalizado específico de la plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado en iOS

Las capturas de pantalla siguientes muestran el mapa, antes y después de la personalización:

![](customized-pin-images/map-layout-ios.png "Control de mapa antes y después de la personalización")

En iOS el pin se denomina un *anotación*, y puede ser una imagen personalizada o un pin definido por el sistema de colores distintos. Opcionalmente pueden mostrar anotaciones de un *llamada*, que se muestra en la respuesta al usuario seleccionar la anotación. Muestra la llamada la `Label` y `Address` propiedades de la `Pin` instancia opcional izquierda y derecha accesorios vistas. En la captura de pantalla anterior, la vista izquierda accesorio es la imagen de un objeto monkey, con la vista derecha accesorio que es el *información* botón.

El ejemplo de código siguiente muestra al representador personalizado para la plataforma de iOS:

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

El `OnElementChanged` método realiza la siguiente configuración de la [ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/) de instancia, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- El [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/) propiedad está establecida en el `GetViewForAnnotation` método. Este método se llama cuando el [ubicación de la anotación se vuelve visible en el mapa](#Displaying_the_Annotation)y se usa para personalizar la antes de mostrar la anotación.
- Controladores de eventos para el `CalloutAccessoryControlTapped`, `DidSelectAnnotationView`, y `DidDeselectAnnotationView` se registran los eventos. Estos eventos activan cuando el usuario [pulsa el accesorio derecho de la llamada](#Tapping_on_the_Right_Callout_Accessory_View)y cuando el usuario [selecciona](#Selecting_the_Annotation) y [anula la selección de](#Deselecting_the_Annotation) la anotación, respectivamente. Los eventos se finalizó su suscripción solo cuando el representador de elemento está asociado a los cambios.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Mostrar la anotación

El `GetViewForAnnotation` método se llama cuando la ubicación de la anotación se vuelve visible en el mapa y se usa para personalizar la antes de mostrar la anotación. Una anotación tiene dos partes:

- `MkAnnotation` : incluye el título, subtítulo y ubicación de la anotación.
- `MkAnnotationView` : contiene la imagen para representar la anotación y, opcionalmente, una llamada que se muestra cuando el usuario pulsa la anotación.

El `GetViewForAnnotation` método acepta un `IMKAnnotation` que contiene los datos de la anotación y devuelve un `MKAnnotationView` para su presentación en el mapa y se muestra en el ejemplo de código siguiente:

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null) {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Id.ToString());
    if (annotationView == null) {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Id.ToString());
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Id = customPin.Id.ToString();
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Este método garantiza que la anotación se mostrará como una imagen personalizada, en lugar de como pin definido por el sistema y que, cuando se pulsa la anotación una llamada se mostrará que incluye contenido adicional hacia la izquierda y derecha del título de la anotación y dirección . Esto se logra como sigue:

1. El `GetCustomPin` método se llama para devolver los datos de NIP personalizado para la anotación.
1. Para ahorrar memoria, la vista de la anotación se agrupa para su uso con la llamada a [ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/).
1. El `CustomMKAnnotationView` clase extiende la `MKAnnotationView` clase con `Id` y `Url` propiedades que corresponden a las propiedades idénticas en el `CustomPin` instancia. Una nueva instancia de la `CustomMKAnnotationView` se crea, siempre que la anotación es `null`:
    - El `CustomMKAnnotationView.Image` propiedad está establecida en la imagen que representará la anotación en el mapa.
    - El `CustomMKAnnotationView.CalloutOffset` propiedad está establecida en un `CGPoint` que especifica que la llamada se centrará por encima de la anotación.
    - El `CustomMKAnnotationView.LeftCalloutAccessoryView` propiedad está establecida en una imagen de un objeto monkey del que aparecerá a la izquierda del título de la anotación y dirección.
    - El `CustomMKAnnotationView.RightCalloutAccessoryView` propiedad está establecida en un *información* botón que aparece a la derecha del título de la anotación y dirección.
    - El `CustomMKAnnotationView.Id` propiedad está establecida en el `CustomPin.Id` propiedad devuelta por la `GetCustomPin` método. Esto permite que la anotación pueda identificarse de forma que tenga [llamada puede personalizarse aún más](#Selecting_the_Annotation)si lo desea.
    - El `CustomMKAnnotationView.Url` propiedad está establecida en el `CustomPin.Url` propiedad devuelta por la `GetCustomPin` método. La dirección URL se abrirá cuando el usuario [pulsa el botón se muestra en la vista de accesorios de llamada correcta](#Tapping_on_the_Right_Callout_Accessory_View).
1. El [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/) propiedad está establecida en `true` para que se muestre la llamada cuando se pulsa la anotación.
1. La anotación se devuelve para su visualización en el mapa.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Seleccionar la anotación

Cuando el usuario pulsa en la anotación, la `DidSelectAnnotationView` desencadena el evento, que a su vez ejecuta la `OnDidSelectAnnotationView` método:

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

Este método extiende la llamada existente (que contiene las vistas izquierdas y derecha accesorios) mediante la adición de un `UIView` instancia a él que contiene una imagen del logotipo de Xamarin, siempre que la anotación seleccionada tiene su `Id` propiedad establecida en `Xamarin`. Esto permite escenarios donde se pueden mostrar llamadas diferentes para distintas anotaciones. El `UIView` instancia se muestra centrado por encima de la llamada existente.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Pulsar en la vista derecha accesorio de llamada

Cuando el usuario pulsa el *información* botón en la vista de accesorios de llamada correcta, el `CalloutAccessoryControlTapped` desencadena el evento, que a su vez ejecuta la `OnCalloutAccessoryControlTapped` método:

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

Este método abre un explorador web y navega a la dirección almacenada en el `CustomMKAnnotationView.Url` propiedad. Tenga en cuenta que la dirección se definió al crear el `CustomPin` colección en el proyecto de biblioteca .NET Standard.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Anule la selección de la anotación

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

Este método garantiza que cuando la llamada existente no está seleccionada, también detendrá la parte de la llamada (la imagen del logotipo de Xamarin) extendida que se muestran y se publicará sus recursos.

Para obtener más información acerca de cómo personalizar un `MKMapView` la instancia, vea [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

Las capturas de pantalla siguientes muestran el mapa, antes y después de la personalización:

![](customized-pin-images/map-layout-android.png "Control de mapa antes y después de la personalización")

En Android el pin se denomina un *marcador*, y puede ser una imagen personalizada o un marcador definido por el sistema de colores distintos. Los marcadores pueden mostrar un *ventana información*, que se muestra en la respuesta para el usuario al pulsar en el marcador. Muestra la ventana de información del `Label` y `Address` propiedades de la `Pin` de instancia y se pueden personalizar para incluir otro tipo de contenido. Sin embargo, sólo una ventana de información se puede mostrar al mismo tiempo.

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

Siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms, el `OnElementChanged` llamadas al método el `MapView.GetMapAsync` método, que obtiene la interfaz `GoogleMap` que está asociado a la vista. Una vez el `GoogleMap` instancia está disponible, el `OnMapReady` invalidación que se va a invocar. Este método registra un controlador de eventos para el `InfoWindowClick` evento, que se desencadena cuando el [se hace clic en la ventana de información](#Clicking_on_the_Info_Window)y se canceló la suscripción desde solo cuando el representador de elemento está asociado a los cambios. El `OnMapReady` también reemplazar las llamadas del `SetInfoWindowAdapter` método para especificar que el `CustomMapRenderer` instancia de la clase proporcionará los métodos para personalizar la ventana de información.

El `CustomMapRenderer` la clase implementa la `GoogleMap.IInfoWindowAdapter` a la interfaz [personalizar la ventana de información](#Customizing_the_Info_Window). Esta interfaz especifica que se deben implementar los métodos siguientes:

- `public Android.Views.View GetInfoWindow(Marker marker)` : Este método se llama para devolver una ventana de información personalizada para un marcador. Si devuelve `null`, entonces se usará la representación predeterminada de la ventana. Si devuelve un `View`, a continuación, que `View` se colocarán dentro del marco de ventana de información.
- `public Android.Views.View GetInfoContents(Marker marker)` : Este método se llama para devolver un `View` que contiene el contenido de la ventana de información y se llamará solo si el `GetInfoWindow` devuelve del método `null`. Si devuelve `null`, entonces se usará el formato predeterminado del contenido de la ventana de información.

En la aplicación de ejemplo, se personaliza solo el contenido de la ventana de información, lo cual la `GetInfoWindow` devuelve del método `null` para habilitar esta opción.

#### <a name="customizing-the-marker"></a>Personalizar el marcador

El icono utilizado para representar un marcador puede personalizarse mediante una llamada a la `MarkerOptions.SetIcon` método. Esto puede realizarse al invalidar el `CreateMarker` método, que se invoca para cada `Pin` que se agrega al mapa:

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

Este método crea un nuevo `MarkerOption` instancia para cada `Pin` instancia. Después de establecer la posición, la etiqueta y la dirección del marcador, su icono está establecido con el `SetIcon` método. Este método toma un `BitmapDescriptor` objeto que contiene los datos necesarios para representar el icono, con el `BitmapDescriptorFactory` clase que proporciona métodos auxiliares para simplificar la creación de la `BitmapDescriptor`.

Para obtener más información sobre el uso de la `BitmapDescriptorFactory` clase para personalizar un marcador, consulte [personalizar un marcador](~/android/platform/maps-and-location/maps/maps-api.md).

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Personalización de la ventana de información

Cuando un usuario pulsa en el marcador, el `GetInfoContents` se ejecuta el método, siempre que el `GetInfoWindow` devuelve del método `null`. El siguiente ejemplo de código muestra la `GetInfoContents` método:

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

    if (customPin.Id.ToString() == "Xamarin") {
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

Este método devuelve un `View` que contiene el contenido de la ventana de información. Esto se logra como sigue:

- Un `LayoutInflater` se recupera la instancia. Esto se usa para crear una instancia de un archivo XML de diseño en su correspondiente `View`.
- El `GetCustomPin` método se llama para devolver los datos de NIP personalizado para la ventana de información.
- El `XamarinMapInfoWindow` diseño se aumenta si el `CustomPin.Id` es igual a la propiedad `Xamarin`. En caso contrario, el `MapInfoWindow` se aumenta el diseño. Esto permite escenarios donde se pueden mostrar los diseños de ventana de información diferente para distintos marcadores.
- El `InfoWindowTitle` y `InfoWindowSubtitle` los recursos se recuperan desde el diseño aumentado y sus `Text` propiedades se establecen en los datos correspondientes de la `Marker` de instancia, siempre que los recursos no están `null`.
- El `View` instancia se devuelve para su visualización en el mapa.

> [!NOTE]
> Una ventana de información no está activo `View`. En su lugar, Android, se convertirá la `View` a ningún elemento estático de mapa de bits y que se muestra como una imagen. Esto significa que, durante una ventana de información pueden responder a un evento click, no puede responder a los eventos de toque o gestos, y los controles individuales en la ventana de información no pueden responder a sus propios eventos de clic.

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

Este método abre un explorador web y navega a la dirección almacenada en el `Url` propiedad de los recuperados `CustomPin` de instancia para el `Marker`. Tenga en cuenta que la dirección se definió al crear el `CustomPin` colección en el proyecto de biblioteca .NET Standard.

Para obtener más información acerca de cómo personalizar un `MapView` la instancia, vea [API de mapas de](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Crear al representador personalizado en la plataforma Universal de Windows

Las capturas de pantalla siguientes muestran el mapa, antes y después de la personalización:

![](customized-pin-images/map-layout-uwp.png "Control de mapa antes y después de la personalización")

En la UWP se denomina el pin una *icono de mapa*, y puede ser una imagen personalizada o la imagen predeterminada definida por el sistema. Puede mostrar un icono de mapa de un `UserControl`, que se muestra en la respuesta para el usuario al pulsar en el icono de mapa. El `UserControl` puede mostrar cualquier contenido, incluido el `Label` y `Address` propiedades de la `Pin` instancia.

El ejemplo de código siguiente muestra al representador personalizado para UWP:

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

El `OnElementChanged` método realiza las siguientes operaciones, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- Borra la `MapControl.Children` colección para quitar los elementos de interfaz de usuario existentes desde el mapa, antes de registrar un controlador de eventos para el `MapElementClick` eventos. Este evento se desencadena cuando el usuario pulsa o hace clic en un `MapElement` en el `MapControl`y se canceló la suscripción desde solo cuando el representador de elemento está asociado a los cambios.
- Cada pin en el `customPins` recopilación se muestra en la ubicación geográfica correcta en el mapa como sigue:
  - La ubicación para el pin se crea como un `Geopoint` instancia.
  - Un `MapIcon` se crea una instancia para representar el pin.
  - La imagen utilizada para representar el `MapIcon` se especifica estableciendo el `MapIcon.Image` propiedad. Sin embargo, la imagen del icono de mapa no siempre garantiza que van a mostrar, tal como se puede estar ocultado por otros elementos del mapa. Por lo tanto, el icono de mapa `CollisionBehaviorDesired` propiedad está establecida en `MapElementCollisionBehavior.RemainVisible`, para asegurarse de que está visible.
  - La ubicación de la `MapIcon` se especifica estableciendo el `MapIcon.Location` propiedad.
  - El `MapIcon.NormalizedAnchorPoint` propiedad está establecida en la ubicación aproximada del puntero en la imagen. Si esta propiedad conserva su valor predeterminado de (0,0), que representa la esquina superior izquierda de la imagen, pueden dar lugar a cambios en el nivel de zoom del mapa en la imagen apunta a una ubicación diferente.
  - El `MapIcon` instancia se agrega a la `MapControl.MapElements` colección. Esto da como resultado el icono de mapa que se muestra en el `MapControl`.

> [!NOTE]
> Cuando se usa la misma imagen de varios iconos de mapa, la `RandomAccessStreamReference` instancia debe declararse en el nivel de página o aplicación para mejorar el rendimiento.

#### <a name="displaying-the-usercontrol"></a>Mostrar el control de usuario

Cuando un usuario pulsa en el icono de mapa, el `OnMapElementClick` se ejecuta el método. El ejemplo de código siguiente muestra este método:

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

            if (customPin.Id.ToString() == "Xamarin")
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

Este método crea un `UserControl` instancia que muestra información sobre el pin. Esto se logra como sigue:

- El `MapIcon` se recupera la instancia.
- El `GetCustomPin` método se llama para devolver los datos de NIP personalizado que se mostrará.
- Un `XamarinMapOverlay` se crea una instancia para mostrar los datos de NIP personalizado. Esta clase es un control de usuario.
- La ubicación geográfica en la que se va a mostrar el `XamarinMapOverlay` a la instancia en el `MapControl` se crea como un `Geopoint` instancia.
- El `XamarinMapOverlay` instancia se agrega a la `MapControl.Children` colección. Esta colección contiene elementos de interfaz de usuario XAML que se mostrará en el mapa.
- La ubicación geográfica de la `XamarinMapOverlay` instancia en el mapa se establece mediante una llamada a la `SetLocation` método.
- La ubicación relativa en el `XamarinMapOverlay` instancia que corresponde a la ubicación especificada, se establece mediante una llamada a la `SetNormalizedAnchorPoint` método. Esto garantiza que los cambios en el nivel de zoom del resultado de la asignación en el `XamarinMapOverlay` siempre que se muestra en la ubicación correcta de la instancia.

Como alternativa, si ya se muestra información sobre el pin en el mapa, pulsar en el mapa quita el `XamarinMapOverlay` instancia desde el `MapControl.Children` colección.

#### <a name="tapping-on-the-information-button"></a>Pulsar en el botón información

Cuando el usuario pulsa el *información* situado en la `XamarinMapOverlay` control de usuario, el `Tapped` desencadena el evento, que a su vez ejecuta la `OnInfoButtonTapped` método:

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Este método abre un explorador web y navega a la dirección almacenada en el `Url` propiedad de la `CustomPin` instancia. Tenga en cuenta que la dirección se definió al crear el `CustomPin` colección en el proyecto de biblioteca .NET Standard.

Para obtener más información acerca de cómo personalizar un `MapControl` la instancia, vea [mapas y ubicación de información general sobre](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) en MSDN.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo crear un representador personalizado para el `Map` control, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma. Xamarin.Forms.Maps para proporciona una abstracción de multiplataforma para mostrar los mapas que usan el mapa nativo experimentan API en cada plataforma para proporcionar un mapa rápido y familiar para los usuarios.


## <a name="related-links"></a>Vínculos relacionados

- [Control de mapas](~/xamarin-forms/user-interface/map.md)
- [Mapas de iOS](~/ios/user-interface/controls/ios-maps/index.md)
- [API de Maps](~/android/platform/maps-and-location/maps/maps-api.md)
- [Pin personalizada (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
