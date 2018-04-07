---
title: Resaltado de un área Circular en un mapa
description: Este artículo explica cómo agregar una superposición circular a un mapa, para resaltar un área circular de la asignación.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 23f36bfbdc4638bb8f35dd2a55124a1438e1d441
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Resaltado de un área Circular en un mapa

_Este artículo explica cómo agregar una superposición circular a un mapa, para resaltar un área circular de la asignación._

## <a name="overview"></a>Información general

Una superposición es un gráfico de capas en un mapa. Superposiciones admiten el dibujo contenido gráfica que escale con la asignación a medida que se aplica el zoom. Las capturas de pantalla siguientes muestran el resultado de agregar una superposición circular a un mapa:

![](circle-map-overlay-images/screenshots.png)

Cuando un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) control se representa por una aplicación de Xamarin.Forms, en iOS la `MapRenderer` se crea la instancia de clase, que a su vez crea una instancia nativo `MKMapView` control. En la plataforma Android, el `MapRenderer` clase crea instancias nativo `MapView` control. En la plataforma Universal de Windows (UWP), el `MapRenderer` nativo crea una instancia de clase `MapControl`. El proceso de representación puede tomar ventaja de implementar personalizaciones de asignación específica de la plataforma mediante la creación de un representador personalizado para un `Map` en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) el mapa personalizado de Xamarin.Forms.
1. [Personalizar](#Customizing_the_Map) la asignación mediante la creación de un representador personalizado para la asignación en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/") debe inicializarse y configurado antes de su uso. Para obtener más información, vea [`Maps Control`](~/xamarin-forms/user-interface/map.md)

Para obtener información acerca de cómo personalizar un mapa con un representador personalizado, consulte [personalizar un Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creación del mapa personalizado

Crear un `CustomCircle` clase que tiene `Position` y `Radius` propiedades:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

A continuación, cree una subclase de la [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) (clase), que agrega una propiedad de tipo `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Consumir el mapa personalizado

Consumir el `CustomMap` control mediante la declaración de una instancia de él en la instancia de la página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MapOverlay;assembly=MapOverlay"
             x:Class="MapOverlay.MapPage">
    <ContentPage.Content>
        <local:CustomMap x:Name="customMap" MapType="Street" WidthRequest="{x:Static local:App.ScreenWidth}" HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

O bien, utilizar el `CustomMap` control mediante la declaración de una instancia del mismo en la instancia de página de C#:

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

Inicializar el `CustomMap` controlar según sea necesario:

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    var pin = new Pin {
      Type = PinType.Place,
      Position = new Position (37.79752, -122.40183),
      Label = "Xamarin San Francisco Office",
      Address = "394 Pacific Ave, San Francisco CA"
    };

    var position = new Position (37.79752, -122.40183);
    customMap.Circle = new CustomCircle {
      Position = position,
      Radius = 1000
    };

    customMap.Pins.Add (pin);
    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (position, Distance.FromMiles (1.0)));
  }
}
```

Agrega esta inicialización [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/) y `CustomCircle` instancias para el mapa personalizado y los coloca la vista del mapa con la [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) método, que cambia la posición y el zoom nivel de la asignación mediante la creación de un [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) desde una [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) y un [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar la asignación

Ahora debe agregarse un representador personalizado a cada proyecto de aplicación para agregar la superposición circular a la asignación.

#### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado de iOS

Crear una subclase de la `MapRenderer` clase e invalidar sus `OnElementChanged` método para agregar la superposición circular:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKCircleRenderer circleRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    circleRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                var circle = formsMap.Circle;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                var circleOverlay = MKCircle.Circle(new CoreLocation.CLLocationCoordinate2D(circle.Position.Latitude, circle.Position.Longitude), circle.Radius);
                nativeMap.AddOverlay(circleOverlay);
            }
        }
        ...
    }
}

