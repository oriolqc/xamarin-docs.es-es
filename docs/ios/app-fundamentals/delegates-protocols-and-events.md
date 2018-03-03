---
title: Eventos, protocolos y delegados
description: "En este artículo se presenta la tecnología de iOS clave usada para recibir devoluciones de llamada y para rellenar los controles de interfaz de usuario con datos. Estas tecnologías son eventos, protocolos y delegados. Este artículo explica lo que cada uno de ellos es y cómo se usa cada uno en C#. Muestra cómo Xamarin.iOS usa controles de iOS para exponer familiarizado .NET eventos, así como la Xamarin.iOS proporciona compatibilidad para Objective-C conceptos como los protocolos y los delegados (delegados Objective-C no deben confundirse con los delegados de C#). En este artículo también proporciona ejemplos que muestran cómo se utilizan protocolos: tanto como base para los delegados de Objective-C y en escenarios no delegado."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 5df7c2bbc7be1089795c94b6f639bd4556b49366
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="events-protocols-and-delegates"></a>Eventos, protocolos y delegados

_En este artículo se presenta la tecnología de iOS clave usada para recibir devoluciones de llamada y para rellenar los controles de interfaz de usuario con datos. Estas tecnologías son eventos, protocolos y delegados. Este artículo explica lo que cada uno de ellos es y cómo se usa cada uno en C#. Muestra cómo Xamarin.iOS usa controles de iOS para exponer familiarizado .NET eventos, así como la Xamarin.iOS proporciona compatibilidad para Objective-C conceptos como los protocolos y los delegados (delegados Objective-C no deben confundirse con los delegados de C#). En este artículo también proporciona ejemplos que muestran cómo se utilizan protocolos: tanto como base para los delegados de Objective-C y en escenarios no delegado._

Xamarin.iOS utiliza controles para exponer eventos para la mayoría de las interacciones de usuario.
Aplicaciones de Xamarin.iOS consumen estos eventos de la misma manera igual que las aplicaciones tradicionales. NET. Por ejemplo, la clase de Xamarin.iOS UIButton tiene un evento denominado TouchUpInside y consume este evento como si se tratara de esta clase y el evento en una aplicación. NET.

Además de este enfoque. NET, Xamarin.iOS expone otro modelo que puede utilizarse para una interacción más compleja y enlace de datos. Esta metodología utiliza lo llama Apple delegados y protocolos. Los delegados son similares en concepto a los delegados en C#, pero en lugar de definir y llamar a un método único, un delegado en Objective C es toda una clase que se ajusta a un protocolo. Un protocolo es similar a una interfaz en C#, salvo que sus métodos pueden ser opcionales. Por ejemplo, para rellenar un UITableView con datos, crearía una clase de delegado que implementa los métodos definidos en el protocolo de UITableViewDataSource llamaría la UITableView se rellene automáticamente.

En este artículo obtendrá información sobre todos estos temas, lo que le proporciona una base sólida para la administración de los escenarios de devolución de llamada en Xamarin.iOS, incluidos:

-  **Eventos** : eventos de uso de .NET con controles de UIKit.
-  **Protocolos** : aprender qué protocolos son y cómo se utilizan, y crear un ejemplo que proporciona datos para una anotación de mapa.
-  **Delegados** : aprendizaje sobre los delegados de Objective-C, siguiendo con el ejemplo de mapa para controlar la interacción del usuario que incluye una anotación y aprendizaje de la diferencia entre los delegados fuertes y débiles y cuándo usar cada uno de ellos.


Para ilustrar los protocolos y los delegados, vamos a crear una aplicación de mapa sencilla que agrega una anotación a un mapa, como se muestra aquí:

 [ ![](delegates-protocols-and-events-images/01-map.png "Un ejemplo de una aplicación de mapa sencilla que agrega una anotación a un mapa") ](delegates-protocols-and-events-images/01-map.png) [ ![ ] (delegates-protocols-and-events-images/04-annotation-with-callout.png "una anotación en el ejemplo se agregan a un mapa")](delegates-protocols-and-events-images/04-annotation-with-callout.png)

Antes de abordar esta aplicación, comencemos examinando los eventos de .NET en el UIKit.

 <a name=".NET_Events_with_UIKit" />


## <a name="net-events-with-uikit"></a>Eventos de .NET con UIKit

Xamarin.iOS expone eventos de .NET en controles de UIKit. Por ejemplo, UIButton tiene un evento TouchUpInside, que se controlan como lo haría normalmente en. NET, como se muestra en el siguiente código que usa una expresión lambda de C#:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

