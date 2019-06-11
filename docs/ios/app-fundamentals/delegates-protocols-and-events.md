---
title: Eventos, protocolos y delegados de Xamarin.iOS
description: Este documento describe cómo trabajar con eventos, protocolos y delega en Xamarin.iOS. Estos conceptos fundamentales son omnipresentes en desarrollo de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: a82d96cf3f290dd28d163dd6f147a9dc28dfaa81
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827603"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Eventos, protocolos y delegados de Xamarin.iOS

Xamarin.iOS utiliza controles para exponer eventos para la mayoría de las interacciones de usuario.
Las aplicaciones de Xamarin.iOS consumir estos eventos en la misma manera igual que las aplicaciones .NET tradicionales. Por ejemplo, la clase Xamarin.iOS UIButton tiene un evento llamado TouchUpInside y consume este evento como si se tratara de esta clase y los eventos en una aplicación. NET.

Además de este enfoque. NET, Xamarin.iOS expone otro modelo que se puede usar para el enlace de datos y una interacción más compleja. Esta metodología usa lo que denomina Apple delegados y protocolos. Los delegados son similares en concepto a los delegados en C#, pero en lugar de definir y llamar a un método único, un delegado en Objective-C es una clase completa que se ajusta a un protocolo. Es similar a una interfaz en un protocolo C#, excepto en que sus métodos pueden ser opcionales. Por ejemplo, para rellenar un UITableView con datos, crearía una clase de delegado que implementa los métodos definidos en el protocolo UITableViewDataSource que llamaría la UITableView se rellene automáticamente.

En este artículo obtendrá información sobre todos estos temas, lo que le ofrece una base sólida para controlar los escenarios de devolución de llamada en Xamarin.iOS, incluyendo:

- **Eventos** : eventos de uso de .NET con controles de UIKit.
- **Protocolos** : aprender lo que son y cómo se utilizan los protocolos y creación de un ejemplo que proporciona datos para una anotación de mapa.
- **Los delegados** : aprendizaje sobre los delegados de Objective-C ampliando el ejemplo de mapa para controlar la interacción del usuario que incluye una anotación y, luego, la diferencia entre los delegados fuertes y débiles y cuándo usar cada uno de ellos de aprendizaje.

Para ilustrar los protocolos y delegados, vamos a crear una aplicación simple de mapa que agrega una anotación a un mapa, como se muestra aquí:

[![](delegates-protocols-and-events-images/01-map-sml.png "Un ejemplo de una aplicación de asignación simple que agrega una anotación a un mapa")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "una anotación en el ejemplo se agregan a un mapa")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Antes de comenzar con esta aplicación, Comencemos echando un vistazo a los eventos de .NET en el UIKit.

## <a name="net-events-with-uikit"></a>Eventos de .NET con UIKit

Xamarin.iOS expone los eventos de .NET en los controles de UIKit. Por ejemplo, UIButton tiene un evento TouchUpInside que controlar como lo haría normalmente en. NET, como se muestra en el siguiente código que usa un C# expresión lambda:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

También podría implementar esto con un C# 2.0 estilo método anónimo como esta:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

El código anterior está dispuesto el `ViewDidLoad` método UIViewController. El `aButton` variable hace referencia a un botón, lo que podría agregarse en el Diseñador de iOS o con código. La siguiente ilustración muestra un botón que se ha agregado en el Diseñador de iOS:

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "Un botón que agregó en el Diseñador de iOS")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin.iOS también es compatible con el estilo de acción de destino de la conexión de su código a una interacción que se produce con un control. Para crear una acción de destino para la **Hello** botón, haga doble clic en el Diseñador de iOS. Se mostrará el archivo de código subyacente de UIViewController y el desarrollador le pedirá que seleccione una ubicación para insertar el método de conexión:

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "El archivo de código subyacente UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Después de selecciona una ubicación, un método nuevo se crea y con cable de seguridad para el control. En el ejemplo siguiente, se escribirá un mensaje en la consola cuando se hace clic en el botón:

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "Se escribirá un mensaje en la consola cuando se hace clic en el botón")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Para obtener más detalles sobre el patrón de acción de destino iOS, consulte la sección de acción de destino de [las competencias de aplicación para iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) en la biblioteca de desarrolladores de iOS de Apple.

Para obtener más información sobre cómo usar el Diseñador de iOS con Xamarin.iOS, consulte el [iOS Designer Overview](~/ios/user-interface/designer/index.md) documentación.

## <a name="events"></a>Eventos

