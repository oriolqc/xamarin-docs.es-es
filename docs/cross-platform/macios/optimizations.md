---
title: Optimizaciones de compilación
description: Este documento explican las diversas optimizaciones que se aplican en tiempo de compilación para las aplicaciones Xamarin.iOS y Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: bradumbaugh
ms.author: brumbaug
dateupdated: 04/16/2018
ms.openlocfilehash: abdd1223c0105156580b8f23fc2c020f2f45caa6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="build-optimizations"></a>Optimizaciones de compilación

Este documento explican las diversas optimizaciones que se aplican en tiempo de compilación para las aplicaciones Xamarin.iOS y Xamarin.Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Quitar UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

Quita las llamadas a [UIApplication.EnsureUIThread] [ 1] (de Xamarin.iOS) o `NSApplication.EnsureUIThread` (para Xamarin.Mac).

Esta optimización cambiará el tipo de código siguiente:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

en lo siguiente:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

Esta optimización requiere que el vinculador esté habilitado y solo se aplica a los métodos con los `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

De forma predeterminada que está habilitada para la versión compilaciones.

El comportamiento predeterminado puede reemplazarse pasando `--optimize=[+|-]remove-uithread-checks` a mtouch/mmp.

[1]: https://developer.xamarin.com/api/member/UIKit.UIApplication.EnsureUIThread/

## <a name="inline-intptrsize"></a>IntPtr.Size en línea

Elementos incorporados el valor constante de `IntPtr.Size` según la plataforma de destino.

Esta optimización cambiará el tipo de código siguiente:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

en el siguiente (al compilar para una plataforma de 64 bits):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Esta optimización requiere que el vinculador esté habilitado y solo se aplica a los métodos con los `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

De forma predeterminada, se habilita si se dirige a una única arquitectura, o para el ensamblado de plataforma (**Xamarin.iOS.dll**, **Xamarin.TVOS.dll**, **Xamarin.WatchOS.dll** o  **Xamarin.Mac.dll**).

Si el destino varias arquitecturas, esta optimización creará distintos ensamblados de la versión de 32 bits y la versión de 64 bits de la aplicación, y ambas versiones tendrá que incluir en la aplicación, de forma eficaz si aumenta el tamaño de la aplicación final en lugar de disminuir se.

El comportamiento predeterminado puede reemplazarse pasando `--optimize=[+|-]inline-intptr-size` a mtouch/mmp.

## <a name="inline-nsobjectisdirectbinding"></a>NSObject.IsDirectBinding en línea

`NSObject.IsDirectBinding` es una propiedad de instancia que determina si una instancia determinada es de un tipo de contenedor o no (un tipo de contenedor es un tipo administrado que se asigna a un tipo nativo; para la instancia los recursos administrados `UIKit.UIView` tipo se asigna a nativo `UIView` - lo contrario es un tipo de usuario en este caso `class MyUIView : UIKit.UIView` sería un tipo de usuario).

Es necesario conocer el valor de `IsDirectBinding` al llamar a Objective-C, porque el valor determina qué versión de `objc_msgSend` a usar.

Dado que solo el código siguiente:

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

Podemos determinar que en `UIView.SomeProperty` el valor de `IsDirectBinding` no es una constante y no se insertarán entre líneas:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

Sin embargo, es posible examinar todos los tipos de la aplicación y determinar que no hay tipos que heredan de `NSUrl`, y, por tanto, resulta seguro en línea el `IsDirectBinding` valor a una constante `true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

En concreto, esta optimización cambiará el tipo de código siguiente (este es el código de enlace de `NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

en el siguiente (cuando se puede determinar que no hay ningún subclases de `NSUrl` en la aplicación):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Esta optimización requiere que el vinculador esté habilitado y solo se aplica a los métodos con los `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Está siempre habilitada de forma predeterminada para Xamarin.iOS y siempre deshabilitada de forma predeterminada para Xamarin.Mac (dado que es posible cargar dinámicamente ensamblados en Xamarin.Mac, no es posible determinar que una clase determinada nunca es una subclase).

El comportamiento predeterminado puede reemplazarse pasando `--optimize=[+|-]inline-isdirectbinding` a mtouch/mmp.

## <a name="inline-runtimearch"></a>Runtime.Arch en línea

Esta optimización cambiará el tipo de código siguiente:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

en el siguiente (al compilar para dispositivo):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Esta optimización requiere que el vinculador esté habilitado y solo se aplica a los métodos con los `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Siempre está habilitado de forma predeterminada para Xamarin.iOS (no está disponible para Xamarin.Mac).

