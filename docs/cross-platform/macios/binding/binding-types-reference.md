---
title: Guía de referencia de tipos de enlace
description: Esta guía describe varios atributos y conceptos que son necesarios para entender al crear C# enlaces a las bibliotecas de Objective-C.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: 369e1a37cc75bb4d10cc71d8f79ed1dd473378ba
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119441"
---
# <a name="binding-types-reference-guide"></a>Guía de referencia de tipos de enlace

Este documento describe la lista de atributos que puede usar para anotar los archivos de contrato de API para impulsar el enlace y el código generado

Contratos de API de Xamarin.Mac y Xamarin.iOS se escriben C# principalmente como definiciones de interfaz que definen la forma en que aparece al código de Objective-C C#. El proceso implica una combinación de las declaraciones de interfaz más algunas definiciones de tipo básico que puede requerir el contrato de API. Para obtener una introducción a los tipos de enlace, consulte nuestra guía complementaria de [bibliotecas de Objective-C de enlace](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Definiciones de tipo

Sintaxis:

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Todas las interfaces en la definición de contrato que tiene el [ `[BaseType]` ](#BaseTypeAttribute) atributo que declara el tipo base para el objeto generado. En la declaración anterior un `MyType` clase C# generará tipos que se enlaza a un tipo de Objective-C se llama a `MyType`.

Si se especifica ningún tipo after typename (en el ejemplo anterior `Protocol1` y `Protocol2`) mediante la sintaxis de la herencia de interfaz el contenido de esas interfaces estará insertado como si se hubieran parte del contrato para `MyType`.
La manera en que las superficies de Xamarin.iOS que un tipo adopta un protocolo es mediante la alineación de todos los métodos y propiedades que se han declarado en el protocolo en el propio tipo.

El siguiente se muestra cómo la declaración de Objective-C para `UITextField` se definiría en un contrato de Xamarin.iOS:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Así como se escribiría una C# contrato de API:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

Puede controlar muchos otros aspectos de la generación de código por aplicar otros atributos a la interfaz, así como configurar el [ `[BaseType]` ](#BaseTypeAttribute) atributo.


### <a name="generating-events"></a>Generar eventos

Una característica de diseño de la API de Xamarin.Mac y Xamarin.iOS es que se asignan las clases de delegado de Objective-C como C# eventos y devoluciones de llamada. Los usuarios pueden elegir en una base por instancia si desean adoptar el modelo de programación Objective-C, mediante la asignación a propiedades como `Delegate` una instancia de una clase que implementa los diversos métodos que se llamaría al tiempo de ejecución Objective-C, o bien elegir el C#-eventos y propiedades de estilo.

Nos gustaría ver un ejemplo de cómo usar el modelo de Objective-C:

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

En el ejemplo anterior, puede ver que hemos elegido sobrescribir los dos métodos, uno una notificación de que un evento de desplazamiento ha tenido lugar y el segundo, que es una devolución de llamada que se debe devolver un valor booleano que indica la `scrollView` si se debe desplazar a la principales o no.

El C# modelo permite al usuario de la biblioteca escuchar las notificaciones mediante el C# sintaxis de eventos o la sintaxis de propiedad para enlazar las devoluciones de llamada que se esperan como valores devueltos.

Se trata cómo el C# de código para la misma característica aspecto con las expresiones lambda:

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

Dado que los eventos no devuelven valores (tienen un tipo de valor devuelto void) puede conectar varias copias. El `ShouldScrollToTop` no es un evento, en realidad es una propiedad con el tipo `UIScrollViewCondition` que tiene esta firma:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Devuelve un `bool` valor, en este caso la sintaxis de expresión lambda nos permite simplemente devolver el valor de la `MakeDecision` función.

El generador de enlace admite la generación de eventos y propiedades que vincula una clase como `UIScrollView` con su `UIScrollViewDelegate` (también llamada a estos la clase del modelo), para ello, anotar su [ `[BaseType]` ](#BaseTypeAttribute) definición con el `Events` y `Delegates` parámetros (se describe a continuación). Además de anotar el [ `[BaseType]` ](#BaseTypeAttribute) con esos parámetros es necesario informar al generador de algunos componentes más.

Para los eventos que tardan más de un parámetro (en Objective-C, la convención es que el primer parámetro en una clase de delegado es la instancia del objeto de remitente) debe proporcionar el nombre que le gustaría para generado `EventArgs` clase sea. Esto se realiza con el [ `[EventArgs]` ](#EventArgsAttribute) atributo en la declaración de método en la clase de modelo. Por ejemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La declaración anterior generará un `UIImagePickerImagePickedEventArgs` clase que derive de `EventArgs` y los módulos de ambos parámetros, el `UIImage` y el `NSDictionary`. El generador produce esto:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

A continuación, expone lo siguiente en el `UIImagePickerController` clase:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Los métodos de modelos que devuelven un valor se enlazan de forma diferente. Los que requieren tanto un nombre para el texto generado C# delegado (la firma del método) y también un valor predeterminado para devolver en caso de que el usuario no proporciona una implementación a sí mismo. Por ejemplo, el `ShouldScrollToTop` definición es esto:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

Lo anterior creará un `UIScrollViewCondition` delegado con la firma que se mostró anteriormente, y si el usuario no proporciona una implementación, el valor devuelto será true.

Además el [ `[DefaultValue]` ](#DefaultValueAttribute) atributo, también puede usar el [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute) atributo que se dirige el generador para devolver el valor del parámetro especificado en la llamada o el [ `[NoDefaultValue]` ](#NoDefaultValueAttribute) parámetro que indica que el generador que no hay ningún valor predeterminado.

<a name="BaseTypeAttribute" />

### <a name="basetypeattribute"></a>BaseTypeAttribute

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

#### <a name="basetypename"></a>BaseType.Name

Usa el `Name` propiedad para controlar el nombre de este tipo se enlazará a en el mundo de C de objetivo. Esto normalmente se usa para asignar el C# escriba un nombre que sea compatible con las instrucciones de diseño de .NET Framework, pero que se asigna a un nombre en Objective-C que no sigue esta convención.

Ejemplo, en el caso siguiente se asigna el Objective-C `NSURLConnection` escriba a `NSUrlConnection`, como las instrucciones de diseño de .NET Framework usa "Url" en lugar de "URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

El nombre especificado se utiliza como el valor para el texto generado `[Register]` atributo en el enlace. Si `Name` no se especifica, el nombre del tipo corto se utiliza como el valor de la `[Register]` atributo en la salida generada.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events y BaseType.Delegates

Estas propiedades se utilizan para controlar la generación de C#-aplicar estilo a los eventos de las clases generadas. Se utilizan para vincular una clase dada con su clase de delegado de Objective-C. Encontrará muchos casos donde una clase utiliza una clase de delegado para enviar notificaciones y eventos. Por ejemplo un `BarcodeScanner` tendría un complemento `BardodeScannerDelegate` clase. El `BarcodeScanner` clase normalmente tendría una `Delegate` propiedad que se asignaría a una instancia de `BarcodeScannerDelegate` para, mientras que esto funciona, es posible que desee exponer a los usuarios un C#-como interfaz de eventos de estilo y en esos casos, se usaría la `Events` y `Delegates` propiedades de la [ `[BaseType]` ](#BaseTypeAttribute) atributo.

Estas propiedades siempre se establecen entre sí y deben tener el mismo número de elementos y se mantienen sincronizados. El `Delegates` matriz contiene una cadena para cada delegado débilmente tipada que se va a incluir, y el `Events` matriz contiene un tipo para cada tipo que desea asociar con ella.

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


#### <a name="basetypekeeprefuntil"></a>BaseType.KeepRefUntil

Si aplica este atributo cuando se crean nuevas instancias de esta clase, la instancia de ese objeto se mantendrán hasta que el método al que hace referencia el `KeepRefUntil` se ha invocado. Esto es útil para mejorar la facilidad de uso de las API, cuando no desea que el usuario para mantener una referencia a un objeto en torno a usar el código. El valor de esta propiedad es el nombre de un método en el `Delegate` clase, lo que debe usar en combinación con la `Events` y `Delegates` también propiedades.

Este ejemplo se muestra cómo se utiliza por `UIActionSheet` en Xamarin.iOS:

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


### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

Cuando este atributo se aplica a la definición de interfaz impedirá que el generador de generar el constructor predeterminado.

Utilice este atributo cuando necesite que el objeto que se inicialice con uno de los otros constructores de la clase.


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Cuando este atributo se aplica a la definición de interfaz marcará el constructor predeterminado como privado. Esto significa que puede seguir crear instancias de objeto de esta clase internamente desde el archivo de extensión, pero simplemente no ser accesible para los usuarios de la clase.

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>Atributo CategoryAttribute

Utilice este atributo en una definición de tipo para enlazar las categorías de Objective-C y exponer como C# métodos de extensión para reflejar la manera de Objective-C expone la funcionalidad.

Categorías son un mecanismo de Objective-C que se usa para extender el conjunto de métodos y propiedades disponibles en una clase.   En la práctica, se usan para extender la funcionalidad de una clase base (por ejemplo `NSObject`) cuando está vinculado a un marco concreto (por ejemplo `UIKit`), hacer que sus métodos disponibles, pero solo si está vinculada a la nueva plataforma.   En otros casos, se utilizan para organizar las características en una clase mediante la funcionalidad.   Son similares en espíritu a C# métodos de extensión.

Se trata de una categoría de aspecto en Objective C:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

El ejemplo anterior se encuentra en una biblioteca que se pueden extender las instancias de `UIView` con el método `makeBackgroundRed`.

Para enlazarlas, puede usar el [ `[Category]` ](#CategoryAttribute) atributo en una definición de interfaz.   Cuando se usa el [ `[Category]` ](#CategoryAttribute) atributo, el significado de la [ `[BaseType]` ](#BaseTypeAttribute) atributo cambia desde que se usa para especificar la clase base para extender hasta que se va a ampliar el tipo.

El siguiente se muestra cómo el `UIView` extensiones están enlazadas y convierten en C# métodos de extensión:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Lo anterior creará un `MyUIViewExtension` una clase que contiene el `MakeBackgroundRed` método de extensión.   Esto significa que ahora puede llamar `MakeBackgroundRed` en cualquier `UIView` subclase, lo que le ofrece la misma funcionalidad que se obtendrá en Objective-C.

En algunos casos encontrará **estático** miembros dentro de categorías, como en el ejemplo siguiente:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Esto dará lugar a un **incorrecto** categoría C# definición de interfaz:

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

Esto es incorrecto porque usar el `BoolMethod` extensión necesita una instancia de `FooObject` pero va a enlazar un ObjC **estático** extensión, se trata de un efecto secundario debido al hecho de cómo C# se implementan los métodos de extensión .

Es la única forma de usar las definiciones anteriores en el siguiente código poco atractivo:

```csharp
(null as FooObject).BoolMethod (range);
```

La recomendación para evitar este problema consiste en línea el `BoolMethod` definición dentro de la `FooObject` definición de interfaz propia, esto le permitirá llamar a esta extensión, como se pretende `FooObject.BoolMethod (range)`.

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Se emitirá una advertencia (BI1117) cada vez que encontramos un [ `[Static]` ](#StaticAttribute) miembro dentro de un [ `[Category]` ](#CategoryAttribute) definición. Si realmente desea tener [ `[Static]` ](#StaticAttribute) miembros dentro de su [ `[Category]` ](#CategoryAttribute) puede silenciar la advertencia mediante el uso de definiciones `[Category (allowStaticMembers: true)]` o mediante la decoración de sus miembros o [ `[Category]` ](#CategoryAttribute) definición con la interfaz [ `[Internal]` ](#InternalAttribute).

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

Cuando este atributo se aplica a una clase solo generará una clase estática, que no se deriva de `NSObject`, por lo que [ `[BaseType]` ](#BaseTypeAttribute) se omite el atributo. Las clases estáticas se usan para hospedar las variables públicas C que se desean exponer.

Por ejemplo:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Se generará un C# clase con la siguiente API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Las definiciones de protocolo y modelo

Los modelos se utilizan normalmente mediante la implementación del protocolo.
Se diferencian en que el tiempo de ejecución solo registrará con Objective-c. los métodos que realmente se han sobrescrito.
En caso contrario, el método no se registrará.

Esto generalmente significa que, cuando subclase una clase que se ha marcado con el `ModelAttribute`, no debe llamar al método base.   Llamada a este método producirá una excepción, se supone que para implementar el comportamiento completo en su subclase de los métodos que reemplace.

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

De forma predeterminada, los miembros que forman parte de un protocolo no son obligatorios. Esto permite a los usuarios crear una subclase de la `Model` objeto derivando de la clase en simplemente C# y reemplazar solo los métodos que les interesen. A veces, el contrato de Objective-C requiere que el usuario proporciona una implementación para este método (los que se marcan con el `@required` la directiva en Objective-C). En esos casos, debería marcar esos métodos con el `[Abstract]` atributo.

El `[Abstract]` atributo puede aplicarse a métodos o propiedades y hace que el generador marcar el miembro generado como abstracta y la clase sea una clase abstracta.

El siguiente procede de Xamarin.iOS:

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

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

Especifica el valor predeterminado que se devuelve mediante un método del modelo si el usuario no proporciona un método para este método en el objeto de modelo

Sintaxis:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Por ejemplo, en la siguiente clase de delegado imaginario de un `Camera` clase, proporcionamos un `ShouldUploadToServer` que se expondría como una propiedad en el `Camera` clase. Si el usuario del `Camera` clase no establece explícitamente un valor a una expresión lambda que puede responder a true o false, el valor predeterminado devuelto en este caso sería false, el valor especificado en el `DefaultValue` atributo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Si el usuario establece un controlador de la clase imaginaria, a continuación, se ignorará este valor:

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

Vea también: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute).

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

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

En el caso anterior si el usuario de la `NSAnimation` clase decidió usar cualquiera de los C# eventos o propiedades y no estableció `NSAnimation.ComputeAnimationCurve` a un método o una expresión lambda, el valor devuelto sería el valor pasado en el parámetro de progreso.

Vea también: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

A veces tiene sentido no exponer un evento o delegar la propiedad desde una clase de modelo en la clase de host, por lo que agrega este atributo, indicará el generador para evitar la generación de cualquier método decorado con él.

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

### <a name="delegatenameattribute"></a>DelegateNameAttribute

Este atributo se usa en los métodos de modelos que devuelven valores para establecer el nombre de la signatura de delegado para usar.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

Con la definición anterior, el generador creará la siguiente declaración pública:

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

Este atributo se utiliza para permitir que el generador cambiar el nombre de la propiedad generada en la clase de host. A veces resulta útil cuando el nombre del método de clase FooDelegate que tenga sentido para la clase de delegado, pero tendría un aspecto extraño en la clase de host como una propiedad.

También esto es realmente útil (y es necesario) al tener dos o más métodos de sobrecarga que tenga sentido para mantenerlos con el nombre porque está en la clase FooDelegate pero desea exponerlos en la clase de host con un mejor nombre dado.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Con la definición anterior, el generador creará la siguiente declaración pública en la clase de host:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

Para los eventos que tardan más de un parámetro (en Objective-C, la convención es que el primer parámetro en una clase de delegado es la instancia del objeto de remitente) debe proporcionar el nombre que desee para que la clase EventArgs generada sea. Esto se realiza con el `[EventArgs]` atributo en la declaración de método en su `Model` clase.

Por ejemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La declaración anterior generará un `UIImagePickerImagePickedEventArgs` clase que deriva de EventArgs y módulos de ambos parámetros, el `UIImage` y `NSDictionary`. El generador produce esto:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

A continuación, expone lo siguiente en el `UIImagePickerController` clase:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

Este atributo se utiliza para permitir que el generador cambiar el nombre de un evento o propiedad generada en la clase. A veces resulta útil cuando el nombre del método de clase de modelo que tenga sentido para la clase del modelo, pero tendría un aspecto extraño en la clase de origen como un evento o propiedad.

Por ejemplo, el `UIWebView` utiliza el siguiente bit desde el `UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

El anterior expone `LoadingFinished` como el método en el `UIWebViewDelegate`, pero `LoadFinished` como el evento al enlazar hasta en un `UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

Al aplicar el `[Model]` atributo a una definición de tipo en su contrato de API, el tiempo de ejecución generará código especial que solo se detectarán las llamadas a métodos en la clase si el usuario ha sobrescrito un método en la clase. Normalmente, este atributo se aplica a todas las API que incluir una clase de delegado de Objective-C.

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Especifica que el método en el modelo no proporciona un valor devuelto predeterminado.

Esto funciona con el tiempo de ejecución Objective-C respondiendo `false` a la solicitud de Objective-C en tiempo de ejecución para determinar si el selector especificado se implementa en esta clase.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

Vea también: [ `[DefaultValue]` ](#DefaultValueAttribute), [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>Protocolos

El concepto de protocolo de Objective-C no existe realmente en C#. Protocolos son similares a C# interfaces pero se diferencian en que no todos los métodos y propiedades declarados en un protocolo deben implementarse mediante la clase que lo adopta. En su lugar, algunos de los métodos y propiedades son opcionales.

Algunos protocolos se suelen usar como clases de modelo, los que se deben enlazar con el [ `[Model]` ](#ModelAttribute) atributo.

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

Se ha incorporado a partir de Xamarin.iOS 7.0, un funcionalidad de enlace de protocolo nuevo y mejorado.  Cualquier definición que contiene el `[Protocol]` atributo realmente generará tres clases auxiliares que mejoran considerablemente la forma en que consume protocolos:

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

El **implementación de la clase** proporciona una clase abstracta completa que puede invalidar los métodos individuales de y obtenga seguridad de tipos completa. Pero debido a C# no admite herencia múltiple, hay escenarios donde es posible que requieren una clase base diferente, pero todavía desea implementar una interfaz.

Aquí es donde generado **definición de interfaz** entra en juego.  Es una interfaz que tiene todos los métodos necesarios del protocolo.  Esto permite que los desarrolladores que desean implementar el protocolo para implementar simplemente la interfaz.  El tiempo de ejecución registrará automáticamente el tipo como adoptar el protocolo.

Tenga en cuenta que la interfaz sólo se enumera los métodos necesarios y exponer los métodos opcionales.   Esto significa que las clases que adopten el protocolo obtendrán completa comprobación de tipos para los métodos necesarios, pero tendrán que recurrir a establecimiento flexible de tipos (manualmente los atributos de exportación y compara la firma) para los métodos de protocolo opcional.

Para que sea conveniente usar una API que usa los protocolos, la herramienta de enlace también generará una clase de método de extensiones que expone todos los métodos opcionales.   Esto significa que, siempre que consume una API, podrá tratar protocolos como si tuviera todos los métodos.

Si desea utilizar las definiciones de protocolo de la API, deberá escribir esqueletos interfaces vacías en la definición de API.   Si desea usar el MiProtocolo en una API, deberá hacer esto:

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

El anterior es necesario porque en tiempo de enlace el `IMyProtocol` no existiría, que es ¿por qué tiene que proporcionar una interfaz vacía.

### <a name="adopting-protocol-generated-interfaces"></a>Adoptar interfaces generadas por el protocolo

Cada vez que implemente una de las interfaces que se generan para los protocolos, similar al siguiente:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

La implementación para los métodos de interfaz obtiene exportan automáticamente con el nombre correcto, por lo que es equivalente a esta:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

No importa si se implementa la interfaz de forma implícita o explícita.

### <a name="protocol-inlining"></a>Protocolo de inserción

Al enlazar tipos de Objective-C existentes que se han declarado como adoptar un protocolo, le interesará a en línea el protocolo directamente. Para ello, simplemente declare su protocolo como una interfaz sin ninguna [ `[BaseType]` ](#BaseTypeAttribute) de atributo y el protocolo en la lista de interfaces base para la interfaz de la lista.

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


## <a name="member-definitions"></a>Definiciones de miembros

Los atributos de esta sección se aplican a los miembros individuales de un tipo: propiedades y las declaraciones de método.


### <a name="alignattribute"></a>AlignAttribute

Se usa para especificar el valor de alineación para tipos de valor devuelto de la propiedad. Ciertas propiedades toman punteros a las direcciones que deben estar alineados en determinados límites (en Xamarin.iOS esto sucede por ejemplo, con algunas `GLKBaseEffect` alinean las propiedades que deben ser de 16 bytes). Puede utilizar esta propiedad para decorar el captador y usar el valor de alineación. Esto se suele utilizar con el `OpenTK.Vector4` y `OpenTK.Matrix4` tipos cuando se integra con las API de C de objetivo.

Ejemplo:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

El `[Appearance]` atributo está limitado a iOS 5, donde se introduce el Administrador de apariencia.

El `[Appearance]` atributo puede aplicarse a cualquier método o propiedad que participan en la `UIAppearance` framework. Cuando este atributo se aplica a un método o propiedad en una clase, dirigirá el generador de enlace para crear una clase fuertemente tipada de aspecto que se usa para definir el estilo de todas las instancias de esta clase, o las instancias que coinciden con determinados criterios.

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

Lo anterior generaría el código siguiente en UIToolbar:

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

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (Xamarin.iOS 5.4)

Use la `[AutoReleaseAttribute]` en métodos y propiedades para encapsular la llamada de método al método en un `NSAutoReleasePool`.

En Objective-C, hay algunos métodos que devuelven valores que se agregan en el valor predeterminado `NSAutoReleasePool`. De forma predeterminada, estos iría a su conversación `NSAutoReleasePool`, pero dado que Xamarin.iOS también mantiene una referencia a los objetos mientras se encuentra el objeto administrado, es posible que no desea mantener una referencia adicional en el `NSAutoReleasePool` que sólo obtenga agotan hasta que el subproceso Devuelve el control al subproceso siguiente o volver al bucle principal.

Este atributo se aplica por ejemplo, en propiedades pesadas (por ejemplo `UIImage.FromFile`) que devuelve objetos que se han agregado en el valor predeterminado `NSAutoReleasePool`. Sin este atributo, se conservarán las imágenes siempre y cuando el subproceso no ha devuelto el control al bucle principal. UF su subproceso era algún tipo de aplicación de descarga en segundo plano que siempre está activo y en espera para el trabajo, nunca se liberaba las imágenes.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

El `[ForcedTypeAttribute]` se utiliza para exigir la creación de un tipo administrado, incluso si el objeto no administrado devuelto no coincide con el tipo se describe en la definición de enlace.

Esto es útil cuando el tipo se describe en un encabezado no coincide con el tipo devuelto del método nativo, por ejemplo, realizar la siguiente definición de Objective-C desde `NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

Indique claramente que devolverá una `NSURLSessionDownloadTask` instancia, pero aún lo **devuelve** un `NSURLSessionTask`, que es una superclase y, por tanto, no puede convertir en `NSURLSessionDownloadTask`. Puesto que estamos en un contexto de seguridad de tipos un `InvalidCastException` sucederá.

Para cumplir con la descripción de encabezado y evitar la `InvalidCastException`, el `[ForcedTypeAttribute]` se utiliza.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

El `[ForcedTypeAttribute]` también acepta un valor booleano denominado `Owns` decir `false` predeterminada `[ForcedType (owns: true)]`. El propietario de parámetro se usa para seguir el [directiva de la propiedad](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) para **Foundation Core** objetos.

El `[ForcedTypeAttribute]` solo es válido en los parámetros, propiedades y valor devuelto.

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

El `[BindAsAttribute]` permite el enlace `NSNumber`, `NSValue` y `NSString`(enumeraciones) en más precisos C# tipos. El atributo se puede usar para crear mejores y más precisos, API de .NET a través de la API nativa.

Puede decorar métodos (en el valor devuelto), parámetros y propiedades con `BindAs`. La única restricción es que el miembro **no debe** estar dentro de un `[Protocol]` o [ `[Model]` ](#ModelAttribute) interfaz.

Por ejemplo:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Salida sería:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente, se realizará la `bool?`  <->  `NSNumber` y `CGRect`  <->  `NSValue` conversiones.

Los tipos de encapsulación compatibles actuales son:

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

La siguiente C# admiten los tipos de datos para encapsular como origen o en `NSValue`:

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

#### <a name="nsnumber"></a>NSNumber

La siguiente C# admiten los tipos de datos para encapsular como origen o en `NSNumber`:

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

#### <a name="nsstring"></a>NSString

[`[BindAs]`](#BindAsAttribute) funciona junto con [enumeraciones respaldado por una constante NSString](#enum-attributes) para que pueda crear API de .NET mejor, por ejemplo:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Salida sería:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Se va a controlar el `enum`  <->  `NSString` conversión solo si el tipo de la enumeración proporcionada a [ `[BindAs]` ](#BindAsAttribute) es [respaldado por una constante NSString](#enum-attributes).

#### <a name="arrays"></a>Matrices

[`[BindAs]`](#BindAsAttribute) También es compatible con matrices de cualquiera de los tipos admitidos, puede tener la siguiente definición de API, por ejemplo:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Salida sería:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

El `rects` parámetro se encapsulará en una `NSArray` que contiene un `NSValue` para cada `CGRect` y de vuelta obtendrá una matriz de `CAScroll?` que se ha creado mediante los valores de devuelto `NSArray` que contiene `NSStrings`.

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

El `[Bind]` atributo tiene dos usos uno cuando se aplica a un método o declaración de propiedad y otra cuando se aplica a la individual captador o establecedor de una propiedad.

Cuando se utiliza para un método o propiedad, el efecto de la `[Bind]` atributo consiste en generar un método que invoca el selector especificado. Pero el método generado resultante no está decorado con el [ `[Export]` ](#ExportAttribute) atributo, lo que significa que no puede participar en el reemplazo del método. Esto normalmente se usa en combinación con el `[Target]` atributo para implementar los métodos de extensión de C de objetivo.

Por ejemplo:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Cuando se usa en un captador o establecedor, el `[Bind]` atributo se usa para modificar los valores predeterminados que se infiere por el generador de código al generar los nombres de selector de Objective-C de captador y establecedor para una propiedad. De forma predeterminada cuando se marca una propiedad con el nombre `fooBar`, generaría el generador de un `fooBar` exportar el captador y `setFooBar:` del establecedor. En algunos casos, no sigue esta convención de Objective-C, normalmente cambie el nombre de captador sea `isFooBar`.
Este atributo se utilizaría para informar al generador de esto.

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

### <a name="asyncattribute"></a>AsyncAttribute

Solo disponible en Xamarin.iOS 6.3 y versiones más recientes.

Este atributo puede aplicarse a los métodos que toman un controlador de finalización como su último argumento.

Puede usar el `[Async]` atributo en los métodos cuyo último argumento es una devolución de llamada.  Cuando esto se aplica a un método, el generador de enlace generará una versión de ese método con el sufijo `Async`.  Si la devolución de llamada no toma ningún parámetro, el valor devuelto será una `Task`, si la devolución de llamada toma un parámetro, el resultado será un `Task<T>`.

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

El siguiente generará este método asincrónico:

```csharp
Task LoadFileAsync (string file);
```

Si la devolución de llamada toma varios parámetros, debe establecer el `ResultType` o `ResultTypeName` para especificar el nombre del tipo generado que contendrá todas las propiedades deseado.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

La siguiente generará este método asincrónico, donde `FileLoading` contiene propiedades para tener acceso a ambos `files` y `byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

Si el último parámetro de devolución de llamada es un `NSError`, a continuación, el generado `Async` método comprobará si el valor no es null, y si es así, el método asincrónico generado establecerá la excepción de tarea.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

Lo anterior genera el siguiente método asincrónico:

```csharp
Task<string> UploadAsync (string file);
```

Y en caso de error, la tarea resultante tendrá la excepción que se establece en un `NSErrorException` que ajusta resultante `NSError`.

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

Use esta propiedad para especificar el valor de la devolución `Task` objeto.   Este parámetro toma un tipo existente, por lo tanto debe definirse de alguna de las definiciones de api principales.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Use esta propiedad para especificar el valor de la devolución `Task` objeto.   Este parámetro toma el nombre de su nombre de tipo deseado, el generador creará una serie de propiedades, uno para cada parámetro que toma la devolución de llamada.

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

Utilice esta propiedad para personalizar el nombre de los métodos asincrónicos generado.   El valor predeterminado es usar el nombre del método y agregue el texto "Async", se puede usar para cambiar este comportamiento predeterminado.

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Este atributo se aplica a los parámetros de cadena o las propiedades de cadena e indica el generador de código para no usar la serialización de cadenas de copia de cero para este parámetro y en su lugar, cree una nueva instancia de NSString desde el C# cadena.
Este atributo solo es necesario en las cadenas si indicar a utilizar la serialización de cadenas de copia de cero mediante el generador del `--zero-copy` opción de línea de comandos o establecer el atributo de nivel de ensamblado `ZeroCopyStringsAttribute`.

Esto es necesario en casos donde la propiedad se declara en Objective-C sea un `retain` o `assign` propiedad en lugar de un `copy` propiedad. Normalmente ocurre en las bibliotecas de terceros que han sido erróneamente "optimizadas" por los desarrolladores. En general, `retain` o `assign` `NSString` propiedades son incorrectas desde `NSMutableString` o clases derivadas de usuario de `NSString` podría alterar el contenido de las cadenas sin el conocimiento del código de biblioteca, sutilmente interrumpir el aplicación. Normalmente esto sucede debido a la optimización prematura.

A continuación muestra dos de estas propiedades en Objective C:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

Al aplicar el `[DisposeAttribute]` a una clase, proporciona un fragmento de código que se agregará a la `Dispose()` implementación del método de la clase.

Puesto que la `Dispose` método genera automáticamente el `bmac-native` y `btouch-native` herramientas, deberá usar el `[Dispose]` atributo insertar algo de código generado `Dispose` implementación del método.

Por ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

El `[Export]` atributo se usa para marcar un método o propiedad se expongan en el tiempo de ejecución de C de objetivo. Este atributo se comparte entre la herramienta de enlace y los tiempos de ejecución real de Xamarin.iOS y Xamarin.Mac. Para los métodos, se pasa el parámetro textual para el código generado, de las exportaciones de propiedades, un captador y establecedor se generan en función de la declaración base (consulte la sección sobre la [ `[BindAttribute]` ](#BindAttribute) para obtener información sobre cómo modificar el comportamiento de la herramienta de enlace).

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

El [selector](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) representa el nombre del método subyacente de Objective-C o la propiedad que se está enlazando.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

Este atributo se utiliza para exponer una variable global C como un campo que se cargan a petición y se expone a C# código. Normalmente, esto es necesario para obtener los valores de constantes que se definen en C u Objective-C y que puede ser cualquier tokens que se usan en algunas API o cuyos valores son opacos y deben usarse como-es código de usuario.

Sintaxis:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

El `symbolName` es el símbolo de C con la que vincular. De forma predeterminada se cargarán desde una biblioteca cuyo nombre se deduce del espacio de nombres donde se define el tipo. Si esto no es la biblioteca donde se busca el símbolo, se debe pasar el `libraryName` parámetro. Si va a vincular una biblioteca estática, use `__Internal` como el `libraryName` parámetro.

Las propiedades generadas siempre son estáticas.

Propiedades de marcado con el atributo de campo pueden ser de los siguientes tipos:

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* Enumeraciones

No se admiten los establecedores para [enumeraciones respaldado por constantes NSString](#enum-attributes), pero pueden ser manualmente enlazadas si es necesario.

Ejemplo:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

El `[Internal]` atributo puede aplicarse a los métodos o propiedades y tiene el efecto de marcar el código generado con el `internal` C# palabra clave que hace el código solo es accesible al código en el ensamblado generado. Esto se usa normalmente para ocultar las API que son demasiado bajo nivel o proporcionar una API pública no óptimo que desea mejorar en o para las API que no son compatibles con el generador y requieren algunos codificación manual.

Al diseñar el enlace, normalmente desea ocultar el método o propiedad con este atributo y proporcione un nombre diferente para el método o propiedad y, a continuación, en su C# archivo auxiliar complementarias, agregaría un contenedor fuertemente tipado que expone el funcionalidad subyacente.

Por ejemplo:

```csharp
[Internal]
[Export ("setValue:forKey:")]
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

<a name="IsThreadStaticAttribute" />

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

Este atributo marca el campo de respaldo para una propiedad que se va a anotar con .NET `[ThreadStatic]` atributo. Esto es útil si el campo es una variable estática del subproceso.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin.iOS 6.0.6)

Este atributo hará un excepciones (Objective-C) del método soporte nativo.
En lugar de llamar `objc_msgSend` directamente, la invocación recorrerá una cama elástica personalizado que detecta las excepciones ObjectiveC y calcula las referencias a las excepciones administradas.

Actualmente sólo unos `objc_msgSend` se admiten las firmas (encontrará información sobre si una firma no se admite cuando nativo de vinculación de una aplicación que usa el enlace produce un error con una falta monotouch_*_objc_msgSend* símbolos), pero pueden ser mucho más Agregar en la solicitud.


### <a name="newattribute"></a>NewAttribute

Este atributo se aplica a los métodos y propiedades para que el generador de generan el `new` palabra clave delante de la declaración.

Sirve para evitar las advertencias del compilador cuando se introduce el nombre de propiedad o el mismo método en una subclase que ya existía en una clase base.

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

Este atributo se puede aplicar a los campos que el proceso del generador una aplicación auxiliar fuertemente tipado clase de notificaciones.

Este atributo se puede usar sin argumentos para las notificaciones que llevar a cabo ninguna carga, o puede especificar un `System.Type` que hace referencia a otra interfaz en la definición de API, normalmente con el nombre termina con "EventArgs". El generador convertirá la interfaz en una clase que cree subclases `EventArgs` e incluirá todas las propiedades en la lista. El [ `[Export]` ](#ExportAttribute) atributo debería usarse en la `EventArgs` clase para mostrar el nombre de la clave utilizada para buscar el diccionario de Objective-C para capturar el valor.

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

Los usuarios de su código, a continuación, pueden suscribirse fácilmente a las notificaciones que se registran en el [NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/) mediante código similar al siguiente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

O para establecer un objeto específico para observar. Si se pasa `null` a `objectToObserve` este método se comporta igual que sus otro del mismo nivel.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

El valor devuelto de `ObserveDidStart` puede usarse fácilmente dejar de recibir notificaciones, similar al siguiente:

```csharp
token.Dispose ();
```

O bien puede llamar [NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject//) y pasar el token. Si la notificación contiene parámetros, debe especificar una aplicación auxiliar `EventArgs` interfaz como esta:

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

Lo anterior, se generará un `MyScreenChangedEventArgs` clase con el `ScreenX` y `ScreenY` propiedades que se capturará los datos desde el [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) con las claves del diccionario `ScreenXKey` y `ScreenYKey` respectivamente y se aplican las conversiones adecuadas. El `[ProbePresence]` atributo se utiliza para el generador para sondear si la clave se establece el `UserInfo`, en lugar de intentar extraer el valor. Esto se usa para los casos donde la presencia de la clave es el valor (normalmente por los valores booleanos).

Esto le permite escribir código como este:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

En algunos casos, no hay ninguna constante asociado con el valor pasado en el diccionario.  Apple usa a veces, las constantes de símbolos públicos y a veces usa las constantes de cadena.  De forma predeterminada el [ `[Export]` ](#ExportAttribute) atributo en que indicaste `EventArgs` clase usará el nombre especificado como un símbolo público que para va a buscar en tiempo de ejecución.  Si esto no es así, y en su lugar, se supone que se puede buscar como una constante de cadena, a continuación, pasar la `ArgumentSemantic.Assign` valor para el atributo Export.

**Novedad en la versión 8.4 de Xamarin.iOS**

A veces, las notificaciones iniciará vida sin argumentos, por lo que el uso de [ `[Notification]` ](#NotificationAttribute) sin argumentos es aceptable.  Pero a veces, se introducirán los parámetros para la notificación.  Para admitir este escenario, el atributo se puede aplicar más de una vez.

Si está desarrollando un enlace y desea evitar romper el código de usuario existente, podría convertir una notificación existente desde:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

En una versión que enumeran los atributos de notificación dos veces, similar al siguiente:

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute" />

### <a name="nullallowedattribute"></a>NullAllowedAttribute

Cuando esto se aplica a una propiedad que marca la propiedad como lo que permite el valor `null` para asignarse a él. Esto solo es válido para tipos de referencia.

Cuando esto se aplica a un parámetro en una firma de método indica que el parámetro especificado puede ser null y que no se debe realizar ninguna comprobación para pasarlo `null` valores.

Si el tipo de referencia no tiene este atributo, la herramienta de enlace generará una comprobación para el valor asignado antes de pasarlo a Objective-C y generará una comprobación de que se producirá un `ArgumentNullException` si el valor asignado es `null`.

Por ejemplo:

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>OverrideAttribute

Utilice este atributo para indicar que el generador de enlace que el enlace para este método debe estar marcado con un `override` palabra clave.

### <a name="presnippetattribute"></a>PreSnippetAttribute

Puede usar este atributo para insertar código para insertar después de que se han validado los parámetros de entrada, pero antes de las llamadas de código en Objective-C.

Ejemplo:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

Puede usar este atributo para insertar algún código que se va a insertar antes de cualquiera de los parámetros se validan en el método generado.

Ejemplo:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Indica que el generador de enlace para invocar la propiedad especificada de esta clase para capturar un valor a partir de él.

Esta propiedad se utiliza normalmente para actualizar la memoria caché que apunta a objetos de referencia que tenga el gráfico de objetos al que hace referencia. Normalmente, se muestra en el código que tiene operaciones como agregar o quitar. Este método se usa para que cuando los elementos se agregan o quitan que actualizar la caché interna para asegurarse de que se conservan las referencias administradas a los objetos que están realmente en uso. Esto es posible porque la herramienta de enlace genera un campo de respaldo para todos los objetos de referencia en un enlace determinado.

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

En este caso, el `Dependencies` propiedad se invocará después de agregar o quitar las dependencias desde el `NSOperation` objeto, lo que garantiza que tenemos un gráfico que represente los datos reales cargado objetos, prevención de pérdidas de memoria así como daños en la memoria.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

Puede usar este atributo para insertar algunas C# código fuente para insertarse cuando el código ha invocado el método subyacente de Objective-C

Ejemplo:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Este atributo se aplica a los valores devueltos para marcarlos como objetos proxy. Algunos objetos proxy devuelto de las API de Objective-C que no se diferencian de los enlaces de usuario. El efecto de este atributo es marcar el objeto como un `DirectBinding` objeto. Para un escenario de Xamarin.Mac, puede ver el [debate sobre este error](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Indica que el generador para mantener una referencia al parámetro administrada o quitar una referencia interna para el parámetro. Esto se usa para mantener los objetos que se hace referenciados.

Sintaxis:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Si el valor de `doAdd` es true, el parámetro se agrega a la `__mt_{0}_var List<NSObject>;`. Donde `{0}` se reemplaza con la determinada `listName`. Debe declarar este campo de respaldo en su clase parcial complementaria a la API.

Para obtener un ejemplo, vea [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) y [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Xamarin.iOS 6.0)

Esto se puede aplicar para devolver tipos para indicar que el generador debe llamar a `Release` en el objeto antes de devolverlo. Esto solo es necesario cuando un método proporciona un objeto retenido (en lugar de un objeto autoreleased, que es el escenario más común)

Ejemplo:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Además, este atributo se propaga al código generado, para que sepa que el tiempo de ejecución de Xamarin.iOS debe conservar el objeto cuando se vuelve a Objective-C de dicha función.


### <a name="sealedattribute"></a>SealedAttribute

Indica que el generador para marcar el método generado como sellada. Si no se especifica este atributo, el valor predeterminado es generar un método virtual (un método virtual, un método abstracto o una invalidación dependiendo de cómo se usan otros atributos).

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

Cuando el `[Static]` atributo se aplica a un método o propiedad, esto genera un método o propiedad estáticos. Si no se especifica este atributo, el generador crea un método de instancia o una propiedad.


### <a name="transientattribute"></a>TransientAttribute

Utilice este atributo a propiedades de indicador cuyos valores son transitorios, es decir, los objetos que se crean temporalmente por iOS, pero no son de larga duración. Cuando este atributo se aplica a una propiedad, el generador no crea un campo de respaldo para esta propiedad, lo que significa que la clase administrada no mantiene una referencia al objeto.

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

En el diseño de los enlaces de Xamarin.iOS/Xamarin.Mac el `[Wrap]` atributo se utiliza para ajustar un objeto débilmente tipada con un objeto fuertemente tipado. Esto entra en juego principalmente con los objetos de delegados de Objective-C que normalmente se declaran como perteneciente al tipo `id` o `NSObject`. La convención usada por Xamarin.iOS y Xamarin.Mac consiste en exponer esos orígenes de datos o los delegados son de tipo `NSObject` y un nombre con la convención de "Weak" + el nombre está expuesto. Un `id delegate` propiedad desde Objective-C se expondría como un `NSObject WeakDelegate { get; set; }` propiedad en el archivo de contrato de API.

Pero normalmente el valor que se asigna a este delegado es de tipo seguro, por lo que el establecimiento inflexible de tipos de superficie y aplicamos la `[Wrap]` atributo, esto significa que los usuarios pueden elegir usar tipos no seguros si necesitan algunos fine-control o si es necesario recurrir a bajo nivel tric KS, o bien pueden usar la propiedad fuertemente tipados para la mayor parte de su trabajo.

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

Y así es como el usuario usa la versión fuertemente tipada, tenga en cuenta que el usuario aprovecha las ventajas de C#utiliza la palabra clave override para declarar su intención y del sistema de tipos y que no tiene que decorar manualmente el método con `[Export]`, desde que creamos Hice que funcionan en el enlace para el usuario:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Otro uso de la `[Wrap]` atributo es para admitir la versión fuertemente tipada de métodos.  Por ejemplo:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Los miembros generados por `[Wrap]` no `virtual` de forma predeterminada, si necesita un `virtual` miembro que se puede establecer en `true` opcional `isVirtual` parámetro.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

## <a name="parameter-attributes"></a>Atributos de parámetro

En esta sección se describe los atributos que se pueden aplicar a los parámetros de una definición de método, así como la `[NullAttribute]` que se aplica a una propiedad como un todo.

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

Este atributo se aplica a los tipos de parámetro en C# declaraciones de delegados para notificar el enlazador que el parámetro en cuestión se ajusta a Objective-C bloquear la convención de llamada y debe calcular referencias de esta manera.

Esto se utiliza normalmente para las devoluciones de llamada que se definen como esta en Objective C:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vea también: [CCallback](#CCallback).

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

Este atributo se aplica a los tipos de parámetro en C# delegar las declaraciones para notificar el enlazador que el parámetro en cuestión se ajusta al puntero de función de ABI de C, la convención de llamada y debe calcular referencias de esta manera.

Esto se utiliza normalmente para las devoluciones de llamada que se definen como esta en Objective C:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vea también: [BlockCallback](#BlockCallback).

### <a name="params"></a>params

Puede usar el `[Params]` atributo en el último parámetro de matriz de una definición de método para que el generador de insertar un "params" en la definición.   Esto permite el enlace permitir fácilmente para los parámetros opcionales.

Por ejemplo, la siguiente definición:

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Permite que escribirse el código siguiente:

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Esto tiene la ventaja añadida de que no requiere que los usuarios crear una matriz exclusivamente para pasar los elementos.

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

Puede usar el `[PlainString]` atributo delante de los parámetros de cadena para indicar el generador de enlace para pasar la cadena como una cadena de C, en lugar de pasar el parámetro como un `NSString`.

Consumir la mayoría de las API de Objective-C `NSString` parámetros, pero un puñado de API de exponer un `char *` API para pasar cadenas, en lugar de la `NSString` variación.
Use `[PlainString]` en esos casos.

Por ejemplo, las declaraciones de Objective-C siguientes:

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

### <a name="retainattribute"></a>RetainAttribute

Indica al generador de mantener una referencia al parámetro especificado. El generador proporcionará el almacén de respaldo para este campo o puede especificar un nombre (el `WrapName`) para almacenar el valor en. Esto es útil para mantener una referencia a un objeto administrado que se pasa como parámetro y Objective-C y cuando se sabe que Objective-C solo mantendrá esta copia del objeto. Por ejemplo, una API como `SetDisplay (SomeObject)` usaría este atributo, ya que es probable que el SetDisplay sólo podría mostrar un objeto a la vez. Si necesita realizar un seguimiento de más de un objeto (por ejemplo, para una API similar a pila) usaría los `[RetainList]` atributo.

Sintaxis:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

Indica que el generador para mantener una referencia al parámetro administrada o quitar una referencia interna para el parámetro. Esto se usa para mantener los objetos que se hace referenciados.

Sintaxis:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Si el valor de `doAdd` es true, el parámetro se agrega a la `__mt_{0}_var List<NSObject>`. Donde `{0}` se reemplaza con la determinada `listName`. Debe declarar este campo de respaldo en su clase parcial complementaria a la API.

Para obtener un ejemplo, vea [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) y [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

Este atributo se aplica a los parámetros y sólo se utiliza al realizar la transición desde Objective-C para C#.  Durante esas transiciones los diversos Objective-C `NSObject` parámetros se encapsulan en una representación del objeto administrada.

El tiempo de ejecución tomará una referencia al objeto nativo y mantener la referencia hasta que la última referencia administrada para el objeto ya no existe, y el GC tiene la oportunidad de ejecutarse.

En algunos casos, es importante para el C# en tiempo de ejecución no se debe mantener una referencia al objeto nativo.  A veces, esto sucede cuando el código nativo subyacente ha adjuntado un comportamiento especial para el ciclo de vida del parámetro.  Por ejemplo: el destructor para el parámetro se realice alguna acción de limpieza o eliminar algunos recursos muy valioso.

Este atributo informa el tiempo de ejecución que desea que el objeto que se van a eliminarse si es posible cuando se devuelven a Objective-C desde su método sobrescrito.

La regla es sencilla: si el tiempo de ejecución se tenía que crear una nueva representación administrada desde el objeto nativo, a continuación, al final de la función, se eliminarán el recuento de conservar para el objeto nativo y se borrará la propiedad de identificador del objeto administrado.   Esto significa que si mantiene una referencia al objeto administrado, esa referencia será inútil (invocación de métodos en el iniciará una excepción).

Si el objeto pasado no se creó, o si ya había una representación administrada pendiente del objeto, al método dispose forzado no llevará a cabo. 


## <a name="property-attributes"></a>Atributos de propiedad

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Este atributo se usa para admitir un modismo Objective-C, donde se introduce una propiedad con un captador de una clase base y una subclase mutable presenta un establecedor.

Puesto que C# no admite este modelo, la clase base debe tener el establecedor y el captador y puede usar una subclase del [OverrideAttribute](#OverrideAttribute).

Este atributo solo se usa en los establecedores de propiedades y se usa para admitir la expresión mutable en Objective-C.

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

<a name="enum-attributes" />

## <a name="enum-attributes"></a>Enum (atributos)

Asignación `NSString` constantes para los valores de enumeración es un método sencillo para crear las API de .NET mejor. Lo:

* permite la terminación de código para ser más útil, mostrando **sólo** los valores correctos para la API;
* Agrega seguridad de tipos, no se puede usar otro `NSString` constante en un contexto incorrecto; y
* permite para ocultar algunas constantes, realizar la finalización de código muestra la lista de API más corto sin perder la funcionalidad.

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

Desde la definición de enlace anterior el generador creará el `enum` propio y también creará un `*Extensions` tipo estático que incluye métodos de dos maneras de conversión entre los valores de enumeración y el `NSString` constantes. Esto significa que las constantes sigue estando disponible para los desarrolladores incluso si no son parte de la API.

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

### <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

Puede decorar **uno** valor enum con este atributo. Esto se convertirá en la constante que se devuelve si no se conoce el valor de enumeración.

En el ejemplo anterior:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Si ningún valor de enumeración se decora un `NotSupportedException` se iniciará.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

Códigos de error se enlazan como los valores de enumeración. Normalmente hay un dominio de error para ellos y no siempre es fácil encontrar cuál se aplica (o si aún existe uno).

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

### <a name="fieldattribute"></a>FieldAttribute

Esto es lo mismo `[Field]` atributo utilizado para las constantes de tipo. También se puede usar dentro de las enumeraciones para asignar un valor con una constante específica.

Un `null` valor puede usarse para especificar qué valor de enumeración se debe devolver un `null` `NSString` se especifica la constante.

En el ejemplo anterior:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Si no hay ningún `null` valor está presente una `ArgumentNullException` se iniciará.

## <a name="global-attributes"></a>Atributos globales

Atributos globales se aplican ya sea mediante el `[assembly:]` modificador de atributo, como el [ `[LinkWithAttribute]` ](#LinkWithAttribute) o se pueden usar desde cualquier lugar, como el [ `[Lion]` ](#SinceAndLionAttributes) y [ `[Since]` ](#SinceAndLionAttributes) atributos.

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

Este es un atributo de nivel de ensamblado que permite a los desarrolladores especificar las marcas de vinculación necesarias para volver a usar una biblioteca dependiente sin necesidad de configurar manualmente el gcc_flags al consumidor de la biblioteca y los argumentos mtouch adicionales pasados a una biblioteca.

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

Este atributo se aplica en el nivel de ensamblado, por ejemplo, esto es lo que el [CorePlot enlaces](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) usar:

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Cuando se usa el `[LinkWith]` atributo especificado `libraryName` se incrusta en el ensamblado resultante, lo que permite a los usuarios enviar un solo archivo DLL que contiene tanto las dependencias no administradas, así como los marcadores de línea de comandos necesarios para consumir correctamente el biblioteca de Xamarin.iOS.

También es posible que no proporcione un `libraryName`, en cuyo caso el `LinkWith` atributo puede utilizarse para especificar solo las marcas de enlazador adicionales:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Constructores LinkWithAttribute

Estos constructores permiten especificar la biblioteca para vincular con e insertar en el ensamblado resultante, los destinos admitidos que admite la biblioteca y los indicadores de biblioteca opcional que son necesarios para vincular con la biblioteca.

Tenga en cuenta que el `LinkTarget` argumento se infiere por Xamarin.iOS y no deben establecerse.

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

#### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

El `ForceLoad` propiedad se utiliza para decidir si el `-force_load` indicador de vínculo se usa para vincular la biblioteca nativa. Por ahora, esta debe ser siempre true.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

Si la biblioteca que se está enlazaba tiene un requisito de disco duro en los marcos de trabajo (distinto de `Foundation` y `UIKit`), debe establecer el `Frameworks` propiedad en una cadena que contiene una lista delimitada por espacios de los marcos de plataforma necesarias. Por ejemplo, si va a enlazar una biblioteca que requiere `CoreGraphics` y `CoreText`, establecería el `Frameworks` propiedad `"CoreGraphics CoreText"`.

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Establezca esta propiedad en true si el archivo ejecutable resultante debe compilarse con un compilador de C++ en lugar del predeterminado, que es un compilador de C. Úsela si la biblioteca que se va a enlazar se ha escrito en C++.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

El nombre de la biblioteca no administrada para agrupar. Se trata de un archivo con la extensión ".a" y puede contener código de objeto para varias plataformas (por ejemplo, ARM y x86 para el simulador).

Comprueban las versiones anteriores de Xamarin.iOS el `LinkTarget` propiedad para determinar la plataforma de la biblioteca admite, pero esto ya está detectado automáticamente y el `LinkTarget` propiedad se omite.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

El `LinkerFlags` cadena proporciona una manera para los autores de enlaces especificar las marcas de enlazador adicionales necesitan al vincular la biblioteca nativa en la aplicación.

Por ejemplo, si la biblioteca nativa requiere libxml2 y zlib, establecería el `LinkerFlags` de string a `"-lxml2 -lz"`.

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

Comprueban las versiones anteriores de Xamarin.iOS el `LinkTarget` propiedad para determinar la plataforma de la biblioteca admite, pero esto ya está detectado automáticamente y el `LinkTarget` propiedad se omite.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Establezca esta propiedad en true si requiere que la biblioteca que desea vincular la biblioteca de control de excepciones de GCC (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

El `SmartLink` propiedad debe establecerse en true para permitir que Xamarin.iOS determinen si `ForceLoad` se requiere o no.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

El `WeakFrameworks` propiedad funciona del mismo modo que el `Frameworks` propiedad, excepto que en tiempo de vínculo, el `-weak_framework` especificador se pasa a gcc para cada uno de los marcos indicados.

`WeakFrameworks` hace posible para las bibliotecas y aplicaciones a un enlace flexible con marcos de plataforma para que puedan utilizarlas, opcionalmente, si están disponibles pero que no tienen una dependencia fuerte en ellos, lo que resulta útil si la biblioteca está pensada para agregar características adicionales en versiones más recientes versiones de iOS. Para obtener más información sobre la vinculación débil, consulte la documentación de Apple en [vinculación débil](http://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Buenos candidatos para la vinculación débil sería `Frameworks` como cuentas, `CoreBluetooth`, `CoreImage`, `GLKit`, `NewsstandKit` y `Twitter` puesto que solo están disponibles en iOS 5.

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) y LionAttribute (macOS)

Usa el `[Since]` de atributo que marca las API como si tuviera que se introdujo en un momento dado. El atributo debería usarse sólo para marcar los tipos y métodos que podrían provocar un problema de tiempo de ejecución si la clase subyacente, método o propiedad no está disponible.

Sintaxis:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

Se debe en general no aplicar a las enumeraciones, las restricciones o nuevas estructuras como los no provocaría un error en tiempo de ejecución si se ejecutan en un dispositivo con una versión anterior del sistema operativo.

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

El `[Lion]` se aplica el atributo de la misma manera, pero los tipos de introducidos con Lion. La razón para utilizar `[Lion]` con respecto al número de versión más específico que se usa en iOS es que iOS se revisa con frecuencia, mientras que las versiones principales de OS X se producen con poca frecuencia y es más fácil de recordar el sistema operativo por su nombre en código que por su número de versión

### <a name="adviceattribute"></a>AdviceAttribute

Utilice este atributo para proporcionar a los desarrolladores una sugerencia sobre otras API que puede ser más cómodo para su uso.   Por ejemplo, si proporciona una versión fuertemente tipada de una API, podría usar este atributo en el atributo débilmente tipada para dirigir al programador a la API de mejor.

La información de este atributo se muestra en la documentación y herramientas se pueden desarrollar para enviar sugerencias de usuario sobre cómo mejorar

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Solo disponible en Xamarin.iOS 5.4 y versiones más recientes.

Este atributo indica que el generador de que el enlace para esta biblioteca específico (si se aplican con `[assembly:]`) o tipo debe utilizar la serialización de cadenas de la copia rápida de cero. Este atributo es equivalente a pasar la opción de línea de comandos `--zero-copy` al generador.

Cuando use la copia de cero para las cadenas, el generador utiliza eficazmente el mismo C# cadena como el que consume Objective-C sin incurrir en la creación de un nuevo `NSString` objeto y evite copiar los datos desde el C# cadenas a la Cadena de C de objetivo. El único inconveniente de utilizar cadenas de copia de cero es que debe asegurarse de que cualquier propiedad de cadena que ajuste que se marcan como `retain` o `copy` tiene la `[DisableZeroCopy]` conjunto de atributos. Es necesario porque el identificador para las cadenas de copia de cero, se asigna en la pila y no es válido en el valor devuelto de función.

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

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Diccionarios fuertemente tipados

Con Xamarin.iOS 8.0 se introdujo la compatibilidad para crear fácilmente clases fuertemente tipadas que ajustan `NSDictionaries`.

Aunque siempre ha sido posible utilizar el [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) del tipo de datos junto con una API manual, ahora resulta mucho más fácil de hacerlo.  Para obtener más información, consulte [exponer tipos de seguro](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

Cuando este atributo se aplica a una interfaz, el generador creará una clase con el mismo nombre que la interfaz que se deriva de [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) y convierte cada propiedad definida en la interfaz en fuertemente tipados captador y establecedor para el diccionario.

Esto genera automáticamente una clase que se puede crear instancias de una existente `NSDictionary` o que se han creado nuevos.

Este atributo toma un parámetro, el nombre de la clase que contiene las claves que se usan para tener acceso a los elementos en el diccionario.   De forma predeterminada, cada propiedad en la interfaz con el atributo buscar a un miembro del tipo especificado para un nombre con el sufijo "Key".

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

En el caso anterior, el `MyOption` clase generará una propiedad de cadena para `Name` que usará el `MyOptionKeys.NameKey` como la clave en el diccionario para recuperar una cadena.   Y usará el `MyOptionKeys.AgeKey` como la clave en el diccionario para recuperar un `NSNumber` que contiene un valor int.

Si desea usar una clave diferente, puede usar el atributo export en la propiedad, por ejemplo:

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

#### <a name="strong-dictionary-types"></a>Tipos de diccionario seguro

Se admiten los siguientes tipos de datos en el `StrongDictionary` definición:

|C#Tipo de interfaz|`NSDictionary` Tipo de almacenamiento|
|---|---|
|`bool`|`Boolean` almacena en un `NSNumber`|
|Valores de enumeración|entero que se almacena en un `NSNumber`|
|`int`|entero de 32 bits que se almacenan en un `NSNumber`|
|`uint`|entero sin signo de 32 bits almacenado en un `NSNumber`|
|`nint`|`NSInteger` almacena en un `NSNumber`|
|`nuint`|`NSUInteger` almacena en un `NSNumber`|
|`long`|entero de 64 bits que se almacenan en un `NSNumber`|
|`float`|entero de 32 bits que se almacena como un `NSNumber`|
|`double`|entero de 64 bits que se almacena como un `NSNumber`|
|`NSObject` y subclases|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array` de `NSObject`|`NSArray`|
|C#`Array` de enumeraciones|`NSArray` que contiene `NSNumber` valores|