Si desea interceptar los eventos de control, tiene una gama de opciones: desde mediante la C# expresiones lambda y funciones de delegado al uso de las API de Objective-C de bajo nivel.

En la sección siguiente se muestra cómo podría capturar el evento TouchDown en un botón, dependiendo de cuánto control que necesita.

## <a name="c-style"></a>C#Estilo

Mediante la sintaxis de delegado:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

Si le gusta las expresiones lambda en su lugar:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Si desea tener varios botones usan el mismo controlador para compartir el mismo código:

```csharp
void handler (object sender, EventArgs args)
{
   if (sender == button1)
      Console.WriteLine ("button1");
   else
      Console.WriteLine ("some other button");
}

button1.TouchDown += handler;
button2.TouchDown += handler;
```

## <a name="monitoring-more-than-one-kind-of-event"></a>Supervisión de más de un tipo de evento

El C# eventos UIControlEvent marcadores tienen una asignación unívoca con las marcas individuales. Si desea tener el mismo fragmento de código controlar dos o más eventos, use el `UIControl.AddTarget` método:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Mediante la sintaxis de expresión lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Si necesita usar características de bajo nivel de Objective-C, como enlazar a una instancia de objeto determinado e invoca un selector determinado:

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Tenga en cuenta, si implementa el método de instancia en una clase base heredada, debe ser un método público.

## <a name="protocols"></a>Protocolos

Un protocolo es una característica del lenguaje de Objective-C que proporciona una lista de declaraciones de método. Tiene una finalidad similar a una interfaz en C#, la principal diferencia está en que un protocolo puede tener métodos opcionales. No se llama a métodos opcionales si la clase que adopta un protocolo no implementarlos. Además, una sola clase en Objective-C puede implementar varios protocolos, al igual que un C# clase puede implementar varias interfaces.

Apple usa protocolos a lo largo de iOS para definir los contratos para las clases adoptar, al abstraer la clase de implementación del llamador, y, por tanto, funciona igual que un C# interfaz. Protocolos se usan tanto en escenarios no delegado (como con el `MKAnnotation` ejemplo se muestra a continuación) y con los delegados (tal y como se presenta más adelante en este documento, en la sección de delegados).

### <a name="protocols-with-xamarinios"></a>Protocolos de Xamarin.ios

Echemos un vistazo a un ejemplo mediante un protocolo de Objective-C de Xamarin.iOS. En este ejemplo, usaremos el `MKAnnotation` protocolo, que forma parte de la `MapKit` framework. `MKAnnotation` es un protocolo que permite a cualquier objeto que adopte para proporcionar información acerca de una anotación que se puede agregar a un mapa. Por ejemplo, un objeto que implementa `MKAnnotation` proporciona la ubicación de la anotación y el título asociado con él.

De este modo, el `MKAnnotation` protocolo se usa para proporcionar los datos pertinentes que acompaña a una anotación. Se crea la vista real para la propia anotación de los datos en el objeto que adopta el `MKAnnotation` protocolo. Por ejemplo, el texto de la llamada que aparece cuando el usuario pulsa en la anotación (como se muestra en la siguiente captura de pantalla) proviene el `Title` propiedad de la clase que implementa el protocolo:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Texto de ejemplo de la llamada cuando el usuario pulsa en la anotación")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Como se describe en la siguiente sección, [protocolos profundización](#protocols-deep-dive), Xamarin.iOS enlaza protocolos a clases abstractas. Para el `MKAnnotation` protocolo, el límite C# clase se denomina `MKAnnotation` imitar el nombre del protocolo y es una subclase de `NSObject`, la clase base raíz para CocoaTouch. El protocolo requiere un captador y establecedor para implementarse para la coordenada; Sin embargo, un título y subtítulo son opcionales. Por lo tanto, en el `MKAnnotation` (clase), el `Coordinate` propiedad es *abstracta*, debe implementarse y `Title` y `Subtitle` las propiedades se marcan *virtual* , haciéndolos opcional, tal como se muestra a continuación:

```csharp
[Register ("MKAnnotation"), Model ]
public abstract class MKAnnotation : NSObject
{
    public abstract CLLocationCoordinate2D Coordinate
    {
        [Export ("coordinate")]
        get;
        [Export ("setCoordinate:")]
        set;
    }

    public virtual string Title
    {
        [Export ("title")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }

    public virtual string Subtitle
    {
        [Export ("subtitle")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }
...
}
```

