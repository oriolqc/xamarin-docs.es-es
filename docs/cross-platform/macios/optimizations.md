---
title: Optimizaciones de las compilaciones
description: Este documento explican las diversas optimizaciones que se aplican en tiempo de compilación para las aplicaciones de Xamarin.iOS y Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: 7d67b924253dfea66781f16b5f83007811de5909
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119038"
---
# <a name="build-optimizations"></a>Optimizaciones de las compilaciones

Este documento explican las diversas optimizaciones que se aplican en tiempo de compilación para las aplicaciones de Xamarin.iOS y Xamarin.Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Quitar UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

Quita las llamadas a [UIApplication.EnsureUIThread] [ 1] (para Xamarin.iOS) o `NSApplication.EnsureUIThread` (para Xamarin.Mac).

Esta optimización se cambiará el tipo de código siguiente:

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

Esta optimización requiere el enlazador esté habilitado y solo se aplica a los métodos con el `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

De forma predeterminada que está habilitada para la versión se basa.

El comportamiento predeterminado puede invalidarse pasando `--optimize=[+|-]remove-uithread-checks` a mmp/mtouch.

[1]: https://developer.xamarin.com/api/member/UIKit.UIApplication.EnsureUIThread/

## <a name="inline-intptrsize"></a>IntPtr.Size en línea

El valor constante de elementos incorporados de `IntPtr.Size` según la plataforma de destino.

Esta optimización se cambiará el tipo de código siguiente:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

en la siguiente (al compilar para una plataforma de 64 bits):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Esta optimización requiere el enlazador esté habilitado y solo se aplica a los métodos con el `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

De forma predeterminada está habilitada si el destino es una única arquitectura, o para el ensamblado de plataforma (**Xamarin.iOS.dll**, **Xamarin.TVOS.dll**, **Xamarin.WatchOS.dll** o  **Xamarin.Mac.dll**).

Si el destino varias arquitecturas, esta optimización creará ensamblados diferentes para la versión de 32 bits y la versión de 64 bits de la aplicación, y ambas versiones deberá incluirse en la aplicación, aumentar el tamaño de aplicación final en lugar de disminuir de forma eficaz él.

El comportamiento predeterminado puede invalidarse pasando `--optimize=[+|-]inline-intptr-size` a mmp/mtouch.

## <a name="inline-nsobjectisdirectbinding"></a>NSObject.IsDirectBinding en línea

`NSObject.IsDirectBinding` es una propiedad de instancia que determina si una instancia determinada de un tipo contenedor o no (un tipo de contenedor es un tipo administrado que se asigna a un tipo nativo; para la instancia administrada `UIKit.UIView` tipo se asigna a nativo `UIView` tipo - de lo contrario es un tipo de usuario en este caso `class MyUIView : UIKit.UIView` sería un tipo de usuario).

Es necesario saber el valor de `IsDirectBinding` al llamar a Objective-C, porque el valor determina qué versión de `objc_msgSend` a usar.

Dado que sólo el código siguiente:

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

Podemos determinar que, en `UIView.SomeProperty` el valor de `IsDirectBinding` no es una constante y no se puede insertar:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

Sin embargo, es posible examinar todos los tipos en la aplicación y determinar que no hay ningún tipo que se heredan de `NSUrl`, y, por tanto, resulta seguro en línea el `IsDirectBinding` valor a una constante `true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

En concreto, esta optimización cambiará el tipo de código siguiente (este es el código de enlace para `NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

en la siguiente (cuando se puede determinar que no hay ningún subclases de `NSUrl` en la aplicación):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Esta optimización requiere el enlazador esté habilitado y solo se aplica a los métodos con el `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Está siempre habilitada de forma predeterminada para Xamarin.iOS y siempre deshabilitado de forma predeterminada para Xamarin.Mac (dado que es posible cargar dinámicamente ensamblados en Xamarin.Mac, no es posible determinar que una clase determinada no es una subclase nunca).

El comportamiento predeterminado puede invalidarse pasando `--optimize=[+|-]inline-isdirectbinding` a mmp/mtouch.

## <a name="inline-runtimearch"></a>Runtime.Arch en línea

Esta optimización se cambiará el tipo de código siguiente:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

en la siguiente (al compilar para dispositivo):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Esta optimización requiere el enlazador esté habilitado y solo se aplica a los métodos con el `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Siempre está habilitado de forma predeterminada para Xamarin.iOS (no está disponible para Xamarin.Mac).

