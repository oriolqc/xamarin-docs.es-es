---
title: "Guía de referencia de tipos de enlace"
ms.topic: article
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 06/26/2017
ms.openlocfilehash: c79b2277073996a580b455bde519ea959795d7ef
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="binding-types-reference-guide"></a>Guía de referencia de tipos de enlace

[//]: # (El archivo original reside en https://github.com/xamarin/xamarin-macios/tree/master/docs/website/)
[//]: # (Esto permite todos los participantes (incluido externo) para enviar, usar PR, actualiza la documentación que coincidan con los cambios de herramientas) [ // ]: # (modificaciones fuera de xamarin-macios/maestro se perderán en el futuro actualizaciones))

Este documento describe la lista de atributos que puede usar para agregar anotaciones a los archivos de contrato de API para controlar el enlace y el código generado

Contratos de Xamarin.iOS y Xamarin.Mac API se escriben en C# principalmente como definiciones de interfaz que definen la forma en que el código Objective C está expuesto a C#. El proceso implica una combinación de las declaraciones de interfaz más algunas definiciones de tipo básico que puede requerir el contrato de API. Para obtener una introducción a los tipos de enlace, vea nuestra guía complementaria de [enlace Objective-C bibliotecas](~/cross-platform/macios/binding/objective-c-libraries.md).


# <a name="type-definitions"></a>Definiciones de tipos

Sintaxis:

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Todas las interfaces en la definición de contrato que tiene el `[BaseType]` atributo que declara el tipo base para el objeto generado. En la declaración anterior un `MyType` se generará la clase tipo de C# que llama se enlaza a un tipo de objetivo-C **MyType**.

Si se especifica ningún tipo after typename (en el ejemplo anterior `Protocol1` y `Protocol2`) utilizando la sintaxis de la herencia de interfaz el contenido de estas interfaces se insertarán entre línea como si se hubieran parte del contrato para `MyType`.
La manera en que es que un tipo adopta un protocolo de superficies de Xamarin.iOS alineando todos los métodos y propiedades que se declaran en el protocolo en el propio tipo.

La siguiente muestra cómo la declaración Objective-C para `UITextField` se definiría en un contrato de Xamarin.iOS:

```csharp
@interface UITextField : UIControl <UITextInput> {

}
```

Se escribirá así como un contrato de API de C#:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

Puede controlar muchos otros aspectos de la generación de código por aplicar otros atributos a la interfaz, así como configurar el atributo BaseType.

 <a name="Generating_Events" />


## <a name="generating-events"></a>Generar eventos

Una característica del diseño Xamarin.iOS y API de Xamarin.Mac es se asignan las clases de delegado Objective-C como las devoluciones de llamada y eventos de C#. Los usuarios pueden elegir en por instancia si desean adoptar el modelo de programación de C objetivo, asignando a las propiedades como **delegado** una instancia de una clase que implementa los diversos métodos que Objective-c. llamaría tiempo de ejecución, también puede hacerlo eligiendo el C#-eventos y propiedades de estilo.

Permítanos vea un ejemplo de cómo utilizar el modelo Objective-C:

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

En el ejemplo anterior, puede ver que hemos decidido sobrescribir los dos métodos, uno en una notificación de que un evento de desplazamiento ha tenido lugar y el segundo, que es una devolución de llamada que debe devolver un valor booleano que indica si se debe desplazar a la parte superior a la scrollView o no.

El modelo de C# permite al usuario de la biblioteca escuchar notificaciones utilizando la sintaxis del evento de C# o la sintaxis de la propiedad para enlazar las devoluciones de llamada que se esperan que devuelven valores.

Este es el código de C# para la misma función de aspecto similar al uso de expresiones lambda:

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

Puesto que los eventos no devuelven valores (tienen un tipo de valor devuelto void) puede conectarse varias copias. El `ShouldScrollToTop` no es un evento, en lugar de ello es una propiedad con el tipo de `UIScrollViewCondition` que tiene esta firma:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Devuelve un valor de tipo bool, en este caso la sintaxis de expresión lambda nos permite devolver el valor de la `MakeDecision` (función).

El generador de enlace admite la generación de eventos y propiedades que estén vinculadas a una clase como `UIScrollView` con su `UIScrollViewDelegate` (también llamada a estos la clase del modelo), se debe anotar la `BaseType` definición con el `Events` y `Delegates`parámetros (descritos a continuación). Además de anotar la `BaseType` con esos parámetros es necesario informar el generador de unos más componentes.

Para eventos que tardan más de un parámetro (en Objective C la convención es que el primer parámetro en una clase de delegado es la instancia del objeto de remitente) debe proporcionar el nombre que desea para que la clase EventArgs generada es. Esto se realiza con el `EventArgs` atributo en la declaración del método en la clase de modelo. Por ejemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La declaración anterior generará un `UIImagePickerImagePickedEventArgs` clase que deriva de `EventArgs` y dos parámetros, los módulos la `UIImage` y `NSDictionary`. El generador produce esta advertencia:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

A continuación, se expone lo siguiente en la clase UIImagePickerController:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Métodos para los modelos que devuelven un valor se enlazan de forma diferente. Los requieren tanto un nombre para el delegado generado de C# (la firma del método) y también un valor predeterminado para devolver en caso de que el usuario no proporciona una implementación de sí mismo. Por ejemplo, el `ShouldScrollToTop` definición es esto:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

Los pasos anteriores, se creará un `UIScrollViewCondition` con la firma de delegado que se mostró anteriormente, y si el usuario no proporciona una implementación, el valor devuelto será true.

Además el `DefaultValue` atributo, también puede usar el `DefaultValueFromArgument` que dirige el generador para devolver el valor del parámetro especificado en la llamada o el `NoDefaultValue` parámetro que indica que el generador de que no hay ningún valor predeterminado.

 <a name="BaseTypeAttribute" />


## <a name="basetypeattribute"></a>BaseTypeAttribute

Sintaxis:

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

### <a name="basetypename"></a>BaseType.Name

Usa el `Name` propiedad para controlar el nombre que se enlazará este tipo en el mundo Objective-C. Normalmente, esto se utiliza para asignar al tipo de C# en un nombre que sea compatible con las instrucciones de diseño de .NET Framework, pero que se asigna a un nombre en Objective-C que no sigue esta convención.

Ejemplo, en el caso siguiente se asigna el objetivo-C `NSURLConnection` escriba a `NSUrlConnection`, que las instrucciones de diseño de .NET Framework usen "Url" en lugar de "URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

Se especifica el nombre especificado se utiliza como el valor para las `[Register]` atributo en el enlace. Si `Name` no se especifica, se utiliza el nombre corto del tipo como el valor de la `Register` atributo en el resultado generado.


### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events y BaseType.Delegates

Estas propiedades se utilizan para controlar la generación de C#: aplicar estilo a eventos en las clases generadas. Se utilizan para vincular una clase dada con su clase de delegado de C de objetivo. Encontrará muchos casos donde una clase utiliza una clase de delegado para enviar notificaciones y eventos. Por ejemplo un `BarcodeScanner` tendría una complementaria `BardodeScannerDelegate` clase. El `BarcodeScanner` clase normalmente tendría una propiedad "delegate" que se podría asignar una instancia de `BarcodeScannerDelegate` para, mientras que esto funciona, puede exponer a los usuarios de C#-como interfaz de eventos de estilo y en los casos en que utilizaría la `Events` y `Delegates` propiedades de la `BaseType` atributo.

Estas propiedades siempre se establecen entre sí y deben tener el mismo número de elementos y se mantiene en sincronización. El `Delegates` matriz contiene una cadena para cada delegado débilmente tipada que se va a incluir y la matriz de eventos contiene un tipo para cada tipo que desea asociar con ella.

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```

 <a name="BaseType.KeepRefUntil" />


### <a name="basetypekeeprefuntil"></a>BaseType.KeepRefUntil

Si aplica este atributo cuando se crean nuevas instancias de esta clase, la instancia de ese objeto se mantendrán hasta que el método al que hace referencia el `KeepRefUntil` se ha invocado. Esto es útil para mejorar la facilidad de uso de las API, cuando no desee que el usuario para mantener una referencia a un objeto alrededor de reutilizar el código. El valor de esta propiedad es el nombre de un método en el `Delegate` de la clase, por lo que debe usar en combinación con los eventos y `Delegates` también las propiedades.

En el ejemplo siguiente se muestra cómo se utiliza por `UIActionSheet` en Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```

 <a name="DisableDefaultCtorAttribute" />


## <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

Cuando este atributo se aplica a la definición de interfaz impedirá que el generador de generar el constructor predeterminado.

Utilice este atributo cuando necesite que el objeto que se iniciará con uno de los otros constructores de la clase.

 <a name="PrivateDefaultCtorAttribute" />


## <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Cuando este atributo se aplica a la definición de interfaz marcará el constructor predeterminado como privado. Esto significa que puede seguir crear instancias de objeto de esta clase internamente desde el archivo de extensión, pero simplemente no ser accesibles para los usuarios de la clase.

<a name="CategoryAttribute" />

## <a name="categoryattribute"></a>CategoryAttribute

Utilice este atributo en una definición de tipo que se va a enlazar categorías Objective-C como exponer estos archivos como métodos de extensión de C# para reflejar la manera que Objective-C expone la funcionalidad.

Categorías son un mecanismo de Objective-C utilizado para ampliar el conjunto de métodos y propiedades disponibles en una clase.   En la práctica, se utilizan para extender la funcionalidad de una clase base (por ejemplo `NSObject`) cuando se vincula un marco concreto en (por ejemplo `UIKit`), que realiza sus métodos disponibles, pero solo si el nuevo marco de trabajo está vinculado en.   En otros casos, se utilizan para organizar las características en una clase según su funcionalidad.   Únicamente son en esencia similares a los métodos de extensión de C#.

Se trata de una categoría de aspecto en C: objetivo

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

El ejemplo anterior si se encuentra en una biblioteca extendería instancias de `UIView` con el método `makeBackgroundRed`.

Para enlazarlas, puede usar el `[Category]` atributo en una definición de interfaz.   Cuando se usa el `Category` de atributo, el significado de la `[BaseType]` atributo cambia desde que se usa para especificar la clase base para extender, para que sea el tipo de extender.

La siguiente muestra cómo el `UIView` extensiones están enlazadas y se convierten en métodos de extensión de C#:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Los pasos anteriores, se creará un `MyUIViewExtension` una clase que contiene el `MakeBackgroundRed` método de extensión.   Esto significa que ahora puede llamar a "MakeBackgroundRed" en cualquier `UIView` subclase, lo que le ofrece la misma funcionalidad que obtendría en objetivo-C.

En algunos casos encontrará **estático** miembros dentro de categorías, como en el ejemplo siguiente:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Esto daría lugar a un **incorrecta** definición de interfaz de C# de categoría:

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Esto es incorrecto porque usar la `BoolMethod` extensión se necesita una instancia de `FooObject` pero va a enlazar una ObjC **estático** extensión, se trata de un efecto secundario debido al hecho de cómo se implementan los métodos de extensión de C#.

La única forma de utilizar las definiciones anteriores es que el siguiente código desagradable:

```csharp
(null as FooObject).BoolMethod (range);
```

La recomendación para evitar esta situación es en línea el `BoolMethod` definición dentro de la `FooObject` definición de interfaz propia, esto le permitirá llamar a esta extensión como se pretende `FooObject.BoolMethod (range)`.

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Se emitirá una advertencia (BI1117) cada vez que encontramos una `[Static]` miembro dentro de un `[Category]` definición. Si realmente desea tener `[Static]` miembros dentro de su `[Category]` definiciones puede silenciar la advertencia utilizando `[Category (allowStaticMembers: true)]` o decorando ya sea el miembro o `[Category]` definición con la interfaz `[Internal]`.

<a name="StaticAttribute" />

## <a name="staticattribute"></a>StaticAttribute

Cuando este atributo se aplica a una clase solo generará una clase estática, uno que no se deriva de `NSObject` por lo que el `[BaseType]` se omite el atributo. Las clases estáticas se utilizan para hospedar C variables públicas que desea exponer.

Por ejemplo:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Se generará una clase de C# con la siguiente API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

<a name="Model_Definitions" />

# <a name="model-definitions"></a>Definiciones de modelo

###<a name="protocol-definitionsmodel"></a>Las definiciones de protocolo o el modelo

Los modelos se utilizan normalmente por la implementación del protocolo.
Se diferencian en que el tiempo de ejecución solo registrará con Objective-c. los métodos que realmente se han sobrescrito.
En caso contrario, el método no se registrará.

Por lo general esto significa que cuando subclase una clase que se ha marcado con el `ModelAttribute`, no debería llamar al método base.   Al llamar a este método producirá una excepción, que debe para implementar el comportamiento todo en la subclase para los métodos que reemplace.

<a name="AbstractAttribute" />

## <a name="abstractattribute"></a>AbstractAttribute

De forma predeterminada, los miembros que forman parte de un protocolo no son obligatorios. Esto permite a los usuarios crear una subclase de la `Model` objeto simplemente derivando de la clase en C# y reemplazando únicamente los métodos que les interesen. A veces el contrato Objective-C requiere que el usuario proporciona una implementación de este método (las que se marcan con la @required directiva en Objective-C). En esos casos, se deben marcar métodos que tengan la `Abstract` atributo.

El `Abstract` atributo puede aplicarse a métodos o propiedades y provoca que el generador marcar los miembros que se genera como "abstract" y la clase para que sea una clase abstracta.

A continuación se toma del Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

 <a name="DefaultValueAttribute" />


## <a name="defaultvalueattribute"></a>DefaultValueAttribute

Especifica el valor predeterminado que se devuelve mediante un método de modelo si el usuario no proporciona un método para este método en el objeto de modelo

Sintaxis:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Por ejemplo, en la siguiente clase de delegado imaginario de un `Camera` clase, proporcionamos un `ShouldUploadToServer` que se expone como una propiedad en la `Camera` clase. Si el usuario del `Camera` clase no establece explícitamente un valor a una expresión lambda que puede responder true o false, el valor predeterminado devuelto en este caso sería false, el valor que se especificó en el `DefaultValue` atributo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Si el usuario establece un controlador de la clase imaginaria, se ignorará este valor:

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```



Vea también: [NoDefaultValueAttribute](#NoDefaultValueAttribute), [DefaultValueFromArgumentAttribute](#DefaultValueFromArgumentAttribute).


## <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

Sintaxis:

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

Este atributo cuando se proporciona en un método que devuelve un valor en una clase de modelo indicará el generador para devolver el valor del parámetro especificado, si el usuario no proporcionó su propio método o una expresión lambda.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

En el caso anterior si el usuario de la `NSAnimation` clase eligió usar cualquiera de los eventos y propiedades de C# y no estableció `NSAnimation.ComputeAnimationCurve` a un método o una expresión lambda, el valor devuelto sería el valor pasado en el parámetro de progreso.



Vea también: [NoDefaultValueAttribute](#NoDefaultValueAttribute), [DefaultValueAttribute](#DefaultValueAttribute)

## <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

A veces, conviene no exponga un evento o delegar la propiedad de una clase de modelo en la clase de host para que agregue este atributo indicará el generador para evitar la generación de cualquier método decorado con él.

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```


## <a name="delegatenameattribute"></a>DelegateNameAttribute

Este atributo se utiliza en los métodos para los modelos que devuelven valores para establecer el nombre de la firma de delegado a usar.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

Con la definición anterior, el generador de generará la siguiente declaración pública:

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

## <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

Este atributo se utiliza para permitir que el generador cambiar el nombre de la propiedad que se genera en la clase de host. A veces resulta útil cuando el nombre del método de clase FooDelegate relacione con la clase de delegado, pero podría parecer extraño en la clase de host como una propiedad.

También esto es realmente útil (y es necesario) al tener dos o más métodos de sobrecarga que tenga sentido para mantengan con el nombre de la clase FooDelegate pero desea exponerlos en la clase de host con un nombre determinado mejor.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Con la definición anterior, el generador de generará la siguiente declaración pública en la clase de host:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```


## <a name="eventargsattribute"></a>EventArgsAttribute

Para eventos que tardan más de un parámetro (en Objective C la convención es que el primer parámetro en una clase de delegado es la instancia del objeto de remitente) debe proporcionar el nombre que desea para que la clase EventArgs generada es. Esto se realiza con el `EventArgs` atributo en la declaración del método en su `Model` clase.

Por ejemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La declaración anterior generará un `UIImagePickerImagePickedEventArgs` clase que deriva de EventArgs y módulos de dos parámetros, el `UIImage` y `NSDictionary`. El generador produce esta advertencia:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

A continuación, se expone lo siguiente en la clase UIImagePickerController:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

<a name="EventNameAttribute" />

## <a name="eventnameattribute"></a>EventNameAttribute

Este atributo se utiliza para permitir que el generador cambiar el nombre de un evento o propiedad que se genera en la clase. A veces resulta útil cuando el nombre de la `Model` método de clase se relacione con la clase del modelo, pero podría parecer extraño en la clase de origen como una propiedad o evento.

Por ejemplo, el `UIWebView` usa el siguiente bit desde el `UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

La muestra anterior `LoadingFinished` como el método en el `UIWebViewDelegate`, pero `LoadFinished` como el evento para establecer un enlace en un `UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

 <a name="ModelAttribute" />


## <a name="modelattribute"></a>ModelAttribute

Al aplicar el `Model` atributo a una definición de tipo en su contrato de API, el tiempo de ejecución generará código especial que solo mostrará las invocaciones a métodos de la clase si el usuario ha sobrescrito un método en la clase. Normalmente, este atributo se aplica a todas las API que contienen una clase de delegado Objective-C.

 <a name="NoDefaultValueAttribute" />


## <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute



Especifica que el método en el modelo no proporciona un valor devuelto predeterminado.

Esto funciona con el tiempo de ejecución de C objetivo respondiendo "false" a la solicitud de Objective-C en tiempo de ejecución para determinar si el selector especificado se implementa en esta clase.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

Vea también: [DefaultValueAttribute](#DefaultValueAttribute) y [DefaultValueAttribute](#DefaultValueAttribute).


# <a name="protocols"></a>Protocolos

El concepto de protocolo Objective-C no existe realmente en C#. Protocolos son similares a las interfaces de C#, pero se diferencian en que no todos los métodos y las propiedades declaradas en un protocolo deben ser implementadas por la clase que se adopte. En su lugar, algunos de los métodos y propiedades son opcionales.

Algunos protocolos se utilizan generalmente como clases de modelo, los deben estar enlazados con el atributo de modelo.

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}
```

Se ha incorporado a partir de MonoTouch 7.0 un funcionalidad de enlace de protocolo nuevo y mejorado.  Cualquier definición que contiene el `[Protocol]` atributo realmente generará tres clases auxiliares que mejoran considerablemente la forma en que se usan protocolos:

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

El **implementación de la clase** proporciona una clase abstracta completa que puede invalidar los métodos individuales de y obtener la seguridad de tipos completa. Pero debido a C# no admite herencia múltiple, hay escenarios donde podría requerir otra clase base, pero todavía desea implementar una interfaz.

Aquí es donde generado **definición de la interfaz** entra en juego.  Es una interfaz que tiene todos los métodos necesarios del protocolo.  Esto permite a los desarrolladores que desean implementar el protocolo para simplemente implementa la interfaz.  El tiempo de ejecución registrará automáticamente el tipo como adoptar el protocolo.

Tenga en cuenta que la interfaz solo muestra los métodos necesarios y exponer los métodos opcionales.   Esto significa que las clases que adopten el protocolo obtendrán completa comprobación de tipos para los métodos necesarios, pero tendrán que recurrir a establecimiento flexible de tipos (manualmente utilizando los atributos de exportación y coincidir con la firma) para los métodos de protocolo opcional.

Para que sea conveniente utilizar una API que usa los protocolos, la herramienta de enlace también generará una clase de método de extensiones que expone todos los métodos opcionales.   Esto significa que siempre que se consume una API, podrá tratar protocolos como si tuviera todos los métodos.

Si desea utilizar las definiciones de protocolo de la API, debe escribir esqueletos interfaces vacías en la definición de API.   Si desea usar el MiProtocolo en una API, deberá hacer esto:

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

Los pasos anteriores es necesaria porque en tiempo de enlace el `IMyProtocol` no existiría, que es por qué necesita proporcionar una interfaz vacía.

## <a name="adopting-protocol-generated-interfaces"></a>Adopción de protocolo genera Interfaces

Cada vez que implemente una de las interfaces que se generan para los protocolos, similar al siguiente:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Automáticamente se exporta la implementación para los métodos de interfaz con el nombre correcto, por lo que es equivalente a esta:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

No importa si se implementa la interfaz de forma implícita o explícita.

## <a name="protocol-inlining"></a>Protocolo de inclusión

Al enlazar tipos Objective-C existentes que se han declarado como la adopción de un protocolo, deseará alineado el protocolo directamente. Para ello, simplemente declare el protocolo como una interfaz sin ninguna `[BaseType]` de atributo y el protocolo en la lista de interfaces base para la interfaz de la lista.

Ejemplo:

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```

 <a name="Member_Definitions" />


# <a name="member-definitions"></a>Definiciones de miembros

Los atributos de esta sección se aplican a los miembros individuales de un tipo: propiedades y las declaraciones de método.

 <a name="AlignAttribute" />


## <a name="alignattribute"></a>AlignAttribute

Se utiliza para especificar el valor de alineación para tipos de valor devuelto de la propiedad. Algunas propiedades toman punteros a las direcciones que se deben alinear en algunos límites (en Xamarin.iOS esto ocurre por ejemplo, con algunas `GLKBaseEffect` alinean de propiedades que deben ser de 16 bytes). Puede utilizar esta propiedad para decorar el captador y usar el valor de alineación. Esto se suele utilizar con la `OpenTK.Vector4` y `OpenTK.Matrix4` tipos cuando se integra con las API de C de objetivo.

Ejemplo:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

 <a name="AppearanceAttribute" />


## <a name="appearanceattribute"></a>AppearanceAttribute

El `Appearance` atributo está limitado a iOS5 donde se introdujo el Gestor de apariencia.

El `Appearance` atributo se puede aplicar a cualquier método o propiedad que participan en la `UIAppearance` framework. Cuando este atributo se aplica a un método o propiedad en una clase, dirigirá el generador de enlace para crear una clase de apariencia fuertemente tipada que se usa para definir el estilo de todas las instancias de esta clase o las instancias que cumplan ciertos criterios.

Ejemplo:

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

Los pasos anteriores generaría el código siguiente en UIToolbar:

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```


## <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (Xamarin.iOS 5.4)

Use la `AutoReleaseAttribute` en métodos y propiedades para ajustar la invocación del método al método en un `NSAutoReleasePool`.

En Objective C hay algunos métodos que devuelven valores que se agregan con el valor predeterminado `NSAutoReleasePool`. De forma predeterminada, se pasó a su subproceso `NSAutoReleasePool`, pero dado que Xamarin.iOS también mantiene una referencia a los objetos mientras se encuentra el objeto administrado, no puede mantener una referencia adicional el `NSAutoReleasePool` que sólo obtener agotan hasta que el subproceso Devuelve el control al subproceso de la siguiente o regresar al bucle principal.

Este atributo se aplica por ejemplo en las propiedades elevadas (por ejemplo `UIImage.FromFile`) que devuelve los objetos que se han agregado en el valor predeterminado `NSAutoReleasePool`. Sin este atributo, las imágenes se conservarán siempre y cuando el subproceso no devolvió control para el bucle principal. UF su subproceso era cierta descargador de segundo plano que siempre está activo y no esperando trabajo, nunca se liberaba las imágenes.


## <a name="forcedtypeattribute"></a>ForcedTypeAttribute

El `ForcedTypeAttribute` se utiliza para exigir la creación de un tipo administrado, incluso si el objeto no administrado devuelto no coincide con el tipo que se describe en la definición de enlace.

Esto es útil cuando el tipo que se describe en un encabezado no coincide con el tipo de valor devuelto del método nativo, por ejemplo, realizar la siguiente definición de Objective-C de `NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

Indica claramente que devolverá una `NSURLSessionDownloadTask` instancia, pero todavía se **devuelve** una `NSURLSessionTask`, que es una superclase y, por tanto, no puede convertir a `NSURLSessionDownloadTask`. Puesto que estamos en un contexto de seguridad de tipos un `InvalidCastException` se realizará.

Para cumplir con la descripción de encabezado y evitar el `InvalidCastException`, el `ForcedTypeAttribute` se utiliza.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

El `ForcedTypeAttribute` también acepta un valor booleano denominado `Owns` decir `false` predeterminada `[ForcedType (owns: true)]`. El propietario parámetro se usa para seguir la [directiva de la propiedad](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) para **Foundation Core** objetos.

El `ForcedTypeAttribute` solo es válido en `parameters`, `properties` y `return value`.



## <a name="bindasattribute"></a>BindAsAttribute

El `BindAsAttribute` permite el enlace `NSNumber`, `NSValue` y `NSString`(enumeraciones) en tipos de C# más precisos. El atributo se puede usar para crear mejor y más precisos, API de .NET a través de la API nativa.

Puede decorar métodos (en el valor devuelto), parámetros y propiedades con `BindAs`. La única restricción es que el miembro **no debe** estar dentro de un `[Protocol]` o `[Model]` interfaz.

Por ejemplo:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Generaría:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente, se realizará la `bool?`  <->  `NSNumber` y `CGRect`  <->  `NSValue` conversiones.

Los tipos de encapsulación compatibles actualmente son:

* `NSValue`
* `NSNumber`
* `NSString`

### <a name="nsvalue"></a>NSValue

Se admiten los siguientes tipos de datos de C# para encapsular desde o en `NSValue`:

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint / PointF
* CGRect / RectangleF
* CGSize / SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

### <a name="nsnumber"></a>NSNumber

Se admiten los siguientes tipos de datos de C# para encapsular desde o en `NSNumber`:

* bool
* byte
* double
* float
* short
* int
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* nint
* nuint
* Enumeraciones

### <a name="nsstring"></a>NSString

`[BindAs]` funciona junto con [enumeraciones respaldado por una constante NSString](#enum-attributes) para que pueda crear mejor API. NET, por ejemplo:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Generaría:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Se va a controlar la `enum`  <->  `NSString` conversión solo si el tipo de la enumeración proporcionada a `[BindAs]` es [respaldado por una constante de NSString](#enum-attributes).

### <a name="arrays"></a>Matrices

`[BindAs]` También es compatible con matrices de cualquiera de los tipos admitidos, puede tener la siguiente definición de API como ejemplo:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Generaría:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

El `rects` parámetro se encapsulará en una `NSArray` que contiene un `NSValue` para cada `CGRect` y de vuelta se obtiene una matriz de `CAScroll?` que se ha creado mediante los valores de devuelto `NSArray` que contiene `NSStrings`.


## <a name="bindattribute"></a>BindAttribute

El `Bind` atributo tiene dos usos uno cuando se aplica a un método o declaración de propiedad y otra cuando se aplica a la individual captador o establecedor de una propiedad.

Cuando se utiliza para un método o propiedad, el efecto del atributo de enlace consiste en generar un método que invoca el selector especificado. Pero el método generado resultante no está decorado con el `[Export]` atributo, lo que significa que no puede participar en el reemplazo del método. Normalmente se utiliza en combinación con la `Target` atributo para implementar métodos de extensión de C de objetivo.

Por ejemplo:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Cuando se utiliza en un captador o un establecedor, el `Bind` atributo se utiliza para modificar los valores predeterminados que se deduce el generador de código al generar los nombres de selector de Objective-C de captador y establecedor de una propiedad. De forma predeterminada cuando se marca una propiedad con el nombre "fooBar", el generador de generaría una exportación "fooBar" para el captador y "setFooBar:" para el establecedor. En algunos casos, Objective-C no sigue esta convención, normalmente cambie el nombre de captador como "isFooBar".
Este atributo se utilizaría para informar el generador de este.

Por ejemplo:

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

 <a name="AsyncAttribute" />


## <a name="asyncattribute"></a>AsyncAttribute

Solo disponible en Xamarin.iOS 6.3 y versiones más recientes.

Este atributo se puede aplicar a métodos que toman un controlador de finalización como el último argumento.

Puede usar el `[Async]` atributo cuyo último argumento es una devolución de llamada de métodos.  Cuando esto se aplica a un método, el generador de enlace generará una versión de ese método con el sufijo `Async`.  Si la devolución de llamada no toma ningún parámetro, el valor devuelto será una `Task`, si la devolución de llamada toma un parámetro, el resultado será una tarea&lt;T&gt;.

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

El siguiente generará este método asincrónico:

```csharp
Task LoadFileAsync (string file);
```

Si la devolución de llamada toma varios parámetros, debe establecer el `ResultType` o `ResultTypeName` para especificar el nombre deseado del tipo generado que contendrá todas las propiedades.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

La siguiente condición generará este método asincrónico, donde `FileLoading` contiene propiedades para tener acceso a "archivos de" y "byteCount":

```csharp
Task<FileLoading> LoadFile (string file);
```

Si el último parámetro de la devolución de llamada es un `NSError`, a continuación, generado `Async` método comprobará si el valor no es null, y si es así, el método asincrónico generado establecerá la excepción de tarea.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

Los pasos anteriores, genera el siguiente método asincrónico:

```csharp
Task<string> UploadAsync (string file);
```

Y en caso de error, la tarea resultante tendrá la excepción que se establece en un `NSErrorException` que encapsula el resultante `NSError`.

### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType



Utilice esta propiedad para especificar el valor de la devolución `Task` objeto.   Este parámetro toma un tipo existente, por lo tanto debe definirse en una de las definiciones de api de núcleo.


### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Utilice esta propiedad para especificar el valor de la devolución `Task` objeto.   Este parámetro toma el nombre de su nombre de tipo deseado, el generador producirá una serie de propiedades, uno para cada parámetro que toma la devolución de llamada.


### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName



Utilice esta propiedad para personalizar el nombre de los métodos asincrónicos generado.   El valor predeterminado consiste en utilizar el nombre del método y agregue el texto "Async", puede utilizar esto para cambiar este comportamiento predeterminado.


## <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Este atributo se aplica a los parámetros de cadena o propiedades de cadena, indica que no use la cadena de copia de cero, el cálculo de referencias para este parámetro el generador de código y en su lugar, cree una nueva instancia de NSString de la cadena de C#.
Este atributo solo es necesario en las cadenas si indicar a utilizar el cálculo de referencias de cadena de copia de cero mediante el generador del `--zero-copy` opción de línea de comandos o establecer el atributo de nivel de ensamblado `ZeroCopyStringsAttribute`.

Esto es necesario en casos donde la propiedad se declara en Objective-C para ser una propiedad "conservar" o "asignar" en lugar de una propiedad de "copy". Estos normalmente se producen en las bibliotecas de terceros que han sido indebidamente "optimizadas" por los desarrolladores. En general, "conservar" o "asignar" `NSString` propiedades son incorrectas desde `NSMutableString` o clases derivadas de usuario de `NSString` puede modificar el contenido de las cadenas sin el conocimiento del código de biblioteca, ligeramente interrumpir la aplicación. Normalmente esto sucede debido a la optimización prematura.

A continuación muestra dos propiedades en C: objetivo

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

 <a name="DisposeAttribute" />


## <a name="disposeattribute"></a>DisposeAttribute

Al aplicar el `DisposeAttribute` en una clase que proporcione un fragmento de código que se agregará a la `Dispose()` método de implementación de la clase.

Puesto que la `Dispose` método genera automáticamente el `bmac-native` y `btouch-native` herramientas, debe usar el `Dispose` atributo inyectar parte del código en los botones generados `Dispose` implementación del método.

Por ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

 <a name="ExportAttribute" />


## <a name="exportattribute"></a>ExportAttribute

El `Export` atributo se usa para marcar un método o propiedad que se exponen al runtime Objective-C. Este atributo se comparte entre la herramienta de enlace y los tiempos de ejecución real, Xamarin.iOS y Xamarin.Mac. Para los métodos, el parámetro se pasa literalmente en el código generado, para las exportaciones de propiedades, un captador y establecedor se generan en función de la declaración base (vea la sección sobre la `BindAttribute` para obtener información acerca de cómo modificar el comportamiento de la herramienta de enlace).

Sintaxis:

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

El [selector](http://developer.apple.com/library/ios/#documentation/cocoa/conceptual/objectivec/Chapters/ocSelectors.html) y representa el nombre del objetivo C subyacente del método o propiedad que se va a enlazar.

 <a name="ExportAttribute.ArgumentSemantic" />


### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

 <a name="FieldAttribute" />


## <a name="fieldattribute"></a>FieldAttribute

Este atributo se utiliza para exponer una variable global de C como un campo que se carga a petición y se exponen al código de C#. Normalmente esto es necesario para obtener los valores de constantes que se definen en C o Objective-C y que puede ser cualquier tokens que se usan en algunas API o cuyos valores son opacos y deben usarse como-es código de usuario.

Sintaxis:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

El `symbolName` es el símbolo de C para vincular con. De forma predeterminada, esto se cargará desde una biblioteca cuyo nombre se deduce del espacio de nombres donde se define el tipo. Si no es la biblioteca donde se busca el símbolo, se debe pasar el `libraryName` parámetro. Si va a vincular una biblioteca estática, use "__Internal" como el `libraryName` parámetro.

Las propiedades generadas siempre son estáticas.

Propiedades marcadas con el atributo de campo pueden ser de tipo `NSString`, `NSArray`, `nint`, `double`, `nfloat` o `System.IntPtr`.

Ejemplo:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```




## <a name="internalattribute"></a>InternalAttribute

El `Internal` atributo se puede aplicar a métodos o propiedades y tiene el efecto de marcar el código generado con la "interno" C# palabra clave haciendo que el código solo puede tener acceso al código en el ensamblado generado. Esto normalmente se usa para ocultar algunas API que son demasiado bajo nivel u ofrecen una API pública poco óptima que se desea mejorar en o para las API que no son compatibles con el generador de y requieren algunos codificación manual.

Cuando se diseña el enlace, normalmente debería ocultar el método o propiedad con este atributo y proporcione un nombre diferente para el método o propiedad y, a continuación, en el archivo de compatibilidad complementaria de C#, debe agregar un contenedor fuertemente tipado que expone subyacente funcionalidad.

Por ejemplo:

```csharp
[Internal]
[Export ("setValue:forKey:");
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

A continuación, en el archivo auxiliar, podría tener algún código similar al siguiente:

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```


## <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

Este atributo marca el campo de respaldo para una propiedad que se pueden anotar con .NET `[ThreadStatic]` atributo. Esto es útil si el campo es una variable estática de subprocesos.

 <a name="MarshalNativeExceptions_(Xamarin.iOS_6.0.6)" />


## <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin.iOS 6.0.6)

Este atributo realizará un excepciones de (ObjectiveC) método nativo de soporte técnico.
En lugar de llamar `objc_msgSend` directamente, la invocación recorrerá una cama elástica personalizado que detecta las excepciones de ObjectiveC y calcula las referencias en las excepciones administradas.

Actualmente sólo unos `objc_msgSend` se admiten las firmas (encontrará le indicará si una firma no se admite al vincular nativo de una aplicación que utilice el enlace produce un error con un monotouch_ falta*_objc_msgSend* símbolos), pero pueden ser más Agregar a petición.

 <a name="NewAttribute" />


## <a name="newattribute"></a>NewAttribute

Este atributo se aplica a los métodos y propiedades para que el generador de genere la palabra clave "new" delante de la declaración.

Se usa para evitar las advertencias del compilador cuando se inserta el mismo método o el nombre de propiedad en una subclase que ya existía en una clase base.

 <a name="NotificationAttribute" />


## <a name="notificationattribute"></a>NotificationAttribute

Este atributo se puede aplicar a los campos que los producen generador una clase de notificaciones de aplicación auxiliar de fuertemente tipado.

Este atributo puede utilizarse sin argumentos para las notificaciones que llevar a cabo ninguna carga, o puede especificar un `System.Type` que hace referencia a otra interfaz en la definición de API, normalmente con el nombre termina con "EventArgs". El generador de convertirá la interfaz en una clase que cree subclases `EventArgs` e incluirá todas las propiedades en la lista. El `[Export]` atributo debe usarse en el `EventArgs` clase para mostrar el nombre de la clave utilizada para buscar el diccionario Objective-C para capturar el valor.

Por ejemplo:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

El código anterior generará una clase anidada `MyClass.Notifications` con los métodos siguientes:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Los usuarios de su código, a continuación, pueden suscribirse fácilmente a las notificaciones que se publican en el [NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/) mediante código similar al siguiente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

O para establecer un objeto específico a observar. Si se pasa `null` a `objectToObserve` este método se comportará como su otro del mismo nivel.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

El valor devuelto de `ObserveDidStart` puede utilizarse para fácilmente dejar de recibir notificaciones, similar al siguiente:

```csharp
token.Dispose ();
```


O puede llamar a [NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject//) y pasar el token. Si la notificación contiene parámetros, debe especificar una aplicación auxiliar `EventArgs` interfaz como esta:

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

Los pasos anteriores, se generarán una `MyScreenChangedEventArgs` clase con la `ScreenX` y `ScreenY` propiedades que capturará los datos de la [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) diccionario utilizando las teclas **ScreenXKey** y **ScreenYKey** respectivamente y aplicar las conversiones apropiadas. El `[ProbePresence]` atributo se usa para el generador para sondear si la clave está establecida el `UserInfo`, en lugar de intentar extraer el valor. Se utiliza para los casos donde la presencia de la clave es el valor (normalmente por valores booleanos).

Esto le permite escribir código similar al siguiente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

En algunos casos, no hay ninguna constante asociado con el valor pasado en el diccionario.  Apple a veces utiliza constantes de símbolos públicos y a veces utiliza las constantes de cadena.  De forma predeterminada el `[Export]` atributo en su proporcionado `EventArgs` clase utilizará el nombre especificado como un símbolo público buscará en tiempo de ejecución.  Si este no es el caso y en su lugar, debe buscar como una constante de cadena, a continuación, pasar la `ArgumentSemantic.Assign` valor para el atributo de exportación.

**Novedad en la versión 8.4 Xamarin.iOS**

A veces, las notificaciones iniciará la vida sin ningún argumento, por lo que el uso de `[Notification]` sin argumentos es aceptable.  Pero a veces, se introducirán los parámetros para la notificación.  Para admitir este escenario, el atributo se puede aplicar más de una vez.

Si está desarrollando un enlace y desea evitar interrumpir el código de usuario existente, se activa una notificación existente desde:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

En una versión que incluye el atributo de notificación dos veces, similar al siguiente:

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```


## <a name="nullallowedattribute"></a>NullAllowedAttribute

Cuando esto se aplica a una propiedad marca la propiedad como lo que permite el valor null que se asignará a ella. Esto solo es válida para los tipos de referencia.

Cuando esto se aplica a un parámetro en una firma de método indica que el parámetro especificado puede ser null y que no se debe realizar ninguna comprobación para pasar valores null.

Si el tipo de referencia no tienen este atributo, la herramienta de enlace generará una comprobación para el valor asignado antes de pasarlo a Objective-C y generará una comprobación de que se producirá una `ArgumentNullException` si el valor asignado es null.

Por ejemplo:

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

## <a name="overrideattribute"></a>OverrideAttribute

Utilice este atributo para indicar que el generador de enlace que el enlace para este método debe estar marcado con una palabra clave "override".

 <a name="PreSnippetAttribute" />


## <a name="presnippetattribute"></a>PreSnippetAttribute

Puede usar este atributo para insertar código para insertar después de que se haya validado los parámetros de entrada, pero antes de las llamadas de código en Objective-C

Ejemplo:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

 <a name="PrologueSnippetAttribute" />


## <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

Puede usar este atributo para insertar código insertará antes de cualquiera de los parámetros se validan en el método generado.

Ejemplo:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

 <a name="PostGetAttribute" />


## <a name="postgetattribute"></a>PostGetAttribute

Indica el generador de enlace para invocar la propiedad especificada de esta clase para capturar un valor de ella.

Esta propiedad se utiliza normalmente para actualizar la memoria caché que apunta a los objetos de referencia que tenga el gráfico de objetos al que hace referencia. Normalmente se muestra en el código que tiene operaciones, como agregar o quitar. Este método se usa para que cuando se agregan o quitan que puede actualizar la caché interna para asegurarse de que los elementos se están mantener las referencias administradas a los objetos que están realmente en uso. Esto es posible porque la herramienta de enlace, genera un campo de respaldo para todos los objetos de referencia en un enlace determinado.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

En este caso, el `Dependencies` propiedad se invocará después de agregar o quitar las dependencias de la `NSOperation` objeto, lo que garantiza que tenemos un gráfico que representa los datos reales cargado objetos, evitar pérdidas de memoria así como daños en la memoria.

 <a name="PostSnippetAttribute" />


## <a name="postsnippetattribute"></a>PostSnippetAttribute

Puede usar este atributo para insertar algún código fuente de C# insertará cuando el código ha invocado el método Objective-C subyacente

Ejemplo:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

 <a name="ProxyAttribute" />


## <a name="proxyattribute"></a>ProxyAttribute

Este atributo se aplica a valores devueltos para ellos marca como objetos proxy. Algunos objetos del proxy devuelto Objective-C API que no se diferencian de los enlaces de usuario. El efecto de este atributo es marcar el objeto como un `DirectBinding` objeto. Para un escenario en Xamarin.Mac, puede ver el [discusión sobre este error](https://bugzilla.novell.com/show_bug.cgi?id=670844).

 <a name="RetainListAttribute" />


## <a name="retainlistattribute"></a>RetainListAttribute

Indica el generador para mantener una referencia administrada para el parámetro o quitar una referencia interna para el parámetro. Esto se usa para mantener los objetos al que hace referencia.

Sintaxis:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Si el valor de "doAdd" es true, el parámetro se agrega a la `__mt_{0}_var List<NSObject>;`. Donde `{0}` se reemplaza con la determinada `listName`. Debe declarar este campo de respaldo en su clase parcial complementaria a la API.

Para obtener un ejemplo, vea [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) y [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

 <a name="ReleaseAttribute_(Xamarin.iOS_6.0)" />


## <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Xamarin.iOS 6.0)

Esto se puede aplicar para devolver tipos para indicar que el generador debe llamar a `Release` en el objeto antes de devolverlo. Esto solo es necesario cuando un método proporciona un objeto retenido (en lugar de un objeto autoreleased, que es el escenario más común)

Ejemplo:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Además, este atributo se propaga al código generado, para que el tiempo de ejecución de Xamarin.iOS sepa que debe conservar el objeto al volver a Objective-C de esa función.

 <a name="SealedAttribute" />


## <a name="sealedattribute"></a>SealedAttribute

Indica el generador para marcar el método generado como sellada. Si no se especifica este atributo, el valor predeterminado consiste en generar un método virtual (un método virtual, un método abstracto o una invalidación dependiendo de cómo se utilizan otros atributos).

 <a name="StaticAttribute" />


## <a name="staticattribute"></a>StaticAttribute

Cuando el `Static` atributo se aplica a un método o propiedad de esta forma genera un método estático o una propiedad. Si no se especifica este atributo, el generador crea un método de instancia o propiedad.

 <a name="TransientAttribute" />


## <a name="transientattribute"></a>TransientAttribute

Utilice este atributo para propiedades de los indicadores cuyos valores son transitorios, es decir, objetos que se crean de forma temporal por iOS pero no son de larga duración. Cuando este atributo se aplica a una propiedad, el generador no crea un campo de respaldo para esta propiedad, lo que significa que la clase administrada no mantiene una referencia al objeto.

 <a name="WrapAttribute" />


## <a name="wrapattribute"></a>WrapAttribute

En el diseño de los enlaces Xamarin.iOS/Xamarin.Mac, la `Wrap` atributo se utiliza para ajustar un objeto con tipos débiles con un objeto fuertemente tipado. Esto entra en juego principalmente con Objective-C "delegar" objetos que normalmente se declaran como perteneciente al tipo `id` o `NSObject`. La convención usada por Xamarin.iOS y Xamarin.Mac consiste en exponer los delegados u orígenes de datos como de tipo `NSObject` y un nombre con la convención "Weak" + el nombre está expuesto. Una propiedad "delegado id" del objetivo de C se expondría como un `NSObject WeakDelegate { get; set; }` propiedad en el archivo de contrato de API.

Pero el valor que se asigna a este delegado suele ser de un tipo seguro, por lo que el establecimiento inflexible de tipos de superficie y aplicar el `Wrap` atributo, esto significa que los usuarios pueden optar por usar tipos no seguros si necesitan cierto control fine o si necesitan que recurrir a bajo nivel tric KS, o se puede utilizar la propiedad fuertemente tipada para la mayor parte de su trabajo.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

Se trata cómo el usuario podría usar la versión débilmente tipada del delegado:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

Y esto es cómo el usuario podría usar la versión fuertemente tipada, tenga en cuenta que el usuario saca partido del sistema de tipos de C# y utiliza la palabra clave override para declarar su intención y que no tiene a manualmente decorar el método con `Export`, ya que hicimos ese w ORK en el enlace para el usuario:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}


var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

 <a name="Parameter_Attributes" />

Otro uso de la `Wrap` atributo es admitir la versión fuertemente tipada de métodos.   Por ejemplo:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Los miembros generados por `[Wrap]` no son `virtual` de forma predeterminada, si necesita un `virtual` miembro puede establecer en `true` opcional `isVirtual` parámetro.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

# <a name="parameter-attributes"></a>Atributos de parámetro

En esta sección se describe los atributos que se pueden aplicar a los parámetros de una definición de método, así como la `NullAttribute` que se aplica a una propiedad como un todo.


## <a name="blockcallback"></a>BlockCallback



Este atributo se aplica a los tipos de parámetro en declaraciones de delegados de C# para notificar al enlazador que el parámetro en cuestión se ajusta al bloque Objective-C convención de llamada y debe calcular referencias de esta manera.

Esto se utiliza normalmente para las devoluciones de llamada que se definen como este en C: objetivo

```csharp
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vea también: [CCallback](#CCallback).


## <a name="ccallback"></a>CCallback



Este atributo se aplica a los tipos de parámetro en declaraciones de delegados de C# para notificar al enlazador que el parámetro en cuestión se ajusta a la convención de llamada de puntero de función de ABI C y debe calcular referencias de esta manera.

Esto se utiliza normalmente para las devoluciones de llamada que se definen como este en C: objetivo

    typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);

Vea también: [BlockCallback](#BlockCallback).


## <a name="params"></a>Params



Puede usar el `[Params]` atributo en el último parámetro de matriz de una definición de método para que el generador de insertar un "params" en la definición.   Esto permite el enlace permite con facilidad para los parámetros opcionales.

Por ejemplo, la siguiente definición:

    [Export ("loadFiles:")]
    void LoadFiles ([Params]NSUrl [] files);

Permite que el código siguiente al escribir:

    foo.LoadFiles (new NSUrl (url));
    foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));

Esto tiene la ventaja adicional que no te obliga a los usuarios crear una matriz para pasar los elementos.

<a name="plainstring" />

## <a name="plainstring"></a>PlainString

Puede usar el `[PlainString]` atributo delante de los parámetros de cadena para indicar el generador de enlace para pasar la cadena como una cadena de C, en lugar de pasar el parámetro como un `NSString`.

Consumir la mayoría de las API de Objective-C `NSString` parámetros, pero una serie de API de exponer un `char *` API para pasar cadenas, en lugar de la `NSString` variación.
Use `[PlainString]` en esos casos.

Por ejemplo, las siguientes declaraciones de C objetivo:

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

Se debe enlazar similar al siguiente:

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

 <a name="RetainAttribute" />


## <a name="retainattribute"></a>RetainAttribute

Indica el generador para mantener una referencia al parámetro especificado. El generador de proporcionará el almacén de copia de seguridad para este campo o puede especificar un nombre (el `WrapName`) para almacenar el valor en. Esto es útil para mantener una referencia a un objeto administrado que se pasa como un parámetro para Objective-C y cuando se sabe que Objective-C solo mantendrá esta copia del objeto. Por ejemplo, una API como `SetDisplay (SomeObject)` usaría este atributo ya que es probable que el SetDisplay sólo puede mostrar un objeto a la vez. Si necesita realizar un seguimiento de más de un objeto (por ejemplo, para una API similar de la pila) usaría el `RetainList` atributo.

Sintaxis:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```

 <a name="RetainListAttribute" />


## <a name="retainlistattribute"></a>RetainListAttribute

Indica el generador para mantener una referencia administrada para el parámetro o quitar una referencia interna para el parámetro. Esto se usa para mantener los objetos al que hace referencia.

Sintaxis:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Si el valor de "doAdd" es true, el parámetro se agrega a la `__mt_{0}_var List<NSObject>`. Donde `{0}` se reemplaza con la determinada `listName`. Debe declarar este campo de respaldo en su clase parcial complementaria a la API.

Para obtener un ejemplo, vea [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) y [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

 <a name="TransientAttribute" />


## <a name="transientattribute"></a>TransientAttribute



Este atributo se aplica a los parámetros y sólo se utiliza al realizar la transición desde Objective-C para C#.  Durante esas transiciones los diversos NSObjects Objective-C encapsula los parámetros en una representación del objeto administrada.

El tiempo de ejecución se toman una referencia al objeto nativo y mantener la referencia hasta que la última referencia administrada para el objeto ya no existe y el catálogo global tiene una oportunidad para que se ejecute.

En algunos casos, es importante para C# en tiempo de ejecución no se debe mantener una referencia al objeto nativo.  A veces, esto sucede cuando el código nativo subyacente ha adjuntado un comportamiento especial para el ciclo de vida del parámetro.  Por ejemplo: el destructor para el parámetro se realizan alguna acción de limpieza, o eliminar algún recurso muy valioso.

Este atributo informa el tiempo de ejecución que desea que el objeto se elimine si es posible cuando se devuelven al objetivo-C desde su método sobrescrito.

La regla es simple: si el tiempo de ejecución se tenía que crear una nueva representación administrada del objeto nativo, a continuación, al final de la función, se eliminarán el recuento de conservar para el objeto nativo y se borrará la propiedad de identificador del objeto administrado.   Esto significa que si mantiene una referencia al objeto administrado, que hacen referencia será inútil (invocación de métodos en el iniciará una excepción).

Si no se creó el objeto pasado, o si ya había una representación administrada pendiente del objeto, la eliminación forzada no tiene lugar. <a name="Global_Attributes" />

# <a name="property-attributes"></a>Atributos de propiedad

## <a name="notimplementedattribute"></a>NotImplementedAttribute

Este atributo se usa para admitir un idioma Objective-C donde se introduce una propiedad con un captador de una clase base y una subclase mutable presenta un establecedor.

Dado que C# no es compatible con este modelo, la clase base debe tener el captador y el establecedor y una subclase puede usar el [OverrideAttribute](#OverrideAttribute).

Este atributo sólo se utiliza en los establecedores de propiedad y se usa para admitir la expresión mutable en el objetivo de C.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

# <a name="enum-attributes"></a>Enum (atributos)

Asignación `NSString` constantes para los valores de enumeración es un método sencillo para crear mejores API. NET. Es:

* permite completar código ser más útil, ya que muestra **sólo** los valores correctos para la API;
* Agrega seguridad de tipos, no puede usar otra `NSString` constante en un contexto incorrecto; y
* permite para ocultar algunas constantes, realizar la finalización de código Mostrar lista de API más corto sin perder la funcionalidad.

Ejemplo:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}
```

Desde la anterior definición de enlace va a crear el generador de la `enum` propio y también creará un `*Extensions` tipo estático que incluye métodos de conversión de dos maneras de entre los valores de enumeración y el `NSString` constantes. Esto significa que las constantes sigue estando disponible para los desarrolladores aunque no forman parte de la API.

Ejemplos:

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

## <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

Puede decorar **una** valor enum a este atributo. Esto se convertirá en la constante que se devuelve si no se conoce el valor de enumeración.

En el ejemplo anterior:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Si ningún valor de enumeración se decora un `NotSupportedException` se iniciará.

## <a name="errordomainattribute"></a>ErrorDomainAttribute

Códigos de error se enlazan como los valores de enumeración. Generalmente es un dominio de error para ellos y no siempre es fácil buscar cuál se aplica (o si aún existe uno).

Puede usar este atributo para asociar el dominio de error a la propia enumeración.

Ejemplo:

```csharp
    [Native]
    [ErrorDomain ("AVKitErrorDomain")]
    public enum AVKitError : nint {
        None = 0,
        Unknown = -1000,
        PictureInPictureStartFailed = -1001
    }
```

A continuación, puede llamar al método de extensión `GetDomain` para obtener el dominio constante de cualquier error.


## <a name="fieldattribute"></a>FieldAttribute

Esto es lo mismo `[Field]` atributo utilizado para las constantes de tipo. También se puede utilizar dentro de las enumeraciones para asignar un valor con una constante específica.

A `null` valor puede utilizarse para especificar qué valor de enumeración se debe devolver si un `null` `NSString` de forma constante.

En el ejemplo anterior:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Si no hay ningún `null` valor está presente un `ArgumentNullException` se iniciará.

# <a name="global-attributes"></a>Atributos globales

Atributos globales se aplican o bien mediante el `[assembly:]` modificador de atributo como el `LinkWithAttribute` o se pueden usar desde cualquier lugar, al igual que el `Lion` y `Since` atributos.

 <a name="LinkWithAttribute" />


## <a name="linkwithattribute"></a>LinkWithAttribute

Se trata de un atributo de nivel de ensamblado que permite a los desarrolladores especificar las marcas de vinculación necesarios para volver a usar una biblioteca dependiente sin forzar el consumidor de la biblioteca para configurar manualmente el gcc_flags y mtouch adicionales argumentos que se pasan a una biblioteca.

Sintaxis:

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

Este atributo se aplica en el nivel de ensamblado, por ejemplo, esto es lo que la [CorePlot enlaces](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) usar:

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Cuando se usa el `LinkWith` atributo especificado `libraryName` se incrusta en el ensamblado resultante, lo que permite a los usuarios enviar un solo archivo DLL que contiene tanto las dependencias no administradas, así como las marcas de línea de comandos necesarias para consumir correctamente el biblioteca de Xamarin.iOS.

También es posible que no proporcione un `libraryName`, en cuyo caso el `LinkWith` atributo se puede usar para especificar solo las marcas de vinculador adicionales:

 ``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
 ```

 <a name="LinkWithAttribute_Constructors" />


### <a name="linkwithattribute-constructors"></a>Constructores de LinkWithAttribute

Estos constructores permiten especificar la biblioteca para vincular con e incrustar en el ensamblado resultante, los destinos admitidos que es compatible con la biblioteca y los indicadores de biblioteca opcional que son necesarios para vincular con la biblioteca.

Tenga en cuenta que el argumento LinkTarget deduce Xamarin.iOS y no deben establecerse.

Ejemplos:

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```

 <a name="LinkWithAttribute.ForceLoad" />


### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

El `ForceLoad` propiedad se utiliza para decidir si o no la `-force_load` indicador de vínculo se utiliza para vincular la biblioteca nativa. Por ahora, siempre debe ser true.

 <a name="LinkWithAttribute.Frameworks" />


### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

Si la biblioteca que se está enlaza tiene un requisito de disco duro en ningún marco (excepto `Foundation` y `UIKit`), debe establecer el `Frameworks` propiedad en una cadena que contiene una lista delimitada por espacios de los marcos de plataforma necesarias. Por ejemplo, si va a enlazar una biblioteca que requiere `CoreGraphics` y `CoreText`, establecería la `Frameworks` propiedad `"CoreGraphics CoreText"`.

 <a name="LinkWithAttribute.IsCxx" />


### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Establezca esta propiedad en true si el archivo ejecutable resultante debe ser compilado mediante un compilador de C++ en lugar del predeterminado, que es un compilador de C. Utilícelo si la biblioteca que se va a enlazar se ha escrito en C++.

 <a name="LinkWithAttribute.LibraryName" />


### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

El nombre de la biblioteca no administrada que se va a agrupar. Se trata de un archivo con la extensión ".una" y puede contener código de objeto para varias plataformas (por ejemplo, ARM y x86 para el simulador).

Las versiones anteriores de Xamarin.iOS comprueban la `LinkTarget` propiedad para determinar la plataforma de la biblioteca admite, pero esto no está ahora detecta automáticamente y el `LinkTarget` propiedad se omite.

 <a name="LinkWithAttribute.LinkerFlags" />


### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

El `LinkerFlags` cadena proporciona una manera para que los autores de enlaces especificar cualquier marca de enlazador adicionales necesarios al vincular la biblioteca nativa en la aplicación.

Por ejemplo, si la biblioteca nativa requiere libxml2 y zlib, establecería la `LinkerFlags` de cadena para `"-lxml2 -lz"`.

 <a name="LinkWithAttribute.LinkTarget" />


### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

Las versiones anteriores de Xamarin.iOS comprueban la `LinkTarget` propiedad para determinar la plataforma de la biblioteca admite, pero esto no está ahora detecta automáticamente y el `LinkTarget` propiedad se omite.



### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Establezca esta propiedad en true si la biblioteca que se está estableciendo vínculos requiere que la biblioteca de control de excepciones de GCC (gcc_eh)

 <a name="LinkWithAttribute.SmartLink" />


### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

El `SmartLink` propiedad debe establecerse en true para habilitar Xamarin.iOS determine si `ForceLoad` se requiere o no.

 <a name="LinkWithAttribute.WeakFrameworks" />


### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

El `WeakFrameworks` propiedad funciona del mismo modo que la `Frameworks` propiedad, salvo que en tiempo de vínculo, el `-weak_framework` especificador se pasa a gcc para cada uno de los marcos de trabajo enumerados.

`WeakFrameworks` permite que las bibliotecas y aplicaciones al vínculo débilmente con marcos de plataforma que puede utilizar opcionalmente si siguen disponibles, pero no tienen una dependencia fuerte en ellos, lo que resulta útil si va a agregar características adicionales en la biblioteca más reciente versiones de iOS. Para obtener más información sobre la vinculación débil, consulte la documentación de Apple en [vinculación débil](http://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Buenos candidatos para una vinculación débil sería `Frameworks` como cuentas, `CoreBluetooth`, `CoreImage`, `GLKit`, `NewsstandKit` y `Twitter` ya que solo están disponibles en iOS 5.

 <a name="SinceAttribute_(iOS)_and_LionAttribute_(MacOS_X)" />


## <a name="sinceattribute-ios-and-lionattribute-macos-x"></a>SinceAttribute (iOS) y LionAttribute (Mac OS X)

Usa el `Since` atributo para marcar las API que indica que se introduce en un momento determinado en el tiempo. El atributo solo debe usarse para marcar tipos y métodos que podrían provocar un problema de tiempo de ejecución si la clase subyacente, método o propiedad no está disponible.

Sintaxis:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

Se debe en general no aplicar a las enumeraciones, restricciones o nuevas estructuras como los no produciría un error en tiempo de ejecución si se ejecutan en un dispositivo con una versión anterior del sistema operativo.

Ejemplo cuando se aplica a un tipo:

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

Ejemplo cuando se aplica a un nuevo miembro:

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

El `Lion` se aplica el atributo de la misma manera pero tipos de introducidos con Lion. La razón para utilizar `Lion` con respecto al número de versión más específico que se usa en iOS es ese iOS se revisa muy a menudo, mientras que las versiones principales de OS X ocurra de forma ocasional y es más fácil de recordar el sistema operativo por su nombre de código que por su número de versión

 <a name="AdviceAttribute" />


## <a name="adviceattribute"></a>AdviceAttribute



Utilice este atributo para proporcionar a los programadores una sugerencia sobre otras API que puede ser más cómodo para su uso.   Por ejemplo, si proporciona una versión fuertemente tipada de una API, podría utilizar este atributo en el atributo débilmente tipado para dirigir el desarrollador a la API mejor.

La información de este atributo se muestra en la documentación y herramientas se pueden desarrollar para proporcionar sugerencias de usuario sobre cómo mejorar su código. <a name="ZeroCopyStringsAttribute" />


## <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Solo disponible en 5.4 Xamarin.iOS y versiones más recientes.

Este atributo indica el generador de que el enlace para esta biblioteca específico (si se aplican con `[assembly:]`) o tipo que debe utilizar la cadena de cero copia rápida de serialización. Este atributo es equivalente a pasar la opción de línea de comandos `--zero-copy` al generador.

Cuando se utiliza la copia de cero para las cadenas, el generador utiliza eficazmente la misma cadena de C# como la cadena que consume Objective-C sin incurrir en la creación de un nuevo `NSString` objeto y evite copiar los datos de las cadenas de C# en la cadena de C de objetivo. El único inconveniente de usar cadenas de copia de cero es que debe asegurarse de que cualquier propiedad de cadena que ajuste que se produce se marca como "conservar" o "Copiar" tiene el `DisableZeroCopy` conjunto de atributos. Esto es necesario porque el identificador de cadenas de copia de cero se asigna en la pila y no es válido en el valor devuelto de función.

Ejemplo:

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

También puede aplicar el atributo en el nivel de ensamblado y se aplicará a todos los tipos del ensamblado:

    [assembly:ZeroCopyStrings]

# <a name="strongly-typed-dictionaries"></a>Diccionarios fuertemente tipados

Con Xamarin.iOS 8.0 se introdujo compatibilidad para crear fácilmente clases fuertemente tipadas que ajustan `NSDictionaries`.

Aunque siempre ha sido posible utilizar el [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) del tipo de datos junto con una API manual, ahora es mucho más fácil de hacerlo.  Para obtener más información, consulte [así como la exposición de tipos de seguro](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionar"/>

## <a name="strongdictionary"></a>StrongDictionary

Cuando este atributo se aplica a una interfaz, el generador producirá una clase con el mismo nombre que la interfaz que se deriva de [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) y convierte cada propiedad definida en la interfaz en un captador fuertemente tipado y el establecedor para el diccionario.

Esto genera automáticamente una clase que se puede crear instancias de una existente `NSDictionary` o que no se han creado nuevos.

Este atributo toma un parámetro, el nombre de la clase que contiene las claves que se usan para tener acceso a los elementos en el diccionario.   De forma predeterminada, cada propiedad en la interfaz con el atributo buscar a un miembro en el tipo especificado para un nombre con el sufijo "Clave".

Por ejemplo:

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

En el caso anterior, el `MyOption` clase generará una propiedad de cadena para `Name` que utilizará el `MyOptionKeys.NameKey` como la clave en el diccionario para recuperar una cadena.   Y usará el `MyOptionKeys.AgeKey` como la clave en el diccionario para recuperar un `NSNumber` que contiene un valor int.

Si desea utilizar una clave diferente, puede utilizar el atributo de exportación en la propiedad, por ejemplo:

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

### <a name="strong-dictionary-types"></a>Tipos de diccionario seguro

Se admiten los siguientes tipos de datos en el `StrongDictionary` definición:

<table border="1" cellpadding="1" cellspacing="1" width="80%">
<tbody>
  <tr>
    <td>Tipo de interfaz de C#</td>
    <td>Tipo de almacenamiento de NSDictionary</td>
  </tr>
  <tr>
    <td>bool</td>
    <td>Valor booleano que se almacenan en un NSNumber</td>
  </tr>
  <tr>
    <td>Valores de enumeración</td>
    <td>entero que se almacena en un NSNumber</td>
  </tr>
  <tr>
    <td>int</td>
    <td>entero de 32 bits que se almacenan en un NSNumber</td>
  </tr>
  <tr>
    <td>uint</td>
    <td>entero sin signo de 32 bits almacenado en un NSNumber</td>
  </tr>
  <tr>
    <td>nint</td>
    <td>NSInteger almacenado en un NSNumber</td>
  </tr>
  <tr>
    <td>nuint</td>
    <td>NSUInteger almacenado en un NSNumber</td>
  </tr>
  <tr>
    <td>long</td>
    <td>entero de 64 bits que se almacenan en un NSNumber</td>
  </tr>
  <tr>
    <td>float</td>
    <td>entero de 32 bits que se almacena como un NSNumber</td>
  </tr>
  <tr>
    <td>double</td>
    <td>entero de 64 bits que se almacena como un NSNumber</td>
  </tr>
  <tr>
    <td>NSObject y subclases</td>
    <td>NSObject</td>
  </tr>
  <tr>
    <td>NSDictionary</td>
    <td>NSDictionary</td>
  </tr>
  <tr>
    <td>cadena</td>
    <td>NSString</td>
  </tr>
  <tr>
    <td>NSString</td>
    <td>NSString</td>
  </tr>
  <tr>
    <td>Matriz de C# de NSObject</td>
    <td>NSArray</td>
  </tr>
  <tr>
    <td>Matriz de C# de enumeraciones</td>
    <td>Que contiene NSNumbers con el valor de NSArray</td>
  </tr>
</tbody>