Cualquier clase puede proporcionar datos de anotación derivando de simplemente `MKAnnotation`, siempre que al menos el `Coordinate` se implementa la propiedad. Por ejemplo, esta es una clase de ejemplo que toma las coordenadas en el constructor y devuelve una cadena para el título:

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return title;
        }
    }
}
```

Mediante el protocolo que está enlazado, cualquier clase que cree subclases `MKAnnotation` puede proporcionar los datos pertinentes que se usará la asignación al crear la vista de la anotación. Para agregar una anotación a un mapa, basta con llamar a la `AddAnnotation` método de un `MKMapView` de instancia, tal como se muestra en el código siguiente:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

La variable de asignación aquí es una instancia de un `MKMapView`, que es la clase que representa la propia asignación. El `MKMapView` usará el `Coordinate` datos derivan el `SampleMapAnnotation` instancia para colocar la vista de anotación en el mapa.

El `MKAnnotation` protocolo proporciona un conjunto conocido de capacidades a través de los objetos que se implementan, sin que el consumidor (en este caso, el mapa) necesidad de conocer los detalles de implementación. Esto simplifica la adición de una variedad de posibles anotaciones a un mapa.

### <a name="protocols-deep-dive"></a>Análisis detallado de protocolos

Puesto que C# interfaces no admiten métodos opcionales, Xamarin.iOS protocolos se asigna a las clases abstractas. Por lo tanto, la adopción de un protocolo en Objective-C se consigue en Xamarin.iOS derivando de la clase abstracta que se enlaza al protocolo e implementando los métodos necesarios. Estos métodos se expondrá como métodos abstractos de la clase. Métodos opcionales del protocolo se enlazará a los métodos virtuales de la C# clase.

Por ejemplo, esta es una parte de la `UITableViewDataSource` protocolo como dependiente de Xamarin.iOS:

```csharp
public abstract class UITableViewDataSource : NSObject
{
    [Export ("tableView:cellForRowAtIndexPath:")]
    public abstract UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath);
    [Export ("numberOfSectionsInTableView:")]
    public virtual int NumberOfSections (UITableView tableView){...}