El comportamiento predeterminado puede invalidarse pasando `--optimize=[+|-]inline-runtime-arch` a mtouch.

## <a name="dead-code-elimination"></a>Eliminación de código muerto

Esta optimización se cambiará el tipo de código siguiente:

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

También evaluará las comparaciones de constantes, similar al siguiente:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

y determinar que la expresión `8 == 8` es un siempre true y reducirlo para:

```csharp
Console.WriteLine ("Doing this");
```

Esto supone una optimización eficaz cuando se usa junto con las optimizaciones de inclusión entre líneas, porque puede transformar el tipo de código siguiente (este es el código de enlace para `NFCIso15693ReadMultipleBlocksConfiguration.Range`):

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

en esta (al compilar para un dispositivo de 64 bits y cuando también puede garantizar que hay ninguna `NFCIso15693ReadMultipleBlocksConfiguration` subclases en la aplicación):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

El compilador AOT ya es capaz de eliminar el código no alcanzado similar al siguiente, pero esta optimización se realiza en el vinculador, lo que significa que el vinculador puede ver que hay varios métodos que ya no se usan y, por tanto, es posible que se quiten (a menos que se utiliza en otro lugar) :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Esta optimización requiere el enlazador esté habilitado y solo se aplica a los métodos con el `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Siempre está habilitado de forma predeterminada (cuando se habilita el enlazador).

El comportamiento predeterminado puede invalidarse pasando `--optimize=[+|-]dead-code-elimination` a mmp/mtouch.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Optimizar las llamadas a BlockLiteral.SetupBlock

El tiempo de ejecución Xamarin.iOS/Mac debe saber que la firma de bloque al crear un bloque de Objective-C para administrado delegar. Podría tratarse de una operación bastante costosa. Esta optimización se calcule la firma de bloque en tiempo de compilación y modifique el IL para llamar a un `SetupBlock` método que toma la firma como argumento en su lugar. Esto evita la necesidad de calcular la firma en tiempo de ejecución.

Pruebas comparativas muestran que esto acelera la llamada a un bloque en un factor de 10 a 15.

Transformará siguiente [código](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

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

Esta optimización requiere el enlazador esté habilitado y solo se aplica a los métodos con el `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Está habilitado de forma predeterminada al usar al registrador estático (en Xamarin.iOS del registrador estático está habilitado de forma predeterminada para las compilaciones de dispositivo y en el registrador estático está habilitado de forma predeterminada para la versión de Xamarin.Mac se basa).

El comportamiento predeterminado puede invalidarse pasando `--optimize=[+|-]blockliteral-setupblock` a mmp/mtouch.

## <a name="optimize-support-for-protocols"></a>Optimizar la compatibilidad con protocolos

El tiempo de ejecución Xamarin.iOS/Mac necesita información acerca de los protocolos cómo administrado implementa Objective-C de tipos. Esta información se almacena en interfaces (y los atributos en estas interfaces), que no es un formato muy eficaz, ni es compatible con el vinculador.

Un ejemplo es que estas interfaces almacenan información acerca de todos los miembros de protocolo en una `[ProtocolMember]` atributo, que, entre otras cosas, contienen referencias a los tipos de parámetro de dichos miembros. Esto significa que basta con implementar esta interfaz hará que el vinculador conservar a todos los tipos usados en esa interfaz, incluso para los miembros opcionales la aplicación nunca llama a o implementa.

Esta optimización hará que el registrador estático almacenar cualquier información necesaria en un formato eficaz que usa memoria pequeño que sea fácil y rápida de buscar en tiempo de ejecución.

También le enseñará al vinculador que no necesita necesariamente conservar estas interfaces, ni ninguno de los atributos relacionados.

Esta optimización requiere el enlazador y el registrador de estático a habilitarse.

En Xamarin.iOS esta optimización está habilitada de forma predeterminada cuando se habilitan el enlazador y el registrador estático.

En Xamarin.Mac esta optimización nunca está habilitada de forma predeterminada, ya que Xamarin.Mac admite la carga de ensamblados dinámicamente y esos ensamblados es posible que no tienen ha conocido en tiempo de compilación (y, por tanto, no se han optimizado).

El comportamiento predeterminado puede invalidarse pasando `--optimize=-register-protocols` a mmp/mtouch.