El comportamiento predeterminado puede reemplazarse pasando `--optimize=[+|-]inline-runtime-arch` a mtouch.

## <a name="dead-code-elimination"></a>Eliminación de código inactivo

Esta optimización cambiará el tipo de código siguiente:

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

en:

```csharp
Console.WriteLine ("Doing this");
```

También se evaluará las constantes comparaciones, similar al siguiente:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

y determinar que la expresión `8 == 8` es un siempre true y reducirse para:

```csharp
Console.WriteLine ("Doing this");
```

Esto supone una optimización eficaz cuando se utiliza junto con las optimizaciones de inclusión, porque puede transformar el tipo de código siguiente (este es el código de enlace de `NFCIso15693ReadMultipleBlocksConfiguration.Range`):

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

en esta (al compilar para un dispositivo de 64 bits y cuando también puede asegurarse de que no hay ningún `NFCIso15693ReadMultipleBlocksConfiguration` subclases de la aplicación):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

El compilador AOT ya puede eliminar cola código similar al siguiente, pero esta optimización se realiza en el vinculador, lo que significa que el vinculador puede ver que hay varios métodos que no se usan ya y, por tanto, pueden quitarse (a menos que se utiliza en otros países) :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Esta optimización requiere que el vinculador esté habilitado y solo se aplica a los métodos con los `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Siempre está habilitado de forma predeterminada (cuando el vinculador está habilitado).

El comportamiento predeterminado puede reemplazarse pasando `--optimize=[+|-]dead-code-elimination` a mtouch/mmp.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Optimizar las llamadas a BlockLiteral.SetupBlock

El tiempo de ejecución Xamarin.iOS/Mac necesita conocer la firma de bloque al crear un bloque Objective-C para administrada delegar. Podría tratarse de una operación bastante costosa. Esta optimización se calcula la firma de bloque en tiempo de compilación y modificar el IL para llamar a un `SetupBlock` método que toma la firma como argumento en su lugar. Esto evita la necesidad de calcular la firma en tiempo de ejecución.

Pruebas comparativas muestran que esto acelera la llamada a un bloque en un factor de 10 a 15.

Transformará el siguiente [código](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

en:

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

Esta optimización requiere que el vinculador esté habilitado y solo se aplica a los métodos con los `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Está habilitada de forma predeterminada al usar al registrador estático (en Xamarin.iOS el registrador estático está habilitado de forma predeterminada para las compilaciones de dispositivo y en el registrador estático está habilitado de forma predeterminada para la versión de Xamarin.Mac las compilaciones).

El comportamiento predeterminado puede reemplazarse pasando `--optimize=[+|-]blockliteral-setupblock` a mtouch/mmp.

## <a name="optimize-support-for-protocols"></a>Optimizar la compatibilidad con protocolos

El tiempo de ejecución Xamarin.iOS/Mac necesita información sobre cómo administrado protocolos implementa Objective-C de tipos. Esta información se almacena en interfaces (y atributos en estas interfaces), que no es un formato muy eficaz, ni es compatible con el vinculador.

Un ejemplo es que estas interfaces almacenan información acerca de todos los miembros de protocolo en un `[ProtocolMember]` atributo, que, entre otras cosas, contienen referencias a los tipos de parámetro de dichos miembros. Esto significa que simplemente la implementación de dicha interfaz hará que el vinculador conservar a todos los tipos usados en esa interfaz, incluso para los miembros opcionales la aplicación nunca llama o que implemente.

Esta optimización hará que el registrador estático almacenar cualquier información necesaria en un formato eficaz que utiliza poca memoria que sea más fácil y rápido buscar en tiempo de ejecución.

También le mostrará al enlazador que no necesariamente necesario conservar estas interfaces, ni ninguno de los atributos relacionados.

Esta optimización requiere que el vinculador y el registrador de estático a habilitarse.

En Xamarin.iOS esta optimización está habilitada de forma predeterminada cuando se habilitan el vinculador y el registrador estático.

En Xamarin.Mac esta optimización nunca está habilitada de forma predeterminada, ya que admite el Xamarin.Mac cargar ensamblados dinámicamente y esos ensamblados podrían no se conoce en tiempo de compilación (y, por tanto, no optimizaron).

El comportamiento predeterminado puede reemplazarse pasando `--optimize=-register-protocols` a mtouch/mmp.

