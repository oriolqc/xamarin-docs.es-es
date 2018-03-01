---
title: Resaltado de una ruta en un mapa
description: "Este artículo explica cómo agregar una superposición de la polilínea en un mapa. Una superposición de polilínea es una serie de segmentos de línea conectados que se utiliza normalmente para mostrar una ruta en un mapa o crear cualquier forma que se necesita."
ms.topic: article
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: c7becef16009596148b4de28e4e8f6892cb44fe1
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="highlighting-a-route-on-a-map"></a>Resaltado de una ruta en un mapa

_Este artículo explica cómo agregar una superposición de la polilínea en un mapa. Una superposición de polilínea es una serie de segmentos de línea conectados que se utiliza normalmente para mostrar una ruta en un mapa o crear cualquier forma que se necesita._

## <a name="overview"></a>Información general

Una superposición es un gráfico de capas en un mapa. Superposiciones admiten el dibujo contenido gráfica que escale con la asignación a medida que se aplica el zoom. Las capturas de pantalla siguientes muestran el resultado de agregar una superposición de la polilínea en un mapa:

![](polyline-map-overlay-images/screenshots.png)

Cuando un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) control se representa por una aplicación de Xamarin.Forms, en iOS la `MapRenderer` se crea la instancia de clase, que a su vez crea una instancia nativo `MKMapView` control. En la plataforma Android, el `MapRenderer` clase crea instancias nativo `MapView` control. En la plataforma Universal de Windows (UWP), el `MapRenderer` nativo crea una instancia de clase `MapControl`. El proceso de representación puede tomar ventaja de implementar personalizaciones de asignación específica de la plataforma mediante la creación de un representador personalizado para un `Map` en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) el mapa personalizado de Xamarin.Forms.
1. [Personalizar](#Customizing_the_Map) la asignación mediante la creación de un representador personalizado para la asignación en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) debe inicializarse y configurado antes de su uso. Para obtener más información, consulte [ `Maps Control` ](~/xamarin-forms/user-interface/map.md).

Para obtener información acerca de cómo personalizar un mapa con un representador personalizado, consulte [personalizar un Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creación del mapa personalizado

Crear una subclase de la [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) (clase), que agrega un `RouteCoordinates` propiedad:

```csharp
public class CustomMap : Map
{
  public List<Position> RouteCoordinates { get; set; }

  public CustomMap ()
  {
    RouteCoordinates = new List<Position> ();
  }
}
```

El `RouteCoordinates` propiedad almacenará una colección de coordenadas que definen la ruta se van a resaltar.

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

Esta inicialización especifica una serie de coordenadas de latitud y longitud, para definir la ruta en el mapa se van a resaltar. A continuación, se coloca la vista del mapa con la [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) método, que cambia la posición y el nivel de zoom del mapa mediante la creación de un [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) desde una [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) y un [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar la asignación

Ahora debe agregarse un representador personalizado a cada proyecto de aplicación para agregar la superposición de la polilínea en la asignación.

#### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado de iOS

Crear una subclase de la `MapRenderer` clase e invalidar sus `OnElementChanged` método para agregar la superposición de polilínea:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolylineRenderer polylineRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polylineRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.RouteCoordinates.Count];
                int index = 0;
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var routeOverlay = MKPolyline.FromCoordinates(coords);
                nativeMap.AddOverlay(routeOverlay);
            }
        }
        ...
    }
}

```

Este método realiza la configuración siguiente, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:

- El `MKMapView.OverlayRenderer` propiedad se establece en un delegado correspondiente.
- Se recupera la colección de las coordenadas de latitud y longitud de la `CustomMap.RouteCoordinates` propiedad y se almacena como una matriz de `CLLocationCoordinate2D` instancias.
- Se crea la polilínea llamando el método estático `MKPolyline.FromCoordinates` método, que especifica la latitud y longitud de cada punto.
- Polilíneas se agregan a la asignación mediante una llamada a la `MKMapView.AddOverlay` método.

A continuación, implementar la `GetOverlayRenderer` método para personalizar la representación de la superposición de:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolylineRenderer polylineRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polylineRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polylineRenderer = new MKPolylineRenderer(overlay as MKPolyline) {
              FillColor = UIColor.Blue,
              StrokeColor = UIColor.Red,
              LineWidth = 3,
              Alpha = 0.4f
          };
      }
      return polylineRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

Crear una subclase de la `MapRenderer` clase e invalidar sus `OnElementChanged` y `OnMapReady` métodos para agregar la superposición de polilínea:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> routeCoordinates;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                routeCoordinates = formsMap.RouteCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polylineOptions = new PolylineOptions();
            polylineOptions.InvokeColor(0x66FF0000);

            foreach (var position in routeCoordinates)
            {
                polylineOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }

            NativeMap.AddPolyline(polylineOptions);
        }
    }
}
```

El `OnElementChanged` método recupera la colección de coordenadas de latitud y longitud de la `CustomMap.RouteCoordinates` propiedad y los almacena en una variable de miembro. A continuación, se llama la `MapView.GetMapAsync` método, que obtiene subyacente `GoogleMap` que está enlazado a la vista, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms. Una vez el `GoogleMap` instancia está disponible, el `OnMapReady` método invocará, donde se crea la polilínea creando un `PolylineOptions` objeto que especifica la latitud y longitud de cada punto. Polilíneas, a continuación, se agregan a la asignación mediante una llamada a la `NativeMap.AddPolyline` método.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Crear al representador personalizado en la plataforma Universal de Windows

Crear una subclase de la `MapRenderer` clase e invalidar sus `OnElementChanged` método para agregar la superposición de polilínea:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
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

                var coordinates = new List<BasicGeoposition>();
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polyline = new MapPolyline();
                polyline.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polyline.StrokeThickness = 5;
                polyline.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polyline);
            }
        }
    }
}
```

Este método realiza las operaciones siguientes, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:

- Se recupera la colección de las coordenadas de latitud y longitud de la `CustomMap.RouteCoordinates` propiedad y convertido en un `List` de `BasicGeoposition` coordenadas.
- Se crea la polilínea creando un `MapPolyline` objeto. El `MapPolygon` clase se utiliza para mostrar una línea en el mapa estableciendo su `Path` propiedad a un `Geopath` objeto que contiene las coordenadas de línea.
- Polilíneas se representan en el mapa al agregarla a la `MapControl.MapElements` colección.

## <a name="summary"></a>Resumen

Este artículo explica cómo agregar una superposición de la polilínea en un mapa, para mostrar una ruta en un mapa o crear cualquier forma que se necesita.


## <a name="related-links"></a>Vínculos relacionados

- [Polilínea mapa Ovlerlay (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [Personalizar un Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