## <a name="remove-the-dynamic-registrar"></a>Quitar al registrador dinámico

Tanto el tiempo de ejecución de Xamarin.Mac y Xamarin.iOS incluyen compatibilidad con [registrar tipos administrados](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/) con el tiempo de ejecución de C de objetivo. O bien puede realizarse en tiempo de compilación o en tiempo de ejecución (o parcialmente en tiempo de compilación y el resto en tiempo de ejecución), pero si se realiza completamente en tiempo de compilación, significa que se puede quitar el código auxiliar para hacerlo en tiempo de ejecución. Esto da como resultado una disminución significativa del tamaño de la aplicación, en particular para las aplicaciones más pequeñas, como extensiones o las aplicaciones para watchOS.

Esta optimización requiere el registrador estático y el enlazador esté habilitado.

El vinculador intentará determinar si es seguro quitar el registrador dinámico y si se intenta quitarlo.

Ya que Xamarin.Mac admite dinámicamente la carga de ensamblados en tiempo de ejecución (que no se conoce en tiempo de compilación), es imposible determinar en tiempo de compilación si se trata de una optimización segura. Esto significa que esta optimización nunca está habilitada de forma predeterminada para las aplicaciones de Xamarin.Mac.

El comportamiento predeterminado puede invalidarse pasando `--optimize=[+|-]remove-dynamic-registrar` a mmp/mtouch.

Si se invalida el valor predeterminado para quitar el registro dinámico, el enlazador emitirá advertencias si detecta que no es seguro (pero todavía se quitará el registrador dinámico).

## <a name="inline-runtimedynamicregistrationsupported"></a>Runtime.DynamicRegistrationSupported en línea

El valor de elementos incorporados de `Runtime.DynamicRegistrationSupported` como se determina en tiempo de compilación.

Si se quita el registrador dinámico (consulte la [quitar el registrador dinámico](#remove-the-dynamic-registrar) optimización), se trata de una constante `false` valor, en caso contrario, es una constante `true` valor.

Esta optimización se cambiará el tipo de código siguiente:

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

en la siguiente cuando no se quita el registrador dinámico:

```csharp
Console.WriteLine ("do something");
```

Esta optimización requiere el enlazador esté habilitado y solo se aplica a los métodos con el `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Siempre está habilitado de forma predeterminada (cuando se habilita el enlazador).

El comportamiento predeterminado puede invalidarse pasando `--optimize=[+|-]inline-dynamic-registration-supported` a mmp/mtouch.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Calcular métodos para crear delegados administrados para los bloques de Objective-c.

Cuando Objective-C llama a un selector que toma un bloque como un parámetro y, a continuación, el código administrado ofrece reemplaza ese método, Xamarin.iOS o porque necesitan en tiempo de ejecución de Xamarin.Mac crear un delegado para ese bloque.

El código de enlace generado por el generador de enlace incluirá un `[BlockProxy]` atributo, que especifica el tipo con un `Create` método que puede hacer esto.

Dado que el código de Objective-C siguiente:

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

y el código de enlace siguiente:

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

Cuando llama Objective-C `[ObjCBlockTester callClassCallback]`, Xamarin.iOS / en tiempo de ejecución de Xamarin.Mac se examinará los `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` atributo en el parámetro. A continuación, buscará el `Create` método en ese tipo y llame a ese método para crear el delegado.

Encontrará esta optimización la `Create` método en tiempo de compilación y el registrador estático generará código que busca el método en tiempo de ejecución mediante los tokens de metadatos en lugar de utilizar el atributo y reflexión (Esto es mucho más rápido y también permite que el vinculador Para quitar el código en tiempo de ejecución correspondiente, con lo que la aplicación más pequeños).

Si no puede encontrar mmp/mtouch el `Create` método, a continuación, se mostrará una advertencia MT4174/MM4174 y se realizará la búsqueda en tiempo de ejecución en su lugar.
La causa más probable se escribe manualmente el código de enlace sin necesaria `[BlockProxy]` atributos.

Esta optimización requiere el registrador de estático a habilitarse.

Siempre está habilitado de forma predeterminada (siempre y cuando está habilitado el registrador estático).

El comportamiento predeterminado puede invalidarse pasando `--optimize=[+|-]static-delegate-to-block-lookup` a mmp/mtouch.
