---
title: Resaltar una región en un mapa
description: Este artículo explica cómo agregar una capa de polígono a un mapa, para resaltar una región en el mapa. Polígonos son una forma cerrada y se rellena su interior.
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: d87237015b9e3d896766894d552c650047137146
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="highlighting-a-region-on-a-map"></a>Resaltar una región en un mapa

_Este artículo explica cómo agregar una capa de polígono a un mapa, para resaltar una región en el mapa. Polígonos son una forma cerrada y se rellena su interior._

## <a name="overview"></a>Información general

Una superposición es un gráfico de capas en un mapa. Superposiciones admiten el dibujo contenido gráfica que escale con la asignación a medida que se aplica el zoom. Las capturas de pantalla siguientes muestran el resultado de agregar una capa de polígono a un mapa:

![](polygon-map-overlay-images/screenshots.png)

Cuando un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) control se representa por una aplicación de Xamarin.Forms, en iOS la `MapRenderer` se crea la instancia de clase, que a su vez crea una instancia nativo `MKMapView` control. En la plataforma Android, el `MapRenderer` clase crea instancias nativo `MapView` control. En la plataforma Universal de Windows (UWP), el `MapRenderer` nativo crea una instancia de clase `MapControl`. El proceso de representación puede tomar ventaja de implementar personalizaciones de asignación específica de la plataforma mediante la creación de un representador personalizado para un `Map` en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Map) un mapa personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) el mapa personalizado de Xamarin.Forms.
1. [Personalizar](#Customizing_the_Map) la asignación mediante la creación de un representador personalizado para la asignación en cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) debe inicializarse y configurado antes de su uso. Para obtener más información, vea [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obtener información acerca de cómo personalizar un mapa con un representador personalizado, consulte [personalizar un Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creación del mapa personalizado

Crear una subclase de la [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) (clase), que agrega un `ShapeCoordinates` propiedad:

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

El `ShapeCoordinates` propiedad almacenará una colección de coordenadas que definen la región se van a resaltar.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

Esta inicialización especifica una serie de coordenadas de latitud y longitud, para definir la región del mapa se van a resaltar. A continuación, se coloca la vista del mapa con la [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) método, que cambia la posición y el nivel de zoom del mapa mediante la creación de un [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) desde una [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) y un [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar la asignación

Ahora debe agregarse un representador personalizado a cada proyecto de aplicación para agregar la superposición de polígono al mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado de iOS

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

Este método realiza la configuración siguiente, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:

- El `MKMapView.OverlayRenderer` propiedad se establece en un delegado correspondiente.
- Se recupera la colección de las coordenadas de latitud y longitud de la `CustomMap.ShapeCoordinates` propiedad y se almacena como una matriz de `CLLocationCoordinate2D` instancias.
- El polígono se crea mediante una llamada a estático `MKPolygon.FromCoordinates` método, que especifica la latitud y longitud de cada punto.
- El polígono se agrega a la asignación mediante una llamada a la `MKMapView.AddOverlay` método. Este método cierra automáticamente el polígono debe dibujar una línea que conecta los puntos primero y último.

A continuación, implementar la `GetOverlayRenderer` método para personalizar la representación de la superposición de:

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

El `OnElementChanged` método recupera la colección de coordenadas de latitud y longitud de la `CustomMap.ShapeCoordinates` propiedad y los almacena en una variable de miembro. A continuación, se llama la `MapView.GetMapAsync` método, que obtiene subyacente `GoogleMap` que está enlazado a la vista, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms. Una vez el `GoogleMap` instancia está disponible, el `OnMapReady` método invocará, donde se crea el polígono creando un `PolygonOptions` objeto que especifica la latitud y longitud de cada punto. El polígono, a continuación, se agrega a la asignación mediante una llamada a la `NativeMap.AddPolygon` método. Este método cierra automáticamente el polígono debe dibujar una línea que conecta los puntos primero y último.

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

Este método realiza las operaciones siguientes, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms:

- Se recupera la colección de las coordenadas de latitud y longitud de la `CustomMap.ShapeCoordinates` propiedad y convertido en un `List` de `BasicGeoposition` coordenadas.
- Se crea el polígono creando un `MapPolygon` objeto. El `MapPolygon` clase se utiliza para mostrar una forma multipunto en el mapa estableciendo su `Path` propiedad a un `Geopath` objeto que contiene las coordenadas de la forma.
- El polígono se representa en el mapa al agregarla a la `MapControl.MapElements` colección. Tenga en cuenta que el polígono se cierran automáticamente mediante el dibujo de una línea que conecta los puntos primero y último.

## <a name="summary"></a>Resumen

Este artículo explica cómo agregar una capa de polígono a un mapa, para resaltar una región del mapa. Polígonos son una forma cerrada y se rellena su interior.


## <a name="related-links"></a>Vínculos relacionados

- [Superposición de mapa de polígono (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polygon/)
- [Personalización de un anclado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
