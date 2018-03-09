---
title: "Diseño de la API"
description: "Perspectiva en el diseño de la API de Xamarin.iOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 5fab7579be256e478c69b76b5e41b8c1b0568ba6
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="api-design"></a>Diseño de la API

Además de la bibliotecas de clases Base que forman parte de Mono, core [Xamarin.iOS](http://www.xamarin.com/iOS) se suministra con enlaces para iOS varias API para permitir a los desarrolladores crear aplicaciones nativas para iOS con Mono.

En el núcleo de Xamarin.iOS, hay un motor de interoperabilidad que relaciona con el mundo Objective-C como el mundo de C#, así como los enlaces en el programa iOS API basadas en C como CoreGraphics y [OpenGLES](#OpenGLES).

El tiempo de ejecución de bajo nivel para comunicarse con el código Objective C está en el [MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime). En la parte superior, enlaces para [Foundation](#MonoTouch.Foundation), CoreFoundation y [UIKit](#MonoTouch.UIKit) se proporcionan.

## <a name="design-principles"></a>Principios de diseño

Estos son algunos de nuestros principios de diseño para el enlace de Xamarin.iOS (también es aplicable a Xamarin.Mac, los enlaces de Mono para Objective-C en OS X):


- Siga las instrucciones de diseño de Framework
- Permiten a los desarrolladores a clases de subclase Objective-C:

  - Derive de una clase existente
  - Llame al constructor base a cadena
  - Invalidar métodos debe realizarse con el sistema de invalidación de C#

- Subclase debe funcionar con construcciones estándar de C#
- No se exponen a los desarrolladores selectores Objective-c.
- Proporcionan un mecanismo para llamar a las bibliotecas arbitrarias Objective-c.
- Realizar tareas comunes de Objective-C fácil y disco duro posible de tareas Objective-c.
- Exponer propiedades Objective-C como propiedades de C#
- Exponer una API fuertemente tipada:
- Aumentar la seguridad de tipos
- Minimizar los errores en tiempo de ejecución
- Obtener IDE intellisense en tipos de valor devuelto
- Permite la documentación de la ventana emergente IDE
- Fomentar la exploración en el IDE de la API:
- Tipos nativos en C#:

    - Ejemplo: en lugar de exponer la matriz débilmente tipada similar al siguiente:
        ```
        NSArray *getViews
        ```
        para exponerlos con tipos seguros, similar al siguiente:
    
        ```
        NSView [] Views { get; set; }
        ```
    
    Esto le ofrece Visual Studio para Mac que permite realizar la finalización automática durante la exploración de la API y también permite que todos los `System.Array` operaciones estén disponibles en el valor devuelto y permite que el valor devuelto participar en LINQ

- [NSString se convierte en cadena](~/ios/internals/api-design/nsstring.md)
- Activar los parámetros de tipo int y uint que deberían haber sido enumeraciones como enumeraciones de C# y enumeraciones de C# con atributos [Flags]
- En lugar de objetos de tipo neutro NSArray exponer matrices como matriz fuertemente tipado.
- Eventos y notificaciones, proporcionar a los usuarios elegir entre:

    - Versión fuertemente tipada es el valor predeterminado
    - Versión con tipos débiles para casos de uso de avance

- Admitir el patrón de delegado Objective-C:

    - Sistema de eventos de C#
    - Exponer a los delegados de C# (las expresiones lambda, métodos anónimos y System.Delegate) a las API de Objective-C como "bloques"

### <a name="assemblies"></a>Ensamblados

Xamarin.iOS incluye un número de ensamblados que conforman la *Xamarin.iOS perfil*. El [ensamblados](~/cross-platform/internals/available-assemblies.md) página proporciona más información.

### <a name="major-namespaces"></a>Espacios de nombres de principales 

 <a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

El [ObjCRuntime](https://developer.xamarin.com/api/namespace/ObjCRuntime/) espacio de nombres permite a los programadores cubrir los lenguajes entre C# y el objetivo de C.
Se trata de un nuevo enlace, diseñado específicamente para el archivo de iOS, en función de la experiencia de cacao # y Gtk #.

 <a name="MonoTouch.Foundation" />


#### <a name="foundation"></a>Foundation

El [Foundation](https://developer.xamarin.com/api/namespace/Foundation/) espacio de nombres proporciona los tipos de datos básicos diseñadas para interoperar con el framework de Foundation Objective-C que forma parte del iOS y es la base de programación en C. objetivo orientado a objetos

Xamarin.iOS refleja en C# de la jerarquía de clases de objetivo de C. Por ejemplo, la clase base Objective-C [NSObject](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html) puede usar desde C# a través de [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/).

Aunque este espacio de nombres proporciona enlaces para los tipos subyacentes de Foundation Objective-C, en algunos casos hemos asignamos los tipos subyacentes a tipos. NET. Por ejemplo:

- En lugar de trabajar con [NSString](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html) y [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), el runtime expone como C# [cadena](https://developer.xamarin.com/api/type/System.String/)s y fuertemente tipadas [matriz](https://developer.xamarin.com/api/type/System.Array/)s a lo largo de la API.

- Aquí se exponen auxiliar varias API que permiten a los desarrolladores enlazar terceros Objective-C API, otros iOS API o las API que no están enlazadas actualmente por Xamarin.iOS.


Para obtener más detalles sobre las API de enlace, vea la [Xamarin.iOS enlace generador](~/cross-platform/macios/binding/binding-types-reference.md) sección.

 <a name="NSObject" />


##### <a name="nsobject"></a>NSObject

El [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) tipo es la base para todos los enlaces de C de objetivo. Tipos de Xamarin.iOS reflejan dos clases de tipos en el CocoaTouch APIs de iOS: los tipos de C (normalmente usan como tipos de CoreFoundation) y los tipos de Objective-C (estos todos se derivan de la clase NSObject).

Para cada tipo que refleja un tipo no administrado, es posible obtener el objeto nativo a través de la [controlar](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propiedad.

Mientras Mono proporcionará recolección para todos los objetos, el `Foundation.NSObject` implementa la [System.IDisposable](https://developer.xamarin.com/api/type/System.IDisposable/) interfaz. Esto significa que puede liberar explícitamente los recursos de cualquier NSObject determinado sin tener que esperar a que el recolector de elementos no utilizados en la puesta en marcha. Esto es importante cuando se usa mucha NSObjects, por ejemplo, UIImages que podría almacenar punteros a bloques grandes de datos.

Si el tipo que se necesita realizar una finalización determinista, invalidar la [NSObject.Dispose(bool) método](https://developer.xamarin.com/api/type/Foundation.NSObject/%2fM%2fDispose) el parámetro a Dispose se "bool va a eliminar", y si establece en true, significa que se llama a su método Dispose porque el usuario llamados explícitamente () de Dispose en el objeto. Si el valor es false, esto significa que se se llama al método Dispose (colocación de bool) desde el finalizador en el subproceso finalizador. []()

<a name="Categories" />

##### <a name="categories"></a>Categorías

A partir de Xamarin.iOS 8.10, es posible crear categorías Objective-C de C#.

Esto se realiza mediante el `Category` atributo, lo que especifica el tipo de extensión como un argumento para el atributo. Por ejemplo, en el ejemplo siguiente se extenderá NSString.

    [Category (typeof (NSString))]

Cada método de categoría es mediante el mecanismo normal para exportar los métodos a Objective-C utilizando la `Export` atributo:

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Todos los métodos de extensión administrados deben ser estáticos, pero es posible crear métodos de instancia Objective-C mediante la sintaxis estándar de métodos de extensión en C#:

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

y el primer argumento para el método de extensión será la instancia en la que se invocó el método.

Ejemplo completo:

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

En este ejemplo agregará un método de instancia nativo toUpper a la clase NSString, que se puede invocar desde el objetivo de C.

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAudoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

Un escenario donde esto es útil es agregar un método a un conjunto completo de clases en el código base, por ejemplo, esto haría que todos los `UIViewController` instancias de informes que pueden girar:

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```

<a name="PreserveAttribute" />

##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute es un atributo personalizado que se usa para indicar mtouch: la herramienta de implementación de Xamarin.iOS: conservar un tipo o un miembro de un tipo, durante la fase de cuándo se procesa la aplicación para reducir su tamaño.

Cada miembro que no está vinculada estáticamente la aplicación está sujeto a quitarse. Por lo tanto, este atributo se utiliza para marcar a los miembros que no hace referencia estáticamente, pero que siguen siendo necesarios para la aplicación.

Por ejemplo, si crea instancias de tipos de forma dinámica, puede que desee conservar el constructor predeterminado de sus tipos. Si usa la serialización XML, puede que desee conservar las propiedades de los tipos.

Este atributo se puede aplicar en todos los miembros de un tipo, o en el propio tipo. Si desea conservar todo el tipo, puede utilizar la sintaxis [conservar (AllMembers = true)] en el tipo.

 <a name="MonoTouch.UIKit" />


#### <a name="uikit"></a>UIKit

El [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) espacio de nombres contiene una asignación unívoca con todos los componentes de interfaz de usuario que componen CocoaTouch en forma de clases de C#. La API se ha modificado para seguir las convenciones utilizadas en el lenguaje C#.

Los delegados de C# se proporcionan para que las operaciones comunes. Consulte la [delegados](#Delegates) sección para obtener más información.

 <a name="OpenGLES" />


#### <a name="opengles"></a>OpenGLES

Para OpenGLES, se distribuye un [modificar versión](https://developer.xamarin.com/api/namespace/OpenTK/) de la [OpenTK](http://www.opentk.com/) API, un enlace orientada a objetos con OpenGL que se ha modificado para utilizar tipos de datos de CoreGraphics y estructuras, así como exponiendo únicamente la funcionalidad que está disponible en iOS.

Funcionalidad de OpenGLES 1.1 está disponible a través del tipo ES11.GL, documentado [aquí](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES11.GL/) tipo.

Funcionalidad de OpenGLES 2.0 está disponible a través del tipo ES20.GL, documentado [aquí](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES20.GL/) tipo.

Funcionalidad de OpenGLES 3.0 está disponible a través del tipo ES30.GL, documentado [aquí](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES30.GL/) tipo.

 <a name="Binding_Design" />


### <a name="binding-design"></a>Diseño de enlace

Xamarin.iOS no es simplemente un enlace a la plataforma objetivo C subyacente. Amplía el sistema de tipos .NET y el sistema de envío a blend mejor C# y objetivo de C.

Al igual que P/Invoke es una herramienta útil para invocar las bibliotecas nativas en Windows y Linux, o como IJW compatibilidad se puede utilizar para la interoperabilidad COM en Windows, Xamarin.iOS extiende el tiempo de ejecución para admitir objetos de enlace C# para objetos de C++ de objetivo.

La explicación en las próximas secciones no es necesario que los usuarios que se va a crear aplicaciones de Xamarin.iOS, pero le ayudará a los desarrolladores entender cómo se realizan cosas y cómo ayudarán al crear aplicaciones más complicadas.


 <a name="Types" />


#### <a name="types"></a>Tipos

Donde realiza sentido, se exponen los tipos de C# en lugar de bajo nivel tipos Foundation, en el universo de C#.  Esto significa que [la API utiliza el tipo de "string" de C# en lugar de NSString](~/ios/internals/api-design/nsstring.md) y utiliza fuertemente tipada de las matrices de C# en lugar de exponer NSArray.

En general, en el diseño de Xamarin.iOS y Xamarin.Mac, subyacente `NSArray` no se expone el objeto. En su lugar, el tiempo de ejecución convierte automáticamente `NSArray`s a las matrices fuertemente tipadas de algunas `NSObject` clase. Por lo tanto, Xamarin.iOS no expone un método débilmente tipada como GetViews para devolver un NSArray:

```csharp
NSArray GetViews ();
```

En su lugar, el enlace expone un valor devuelto fuertemente tipado, similar al siguiente:

```csharp
UIView [] GetViews ();
```

Hay algunos de los métodos expuestos en `NSArray`, para los casos más complejos que desea usar un `NSArray` directamente, pero se desaconseja su uso en el enlace de API.

Además, en la **API clásico** en lugar de exponer `CGRect`, `CGPoint` y `CGSize` a través de la API CoreGraphics, aquellos con reemplazamos el `System.Drawing` implementaciones `RectangleF`, `PointF`y `SizeF` tal y como se haría ayudan a los desarrolladores conservación el código existente de OpenGL que utiliza OpenTK. Cuando se usa el nuevo 64-bit **API unificada**, se debe usar la API de CoreGraphics.

 <a name="Inheritance" />


#### <a name="inheritance"></a>Herencia

El diseño de la API de Xamarin.iOS permite a los programadores ampliar los tipos nativos Objective-C de la misma manera que extendería a un tipo de C#, utilizando la palabra clave "override" en una clase derivada, así como el encadenamiento de seguridad de la implementación base mediante la palabra clave de C# "base".

Este diseño permite a los desarrolladores evitar problemas con los selectores Objective-C como parte de su proceso de desarrollo, ya que todo el sistema Objective-C ya se ajusta dentro de las bibliotecas de Xamarin.iOS.

 <a name="Types_and_Interface_Builder" />


#### <a name="types-and-interface-builder"></a>Tipos y el generador de interfaz

Al crear clases de .NET que son instancias de tipos creados por el generador de interfaz, debe proporcionar un constructor que toma una sola `IntPtr` parámetro.
Esto es necesario para enlazar la instancia de objeto administrado con el objeto no administrado.
El código está compuesto de una sola línea, similar al siguiente:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```


 <a name="Delegates" />


#### <a name="delegates"></a>Delegados

Objective-C y C# tienen significados diferentes para el delegado de word en cada idioma.

En el mundo Objective-C y en la documentación que encontrará en línea sobre CocoaTouch, un delegado es normalmente una instancia de una clase que responda a un conjunto de métodos. Esto es muy similar a una interfaz de C#, con la diferencia radica en que los métodos no siempre son obligatorios.

Estos delegados desempeñan un papel importante en UIKit y otras APIs CocoaTouch. Se utilizan para realizar diversas tareas:

-  Para proporcionar notificaciones en el código (Similar a la entrega de eventos en C# o Gtk +).
-  Para implementar modelos para los controles de visualización de datos.
-  Para controlar el comportamiento de un control.


El modelo de programación se ha diseñado para minimizar la creación de clases derivadas para modificar el comportamiento de un control. Esta solución es en esencia similares a lo han hecho otros kits de herramientas de interfaz gráfica de usuario en los años: Gtk señales, ranuras Qt, eventos de formularios Windows Forms, WPF y Silverlight eventos y así sucesivamente. Para evitar tener cientos de interfaces (uno para cada acción) o exigir a los desarrolladores implementar demasiados métodos que no es necesario, Objective-C admite las definiciones de método opcional. Esto es distinto a interfaces de C# que requieren todos los métodos para que se implementen.

En las clases Objective-C, verá que las clases que utilicen este modelo de programación exponen una propiedad, normalmente se denominan `delegate`, lo cual es necesario para implementar los elementos obligatorios de la interfaz y las partes de cero o más, de la parte opcional.

En Xamarin.iOS se ofrecen tres mecanismos mutuamente excluyentes para enlazar a estos delegados:

1.  [A través de eventos](#Events) .
2.  [Fuertemente tipados a través de un `Delegate`propiedad](#StrongDelegate) .
3.  [Establecimiento flexible de tipos a través de un `WeakDelegate`propiedad](#WeakDelegate) .


Por ejemplo, considere la [UIWebView](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html) clase. Esto lo envía a un [UIWebViewDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html) instancia, que se asigna a la [delegar](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate) propiedad.

 <a name="Via_Events" />


##### <a name="via-events"></a>A través de eventos

Para muchos tipos de Xamarin.iOS creará automáticamente un delegado adecuado que se reenviará el `UIWebViewDelegate` llamadas a eventos de C#. Para `UIWebView`:

-  El [webViewDidStartLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:) método se asigna a la [UIWebView.LoadStarted](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadStarted/) eventos.
-  El [webViewDidFinishLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:) método se asigna a la [UIWebView.LoadFinished](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadFinished/) eventos.
-  El [webView:didFailLoadWithError](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:) método se asigna a la [UIWebView.LoadError](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadError/) eventos.


Por ejemplo, este programa simple registra los tiempos de inicio y finalización cuando se visualiza la carga de un sitio web:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

 <a name="Via_Properties" />


##### <a name="via-properties"></a>Propiedades de VIA

Los eventos resultan útiles cuando podría haber más de un suscriptor al evento. Además, se limitan a casos de eventos donde no hay ningún valor devuelto desde el código.

Para los casos donde se espera el código para devolver un valor, se quitan en su lugar para las propiedades. Esto significa que solo un método puede establecerse en un momento dado en un objeto.

Por ejemplo, puede utilizar este mecanismo para descartar el teclado en la pantalla en el controlador para un `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

El `UITextField`del `ShouldReturn` propiedad en este caso toma como argumento un delegado que devuelve un valor booleano y determina si el campo de texto debería hacer algo con el botón "volver" pulsación. En el método, devolvemos *true* al llamador, pero también quitar el teclado de la pantalla (Esto sucede cuando el campo de texto llama `ResignFirstResponder`).


##### <a name="strongly-typed-via-a-delegate-property"></a>Establecimiento inflexible de tipos a través de una propiedad de delegado

Si prefiere no usar eventos, también puede proporcionar su propia [UIWebViewDelegate](https://developer.xamarin.com/api/type/UIKit.UIWebViewDelegate/) subclase y asígnelo a la [UIWebView.Delegate](https://developer.xamarin.com/api/property/UIKit.UIWebView.Delegate/) propiedad. Una vez que se ha asignado UIWebView.Delegate, el mecanismo de envío de eventos de UIWebView dejarán de funcionar y los métodos de UIWebViewDelegate se invocará cuando se producen los eventos correspondientes.

Por ejemplo, este tipo simple registra el tiempo que se tarda en cargar una vista web:

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

Los pasos anteriores se utilizan en código similar al siguiente:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Los pasos anteriores, creará un UIWebViewer e indicará a él para enviar mensajes a una instancia de notificador, una clase que creamos para responder a los mensajes.

Este patrón también se utiliza para controlar el comportamiento de determinados controles como, por ejemplo, en el caso de UIWebView, la [UIWebView.ShouldStartLoad](https://developer.xamarin.com/api/property/UIKit.UIWebView.ShouldStartLoad/) propiedad permite el `UIWebView` instancia para controlar si el `UIWebView` se cargará un página o no.

El patrón también se utiliza para proporcionar los datos a petición para algunos controles. Por ejemplo, el [UITableView](https://developer.xamarin.com/api/type/UIKit.UITableView/) el control es un control eficaz de representación de la tabla, y la apariencia y el contenido está determinado por una instancia de un [UITableViewDataSource](https://developer.xamarin.com/api/type/UIKit.UITableView/DataSource)


@### Establecimiento flexible de tipos a través de la propiedad WeakDelegate

Además de la propiedad fuertemente tipada, también es débil con tipo delegado que permite al desarrollador enlazar cosas de manera diferente si lo desea.
Everywhere fuertemente tipado `Delegate` propiedad se expone en el enlace del Xamarin.iOS, correspondiente `WeakDelegate` también se expone la propiedad.

Cuando se usa el `WeakDelegate`, tú eres responsable de decorando correctamente su clase utilizando el [exportar](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) atributo para especificar el selector. Por ejemplo:

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

Tenga en cuenta que una vez el `WeakDelegate` se ha asignado la propiedad, el `Delegate` no se usará la propiedad. Además, si el método se implementa en una clase base heredada que deseen [Exportar], debe convertirlo un método público.


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>Asignación del modelo de delegado Objective-C a C &#35;

Cuando vea ejemplos de C de objetivo que tengan un aspecto similar al siguiente:

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

Esto indica el idioma para crear y construir una instancia de la clase "SomethingDelegate" y asigne el valor a la propiedad de delegado en la variable foo. Este mecanismo es compatible con Xamarin.iOS y la sintaxis de C# es:

```csharp
foo.Delegate = new SomethingDelegate ();
```

En Xamarin.iOS se ha proporcionado clases fuertemente tipadas que se asignan a Objective-C clases de delegado. Para poder utilizarlos, se creación de subclases y reemplazar los métodos definidos por la implementación de Xamarin.iOS. Para obtener más información sobre cómo funcionan, vea la sección "modelos" a continuación.


##### <a name="mapping-delegates-to-c35"></a>Asignar a delegados a C &#35;

En general, UIKit utiliza Objective-C delegados de dos formas.

La primera forma proporciona una interfaz al modelo de un componente. Por ejemplo, como un mecanismo para proporcionar datos a petición para una vista, como la instalación de almacenamiento de datos para una vista de lista.  En estos casos, siempre debe crear una instancia de la clase adecuada y asigne la variable.

En el ejemplo siguiente, se proporcionan los `UIPickerView` con una implementación de un modelo que utiliza cadenas:

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

La segunda forma es proporcionar una notificación de eventos. En esos casos, aunque todavía se expone la API en el formulario que se ha descrito anteriormente, también proporcionamos eventos de C#, que deben ser más fácil de usar para las operaciones de rápido y se integra con delegados anónimos y expresiones lambda en C#.

Por ejemplo, puede suscribirse a `UIAccelerometer` eventos:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Las dos opciones están disponibles cuando tienen sentido, pero como programador debe elegir uno u otro. Si crea su propia instancia de un servicio de respuesta/delegado fuertemente tipado y asignarlo, los eventos de C# no será funcionales. Si utiliza los eventos de C#, nunca se llamará a los métodos de la clase de servicio de respuesta/delegado.

El ejemplo anterior que usa `UIWebView` se pueden escribir con expresiones lambda de C# 3.0 similar al siguiente:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>Respuesta a eventos

En el código Objective-C, a veces, controladores de eventos para varios controles y los proveedores de la información de varios controles, se hospeda en la misma clase. Esto es posible porque las clases responden a mensajes, y como clases responden a mensajes, es posible vincular objetos entre sí.

Como se describe anteriormente, Xamarin.iOS admite tanto el C# basado en eventos y modelo de programación y el modelo de delegado Objective-C, donde puede crear una nueva clase que implementa al delegado e invalida los métodos deseados.

También es posible admitir el patrón del objetivo-C donde los servicios de respuesta en varias operaciones diferentes están hospedadas en la misma instancia de una clase. Para hacer esto sin embargo, tendrá que usar características de bajo nivel del enlace Xamarin.iOS.

Por ejemplo, si desea que la clase para que responda a ambos el `UITextFieldDelegate.textFieldShouldClear`: mensaje y `UIWebViewDelegate.webViewDidStartLoad`: en la misma instancia de una clase, deberá utilizar la declaración de atributo [Exportar]:

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

Los nombres de C# para los métodos no son importantes; todo lo que realmente importa son las cadenas que se pasan al atributo [Exportar].

Al utilizar este estilo de programación, asegúrese de que los parámetros de C# coinciden con los tipos reales que pasará el motor en tiempo de ejecución.


#### <a name="models"></a>Modelos

En instalaciones de almacenamiento de UIKit, o en los servicios de respuesta que se implementan utilizando las clases auxiliares, estos se conocen normalmente en el código Objective-C como delegados, y se implementan como protocolos.

Protocolos de Objective-C son similares a las interfaces, pero admiten métodos opcionales; es decir, no todos los métodos deben implementarse para que funcione el protocolo.

Hay dos maneras de implementar un modelo. Puede implementar de forma manual o utilizar las definiciones existentes fuertemente tipadas.


El mecanismo manual es necesario cuando se intenta implementar una clase que no se ha enlazado por Xamarin.iOS. Es muy fácil de hacer:

-  Marca la clase para el registro con el tiempo de ejecución
-  Aplique el atributo [Exportar] con el nombre de selector real en cada método que va a invalidar
-  Crear una instancia de la clase y para pasar.

Por ejemplo, a continuación implementa solo uno de los métodos opcionales en la definición de protocolo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

El nombre del selector Objective-C ("applicationDidFinishLaunching:") se declara con el atributo de exportación y la clase se registra con el `[Register]` atributo.

Xamarin.iOS proporciona fuertemente tipadas declaraciones, listo para usar, que no requieren enlace manual. Para admitir este modelo de programación, el tiempo de ejecución de Xamarin.iOS es compatible con el atributo [modelo] en una declaración de clase. Esto informa el tiempo de ejecución que debe no conecte todos los métodos de la clase, a menos que los métodos son se implementa explícitamente.

Esto significa que, en UIKit, las clases que representan un protocolo con métodos opcionales se escriben similar al siguiente:

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

Si desea implementar un modelo que implementa solo algunos de los métodos, lo único que debe hacer es que invalide los métodos que les interesa y pasar por alto los demás métodos. Los métodos de sobrescribir, no los métodos originales para el mundo Objective-C solo enlazará el tiempo de ejecución.

Es el equivalente al ejemplo anterior manual:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

Las ventajas son que no es necesario para profundizar en los archivos de encabezado Objective-C para buscar el selector, los tipos de los argumentos o la asignación a C#, y que obtiene intellisense de Visual Studio para Mac, junto con tipos seguros


#### <a name="xib-outlets-and-c35"></a>Tomas XIB y C &#35;

> [!IMPORTANT]
> Esta sección explica la integración de IDE con distribuidores cuando se usan archivos XIB. Al usar el Diseñador de Xamarin para iOS, esto se todos reemplaza escribiendo un nombre en **identidad > nombre** en la sección de propiedades de su IDE, tal y como se muestra a continuación:
>
> [![](images/designeroutlet.png "Escriba un nombre de elemento en el Diseñador de iOS")](images/designeroutlet.png)
>
>Para obtener más información sobre el Diseñador de iOS, revise la [introducción en el Diseñador de iOS](~/ios/user-interface/designer/introduction.md#how-it-works) documento.

Esto es una descripción de bajo nivel de cómo integran las salidas con C# y se proporciona para los usuarios avanzados de Xamarin.iOS. Cuando uso de Visual Studio para Mac la asignación se realiza automáticamente en segundo plano mediante genera código en el vuelo.

Al diseñar la interfaz de usuario con el generador de interfaz, que solo puede diseñar la apariencia de la aplicación y establecerá algunas conexiones de forma predeterminada. Si desea obtener información, modificar el comportamiento de un control en tiempo de ejecución o modificar el control en tiempo de ejecución mediante programación, es necesario enlazar algunos de los controles en el código administrado.

Esto se hace en unos pocos pasos:

1.  Agregar el **declaración de toma de corriente** a su **propietario del archivo**.
1.  Conectar el control a la **propietario del archivo**.
1.  Almacén de la interfaz de usuario además de las conexiones en el archivo XIB/NIB.
1.  Cargar el archivo NIB en tiempo de ejecución.
1.  Obtener acceso a la variable de salida.


Los pasos (1) (3) se tratan en la documentación de Apple para la creación de interfaces con el generador de interfaz.

Al utilizar Xamarin.iOS, la aplicación necesitará crear una clase que deriva de UIViewController. Se implementa, similar al siguiente:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

A continuación, para cargar su ViewController desde un archivo NIB, para ello:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Esto carga la interfaz de usuario de la NIB. Ahora, para tener acceso a las salidas, es necesario informar al runtime que se va a tener acceso a ellos. Para ello, el `UIViewController` subclase debe declarar las propiedades y anotar en ellos con el atributo [conectar]. Así:

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

La implementación de propiedades es el que realmente se recopila y almacena el valor para el tipo nativo real.

No es necesario preocuparse de esto al utilizar Visual Studio para Mac y InterfaceBuilder. Visual Studio para Mac refleja automáticamente todas las salidas declaradas con código en una clase parcial que se compila como parte de su proyecto.

#### <a name="selectors"></a>Selectores

Un concepto básico de la programación de C objetivo es selectores. A menudo procederán a través de las API que requieren pasar un selector o espera que el código para responder a un selector.

Crear nuevos selectores en C# es muy sencillo: simplemente crear una nueva instancia de la `ObjCRuntime.Selector` clase y utilizar el resultado en cualquier lugar de la API que lo requiera. Por ejemplo:

```csharp
var selector_add = new Selector ("add:plus:");
```

Para un C# responden a una llamada de selector de método, debe heredar de la `NSObject` tipo y el método de C# se deben decorar con el nombre de selector mediante el `[Export]` atributo. Por ejemplo:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Tenga en cuenta que selector nombres **debe** coinciden exactamente, incluidos todos los intermedios y final signos de dos puntos (":"), si está presente.

#### <a name="nsobject-constructors"></a>Constructores de NSObject

La mayoría de Xamarin.iOS que se derivan de clases `NSObject` expondrá constructores específicos a la funcionalidad del objeto, pero también se exponen varios constructores que no son tan evidentes.

Los constructores se utilizan como sigue:

```csharp
public Foo (IntPtr handle)
```

Este constructor se utiliza para crear una instancia de la clase cuando el tiempo de ejecución debe asignar la clase a una clase no administrada. Esto sucede cuando se carga un archivo XIB/NIB.  En este momento, el tiempo de ejecución de C de objetivo se habrá creado un objeto en el mundo no administrado y se llama a este constructor para inicializar la parte administrada.

Por lo general, todo lo que necesita hacer es llamar al constructor base con el parámetro de identificador y en el cuerpo, realizar cualquier inicialización que sea necesario.

```csharp
public Foo ()
```

Éste es el constructor predeterminado para una clase, y en Xamarin.iOS proporcionada clases, esto inicializa la clase Foundation.NSObject y todas las clases de en medio y al final, encadena a Objective-C `init` método en la clase.

```csharp
public Foo (NSObjectFlag x)
```

Este constructor se utiliza para inicializar la instancia, pero impide que el código de llamada al método de "inicialización" de Objective-C al final. Se usa generalmente cuando ya haya registrado para la inicialización (cuando se usa `[Export]` en el constructor) o cuando aún lo ha hecho la inicialización a través de otro medio.

```csharp
public Foo (NSCoder coder)
```

Este constructor se proporciona para los casos donde se está inicializando el objeto de una instancia de NSCoding. Para obtener más información, consulte de Apple [archivos y Guía de programación de serialización.](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>Excepciones

El diseño de la API de Xamarin.iOS no provocan excepciones de Objective-C como excepciones de C#. El diseño exige que no hay elementos no utilizados enviará al mundo Objective-C en primer lugar y que cualquier excepción que se debe producir es generados por el propio enlace antes de que los datos no válidos nunca se pasan al mundo Objective-C.

#### <a name="notifications"></a>Notificaciones

En iOS y OS X, los desarrolladores pueden suscribirse a las notificaciones que se emiten por la plataforma subyacente. Esto se realiza mediante el `NSNotificationCenter.DefaultCenter.AddObserver` método. El `AddObserver` método toma dos parámetros, uno es la notificación que desea suscribirse a; el otro es el método que se invocará cuando se produce la notificación.

En Xamarin.iOS y Xamarin.Mac, las claves para las distintas notificaciones que se hospedan en la clase que desencadena las notificaciones. Por ejemplo, las notificaciones generan por el `UIMenuController` se alojan como `static NSString` propiedades en el `UIMenuController` clases que terminan con el nombre "Notificaciones".

### <a name="memory-management"></a>Administración de memoria

Xamarin.iOS tiene un recolector de elementos no utilizados que se encargará de liberar recursos automáticamente cuando ya no están en uso. Además el recolector de elementos no utilizados, todos los objetos que derivan de `NSObject` implementar la `System.IDisposable` interfaz.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Exponer el `IDisposable` interfaz es una manera cómoda de ayudar a los desarrolladores al liberar objetos que pueden encapsular grandes bloques de memoria (por ejemplo, un `UIImage` podría ser similar a un puntero de inofensivo, pero podría apuntar a una imagen 2 megabytes ) y otros recursos importantes y finitos (por ejemplo, un búfer de descodificación de vídeo).

NSObject implementa la interfaz IDisposable y también el [patrón Dispose de .NET](http://msdn.microsoft.com/en-us/library/fs2xkftw.aspx). Esto permite a los desarrolladores que subclase NSObject para invalidar el comportamiento de Dispose y liberar sus propios recursos a petición. Por ejemplo, considere la posibilidad de este controlador de vista que mantiene alrededor de un grupo de imágenes:

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

Cuando se elimina un objeto administrado, ya no es útil. Todavía es posible que tenga una referencia a los objetos, pero el objeto por todos los propósitos es válido en este momento. Algunas API de .NET garantizarlo iniciando una excepción ObjectDisposedException si se intenta tener acceso a los métodos en un objeto desechado, por ejemplo:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Aunque todavía puede tener acceso a la variable "imagen", es realmente una referencia no válida y ya no señala al objeto Objective-C que mantiene la imagen.

Pero desechar un objeto en C# no significa que necesariamente se destruirá el objeto. Todo lo que hacer es la versión de la referencia de C# tenía que el objeto. Es posible que el entorno de cacao podría ha guardado una referencia alrededor para su propio uso. Por ejemplo, si establece la propiedad de imagen del UIImageView a una imagen y, a continuación, eliminar la imagen, el UIImageView subyacente tuvo su propia referencia y mantendrá una referencia a este objeto hasta que haya terminado con él.

#### <a name="when-to-call-dispose"></a>Cuándo se debe llamar a Dispose

Debe llamar a Dispose cuando necesite Mono en deshacerse del objeto. Un caso de uso posible resulta Mono no tiene ningún conocimiento que su NSObject realmente mantiene una referencia a un recurso importante como la memoria o un grupo de información. En esos casos, debe llamar a Dispose para liberar inmediatamente la referencia a la memoria, en lugar de esperar Mono realizar un ciclo de recolección.

Internamente, cuando se crea Mono [NSString hace referencia de C# cadenas](~/ios/internals/api-design/nsstring.md), eliminará inmediatamente para reducir la cantidad de trabajo que el recolector de elementos no utilizados tiene que ver. Los objetos menos para tratar con, cuanto más rápido el catálogo global se ejecutarán.

#### <a name="when-to-keep-references-to-objects"></a>Cuándo se debe mantener las referencias a objetos

Un efecto secundario que tenga la administración de memoria automática es que el catálogo global se deshacerse de objetos no utilizados siempre que no hay ninguna referencia a ellos. Esto a veces puede tener efectos secundarios incoherentes, por ejemplo, si crea una variable local para almacenar el controlador de vista de nivel superior, o la ventana de nivel superior y, a continuación, con los desaparecerán detrás de la copia de seguridad.

Si no mantiene una referencia en su estático o variables de instancia en los objetos, Mono, llamará al método Dispose() en ellos y liberará la referencia al objeto. Puesto que esto podría ser la referencia solo pendiente, el tiempo de ejecución de C objetivo destruirá el objeto para usted.

## <a name="related-links"></a>Vínculos relacionados

- [Enlazar campos](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
