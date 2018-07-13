---
title: Resaltado de un área Circular en un mapa
description: En este artículo se explica cómo agregar una superposición circular a un mapa, para resaltar un área circular del mapa. Si bien iOS y Android proporcionan las API para agregar la superposición circular al mapa, en UWP la superposición se representa como un polígono.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 3064296d4c78a3342fb27afc971c37a029987e5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998562"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Resaltado de un área Circular en un mapa

_En este artículo se explica cómo agregar una superposición circular a un mapa, para resaltar un área circular del mapa._

## <a name="overview"></a>Información general

Una superposición es un gráfico en capas en un mapa. Superposiciones admiten contenido de dibujo gráfico que se escala con el mapa como lo está ampliado. Las capturas de pantalla siguientes muestran el resultado de agregar una superposición circular a un mapa:

![](circle-map-overlay-images/screenshots.png)

Cuando un [ `Map` ](xref:Xamarin.Forms.Maps.Map) control se representa mediante una aplicación de Xamarin.Forms, en iOS el `MapRenderer` se crea la instancia de clase, que a su vez crea una instancia nativa `MKMapView` control. En la plataforma Android, el `MapRenderer` nativo crea una instancia de clase `MapView` control. En la plataforma Universal de Windows (UWP), el `MapRenderer` nativo crea una instancia de clase `MapControl`. El proceso de representación puede aprovecharse para implementar las personalizaciones de asignación específicos de la plataforma mediante la creación de un representador personalizado para un `Map` en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) mapa personalizado de Xamarin.Forms.
1. [Personalizar](#Customizing_the_Map) el mapa mediante la creación de un representador personalizado para el mapa en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) se deben inicializar y configurar antes de su uso. Para obtener más información, vea [`Maps Control`](~/xamarin-forms/user-interface/map.md)

Para obtener información acerca de cómo personalizar un mapa mediante un representador personalizado, vea [personalizar un Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Crear el mapa personalizado

Crear un `CustomCircle` clase que tiene `Position` y `Radius` propiedades:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

A continuación, cree una subclase de la [ `Map` ](xref:Xamarin.Forms.Maps.Map) (clase), que agrega una propiedad de tipo `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Consumo de mapa personalizado

Consumir el `CustomMap` control mediante la declaración de una instancia de ella en la instancia de la página XAML:

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

Como alternativa, consumir el `CustomMap` control mediante la declaración de una instancia de ella en la instancia page de C#:

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

Agrega esta inicialización [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) y `CustomCircle` instancias a la asignación personalizada y los coloca la vista del mapa con el [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método, que cambia la posición y el zoom nivel de la asignación mediante la creación de un [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) desde un [ `Position` ](xref:Xamarin.Forms.Maps.Position) y un [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar el mapa

Ahora debe agregarse un representador personalizado a cada proyecto de aplicación para agregar la superposición circular al mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado en iOS

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

Este método realiza la configuración siguiente, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- El `MKMapView.OverlayRenderer` propiedad está establecida en un delegado correspondiente.
- El círculo se crea mediante el establecimiento estático `MKCircle` objeto que especifica el centro del círculo y el radio del círculo en metros.
- El círculo se agrega a la asignación mediante una llamada a la `MKMapView.AddOverlay` método.

A continuación, implemente el `GetOverlayRenderer` método para personalizar la representación de la superposición:

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

El `OnElementChanged` llamadas al método el `MapView.GetMapAsync` método, que obtiene la interfaz `GoogleMap` que está asociado a la vista, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. Una vez el `GoogleMap` instancia está disponible, el `OnMapReady` método se invocará, donde se crea el círculo creando un `CircleOptions` objeto que especifica el centro del círculo y el radio del círculo en metros. El círculo, a continuación, se agrega a la asignación mediante una llamada a la `NativeMap.AddCircle` método.

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

Este método realiza las operaciones siguientes, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- La posición de círculo y radius se recuperan de la `CustomMap.Circle` propiedad y se pasa a la `GenerateCircleCoordinates` método, que genera la latitud y longitud coordenadas para el perímetro del círculo. El código para este método auxiliar se muestra a continuación.
- Las coordenadas de perímetro del círculo se convierten en un `List` de `BasicGeoposition` coordenadas.
- Se crea el círculo creando un `MapPolygon` objeto. El `MapPolygon` clase se utiliza para mostrar una forma de varios punto en el mapa estableciendo su `Path` propiedad a un `Geopath` objeto que contiene las coordenadas de la forma.
- El polígono se represente en el mapa agregándolo a la `MapControl.MapElements` colección.


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

En este artículo se explica cómo agregar una superposición circular a un mapa, para resaltar un área circular del mapa.


## <a name="related-links"></a>Vínculos relacionados

- [Ovlerlay de mapa circular (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [Personalización de un anclado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps para](xref:Xamarin.Forms.Maps)