También se puede implementar con un método C# 2.0 estilo anónimo como esta:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

El código anterior está dispuesto en el método ViewDidLoad de la UIViewContoller. La variable aButton hace referencia a un botón, que puede agregar en el Diseñador de iOS o con código. En la siguiente ilustración se muestra este botón a medida que se agregan en iOS diseñador, tomadas del ejemplo que se incluye en este artículo:

 [ ![](delegates-protocols-and-events-images/02-interface-builder-outlet.png "Un botón que agregó en el Diseñador de iOS")](delegates-protocols-and-events-images/02-interface-builder-outlet.png)

Xamarin.iOS también admite el estilo de acción de destino de la conexión de su código a una interacción que tiene lugar con un control. Para crear una acción de destino para el botón de Hello, haga doble clic en él en el Diseñador de iOS. Se mostrará el archivo de código subyacente del UIViewController y el programador se le pide que seleccione una ubicación para insertar el método de conexión:

 [ ![](delegates-protocols-and-events-images/03-interface-builder-action.png "El archivo de código subyacente de UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png)

Después de selecciona una ubicación, un nuevo método se crea y por cable telefónico al control. En el ejemplo siguiente, se escribirá un mensaje en la consola cuando se hace clic en el botón:

 [ ![](delegates-protocols-and-events-images/05-interface-builder-action.png "Un mensaje se escribirá en la consola cuando se hace clic en el botón")](delegates-protocols-and-events-images/05-interface-builder-action.png)

Para obtener más información sobre el patrón de acción de destino de iOS, vea la sección de acción de destino de " [las competencias de aplicación para iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)" en la biblioteca para desarrolladores de iOS de Apple.

Para obtener más información sobre cómo usar el Diseñador de iOS con Xamarin.iOS, consulte la " [iOS Designer Overview](~/ios/user-interface/designer/index.md)" documentación.

 <a name="Events" />


## <a name="events"></a>Eventos

Si desea interceptar los eventos de control, tiene varias opciones: del uso de las expresiones lambda de C# y las funciones de delegado al uso de las API de C de objetivo de bajo nivel.

La sección siguiente muestra cómo se captura el evento informativos en un botón, dependiendo de cuánto control necesita.

 <a name="C#_Style" />


## <a name="c-style"></a>Estilo de C#

Mediante la sintaxis de delegado:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {    
    Console.WriteLine ("Touched");
};
```

Si está satisfecho con las expresiones lambda en su lugar:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Si desea tener varios botones utilizan el mismo controlador para compartir el mismo código:

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

<a name="Monitoring_more_than_one_kind_of_Event" />


## <a name="monitoring-more-than-one-kind-of-event"></a>Supervisión de más de un tipo de evento

Los eventos de C# para UIControlEvent marcas tienen una asignación unívoca con las marcas individuales. Si desea tener el mismo fragmento de código controlar dos o más eventos, use la `UIControl.AddTarget` método:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Mediante la sintaxis de expresión lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Si necesita usar características de bajo nivel de Objective-C, como enlazar a una instancia de objeto determinado e invocando un selector determinado:

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

 <a name="Protocols" />


## <a name="protocols"></a>Protocolos

Un protocolo es una característica de lenguaje de C de objetivo que proporciona una lista de declaraciones de método. Sirve un propósito similar a una interfaz en C#, la diferencia principal radica en que puede tener un protocolo métodos opcionales. No se denominan métodos opcionales si la clase que adopta un protocolo no implementarlos. Además, una sola clase en Objective C puede implementar varios protocolos, tal y como una clase de C# puede implementar varias interfaces.

Apple usa protocolos a lo largo de iOS para definir los contratos para que las clases adoptar mientras abstraer la clase de implementación desde el llamador, lo que funciona como una interfaz de C#. Los protocolos se utilizan en escenarios de delegado no (como con el `MKAnnotation` ejemplo se muestra a continuación) y con los delegados (tal y como se presenta más adelante en este documento, en la sección de delegados).

 <a name="Protocols_with_Monotouch" />


### <a name="protocols-with-xamarinios"></a>Protocolos con Xamarin.ios

¡Eche un vistazo a un ejemplo de cómo utilizar un protocolo Objective-C de Xamarin.iOS. En este ejemplo, usaremos el `MKAnnotation` protocolo, que forma parte de la `MapKit` framework. `MKAnnotation` es un protocolo que permite a cualquier objeto que adopte para proporcionar información sobre una anotación que se pueden agregar a un mapa. Por ejemplo, un objeto que implementa `MKAnnotation` proporciona la ubicación de la anotación y el título asociado con él.

De esta manera, el `MKAnnotation` protocolo se utiliza para proporcionar datos pertinentes que acompaña a una anotación. La vista para la anotación propio real se crea a partir de los datos en el objeto que adopta el `MKAnnotation` protocolo. Por ejemplo, el texto de la llamada que aparece cuando el usuario puntea en la anotación (tal y como se muestra en la siguiente captura de pantalla) proviene el `Title` propiedad de la clase que implementa el protocolo:

 [ ![](delegates-protocols-and-events-images/04-annotation-with-callout.png "Texto de ejemplo para la llamada cuando el usuario puntea en la anotación")](delegates-protocols-and-events-images/04-annotation-with-callout.png)

Como se describe en la siguiente sección, profundización en protocolos, Xamarin.iOS enlaza protocolos a clases abstractas. Para el `MKAnnotation` protocolo, la clase de C# enlazada se denomina `MKAnnotation` imitar el nombre del protocolo y es una subclase de `NSObject`, la clase base raíz para CocoaTouch. El protocolo requiere un captador y establecedor para que se implementen para la coordenada; Sin embargo, un título y el subtítulo son opcionales. Por lo tanto, en la `MKAnnotation` (clase), el `Coordinate` propiedad es *abstracta*, que se le solicite para que se implementen y `Title` y `Subtitle` propiedades están marcadas *virtual* , haciéndolos opcionales, tal y como se muestra a continuación:

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

Cualquier clase puede proporcionar datos de anotación, simplemente derive de `MKAnnotation`, siempre que al menos el `Coordinate` se implementa la propiedad. Por ejemplo, aquí es una clase de ejemplo que toma la coordenada en el constructor y devuelve una cadena para el título:

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string _title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        _title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return _title;
        }
    }
}
```

A través del protocolo que está enlazado, cualquier clase que cree subclases `MKAnnotation` puede proporcionar datos relevantes que se utilizará la asignación cuando crea la vista de la anotación. Para agregar una anotación a un mapa, basta con llamar a la `AddAnnotation` método de una `MKMapView` de instancia, tal como se muestra en el código siguiente:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
new CLLocationCoordinate2D (42.3467512, -71.0969456);

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

Aquí, la variable de asignación es una instancia de un `MKMapView`, que es la clase que representa la propia asignación. El `MKMapView` usará la `Coordinate` datos derivan la `SampleMapAnnotation` instancia para colocar la vista de anotación en el mapa.

El `MKAnnotation` protocolo proporciona un conjunto conocido de capacidades a través de los objetos que lo implementan, sin que el consumidor (en este caso, la asignación) necesidad de conocer los detalles de implementación. Esto optimiza la adición de una variedad de anotaciones posibles a un mapa.

 <a name="Protocols_Deep_Dive" />


### <a name="protocols-deep-dive"></a>Profundización en protocolos

Puesto que las interfaces de C# no admiten métodos opcionales, Xamarin.iOS asigna protocolos a clases abstractas. Por lo tanto, la adopción de un protocolo en Objective C se realiza en Xamarin.iOS derivando de la clase abstracta que se enlaza al protocolo e implementando los métodos necesarios. Estos métodos se expondrá como métodos abstractos de la clase. Métodos opcionales del protocolo se enlazará a los métodos virtuales de la clase de C#.

Por ejemplo, esta es una parte de la `UITableViewDataSource` protocolo como enlazadas en Xamarin.iOS:

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

Tenga en cuenta que la clase es abstracta. Xamarin.iOS hace que la clase abstracta para admitir los métodos opcionales / / / requerida en protocolos.
Sin embargo, a diferencia de los protocolos de Objective-C (o interfaces de C#), clases de C# no admiten la herencia múltiple. Esto afecta al diseño de código de C# que usa los protocolos y normalmente genera clases anidadas. Más acerca de este problema se trata más adelante en este documento, en la sección de delegados.

 `GetCell(…)` es un método abstracto, enlazado con el objetivo-C *selector*, `tableView:cellForRowAtIndexPath:`, que es un método necesario de la `UITableViewDataSource` protocolo. Selector es el término de Objective-C para el nombre del método. Para aplicar el método según sea necesario, Xamarin.iOS lo declara como abstracto. El otro método, `NumberOfSections(…)`, está enlazado a `numberOfSectionsInTableview:`. Este método es opcional en el protocolo, por lo que Xamarin.iOS lo declara como virtuales, lo que opcional override en C#.

Xamarin.iOS se encarga de todo el enlace de iOS para usted. Sin embargo, si alguna vez necesita enlazar a un protocolo de Objective-C manualmente, puede hacerlo mediante la decoración de una clase con la `ExportAttribute`. Este es el mismo método usado por Xamarin.iOS propio.

Para obtener más información sobre cómo enlazar tipos Objective-C en Xamarin.iOS, vea el artículo [enlace Objective-C tipos](~/ios/platform/binding-objective-c/index.md).

No estamos a través con protocolos, aunque. También se utilizan en iOS como base para los delegados de Objective-C, que es el tema de la sección siguiente.

 <a name="Delegates" />


## <a name="delegates"></a>Delegados

iOS usa a Objective-C delegados para implementar el patrón de delegación, en el que un objeto pasa trabajo a otro. El objeto que realiza el trabajo es el delegado del primer objeto. Un objeto se indica a su delegado para realizar el trabajo mediante el envío de mensajes de una vez que se producen ciertas acciones. Enviar un mensaje similar al siguiente en Objective C es funcionalmente equivalente a llamar a un método en C#. Un delegado implementa métodos en respuesta a estas llamadas y, por lo que proporciona funcionalidad a la aplicación.

Los delegados permiten extender el comportamiento de las clases sin necesidad de crear subclases. Las aplicaciones de iOS a menudo utilizan a delegados cuando una clase vuelve a llamar a otro después de que se produce una acción importante. Por ejemplo, el `MKMapView` clase llamadas de vuelta a su delegado cuando el usuario puntea una anotación en un mapa, lo que ofrece el autor de la clase de delegado la oportunidad de responder dentro de la aplicación. Puede trabajar con un ejemplo de este tipo de delegado de uso más adelante en este artículo, en el ejemplo del uso de un delegado con Xamarin.iOS.

En este punto, quizás se pregunte cómo una clase determina qué métodos para llamar en su delegado. Se trata de otro lugar donde se utilizan protocolos. Por lo general, los métodos disponibles para un delegado proceden de los protocolos que adopten.

 <a name="How_Protocols_are_used_with_Delegates" />


### <a name="how-protocols-are-used-with-delegates"></a>Cómo se utilizan los protocolos con delegados

Hemos visto anteriormente cómo se utilizan los protocolos para admitir agregar anotaciones a un mapa.
Protocolos también se usan para proporcionar un conjunto conocido de métodos para llamar a una vez determinados eventos producirse, como después de las derivaciones de usuario una anotación en un mapa o selecciona una celda de una tabla. Las clases que implementan estos métodos se conocen como los delegados de las clases que llaman a ellos.

Las clases que admiten la delegación hacerlo mediante la exposición de una propiedad de delegado, al que se asigna una clase que implementa al delegado. Los métodos que implementa el delegado de dependerá del protocolo que adopta el delegado determinado. Para el `UITableView` (método), implementar la `UITableViewDelegate` protocolo, para la `UIAccelerometer` método, que se implementaría `UIAccelerometerDelegate`, y así sucesivamente para cualquier otra clase a lo largo de iOS para que se va a expone un delegado.

La `MKMapView` clase vimos en el ejemplo anterior también tiene una propiedad denominada delegado, que llamará cuando se producen varios eventos. El delegado de `MKMapView` es de tipo `MKMapViewDelegate`.
Se usará en breve ejemplo para responder a la anotación después de seleccionarlo, pero primero vamos a explicar la diferencia entre los delegados fuertes y débiles.

 <a name="Strong_Delegates_vs._Weak_Delegates" />


### <a name="strong-delegates-vs-weak-delegates"></a>Frente a los delegados de seguro. Delegados débiles

Los delegados, hemos analizado hasta ahora son seguros delegados, lo que significa que están fuertemente tipados. Los enlaces de Xamarin.iOS vienen con una clase fuertemente tipada para todos los protocolos de delegado en iOS. Sin embargo, iOS también tiene el concepto de un delegado débil. En lugar de creación de subclases de una clase enlazada con el protocolo Objective-C para un determinado delegado, iOS también le permite elegir enlazar los métodos de protocolo en cualquier clase que deriva de NSObject, decorando los métodos con ExportAttribute y, a continuación, suministra los selectores adecuados.
Al adoptar este enfoque, asigne una instancia de la clase a la propiedad WeakDelegate en lugar de a la propiedad de delegado. Un delegado débil le ofrece la flexibilidad para aprovechar la clase de delegado hacia abajo en una jerarquía de herencia diferente. Veamos un ejemplo de Xamarin.iOS que usa a delegados fuertes y débiles.

 <a name="Example_using_a_Delegate_with_Xamarin.iOS" />


### <a name="example-using-a-delegate-with-xamarinios"></a>Ejemplo de cómo utilizar a un delegado con Xamarin.iOS

Para ejecutar código en respuesta al usuario al puntear en la anotación en nuestro ejemplo, se puede crear subclases de `MKMapViewDelegate` y asignar una instancia de la `MKMapView`del `Delegate` propiedad. El `MKMapViewDelegate` protocolo contiene solo los métodos opcionales.
Por lo tanto, todos los métodos son virtuales que están enlazados a este protocolo en el Xamarin.iOS `MKMapViewDelegate` clase. Cuando el usuario selecciona una anotación, la `MKMapView` instancia enviará el `mapView:didSelectAnnotationView:` mensaje a su delegado. Para controlar esto en Xamarin.iOS, es necesario reemplazar el `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` método en la subclase MKMapViewDelegate similar al siguiente:

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

La clase SampleMapDelegate mostrada anteriormente se implementa como una clase anidada en el controlador que contiene el `MKMapView` instancia. En Objective-C, a menudo verá el controlador de adoptar varios protocolos directamente dentro de la clase. Sin embargo, puesto que los protocolos están enlazados a las clases en Xamarin.iOS, las clases que implementan fuertemente tipados delegados son normalmente se incluyen como clases anidadas.

Con la implementación de la clase de delegado en su lugar, basta con crear una instancia del delegado en el controlador y asígnelo a la `MKMapView`del `Delegate` propiedad tal y como se muestra aquí:

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

Para usar un delegado débil para lograr lo mismo, debe enlazar el método en cualquier clase que deriva de `NSObject` y asígnelo a la `WeakDelegate` propiedad de la `MKMapView`. Puesto que la `UIViewController` clase se deriva en última instancia `NSObject` (por ejemplo, cada clase Objective-C en CocoaTouch), simplemente se puede implementar un método enlazado a `mapView:didSelectAnnotationView:` directamente en el controlador y asignar el controlador de `MKMapView`del `WeakDelegate`, evitando la necesidad de la clase anidada extra. El código siguiente muestra este enfoque:

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

Cuando se ejecuta este código, la aplicación se comporta exactamente igual que cuando se ejecuta la versión fuertemente tipada de delegado. La ventaja de este código es que el delegado no seguro no requiere la creación de la clase adicional que se creó cuando se usa el delegado fuertemente tipado. Sin embargo, esto es a costa de la seguridad de tipos. Si tuviera que comete un error en el selector que se pasó a la `ExportAttribute`, no sería averiguar hasta el tiempo de ejecución.

 <a name="Events_and_Delegates" />


### <a name="events-and-delegates"></a>Eventos y delegados

Los delegados se utilizan para las devoluciones de llamada de iOS de forma similar a la forma en que .NET usa eventos. Para hacer iOS API y la forma en que usan delegados Objective-C parecer más. NET, Xamarin.iOS expone eventos de .NET en muchos lugares donde los delegados se utilizan en iOS.

Por ejemplo, la implementación anterior donde el `MKMapViewDelegate` respondió a una anotación seleccionada también podría implementarse en Xamarin.iOS mediante el uso de un evento. NET. En ese caso, el evento se definiría en `MKMapView` y llama a `DidSelectAnnotationView`. Habría un `EventArgs` subclase del tipo `MKMapViewAnnotationEventsArgs`. El `View` propiedad de `MKMapViewAnnotationEventsArgs` proporcionaría una referencia a la vista de anotación, desde que puede continuar con la misma implementación tenía anteriormente, como se muestra aquí:

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

 <a name="Summary" />


## <a name="summary"></a>Resumen

En este artículo se trata cómo usar delegados, eventos y protocolos en Xamarin.iOS. Hemos visto cómo Xamarin.iOS expone eventos normales de estilo de .NET para los controles.
A continuación hemos visto acerca de los protocolos de Objective-C, incluido cómo son diferentes de las interfaces de C# y cómo los utiliza Xamarin.iOS. Por último, examinamos a los delegados Objective-C desde la perspectiva de Xamarin.iOS. Hemos visto cómo Xamarin.iOS es compatible con ambos fuertemente y débilmente tipado delegados y cómo enlazar los eventos de .NET para delegar métodos.


## <a name="related-links"></a>Vínculos relacionados

- [Protocolos, delegados y eventos (ejemplo)](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Tipos de enlaces Objective-c.](~/ios/platform/binding-objective-c/index.md)
- [El lenguaje de programación Objective-c.](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Diseñar Interfaces de usuario en Xcode 4](http://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Competencias de aplicación para iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
