---
title: Resaltar una región en un mapa
description: En este artículo se explica cómo agregar una capa de polígono a un mapa, para resaltar una región en el mapa. Polígonos son una forma cerrada y se rellena su interior.
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 0a11e9c25922531727ad2fee3bbed9c8d4e2b80c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998139"
---
# <a name="highlighting-a-region-on-a-map"></a>Resaltar una región en un mapa

_En este artículo se explica cómo agregar una capa de polígono a un mapa, para resaltar una región en el mapa. Polígonos son una forma cerrada y se rellena su interior._

## <a name="overview"></a>Información general

Una superposición es un gráfico en capas en un mapa. Superposiciones admiten contenido de dibujo gráfico que se escala con el mapa como lo está ampliado. Las capturas de pantalla siguientes muestran el resultado de agregar una capa de polígono a un mapa:

![](polygon-map-overlay-images/screenshots.png)

Cuando un [ `Map` ](xref:Xamarin.Forms.Maps.Map) control se representa mediante una aplicación de Xamarin.Forms, en iOS el `MapRenderer` se crea la instancia de clase, que a su vez crea una instancia nativa `MKMapView` control. En la plataforma Android, el `MapRenderer` nativo crea una instancia de clase `MapView` control. En la plataforma Universal de Windows (UWP), el `MapRenderer` nativo crea una instancia de clase `MapControl`. El proceso de representación puede aprovecharse para implementar las personalizaciones de asignación específicos de la plataforma mediante la creación de un representador personalizado para un `Map` en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) mapa personalizado de Xamarin.Forms.
1. [Personalizar](#Customizing_the_Map) el mapa mediante la creación de un representador personalizado para el mapa en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) se deben inicializar y configurar antes de su uso. Para obtener más información, vea [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obtener información acerca de cómo personalizar un mapa mediante un representador personalizado, vea [personalizar un Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Crear el mapa personalizado

Crear una subclase de la [ `Map` ](xref:Xamarin.Forms.Maps.Map) (clase), que agrega un `ShapeCoordinates` propiedad:

```csharp
public class CustomMap : Map
{
  public List<Position> ShapeCoordinates { get; set; }

  public CustomMap ()
  {
    ShapeCoordinates = new List<Position> ();
  }
}
```

El `ShapeCoordinates` propiedad almacenará una colección de coordenadas que definen la región que se van a resaltar.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

Esta inicialización especifica una serie de coordenadas de latitud y longitud, para definir la región del mapa que se resalte. A continuación, coloca la vista del mapa con el [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método, que cambia la posición y el nivel de zoom del mapa mediante la creación de un [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) desde un [ `Position` ](xref:Xamarin.Forms.Maps.Position) y un [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar el mapa

Ahora debe agregarse un representador personalizado a cada proyecto de aplicación para agregar la superposición de polígono al mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado en iOS

Crear una subclase de la `MapRenderer` clase e invalidar sus `OnElementChanged` método para agregar la superposición de polígono:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolygonRenderer polygonRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polygonRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.ShapeCoordinates.Count];

                int index = 0;
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var blockOverlay = MKPolygon.FromCoordinates(coords);
                nativeMap.AddOverlay(blockOverlay);
            }
        }
        ...
    }
}

```

Este método realiza la configuración siguiente, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- El `MKMapView.OverlayRenderer` propiedad está establecida en un delegado correspondiente.
- Se recupera la colección de coordenadas de latitud y longitud de la `CustomMap.ShapeCoordinates` propiedad y se almacena como una matriz de `CLLocationCoordinate2D` instancias.
- El polígono se crea mediante una llamada a estático `MKPolygon.FromCoordinates` método, que especifica la latitud y longitud de cada punto.
- El polígono se agrega a la asignación mediante una llamada a la `MKMapView.AddOverlay` método. Este método cierra automáticamente el polígono, dibuje una línea que conecta los puntos primeros y últimos.

A continuación, implemente el `GetOverlayRenderer` método para personalizar la representación de la superposición:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolygonRenderer polygonRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polygonRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polygonRenderer = new MKPolygonRenderer(overlay as MKPolygon) {
              FillColor = UIColor.Red,
              StrokeColor = UIColor.Blue,
              Alpha = 0.4f,
              LineWidth = 9
          };
      }
      return polygonRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

Crear una subclase de la `MapRenderer` clase e invalidar sus `OnElementChanged` y `OnMapReady` métodos para agregar la superposición de polígono:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> shapeCoordinates;

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
                shapeCoordinates = formsMap.ShapeCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polygonOptions = new PolygonOptions();
            polygonOptions.InvokeFillColor(0x66FF0000);
            polygonOptions.InvokeStrokeColor(0x660000FF);
            polygonOptions.InvokeStrokeWidth(30.0f);

            foreach (var position in shapeCoordinates)
            {
                polygonOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }
            NativeMap.AddPolygon(polygonOptions);
        }
    }
}
```

El `OnElementChanged` método recupera la colección de las coordenadas de latitud y longitud de la `CustomMap.ShapeCoordinates` propiedad y los almacena en una variable de miembro. A continuación, llama el `MapView.GetMapAsync` método, que obtiene la interfaz `GoogleMap` que está asociado a la vista, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. Una vez el `GoogleMap` instancia está disponible, el `OnMapReady` método se invocará, donde se crea el polígono creando instancias de un `PolygonOptions` objeto que especifica la latitud y longitud de cada punto. El polígono, a continuación, se agrega a la asignación mediante una llamada a la `NativeMap.AddPolygon` método. Este método cierra automáticamente el polígono, dibuje una línea que conecta los puntos primeros y últimos.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Crear al representador personalizado en la plataforma Universal de Windows

Crear una subclase de la `MapRenderer` clase e invalidar sus `OnElementChanged` método para agregar la superposición de polígono:

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
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 0, 0, 255);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
    }
}
```

Este método realiza las operaciones siguientes, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms:

- Se recupera la colección de coordenadas de latitud y longitud de la `CustomMap.ShapeCoordinates` propiedad y convertir en un `List` de `BasicGeoposition` coordenadas.
- El polígono se crea al crear una instancia un `MapPolygon` objeto. El `MapPolygon` clase se utiliza para mostrar una forma de varios punto en el mapa estableciendo su `Path` propiedad a un `Geopath` objeto que contiene las coordenadas de la forma.
- El polígono se represente en el mapa agregándolo a la `MapControl.MapElements` colección. Tenga en cuenta que el polígono se cerrarán automáticamente al dibujar una línea que conecta los puntos primeros y últimos.

## <a name="summary"></a>Resumen

En este artículo se explica cómo agregar una capa de polígono a un mapa, para resaltar una región del mapa. Polígonos son una forma cerrada y se rellena su interior.


## <a name="related-links"></a>Vínculos relacionados

- [Polígono de mapa de superposición (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polygon/)
- [Personalización de un anclado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps para](xref:Xamarin.Forms.Maps)