## <a name="remove-the-dynamic-registrar"></a>Quitar al registrador dinámico

El Xamarin.iOS y el tiempo de ejecución de Xamarin.Mac incluyen compatibilidad para [registrar tipos administrados](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/) con el tiempo de ejecución de C de objetivo. O bien se puede realizar en tiempo de compilación o en tiempo de ejecución (o parcialmente en tiempo de compilación y el resto en tiempo de ejecución), pero si se completaron totalmente en tiempo de compilación, significa que se puede quitar el código auxiliar para realizar en tiempo de ejecución. Esto da como resultado una disminución significativa del tamaño de la aplicación, en particular para las aplicaciones más pequeñas como extensiones o watchOS.

Esta optimización requiere el registrador de estático y el vinculador esté habilitado.

El vinculador intentará determinar si es seguro quitar el registrador dinámico y si se intenta quitarlo.

Dado que Xamarin.Mac admite dinámicamente la carga de ensamblados en tiempo de ejecución (que no se conoce en tiempo de compilación), es imposible determinar en tiempo de compilación si se trata de una optimización segura. Esto significa que esta optimización nunca está habilitada de forma predeterminada para las aplicaciones de Xamarin.Mac.

El comportamiento predeterminado puede reemplazarse pasando `--optimize=[+|-]remove-dynamic-registrar` a mtouch/mmp.

Si se invalida el valor predeterminado para quitar el registro dinámico, el vinculador emitirá advertencias si detecta que no es seguro (pero se quitarán el registrador dinámico).

## <a name="inline-runtimedynamicregistrationsupported"></a>Runtime.DynamicRegistrationSupported en línea

El valor de elementos incorporados de `Runtime.DynamicRegistrationSupported` tal y como se determina en tiempo de compilación.

Si se quita el registrador dinámico (vea la [quitar el registrador dinámico](#remove-the-dynamic-registrar) optimización), se trata de una constante `false` valor, en caso contrario, es una constante `true` valor.

Esta optimización cambiará el tipo de código siguiente:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

en la siguiente cuando se quita el registrador dinámico:

```csharp
throw new Exception ("dynamic registration is not supported");
```

en lo siguiente cuando no se quita el registrador dinámico:

```csharp
Console.WriteLine ("do something");
```

Esta optimización requiere que el vinculador esté habilitado y solo se aplica a los métodos con los `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Siempre está habilitado de forma predeterminada (cuando el vinculador está habilitado).

El comportamiento predeterminado puede reemplazarse pasando `--optimize=[+|-]inline-dynamic-registration-supported` a mtouch/mmp.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Calcula previamente métodos para crear delegados administrados para los bloques de Objective-c.

Cuando Objective-C llama a un selector que toma un bloque como un parámetro y, a continuación, el código administrado ofrece reemplazada ese método, el Xamarin.iOS / Xamarin.Mac en tiempo de ejecución tiene que crear un delegado para ese bloque.

El código de enlace generado por el generador de enlace incluirá un `[BlockProxy]` atributo, que especifica el tipo con un `Create` método que puede hacer esto.

Dado el siguiente código de C objetivo:

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

y el código de enlace siguientes:

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

el generador creará:

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }
    
    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...
        
    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;
        
        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }
    
    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

Cuando se llama Objective-C `[ObjCBlockTester callClassCallback]`, el Xamarin.iOS / veremos en tiempo de ejecución de Xamarin.Mac la `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` atributo en el parámetro. A continuación, se buscará el `Create` método en ese tipo y llame a ese método para crear el delegado.

Encontrará esta optimización la `Create` método en tiempo de compilación y el registrador estático generará código que busca el método en tiempo de ejecución mediante los tokens de metadatos en lugar de utilizar el atributo y reflexión (Esto es mucho más rápido y también permite que el vinculador Para quitar el código en tiempo de ejecución correspondiente, hace que la aplicación sea menor).

Si no puede encontrar mmp/mtouch la `Create` método, a continuación, se mostrará una advertencia de MT4174/MM4174 y la búsqueda se realizará en tiempo de ejecución en su lugar.
La causa más probable manualmente se escribe código de enlace sin necesaria `[BlockProxy]` atributos.

Esta optimización requiere el registrador estático esté habilitado.

Siempre está habilitado de forma predeterminada (siempre que el registrador estático está habilitado).

El comportamiento predeterminado puede reemplazarse pasando `--optimize=[+|-]static-delegate-to-block-lookup` a mtouch/mmp.
