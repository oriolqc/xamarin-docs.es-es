---
title: Nuevas características de MapKit en iOS 11
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: 3b1ac8015a86292f00f26133277ead2f211dca33
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Nuevas características de MapKit en iOS 11

iOS 11 agrega las siguientes características nuevas a MapKit:

- [Anotación de agrupación en clústeres](#clustering)
- [Botón brújula](#compass)
- [Vista de escala](#scale)
- [Botón de seguimiento de usuario](#user-tracking)

![Mapa que muestra marcadores en clúster y compass botón](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>Marcadores de agrupación automáticamente al hacer zoom

El ejemplo [MapKit ejemplo "Tandm"](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) muestra cómo implementar la anotación de iOS 11 nueva característica de clústeres.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. Crear un `MKPointAnnotation` subclase

La clase de anotación de punto representa cada marcador del mapa. Se pueden agregar individualmente con `MapView.AddAnnotation()` o desde una matriz mediante `MapView.AddAnnotations()`.

Clases de anotación de punto no tienen una representación visual, que solo son necesarias para representar los datos asociados con el marcador (lo que es más importante, la `Coordinate` propiedad que es su latitud y longitud en el mapa) y cualquier propiedad personalizada:

```csharp
public class Bike : MKPointAnnotation
{
  public BikeType Type { get; set; } = BikeType.Tricycle;
  public Bike(){}
  public Bike(NSNumber lat, NSNumber lgn, NSNumber type)
  {
    Coordinate = new CLLocationCoordinate2D(lat.NFloatValue, lgn.NFloatValue);
    switch(type.NUIntValue) {
      case 0:
        Type = BikeType.Unicycle;
        break;
      case 1:
        Type = BikeType.Tricycle;
        break;
    }
  }
}
```

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. Crear un `MKMarkerAnnotationView` subclase para los marcadores únicos

La vista de la anotación de marcador es la representación visual de cada anotación y estilo mediante propiedades como:

- **MarkerTintColor** : el color del marcador.
- **GlyphText** : mostrar texto en el marcador.
- **GlyphImage** – establece la imagen que se muestra en el marcador.
- **DisplayPriority** – determina el orden z (comportamiento apilamiento) cuando se llena con marcadores de la asignación. Utilice uno de `Required`, `DefaultHigh`, o `DefaultLow`.

Para admitir clústeres automática, también debe establecer:

- **ClusteringIdentifier** : esta propiedad controla qué marcadores obtengan agrupados juntos. Puede utilizar el mismo identificador para todos los marcadores o utilizan identificadores diferentes para controlar la manera en que están agrupados juntos.

```csharp
[Register("BikeView")]
public class BikeView : MKMarkerAnnotationView
{
  public static UIColor UnicycleColor = UIColor.FromRGB(254, 122, 36);
  public static UIColor TricycleColor = UIColor.FromRGB(153, 180, 44);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;

      var bike = value as Bike;
      if (bike != null){
        ClusteringIdentifier = "bike";
        switch(bike.Type){
          case BikeType.Unicycle:
            MarkerTintColor = UnicycleColor;
            GlyphImage = UIImage.FromBundle("Unicycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultLow;
            break;
          case BikeType.Tricycle:
            MarkerTintColor = TricycleColor;
            GlyphImage = UIImage.FromBundle("Tricycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
            break;
        }
      }
    }
  }
```

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. Crear un `MKAnnotationView` para representar los clústeres de marcadores

Mientras que la vista de anotación que representa un clúster de marcadores _podría_ ser una imagen sencilla, los usuarios esperan que la aplicación proporcione indicaciones visuales sobre marcadores cuántos se han agrupado.

El [código de ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) CoreGraphics se utiliza para representar el número de marcadores en el clúster, así como una representación gráfica de círculo de la proporción de cada tipo de marcador.

También debe establecer:

- **DisplayPriority** – determina el orden z (comportamiento apilamiento) cuando se llena con marcadores de la asignación. Utilice uno de `Required`, `DefaultHigh`, o `DefaultLow`.
- **CollisionMode** : `Circle` o `Rectangle`.

```csharp
[Register("ClusterView")]
public class ClusterView : MKAnnotationView
{
  public static UIColor ClusterColor = UIColor.FromRGB(202, 150, 38);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;
      var cluster = MKAnnotationWrapperExtensions.UnwrapClusterAnnotation(value);
      if (cluster != null)
      {
        var renderer = new UIGraphicsImageRenderer(new CGSize(40, 40));
        var count = cluster.MemberAnnotations.Length;
        var unicycleCount = CountBikeType(cluster.MemberAnnotations, BikeType.Unicycle);

        Image = renderer.CreateImage((context) => {
          // Fill full circle with tricycle color
          BikeView.TricycleColor.SetFill();
          UIBezierPath.FromOval(new CGRect(0, 0, 40, 40)).Fill();
          // Fill pie with unicycle color
          BikeView.UnicycleColor.SetFill();
          var piePath = new UIBezierPath();
          piePath.AddArc(new CGPoint(20,20), 20, 0, (nfloat)(Math.PI * 2.0 * unicycleCount / count), true);
          piePath.AddLineTo(new CGPoint(20, 20));
          piePath.ClosePath();
          piePath.Fill();
          // Fill inner circle with white color
          UIColor.White.SetFill();
          UIBezierPath.FromOval(new CGRect(8, 8, 24, 24)).Fill();
          // Finally draw count text vertically and horizontally centered
          var attributes = new UIStringAttributes() {
            ForegroundColor = UIColor.Black,
            Font = UIFont.BoldSystemFontOfSize(20)
          };
          var text = new NSString($"{count}");
          var size = text.GetSizeUsingAttributes(attributes);
          var rect = new CGRect(20 - size.Width / 2, 20 - size.Height / 2, size.Width, size.Height);
          text.DrawString(rect, attributes);
        });
      }
    }
  }
  public ClusterView(){}
  public ClusterView(MKAnnotation annotation, string reuseIdentifier) : base(annotation, reuseIdentifier)
  {
    DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
    CollisionMode = MKAnnotationViewCollisionMode.Circle;
    // Offset center point to animate better with marker annotations
    CenterOffset = new CoreGraphics.CGPoint(0, -10);
  }
  private nuint CountBikeType(IMKAnnotation[] members, BikeType type) {
    nuint count = 0;
    foreach(Bike member in members){
      if (member.Type == type) ++count;
    }
    return count;
  }
}
```

### <a name="4-register-the-view-classes"></a>4. Registrar las clases de vista

Cuando se crea el control de vista de mapa y se agrega a una vista, registre los tipos de vista de anotación para habilitar el comportamiento de agrupación en clústeres automática como la asignación de entrada y salida ampliada:

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. Representar el mapa.

Cuando se representa el mapa, marcadores de anotación se en clúster o se representan en función del nivel de zoom. A medida que cambia el nivel de zoom, marcadores animan dentro y fuera de los clústeres.

![Simulador mostrar marcadores en clúster en el mapa](mapkit-images/cyclemap-sml.png)

Hacer referencia a la [asigna sección](~/ios/user-interface/controls/ios-maps/index.md) para obtener más información acerca de cómo mostrar datos con MapKit.

<a name="compass" />

## <a name="compass-button"></a>Botón brújula

iOS 11 agrega la capacidad de extraer la brújula fuera de la asignación y representarlo en otro lugar en la vista. Consulte la [aplicación de ejemplo de Tandm](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) para obtener un ejemplo.

Crear un botón que parece una brújula (incluido en vivo animación cuando se cambia la orientación del mapa), y lo representa en otro control.

![Botón brújula en la barra de navegación](mapkit-images/compass-sml.png)

El código siguiente crea un botón brújula y lo representa en la barra de navegación:

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

El `ShowsCompass` propiedad puede utilizarse para controlar la visibilidad de la brújula predeterminado dentro de la vista del mapa.

<a name="scale" />

## <a name="scale-view"></a>Vista de escala

Agregar la escala en otra parte de la vista utilizando la `MKScaleView.FromMapView()` método para obtener una instancia de la vista de escala para agregar en otra parte de la jerarquía de vista.

![Vista de escala superpuesta en un mapa](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

El `ShowsScale` propiedad puede utilizarse para controlar la visibilidad de la brújula predeterminado dentro de la vista del mapa.

<a name="user-tracking" />

## <a name="user-tracking-button"></a>Botón de seguimiento de usuario

El botón de seguimiento de usuario centra el mapa en la ubicación del usuario actual. Use la `MKUserTrackingButton.FromMapView()` para obtener una instancia del botón, aplicar cambios de formato y agregar en otra parte de la jerarquía de vista.

![Botón de ubicación de usuario situado en un mapa](mapkit-images/user-location-sml.png)

```csharp
var button = MKUserTrackingButton.FromMapView(MapView);
button.Layer.BackgroundColor = UIColor.FromRGBA(255,255,255,80).CGColor;
button.Layer.BorderColor = UIColor.White.CGColor;
button.Layer.BorderWidth = 1;
button.Layer.CornerRadius = 5;
button.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(button); // constraints omitted for simplicity
```


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de MapKit 'Tandm'](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [What's New en MapKit (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/237/)