...
}
```

Tenga en cuenta que esta clase es abstracta. Xamarin.iOS hace que la clase abstracta para admitir métodos opcionales o necesarios en los protocolos.
Sin embargo, a diferencia de los protocolos de Objective-C (o C# interfaces), C# clases no admiten la herencia múltiple. Esto afecta al diseño de C# código que usa los protocolos y conduce normalmente a clases anidadas. Más sobre este problema se trata más adelante en este documento, en la sección de delegados.

 `GetCell(…)` es un método abstracto, enlazado a Objective-C *selector*, `tableView:cellForRowAtIndexPath:`, que es un método necesario de la `UITableViewDataSource` protocolo. Selector es el término de Objective-C para el nombre del método. Para aplicar el método según sea necesario, Xamarin.iOS lo declara como abstracto. El otro método `NumberOfSections(…)`, está enlazado a `numberOfSectionsInTableview:`. Este método es opcional en el protocolo, por lo que Xamarin.iOS lo declara como virtual, lo que opcional para reemplazar en C#.

Xamarin.iOS se encarga de todo el enlace de iOS para usted. Sin embargo, si alguna vez necesita enlazar manualmente un protocolo desde Objective-C, puede hacerlo mediante la decoración de una clase con el `ExportAttribute`. Este es el mismo método usado por Xamarin.iOS propio.

Para obtener más información sobre cómo enlazar tipos de Objective-C en Xamarin.iOS, consulte el artículo [tipos de enlace de Objective-C](~/ios/platform/binding-objective-c/index.md).

Aún no estamos a través de protocolos, sin embargo. También se usan iOS como base para los delegados de Objective-C, que es el tema de la sección siguiente.

## <a name="delegates"></a>Delegados

iOS usa a delegados de Objective-C para implementar el modelo de delegación, en el que un objeto trasmite trabajo a otro. El objeto que realiza el trabajo es el delegado del primer objeto. Un objeto se indica a su delegado para realizar el trabajo mediante el envío de mensajes que se hayan producen ciertas cosas. Enviar un mensaje similar al siguiente en Objective-C es funcionalmente equivalente a llamar a un método C#. Implementa métodos en respuesta a estas llamadas a un delegado y por lo que proporciona funcionalidad para la aplicación.

Los delegados permiten extender el comportamiento de las clases sin necesidad de crear subclases. Las aplicaciones de iOS suelen usar a delegados cuando una clase llama de nuevo a otro después de que se produce una acción importante. Por ejemplo, el `MKMapView` clase llamadas de vuelta a su delegado cuando el usuario pulsa una anotación en un mapa, lo que proporciona el autor de la clase de delegado la oportunidad de responder dentro de la aplicación. Puede trabajar a través de un ejemplo de este tipo de delegado de uso más adelante en este artículo, en el ejemplo que usa un delegado con Xamarin.iOS.

En este punto, quizás se pregunte cómo una clase determina qué métodos para llamar a su delegado. Se trata de otro lugar donde se utilizan protocolos. Normalmente, los métodos disponibles para un delegado proceden de los protocolos que adopten.

### <a name="how-protocols-are-used-with-delegates"></a>Usan de protocolos con delegados

Hemos visto anteriormente cómo se usan los protocolos para admitir agregar anotaciones a un mapa.
Protocolos también se usan para proporcionar un conjunto conocido de métodos para llamar a ciertos eventos ocurren, tal como el usuario pulsa una anotación en un mapa o selecciona una celda de una tabla después de que las clases. Las clases que implementan estos métodos se conocen como los delegados de las clases que llaman.

Las clases que admiten la delegación hacerlo mediante la exposición de una propiedad de delegado al que se asigna una clase que implementa al delegado. Los métodos que implementa para el delegado dependerán del protocolo que adopta el delegado concreto. Para el `UITableView` método, implementa la `UITableViewDelegate` protocolo, para el `UIAccelerometer` método, implementaría `UIAccelerometerDelegate`, y así sucesivamente para cualquier otra clase a lo largo de iOS para el que se desea expone un delegado.

La `MKMapView` clase vimos en nuestro ejemplo anterior también tiene una propiedad denominada delegado, que llamará una vez que se producen varios eventos. El delegado para `MKMapView` es de tipo `MKMapViewDelegate`.
Se usará en un ejemplo para responder a la anotación después de seleccionarlo, pero primero vamos a explicar la diferencia entre los delegados fuertes y débiles.

### <a name="strong-delegates-vs-weak-delegates"></a>Los delegados seguro frente a Delegados no seguros

Los delegados que hemos analizado hasta ahora son seguros delegados, lo que significa que están fuertemente tipadas. Los enlaces de Xamarin.iOS se distribuyen con una clase fuertemente tipada para todos los protocolos de delegado en iOS. Sin embargo, iOS también tiene el concepto de un delegado débil. Creación de subclases de una clase enlazada con el protocolo de Objective-C para un determinado delegado, en lugar de iOS también le permite elegir enlazar los métodos de protocolo en el que cualquier clase que deriva de NSObject, decoración de los métodos con ExportAttribute y, a continuación, se ha proporcionado los selectores adecuados.
Al adoptar este enfoque, asigne una instancia de la clase a la propiedad WeakDelegate en lugar de a la propiedad de delegado. Un delegado débil le ofrece la flexibilidad para tomar la clase delegada hacia abajo de una jerarquía de herencia diferente. Veamos un ejemplo de Xamarin.iOS que usa a delegados fuertes y débiles.

### <a name="example-using-a-delegate-with-xamarinios"></a>Ejemplo de cómo utilizar a un delegado con Xamarin.iOS

Para ejecutar código en respuesta al usuario al puntear en la anotación en nuestro ejemplo, se puede crear subclases `MKMapViewDelegate` y asignar una instancia de la `MKMapView`del `Delegate` propiedad. El `MKMapViewDelegate` protocolo contiene únicamente los métodos opcionales.
Por lo tanto, todos los métodos son virtuales que están enlazados a este protocolo en Xamarin.iOS `MKMapViewDelegate` clase. Cuando el usuario selecciona una anotación, la `MKMapView` instancia enviará el `mapView:didSelectAnnotationView:` mensaje a su delegado. Para controlar todo esto en Xamarin.iOS, necesitamos reemplazar el `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` método en la subclase MKMapViewDelegate similar al siguiente:

```csharp
public class SampleMapDelegate : MKMapViewDelegate
{
    public override void DidSelectAnnotationView (
        MKMapView mapView, MKAnnotationView annotationView)
    {
        var sampleAnnotation =
            annotationView.Annotation as SampleMapAnnotation;

        if (sampleAnnotation != null) {

            //demo accessing the coordinate of the selected annotation to
            //zoom in on it
            mapView.Region = MKCoordinateRegion.FromDistance(
                sampleAnnotation.Coordinate, 500, 500);

            //demo accessing the title of the selected annotation
            Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
        }
    }
}
```

La clase SampleMapDelegate mostrada anteriormente se implementa como una clase anidada en el controlador que contiene el `MKMapView` instancia. En Objective-C, a menudo verá el controlador de adoptar varios protocolos directamente dentro de la clase. Sin embargo, puesto que los protocolos están enlazados a las clases de Xamarin.iOS, las clases que implementan delegados fuertemente tipados son normalmente se incluyen como clases anidadas.

Con la implementación de la clase de delegado en su lugar, basta con crear una instancia del delegado en el controlador y asígnelo a la `MKMapView`del `Delegate` propiedad como se muestra aquí:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    SampleMapDelegate _mapDelegate;
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //set the map's delegate
        _mapDelegate = new SampleMapDelegate ();
        map.Delegate = _mapDelegate;
        ...
    }
    class SampleMapDelegate : MKMapViewDelegate
    {
        ...
    }
}
```