```

Este método realiza la configuración siguiente, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:

- El `MKMapView.OverlayRenderer` propiedad se establece en un delegado correspondiente.
- El círculo se creó estableciendo una variable static `MKCircle` objeto que especifica el centro del círculo y el radio del círculo en metros.
- El círculo se agrega a la asignación mediante una llamada a la `MKMapView.AddOverlay` método.

A continuación, implementar la `GetOverlayRenderer` método para personalizar la representación de la superposición de:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKCircleRenderer circleRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (circleRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          circleRenderer = new MKCircleRenderer(overlay as MKCircle) {
              FillColor = UIColor.Red,
              Alpha = 0.4f
          };
      }
      return circleRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

Crear una subclase de la `MapRenderer` clase e invalidar sus `OnElementChanged` y `OnMapReady` métodos para agregar la superposición circular:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        CustomCircle circle;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Xamarin.Forms.Maps.Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                circle = formsMap.Circle;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var circleOptions = new CircleOptions();
            circleOptions.InvokeCenter(new LatLng(circle.Position.Latitude, circle.Position.Longitude));
            circleOptions.InvokeRadius(circle.Radius);
            circleOptions.InvokeFillColor(0X66FF0000);
            circleOptions.InvokeStrokeColor(0X66FF0000);
            circleOptions.InvokeStrokeWidth(0);

            NativeMap.AddCircle(circleOptions);
        }
    }
}
```

El `OnElementChanged` llamadas al método el `MapView.GetMapAsync` método, que obtiene subyacente `GoogleMap` que está enlazado a la vista, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms. Una vez el `GoogleMap` instancia está disponible, el `OnMapReady` método invocará, donde se crea el círculo creando un `CircleOptions` objeto que especifica el centro del círculo y el radio del círculo en metros. El círculo, a continuación, se agrega a la asignación mediante una llamada a la `NativeMap.AddCircle` método.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Crear al representador personalizado en la plataforma Universal de Windows

Crear una subclase de la `MapRenderer` clase e invalidar sus `OnElementChanged` método para agregar la superposición circular:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        const int EarthRadiusInMeteres = 6371000;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }
            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MapControl;
                var circle = formsMap.Circle;

                var coordinates = new List<BasicGeoposition>();
                var positions = GenerateCircleCoordinates(circle.Position, circle.Radius);
                foreach (var position in positions)
                {
                    coordinates.Add(new BasicGeoposition { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
        // GenerateCircleCoordinates helper method (below)
    }
}
```

Este método realiza las operaciones siguientes, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:

- La posición del círculo y radius se recuperan de la `CustomMap.Circle` propiedad y se pasa a la `GenerateCircleCoordinates` método, que genera la latitud y longitud coordenadas del perímetro del círculo. El código de este método auxiliar se muestra a continuación.
- Las coordenadas de perímetro círculo se convierten en un `List` de `BasicGeoposition` coordenadas.
- Se crea el círculo creando un `MapPolygon` objeto. El `MapPolygon` clase se utiliza para mostrar una forma multipunto en el mapa estableciendo su `Path` propiedad a un `Geopath` objeto que contiene las coordenadas de la forma.
- El polígono se representa en el mapa al agregarla a la `MapControl.MapElements` colección.


```
List<Position> GenerateCircleCoordinates(Position position, double radius)
{
    double latitude = position.Latitude.ToRadians();
    double longitude = position.Longitude.ToRadians();
    double distance = radius / EarthRadiusInMeteres;
    var positions = new List<Position>();

    for (int angle = 0; angle <=360; angle++)
    {
        double angleInRadians = ((double)angle).ToRadians();
        double latitudeInRadians = Math.Asin(Math.Sin(latitude) * Math.Cos(distance) + Math.Cos(latitude) * Math.Sin(distance) * Math.Cos(angleInRadians));
        double longitudeInRadians = longitude + Math.Atan2(Math.Sin(angleInRadians) * Math.Sin(distance) * Math.Cos(latitude), Math.Cos(distance) - Math.Sin(latitude) * Math.Sin(latitudeInRadians));

        var pos = new Position(latitudeInRadians.ToDegrees(), longitudeInRadians.ToDegrees());
        positions.Add(pos);
    }

    return positions;
}
```

## <a name="summary"></a>Resumen

Este artículo explica cómo agregar una superposición circular a un mapa, para resaltar un área circular de la asignación.


## <a name="related-links"></a>Vínculos relacionados

- [Circular mapa Ovlerlay (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [Personalización de un anclado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
