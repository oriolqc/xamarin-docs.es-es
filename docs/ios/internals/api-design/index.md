---
title: Diseño de API de Xamarin.iOS
description: Este documento describe algunos de los principios fundamentales que se usan para diseñar las APIs de Xamarin.iOS y cómo se relacionan estos a Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: cd25e5c78885f53902c577a900958b842a70219c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116958"
---
# <a name="xamarinios-api-design"></a>Diseño de API de Xamarin.iOS

Además de la bibliotecas de clases Base que forman parte de Mono, core [Xamarin.iOS](http://www.xamarin.com/iOS) se suministra con enlaces para iOS varias API para permitir a los desarrolladores crear aplicaciones nativas de iOS con Mono.

En el núcleo de Xamarin.iOS, hay un motor de interoperabilidad que relaciona el mundo de C# con el mundo de Objective-C, así como los enlaces para las API basadas en C como CoreGraphics iOS y [OpenGL ES](#OpenGLES).

Es el tiempo de ejecución de bajo nivel para comunicarse con el código de Objective-C en [MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime). En la parte superior, los enlaces para [Foundation](#MonoTouch.Foundation), CoreFoundation, y [UIKit](#MonoTouch.UIKit) se proporcionan.

## <a name="design-principles"></a>Principios de diseño

Estos son algunos de nuestros principios de diseño para los enlaces de Xamarin.iOS (también se aplican a Xamarin.Mac, los enlaces de Mono para Objective-C en macOS):

- Siga el [instrucciones de diseño de Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Permitir a los desarrolladores a las clases de subclase Objective-C:

  - Derivar una clase existente
  - Llame al constructor base a cadena
  - Invalidar los métodos debe realizarse con el sistema de invalidación de C#
  - Creación de subclases debería funcionar con construcciones de estándar de C#

- No exponga a los desarrolladores los selectores de Objective-c.
- Proporcionan un mecanismo para llamar a las bibliotecas de Objective-C arbitrarias
- Realizar tareas comunes de Objective-C fácil y difícil posible de las tareas de Objective-c.
- Exponer propiedades de Objective-C como propiedades de C#
- Exponer una API fuertemente tipada:

  - Aumentar la seguridad de tipos
  - Minimizar los errores en tiempo de ejecución
  - Obtener IDE IntelliSense en tipos de valor devuelto
  - Permite la documentación de la ventana emergente IDE

- Recomendamos la exploración en el IDE de la API:

  - Por ejemplo, en lugar de exponer una matriz débilmente tipada similar al siguiente:
    
    ```objc
    NSArray *getViews
    ```
    Exponer un tipo seguro, similar al siguiente:
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    Esto proporciona a la capacidad para realizar la finalización automática mientras navega por la API de Visual Studio para Mac, hace que todos los `System.Array` las operaciones disponibles en el valor devuelto y permite que el valor devuelto participar en LINQ.

- Tipos nativos en C#:

  - [`NSString` se convierte en `string`](~/ios/internals/api-design/nsstring.md)
  - Activar `int` y `uint` parámetros que deberían haber sido enumeraciones en las enumeraciones de C# y las enumeraciones de C# con `[Flags]` atributos
  - En lugar de tipo neutro `NSArray` objetos, se exponen las matrices como matrices fuertemente tipado.
  - Para los eventos y notificaciones, proporcionar a los usuarios elegir entre:

    - Una versión fuertemente tipada de forma predeterminada
    - Una versión débilmente tipada para casos de uso avanzadas

- Admitir el patrón de delegado de Objective-C:

    - Sistema de eventos en C#
    - Exponer los delegados de C# (las expresiones lambda, los métodos anónimos, y `System.Delegate`) a las API de Objective-C como bloques

### <a name="assemblies"></a>Ensamblados

Xamarin.iOS incluye una serie de ensamblados que conforman el *Xamarin.iOS perfil*. El [ensamblados](~/cross-platform/internals/available-assemblies.md) página encontrará más información.

### <a name="major-namespaces"></a>Espacios de nombres principal 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

El [ObjCRuntime](https://developer.xamarin.com/api/namespace/ObjCRuntime/) espacio de nombres permite a los desarrolladores mundos entre C# y Objective-C.
Se trata de un nuevo enlace, diseñado específicamente para iOS, según la experiencia de cacao # y Gtk #.

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

El [Foundation](https://developer.xamarin.com/api/namespace/Foundation/) espacio de nombres proporciona los tipos de datos básica diseñado para interoperar con el marco de Foundation de Objective-C que forma parte de iOS y es la base para orientada a objetos de programación en Objective-C.

Xamarin.iOS refleja en C# de la jerarquía de clases desde Objective-C. Por ejemplo, la clase base de Objective-C [NSObject](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html) se puede usar desde C# a través de [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/).

Aunque este espacio de nombres proporciona enlaces para los tipos subyacentes de Foundation de Objective-C, en algunos casos, asignamos los tipos subyacentes a tipos .NET. Por ejemplo:

- En lugar de lidiar con [NSString](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html) y [nsarray frente](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), el runtime expone estos elementos como C# [cadena](xref:System.String)s y fuertemente tipadas [matriz](xref:System.Array)s a lo largo de la API.

- Aplicación auxiliar de diversas API se exponen aquí para permitir que los desarrolladores enlazar las API de Objective-C, otras API o las API que no están enlazadas actualmente por Xamarin.iOS de iOS de terceros.

Para obtener más detalles sobre las API de enlace, consulte el [generador de enlace de Xamarin.iOS](~/cross-platform/macios/binding/binding-types-reference.md) sección.


##### <a name="nsobject"></a>NSObject

El [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) tipo es la base para todos los enlaces de Objective-C. Tipos de Xamarin.iOS reflejan dos clases de tipos desde el CocoaTouch APIs de iOS: los tipos de C (normalmente llamados a tipos CoreFoundation) y los tipos de Objective-C (estos todos se derivan de la clase de NSObject).

Para cada tipo que refleja un tipo no administrado, es posible obtener el objeto nativo a través de la [controlar](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propiedad.

Mientras Mono proporcionará la recolección de elementos de todos los objetos, el `Foundation.NSObject` implementa el [System.IDisposable](xref:System.IDisposable) interfaz. Esto significa que puede liberar explícitamente los recursos de cualquier NSObject determinado sin tener que esperar a que el recolector de elementos no utilizados de puesta en marcha. Esto es importante cuando se usa NSObjects pesadas, por ejemplo, UIImages que podría almacenar punteros a bloques grandes de datos.

Si el tipo se necesita realizar la finalización determinista, reemplace el [NSObject.Dispose(bool) método](https://developer.xamarin.com/api/type/Foundation.NSObject/%2fM%2fDispose) el parámetro a Dispose es "bool disposing", y si establece en true, significa que se llama su método Dispose porque el usuario llamados explícitamente () de Dispose en el objeto. Si el valor es false, esto significa que el método Dispose (bool disposing) es que se llama desde el finalizador en el subproceso del finalizador. []()


##### <a name="categories"></a>Categorías

Comenzando con Xamarin.iOS 8.10 es posible crear categorías de Objective-C desde C#.

Esto se realiza mediante el `Category` atributo, lo que especifica el tipo de extensión como un argumento para el atributo. Por ejemplo, en el ejemplo siguiente se extenderá NSString.

    [Category (typeof (NSString))]

Cada método de categoría es mediante el mecanismo normal de exportación de los métodos a Objective-C mediante la `Export` atributo:

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Todos los métodos de extensión administrados deben ser estáticos, pero es posible crear métodos de instancia de Objective-C utilizando la sintaxis estándar para los métodos de extensión en C#:

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

y el primer argumento al método de extensión será la instancia en el que se invocó el método.

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

En este ejemplo se agregará un método de instancia nativo toUpper a la clase NSString, que se puede invocar desde Objective-C.

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

Un escenario donde esto es útil es agregar un método a un conjunto completo de las clases en el código base, por ejemplo, esto haría que todos los `UIViewController` instancias de informes que pueden girar:

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


##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute es un atributo personalizado que se usa para indicar mtouch: la herramienta de implementación de Xamarin.iOS: conservar un tipo o miembro de un tipo, durante la fase de cuándo se procesa la aplicación para reducir su tamaño.

Todos los miembros no vinculados de forma estática por la aplicación podrían eliminarse. Por lo tanto, este atributo se usa para marcar a los miembros que no se hace referencia, pero que aún son necesarios para la aplicación.

Por ejemplo, si crea instancias de tipos de forma dinámica, puede que desee conservar el constructor predeterminado de sus tipos. Si usa la serialización XML, puede que desee conservar las propiedades de los tipos.

Este atributo se puede aplicar en todos los miembros de un tipo o en el propio tipo. Si desea conservar todo el tipo, puede usar la sintaxis [conservar (AllMembers = true)] en el tipo.

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

El [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) espacio de nombres contiene una asignación unívoca a todos los componentes de interfaz de usuario que componen CocoaTouch en forma de clases de C#. La API se ha modificado para seguir las convenciones utilizadas en el lenguaje C#.

Delegados de C# se proporcionan para las operaciones comunes. Consulte la [delegados](#Delegates) sección para obtener más información.

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGL es

Para OpenGLES, distribuimos un [versión modificada](https://developer.xamarin.com/api/namespace/OpenTK/) de la [OpenTK](http://www.opentk.com/) API, un enlace orientada a objetos con OpenGL que se ha modificado para utilizar CoreGraphics los tipos de datos y estructuras, así como exponer solo los funcionalidad que está disponible en iOS.

Funcionalidad de OpenGL es 1.1 está disponible a través del tipo ES11.GL, documentado [aquí](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES11.GL/) tipo.

Funcionalidad de OpenGL es 2.0 está disponible a través del tipo ES20.GL, documentado [aquí](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES20.GL/) tipo.

Funcionalidad de OpenGL es 3.0 está disponible a través del tipo ES30.GL, documentado [aquí](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES30.GL/) tipo.


### <a name="binding-design"></a>Diseño de enlace

Xamarin.iOS no es simplemente un enlace a la plataforma subyacente de C de objetivo. Amplía el sistema de tipos .NET y el sistema de envío para blend mejor C# y Objective-C.

Al igual que P/Invoke es una herramienta útil para invocar las bibliotecas nativas en Windows y Linux, o como IJW compatibilidad puede usarse para la interoperabilidad COM en Windows, Xamarin.iOS extiende el tiempo de ejecución para admitir objetos C# de enlace a objetos C de objetivo.

La discusión en las próximas secciones no es necesario para los usuarios que se va a crear aplicaciones de Xamarin.iOS, pero le ayudarán a los desarrolladores comprender cómo las cosas se realizan y ayudarán al crear aplicaciones más complicadas.



#### <a name="types"></a>Tipos

Donde tenía sentido, los tipos de C# se exponen en lugar de bajo nivel tipos base, en el universo de C#.  Esto significa que [la API usa el tipo "string" de C# en lugar de NSString](~/ios/internals/api-design/nsstring.md) y usa matrices de C# fuertemente tipadas en lugar de exponer nsarray frente.

En general, en el diseño de Xamarin.iOS y Xamarin.Mac, subyacente `NSArray` no se expone el objeto. En su lugar, el tiempo de ejecución convierte automáticamente `NSArray`s a las matrices fuertemente tipadas de algunas `NSObject` clase. Por lo tanto, Xamarin.iOS no expone un método débilmente tipada como GetViews para devolver un nsarray frente:

```csharp
NSArray GetViews ();
```

En su lugar, el enlace expone un valor devuelto fuertemente tipado, similar al siguiente:

```csharp
UIView [] GetViews ();
```

Hay una serie de métodos expuestos en `NSArray`, para los casos donde desea usar un `NSArray` directamente, pero se desaconseja su uso en el enlace de API.

Además, en el **API clásica** en lugar de exponer `CGRect`, `CGPoint` y `CGSize` de la API CoreGraphics, aquellos con reemplazamos la `System.Drawing` implementaciones `RectangleF`, `PointF`y `SizeF` ya que podrían ayudar a los desarrolladores de conservación el código existente de OpenGL que utiliza OpenTK. Cuando se usa el nuevo 64-bit **Unified API**, se debe usar la API CoreGraphics.

<a name="Inheritance" />

#### <a name="inheritance"></a>Herencia

El diseño de API de Xamarin.iOS permite a los programadores extender tipos nativos de Objective-C de la misma manera que podría extender un tipo de C#, utilizando la palabra clave "override" en una clase derivada, así como el encadenamiento la implementación base mediante la palabra clave de C# "base".

Este diseño permite a los desarrolladores evitar lidiar con los selectores de Objective-C como parte de su proceso de desarrollo, ya que todo el sistema de Objective-C ya se ajusta dentro de las bibliotecas de Xamarin.iOS.


#### <a name="types-and-interface-builder"></a>Tipos y en Interface Builder

Al crear clases de .NET que son instancias de tipos creados mediante Interface Builder, deberá proporcionar un constructor que toma un único `IntPtr` parámetro.
Esto es necesario para enlazar la instancia de objeto administrado con el objeto no administrado.
El código consta de una sola línea, similar al siguiente:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>Delegados

Objective-C y C# tienen significados diferentes para el delegado de word en cada idioma.

En el mundo de Objective-C y en la documentación que encontrará en línea sobre CocoaTouch, un delegado es normalmente una instancia de una clase que responderá a un conjunto de métodos. Esto es muy similar a una interfaz de C#, con la diferencia radica en que los métodos no siempre son obligatorios.

Estos delegados desempeñan un papel importante en UIKit y otras APIs de CocoaTouch. Se usan para realizar varias tareas:

-  Para proporcionar notificaciones a su código (Similar a la entrega de eventos en C# o Gtk +).
-  Para implementar los modelos para los controles de visualización de datos.
-  Para controlar el comportamiento de un control.


El modelo de programación se diseñó para minimizar la creación de las clases derivadas para modificar el comportamiento de un control. Esta solución es similar en espíritu a lo que han hecho otros kits de herramientas de GUI durante los años: Gtk señales, ranuras Qt, eventos de Winforms, WPF o Silverlight eventos y así sucesivamente. Para evitar tener cientos de interfaces (uno para cada acción) ni a los desarrolladores implementar demasiados métodos que no es necesario, Objective-C es compatible con las definiciones de método opcional. Esto es diferente a interfaces de C# que requieren que se implementan todos los métodos.

En las clases de Objective-C, verá que las clases que utilicen este modelo de programación exponen una propiedad, normalmente se denomina `delegate`, lo cual es necesario para implementar los elementos obligatorios de la interfaz y las partes de cero o más, de opcional.

En Xamarin.iOS se ofrecen tres mecanismos mutuamente excluyentes para enlazar a estos delegados:

1.  [A través de eventos](#Via_Events).
2.  [Fuertemente tipado a través de un `Delegate` propiedad](#StrongDelegate)
3.  [Establecimiento flexible de tipos a través de un `WeakDelegate` propiedad](#WeakDelegate)

Por ejemplo, considere la [UIWebView](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html) clase. Esto envía a un [UIWebViewDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html) instancia, que se asigna a la [delegar](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate) propiedad.

<a name="Via_Events" />

##### <a name="via-events"></a>A través de eventos

Para muchos tipos, Xamarin.iOS creará automáticamente un delegado adecuado que reenviará la `UIWebViewDelegate` llamadas a eventos de C#. Para `UIWebView`:

-  El [webViewDidStartLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:) método se asigna a la [UIWebView.LoadStarted](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadStarted/) eventos.
-  El [webViewDidFinishLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:) método se asigna a la [UIWebView.LoadFinished](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadFinished/) eventos.
-  El [webView:didFailLoadWithError](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:) método se asigna a la [UIWebView.LoadError](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadError/) eventos.

Por ejemplo, este sencillo programa registra los tiempos de inicio y finalización cuando se visualice la carga de un sitio web:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>A través de propiedades

Los eventos son útiles cuando puede haber más de un suscriptor del evento. Además, los eventos se limitan a casos donde no hay ningún valor devuelto desde el código.

Para los casos donde el código debe devolver un valor, hemos decidido en su lugar para las propiedades. Esto significa que solo un método se puede establecer en un momento dado en un objeto.

Por ejemplo, puede usar este mecanismo para descartar el teclado en pantalla en el controlador para un `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

El `UITextField`del `ShouldReturn` propiedad en este caso toma como argumento un delegado que devuelve un valor booleano y determina si el campo de texto debe hacer algo con el valor devuelto pulsación del botón. En el método, devolvemos *true* al llamador, pero también Quitamos el teclado en la pantalla (Esto ocurre cuando se llama textfield `ResignFirstResponder`).

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>Establecimiento inflexible de tipos a través de una propiedad de delegado

Si prefiere no usar eventos, puede proporcionar su propio [UIWebViewDelegate](https://developer.xamarin.com/api/type/UIKit.UIWebViewDelegate/) subclase y asígnelo a la [UIWebView.Delegate](https://developer.xamarin.com/api/property/UIKit.UIWebView.Delegate/) propiedad. Una vez que se ha asignado UIWebView.Delegate, el mecanismo de envío de eventos UIWebView dejarán de funcionar y los métodos UIWebViewDelegate se invocará cuando se producen los eventos correspondientes.

Por ejemplo, este tipo simple registra el tiempo necesario para cargar una vista web:

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

Lo anterior se usa en código similar al siguiente:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Lo anterior creará un UIWebViewer e indicará que pueda enviar mensajes a una instancia de notificador, una clase que creamos para responder a los mensajes.

Este patrón también se usa para controlar el comportamiento de ciertos controles, por ejemplo, en el caso de UIWebView, el [UIWebView.ShouldStartLoad](https://developer.xamarin.com/api/property/UIKit.UIWebView.ShouldStartLoad/) propiedad permite el `UIWebView` instancia para controlar si el `UIWebView` cargará un página o no.

El patrón también se usa para proporcionar los datos a petición para algunos controles. Por ejemplo, el [UITableView](https://developer.xamarin.com/api/type/UIKit.UITableView/) es un control eficaz de representación de tabla: y la apariencia y el contenido controlado por una instancia de un [UITableViewDataSource](https://developer.xamarin.com/api/type/UIKit.UITableView/DataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Establecimiento flexible de tipos a través de la propiedad WeakDelegate

Además de la propiedad fuertemente tipada, también es débil con tipo delegado que permite al desarrollador enlazar las cosas de manera diferente si lo desea.
En todas partes fuertemente tipado `Delegate` propiedad se expone en el enlace de Xamarin.iOS, correspondiente `WeakDelegate` también se expone la propiedad.

Cuando se usa el `WeakDelegate`, usted es responsable de decoración correctamente de la clase mediante el [exportar](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) atributo para especificar el selector. Por ejemplo:

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

Tenga en cuenta que una vez el `WeakDelegate` se ha asignado la propiedad, el `Delegate` no se usará la propiedad. Además, si implementa el método en una clase base heredada que deseen [Exportar], debe convertirlo un método público.


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>Asignación del modelo de delegado de Objective-C a C&#35;

Cuando vea los ejemplos de Objective-C que tienen un aspecto similar al siguiente:

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

Esto indica que el lenguaje para crear y construir una instancia de la clase "SomethingDelegate" y asigne el valor a la propiedad de delegado en la variable de foo. Este mecanismo es compatible con Xamarin.iOS y la sintaxis de C# es:

```csharp
foo.Delegate = new SomethingDelegate ();
```

En Xamarin.iOS entregamos clases fuertemente tipadas que se asignan a Objective-C de clases de delegado. Para poder utilizarlos, va a crear subclases y reemplazar los métodos definidos por la implementación de Xamarin.iOS. Para obtener más información sobre cómo funcionan, vea la sección "modelos" a continuación.


##### <a name="mapping-delegates-to-c35"></a>Asignación de delegados en C&#35;

En general, UIKit utiliza delegados de Objective-C de dos formas.

El primer formulario proporciona una interfaz para el modelo de un componente. Por ejemplo, como un mecanismo para proporcionar datos a petición para obtener una vista, por ejemplo, el servicio de almacenamiento de datos para una vista de lista.  En estos casos, siempre debe crear una instancia de la clase adecuada y asigne la variable.

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

La segunda forma es proporcionar una notificación de eventos. En esos casos, aunque aún exponemos la API en el formulario que se ha descrito anteriormente, también proporcionamos eventos de C#, que deberían ser más fácil de usar para las operaciones de rápido e integrado con delegados anónimos y expresiones lambda en C#.

Por ejemplo, puede suscribirse a `UIAccelerometer` eventos:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Las dos opciones están disponibles que tengan sentido, pero como programador debe elegir uno u otro. Si crea su propia instancia de un servicio de respuesta/delegado fuertemente tipado y asignarlo, los eventos de C# no será funcionales. Si utiliza los eventos de C#, nunca se llamará a los métodos de la clase de servicio de respuesta o delegado.

El ejemplo anterior que utiliza `UIWebView` puede escribirse mediante expresiones lambda de C# 3.0 similar al siguiente:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>Respuesta a eventos

En el código de Objective-C, en ocasiones, los controladores de eventos para varios controles y los proveedores de información para varios controles, se hospedará en la misma clase. Esto es posible porque las clases de responden a mensajes, y siempre y cuando las clases de responden a mensajes, es posible vincular objetos entre sí.

Tal como se indicó anteriormente, Xamarin.iOS admite tanto el C# basado en eventos modelo de programación y el modelo de delegado de Objective-C, donde puede crear una nueva clase que implementa al delegado e invalida los métodos deseados.

También es posible admitir el patrón de Objective-C donde los servicios de respuesta para varias operaciones distintas se hospedan en la misma instancia de una clase. Para ello no obstante, deberá usar las características de bajo nivel del enlace de Xamarin.iOS.

Por ejemplo, si deseara que su clase para responder a ambos el `UITextFieldDelegate.textFieldShouldClear`: mensaje y el `UIWebViewDelegate.webViewDidStartLoad`: en la misma instancia de una clase, se tendría que usar la declaración de atributos [Exportar]:

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

El C# los nombres de los métodos no son importantes; lo único que importa son las cadenas que se pasa al atributo [Exportar].

Cuando se usa este estilo de programación, asegúrese de que los parámetros de C# coinciden con los tipos reales que va a pasar el motor en tiempo de ejecución.

<a name="Models" />

#### <a name="models"></a>Modelos

En instalaciones de almacenamiento de UIKit, o en los servicios de respuesta que se implementan mediante clases auxiliares, estos se conocen normalmente en el código de Objective-C como delegados, y se implementan como protocolos.

Protocolos de Objective-C son como las interfaces, pero admiten métodos opcionales; es decir, no todos los métodos deben implementarse para que funcione el protocolo.

Hay dos maneras de implementar un modelo. Puede implementarlo manualmente o usar las definiciones existentes fuertemente tipadas.


El mecanismo manual es necesario cuando se intenta implementar una clase que no se ha enlazado con Xamarin.iOS. Es muy fácil hacer:

-  Marca la clase para el registro con el tiempo de ejecución
-  Aplicar el atributo [Exportar] con el nombre real del selector en cada método que desee invalidar
-  Crear una instancia de la clase y pasarla.

Por ejemplo, el siguiente implementa solo uno de los métodos opcionales en la definición de protocolo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

El nombre de selector de Objective-C ("applicationDidFinishLaunching:") se declara con el atributo de exportación y la clase está registrada con el `[Register]` atributo.

Xamarin.iOS proporciona fuertemente tipadas declaraciones, listos para usar, que no requieren enlace manual. Para admitir este modelo de programación, el tiempo de ejecución de Xamarin.iOS es compatible con el atributo [modelo] en una declaración de clase. Esto informa el tiempo de ejecución que no debería conectar copia todos los métodos de la clase, a menos que los métodos son se implementa explícitamente.

Esto significa que en UIKit, las clases que representan un protocolo con métodos opcionales se escriben de forma similar al siguiente:

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

Cuando desea implementar un modelo que implementa sólo algunos de los métodos, lo único que debe hacer es reemplazar los métodos que esté interesado y pasar por alto los demás métodos. El tiempo de ejecución solo se enlazará los métodos sobrescritos, no los métodos originales al mundo de C de objetivo.

Es el equivalente al ejemplo anterior manual:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

Las ventajas son que no hay ninguna necesidad de profundizar en los archivos de encabezado Objective-C para buscar el selector, los tipos de los argumentos o la asignación a C#, y que obtiene intellisense de Visual Studio para Mac, junto con tipos seguros


#### <a name="xib-outlets-and-c35"></a>C y las salidas XIB&#35;

> [!IMPORTANT]
> Esta sección explica la integración de IDE salidas con cuando se usan los archivos XIB. Cuando se usa el Diseñador de Xamarin para iOS, esto es todo reemplazar escribiendo un nombre en **identidad > nombre** en la sección de propiedades de su IDE, como se muestra a continuación:
>
> [![](images/designeroutlet.png "Especifica un nombre de artículo en el Diseñador de iOS")](images/designeroutlet.png#lightbox)
>
>Para obtener más información sobre el Diseñador de iOS, consulte el [Introducción a iOS Designer](~/ios/user-interface/designer/introduction.md#how-it-works) documento.

Esto es una descripción de bajo nivel de cómo integran salidas con C# y se proporciona para los usuarios avanzados de Xamarin.iOS. Cuando el uso de Visual Studio para Mac la asignación realiza automáticamente en segundo plano mediante genera código en el vuelo.

Al diseñar la interfaz de usuario con Interface Builder, que solo se puede diseñar el aspecto de la aplicación y establecerá algunas conexiones de forma predeterminada. Si desea capturar información mediante programación, alterar el comportamiento de un control en tiempo de ejecución o modificar el control en tiempo de ejecución, es necesario enlazar algunos de los controles en el código administrado.

Esto se realiza en unos pocos pasos:

1.  Agregar el **declaración toma** a su **propietario del archivo**.
1.  Conectar el control a la **propietario del archivo**.
1.  Store las conexiones en el archivo XIB/NIB además de la interfaz de usuario.
1.  Cargue el archivo NIB en tiempo de ejecución.
1.  Obtener acceso a la variable de salida.


Los pasos (1) (3) se tratan en la documentación de Apple para la creación de interfaces con Interface Builder.

Cuando se usa Xamarin.iOS, la aplicación deberá crear una clase que derive de UIViewController. Se implementa lo siguiente:

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

Esto carga la interfaz de usuario desde el NIB. Ahora, para tener acceso a las salidas, es necesario informar al runtime que se va a tener acceso a ellos. Para ello, el `UIViewController` subclase debe declarar las propiedades y anotar en ellos con el atributo [conexión]. Así:

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

La implementación de la propiedad es lo que realmente se recupera y almacena el valor para el tipo nativo real.

No es necesario preocuparse por esto cuando se usa Visual Studio para Mac y InterfaceBuilder. Visual Studio para Mac refleja automáticamente todas las salidas declaradas con el código en una clase parcial que se compila como parte del proyecto.

#### <a name="selectors"></a>Selectores

Un concepto básico de programación Objective-C es selectores. A menudo procederán a través de las API que requieren pasar un selector o espera que su código para responder a un selector.

Crear nuevos selectores en C# es muy fácil: simplemente crear una nueva instancia de la `ObjCRuntime.Selector` clase y usar el resultado en cualquier lugar en la API que lo requiera. Por ejemplo:

```csharp
var selector_add = new Selector ("add:plus:");
```

Para un C# responde a una llamada de selector de método, debe heredar de la `NSObject` tipo y el método de C# deben decorarse con el nombre de selector mediante el `[Export]` atributo. Por ejemplo:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Tenga en cuenta ese selector nombres **debe** coinciden exactamente, incluidos todos los puntos intermedios y finales (":"), si está presente.

#### <a name="nsobject-constructors"></a>Constructores de NSObject

La mayoría de Xamarin.iOS que se derivan de clases `NSObject` expondrá constructores específicos a la funcionalidad del objeto, pero también expondrá varios constructores que no son inmediatamente obvios.

Los constructores se usan como sigue:

```csharp
public Foo (IntPtr handle)
```

Este constructor se utiliza para crear una instancia de la clase cuando el tiempo de ejecución debe asignar la clase a una clase no administrada. Esto sucede cuando se carga un archivo XIB/NIB.  En este momento, el tiempo de ejecución Objective-C, habrá creado un objeto en el mundo no administrado y se llamará a este constructor para inicializar la parte administrada.

Normalmente, todo lo que necesita hacer es llamar al constructor base con el parámetro de identificador y en el cuerpo, realizar cualquier inicialización que sea necesario.

```csharp
public Foo ()
```

Éste es el constructor predeterminado para una clase, y en Xamarin.iOS proporcionada las clases, esto inicializa la clase Foundation.NSObject y todas las clases, entre ellos y, al final, encadena esto y el Objective-C `init` método en la clase.

```csharp
public Foo (NSObjectFlag x)
```

Este constructor se utiliza para inicializar la instancia, pero impide que el código de llamar al método Objective-C "init" al final. Se usa generalmente cuando ya haya registrado para la inicialización (cuando se usa `[Export]` en su constructor) o cuando aún ha hecho la inicialización a través de otro medio.

```csharp
public Foo (NSCoder coder)
```

Este constructor se proporciona para los casos donde se está inicializando el objeto de una instancia de NSCoding. Para obtener más información, consulte Apple [archivos y la Guía de programación de serialización.](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>Excepciones

El diseño de API de Xamarin.iOS no genera excepciones de Objective-C como excepciones de C#. El diseño exige que no hay elementos no utilizados se envía a todo el mundo de Objective-C en primer lugar y que cualquier excepción que se debe producir generados por el propio enlace antes de que los datos no válidos nunca se pasan al mundo de Objective-C.

#### <a name="notifications"></a>Notificaciones

En iOS y OS X, los desarrolladores pueden suscribirse a notificaciones que se difunden por la plataforma subyacente. Esto se realiza mediante el `NSNotificationCenter.DefaultCenter.AddObserver` método. El `AddObserver` método toma dos parámetros; una es la notificación que desea suscribirse a; el otro es el método que se invocará cuando se desencadena la notificación.

En Xamarin.iOS y Xamarin.Mac, las claves para las distintas notificaciones que se hospedan en la clase que desencadena las notificaciones. Por ejemplo, las notificaciones generan por el `UIMenuController` se hospedan como `static NSString` propiedades en el `UIMenuController` las clases que terminan con el nombre "Notificación".

### <a name="memory-management"></a>Administración de memoria

Xamarin.iOS tiene un recolector de elementos no utilizados que se encargará de liberar recursos automáticamente cuando ya no están en uso. El recolector de elementos no utilizados, además de todos los objetos que se derivan de `NSObject` implementar el `System.IDisposable` interfaz.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Exponer el `IDisposable` interfaz es una manera cómoda de ayudar a los desarrolladores de liberación de objetos que pueden encapsular bloques grandes de memoria (por ejemplo, un `UIImage` podría ser similar a un puntero de inofensivo, pero podría apuntar a una imagen 2 megabytes ) y otros recursos importantes y finitos (por ejemplo, un búfer de descodificación de vídeo).

NSObject implementa la interfaz IDisposable y también el [patrón Dispose .NET](http://msdn.microsoft.com/library/fs2xkftw.aspx). Esto permite a los desarrolladores esa subclase NSObject para invalidar el comportamiento de Dispose y liberar sus recursos a petición. Por ejemplo, considere la posibilidad de este controlador de vista que se mantiene en torno a un conjunto de imágenes:

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

Cuando se elimina un objeto administrado, ya no es útil. Puede seguir teniendo una referencia a los objetos, pero el objeto por todos los propósitos es válido en este momento. Algunas API de .NET garantizarlo iniciando una excepción ObjectDisposedException si se intenta tener acceso a los métodos en un objeto desechado, por ejemplo:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Aunque todavía puede obtener acceso a la variable "imagen", es realmente una referencia no válida y ya no señala al objeto de Objective-C que mantienen la imagen.

Pero desechar un objeto en C# no significa que el objeto se destruirá necesariamente. Todo lo que hacer es liberar la referencia de C# tenía al objeto. Es posible que el entorno de cacao podría han mantenido una referencia en torno a para su propio uso. Por ejemplo, si establece la propiedad Image de un UIImageView en una imagen y, a continuación, eliminar la imagen, el UIImageView subyacente tuvo su propia referencia y mantengan una referencia a este objeto hasta que haya terminado con él.

#### <a name="when-to-call-dispose"></a>Cuándo se debe llamar al método Dispose

Debe llamar a Dispose cuando necesite Mono en deshacerse de su objeto. Un caso de uso posible es cuando Mono no tiene ningún conocimiento de que su NSObject realmente mantiene una referencia a un recurso importante como la memoria o un grupo de información. En esos casos, debe llamar a Dispose para liberar inmediatamente la referencia a la memoria, en lugar de esperar Mono realizar un ciclo de recolección.

Internamente, cuando se crea Mono [NSString hace referencia a partir de cadenas de C#](~/ios/internals/api-design/nsstring.md), eliminará inmediatamente para reducir la cantidad de trabajo que el recolector de elementos no utilizados tiene que ver. Cuantos menos objetos a fin de tratar con, más rápido el GC se ejecutarán.

#### <a name="when-to-keep-references-to-objects"></a>Cuándo se debe mantener las referencias a objetos

Un efecto que tiene la administración de memoria automática es que el GC permitirán deshacerme de objetos no utilizados siempre y cuando no hay ninguna referencia a ellos. Esto a veces puede tener sorprendentes efectos secundarios, por ejemplo, si crea una variable local que contenga el controlador de vista de nivel superior o la ventana de nivel superior y, a continuación, provocando que esos desaparecerán detrás de la copia de seguridad.

Si no mantiene una referencia en su estático o variables de instancia para los objetos, Mono, llamará al método Dispose() en ellos y liberará la referencia al objeto. Puesto que esto podría ser la referencia solo pendiente, el tiempo de ejecución Objective-C destruirá el objeto para usted.

## <a name="related-links"></a>Vínculos relacionados

- [Campos de enlace](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
