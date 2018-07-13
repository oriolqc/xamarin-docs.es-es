---
title: Resaltado de una ruta en un mapa
description: En este artículo se explica cómo agregar una superposición de polilínea a un mapa. Una superposición de polilínea es una serie de segmentos de línea conectados que se usan normalmente para mostrar una ruta en un mapa o crear cualquier forma que se necesita.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 786f050495d4682b719178f2723c482929544678
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998727"
---
# <a name="highlighting-a-route-on-a-map"></a>Resaltado de una ruta en un mapa

_En este artículo se explica cómo agregar una superposición de polilínea a un mapa. Una superposición de polilínea es una serie de segmentos de línea conectados que se usan normalmente para mostrar una ruta en un mapa o crear cualquier forma que se necesita._

## <a name="overview"></a>Información general

Una superposición es un gráfico en capas en un mapa. Superposiciones admiten contenido de dibujo gráfico que se escala con el mapa como lo está ampliado. Las capturas de pantalla siguientes muestran el resultado de agregar una superposición de polilínea a un mapa:

![](polyline-map-overlay-images/screenshots.png)

Cuando un [ `Map` ](xref:Xamarin.Forms.Maps.Map) control se representa mediante una aplicación de Xamarin.Forms, en iOS el `MapRenderer` se crea la instancia de clase, que a su vez crea una instancia nativa `MKMapView` control. En la plataforma Android, el `MapRenderer` nativo crea una instancia de clase `MapView` control. En la plataforma Universal de Windows (UWP), el `MapRenderer` nativo crea una instancia de clase `MapControl`. El proceso de representación puede aprovecharse para implementar las personalizaciones de asignación específicos de la plataforma mediante la creación de un representador personalizado para un `Map` en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) mapa personalizado de Xamarin.Forms.
1. [Personalizar](#Customizing_the_Map) el mapa mediante la creación de un representador personalizado para el mapa en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) se deben inicializar y configurar antes de su uso. Para obtener más información, vea [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obtener información acerca de cómo personalizar un mapa mediante un representador personalizado, vea [personalizar un Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Crear el mapa personalizado

Crear una subclase de la [ `Map` ](xref:Xamarin.Forms.Maps.Map) (clase), que agrega un `RouteCoordinates` propiedad:

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

El `RouteCoordinates` propiedad almacenará una colección de coordenadas que definen la ruta que se van a resaltar.

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

Esta inicialización especifica una serie de coordenadas de latitud y longitud, para definir la ruta en el mapa se van a resaltar. A continuación, coloca la vista del mapa con el [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método, que cambia la posición y el nivel de zoom del mapa mediante la creación de un [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) desde un [ `Position` ](xref:Xamarin.Forms.Maps.Position) y un [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar el mapa

Ahora debe agregarse un representador personalizado a cada proyecto de aplicación para agregar la superposición de polilínea al mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado en iOS

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

Este método realiza la configuración siguiente, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- El `MKMapView.OverlayRenderer` propiedad está establecida en un delegado correspondiente.
- Se recupera la colección de coordenadas de latitud y longitud de la `CustomMap.RouteCoordinates` propiedad y se almacena como una matriz de `CLLocationCoordinate2D` instancias.
- Se crea la polilínea mediante una llamada a estático `MKPolyline.FromCoordinates` método, que especifica la latitud y longitud de cada punto.
- Polilíneas se agregan a la asignación mediante una llamada a la `MKMapView.AddOverlay` método.

A continuación, implemente el `GetOverlayRenderer` método para personalizar la representación de la superposición:

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

El `OnElementChanged` método recupera la colección de las coordenadas de latitud y longitud de la `CustomMap.RouteCoordinates` propiedad y los almacena en una variable de miembro. A continuación, llama el `MapView.GetMapAsync` método, que obtiene la interfaz `GoogleMap` que está asociado a la vista, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. Una vez el `GoogleMap` instancia está disponible, el `OnMapReady` método se invocará, donde se crea la polilínea creando instancias de un `PolylineOptions` objeto que especifica la latitud y longitud de cada punto. Polyline, a continuación, se agrega a la asignación mediante una llamada a la `NativeMap.AddPolyline` método.

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

Este método realiza las operaciones siguientes, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- Se recupera la colección de coordenadas de latitud y longitud de la `CustomMap.RouteCoordinates` propiedad y convertir en un `List` de `BasicGeoposition` coordenadas.
- Se crea la polilínea creando instancias de un `MapPolyline` objeto. El `MapPolygon` clase se utiliza para mostrar una línea en el mapa estableciendo su `Path` propiedad a un `Geopath` objeto que contiene las coordenadas de línea.
- Polilíneas se representan en el mapa agregándolo a la `MapControl.MapElements` colección.

## <a name="summary"></a>Resumen

En este artículo se explica cómo agregar una superposición de polilínea a un mapa para mostrar una ruta en un mapa o crear cualquier forma que se necesita.


## <a name="related-links"></a>Vínculos relacionados

- [Mapa de polilínea Ovlerlay (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [Personalización de un anclado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps para](xref:Xamarin.Forms.Maps)