Para utilizar un delegado débil para lograr lo mismo, tiene que enlazar el método en cualquier clase que derive de `NSObject` y asígnelo a la `WeakDelegate` propiedad de la `MKMapView`. Puesto que la `UIViewController` en última instancia, la clase se deriva de `NSObject` (por ejemplo, cada clase de Objective-C en CocoaTouch), simplemente podemos implementar un método enlazado a `mapView:didSelectAnnotationView:` directamente en el controlador y asignar el controlador de `MKMapView`del `WeakDelegate`, evitando la necesidad de la clase anidada extra. El código siguiente muestra este enfoque:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        //assign the controller directly to the weak delegate
        map.WeakDelegate = this;
    }
    //bind to the Objective-C selector mapView:didSelectAnnotationView:
    [Export("mapView:didSelectAnnotationView:")]
    public void DidSelectAnnotationView (MKMapView mapView,
        MKAnnotationView annotationView)
    {
        ...
    }
}
```

Cuando se ejecuta este código, la aplicación se comporta exactamente igual que cuando se ejecuta la versión fuertemente tipada de delegado. La ventaja de este código es que el delegado no seguro no requiere la creación de la clase adicional que se creó cuando se usa el delegado fuertemente tipado. Sin embargo, esto supone obtener una seguridad de tipos. Si tuviera que comete un error en el selector que se pasó a la `ExportAttribute`, no sería averiguar hasta el tiempo de ejecución.

### <a name="events-and-delegates"></a>Eventos y delegados

Los delegados se usan para las devoluciones de llamada de iOS de forma similar a la forma en que .NET utiliza los eventos. Para hacer iOS API y la forma en que usan delegados de Objective-C parecer más. NET, Xamarin.iOS expone los eventos de .NET en muchos lugares donde los delegados se utilizan en iOS.

Por ejemplo, la implementación anterior donde el `MKMapViewDelegate` respondió a una anotación seleccionada también podría implementarse en Xamarin.iOS con un evento. NET. En ese caso, el evento se definirían en `MKMapView` y llama a `DidSelectAnnotationView`. Tendría un `EventArgs` subclase del tipo `MKMapViewAnnotationEventsArgs`. El `View` propiedad de `MKMapViewAnnotationEventsArgs` daría a una referencia a la vista de la anotación, desde que se pudo continuar con la misma implementación tenía anteriormente, como se muestra aquí:

```csharp
map.DidSelectAnnotationView += (s,e) => {
    var sampleAnnotation = e.View.Annotation as SampleMapAnnotation;
    if (sampleAnnotation != null) {
        //demo accessing the coordinate of the selected annotation to
        //zoom in on it
        mapView.Region = MKCoordinateRegion.FromDistance (
            sampleAnnotation.Coordinate, 500, 500);

        //demo accessing the title of the selected annotation
        Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
    }
};
```

## <a name="summary"></a>Resumen

Este artículo trata cómo usar eventos, protocolos y delegados en Xamarin.iOS. Hemos visto cómo Xamarin.iOS expone eventos normales de estilo de .NET para los controles.
A continuación, hemos aprendido acerca de los protocolos de Objective-C, incluido cómo son diferentes de C# interfaces y cómo los usa Xamarin.iOS. Por último, examinamos a los delegados de Objective-C desde una perspectiva de Xamarin.iOS. Hemos visto cómo Xamarin.iOS es compatible con ambos fuertemente y los delegados débilmente tipados y cómo enlazar los eventos de .NET para delegar en métodos.

## <a name="related-links"></a>Vínculos relacionados

- [Protocolos, delegados y eventos (ejemplo)](https://developer.xamarin.com/samples/monotouch/Protocols_Delegates_Events/)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Tipos de enlace de Objective-c.](~/ios/platform/binding-objective-c/index.md)
- [El lenguaje de programación Objective-c.](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Diseño de Interfaces de usuario en Xcode 4](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Competencias de aplicación para iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
