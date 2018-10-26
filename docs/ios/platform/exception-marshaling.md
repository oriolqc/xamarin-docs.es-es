---
title: Excepción al calcular las referencias en Xamarin.iOS
description: Este documento describe cómo trabajar con excepciones administradas y nativas en una aplicación de Xamarin.iOS. Describe problemas que pueden producirse y una solución para estos problemas.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/05/2017
ms.openlocfilehash: 167d6ac421bdd2652e7f8474e1ea21bd9040723f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114306"
---
# <a name="exception-marshaling-in-xamarinios"></a>Excepción al calcular las referencias en Xamarin.iOS

_Xamarin.iOS contiene nuevos eventos para ayudar a responder a las excepciones, especialmente en código nativo._

Código administrado y Objective-C tienen compatibilidad para las excepciones de tiempo de ejecución (cláusulas try/catch/finally).

Sin embargo, sus implementaciones son diferentes, lo que significa que las bibliotecas en tiempo de ejecución (el tiempo de ejecución Mono y las bibliotecas de tiempo de ejecución Objective-C) tienen problemas cuando tienen que controlar las excepciones y, a continuación, ejecutar código escrito en otros lenguajes.

Este documento explica los problemas que pueden producirse y las posibles soluciones.

También incluye un proyecto de ejemplo, [serialización de excepciones](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), que puede usarse para probar diferentes escenarios y sus soluciones.

## <a name="problem"></a>Problema

El problema se produce cuando una excepción se produce y se encuentra durante el desenredo de un marco de pila que no coincide con el tipo de excepción que se produjo.

Un ejemplo típico de este para Xamarin.iOS o Xamarin.Mac es cuando una API nativa produce una excepción de Objective-C y, a continuación, esa excepción Objective-C alguna forma de controlarse cuando el proceso de desenredo de pila alcanza un marco administrado.

La acción predeterminada es no hacer nada. Para el ejemplo anterior, esto significa que permite a los marcos de desenredado administrado de Objective-C en tiempo de ejecución. Esto es problemático, ya que el tiempo de ejecución Objective-C no sabe cómo desenredar marcos administrados; Por ejemplo no se ejecutará cualquier `catch` o `finally` cláusulas en ese marco.

### <a name="broken-code"></a>Código roto

Considere el ejemplo de código siguiente:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Esto iniciará un NSInvalidArgumentException Objective-C en código nativo:

    NSInvalidArgumentException *** setObjectForKey: key cannot be nil

Y el seguimiento de pila tendrá un aspecto similar al siguiente:

    0   CoreFoundation          __exceptionPreprocess + 194
    1   libobjc.A.dylib         objc_exception_throw + 52
    2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
    3   libobjc.A.dylib         objc_msgSend + 102
    4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
    5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
    6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()

Marcos de 0-3 son marcos nativos y el desenredado de pila en tiempo de ejecución Objective-C _puede_ esos marcos de desenredo. En concreto, ejecutará cualquier Objective-C `@catch` o `@finally` cláusulas.

Sin embargo, el desenredado de pila de Objective-C es _no_ capaz de desenredado correctamente los marcos administrados (fotogramas 4-6), en que los fotogramas se desenreda, pero no se ejecutarán la lógica de excepción administrada.

Lo que significa que normalmente no es posible detectar estas excepciones en la siguiente manera:

```csharp
try {
    var dict = new NSMutableDictionary ();
    dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero);
} catch (Exception ex) {
    Console.WriteLine (ex);
} finally {
    Console.WriteLine ("finally");
}
```

Esto es porque el desenredado de pila de Objective-C no sabe acerca de los recursos administrados `catch` cláusula y no se la `finally` cláusula se ejecuta.

Cuando el ejemplo de código anterior _es_ eficaz, es porque Objective-C tiene un método de notificación de las excepciones no controladas de Objective-C, [`NSSetUncaughtExceptionHandler`][2], que Xamarin.iOS y el uso de Xamarin.Mac y en ese momento intenta convertir las excepciones Objective-C en las excepciones administradas.

## <a name="scenarios"></a>Escenarios

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Escenario 1: detectar excepciones de Objective-C con un controlador catch administrado

En el siguiente escenario, es posible detectar las excepciones de Objective-C utilizando managed `catch` controladores:

1. Se produce una excepción de C de objetivo.
2. El tiempo de ejecución Objective-C recorre la pila (pero la desenredo), buscando nativo `@catch` controlador que pueda controlar la excepción.
3. El tiempo de ejecución Objective-C no encuentra ninguno `@catch` controladores, las llamadas `NSGetUncaughtExceptionHandler`e invoca el controlador instalado por Xamarin.iOS/Xamarin.Mac.
4. Controlador Xamarin.iOS/Xamarin.Mac's convertirá la excepción de Objective-C en una excepción administrada y producirla. Desde Objective-C en tiempo de ejecución no desenredar la pila (solo ha avanzado lo), el marco actual es el mismo donde se produjo la excepción de Objective-C.

Otro problema se produce en este caso, porque el tiempo de ejecución Mono no sabe cómo desenredar marcos de Objective-C correctamente.

Cuando se llama a la devolución de llamada de excepción de Objective-C no detectada de Xamarin.iOS, la pila es similar al siguiente:

     0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "*** setObjectForKey: key cannot be nil")
     1 CoreFoundation           __handleUncaughtException + 809
     2 libobjc.A.dylib          _objc_terminate() + 100
     3 libc++abi.dylib          std::__terminate(void (*)()) + 14
     4 libc++abi.dylib          __cxa_throw + 122
     5 libobjc.A.dylib          objc_exception_throw + 337
     6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
     7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
     8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
     9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
    10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]

En este caso, los marcos administrados solo son marcos 8-10, pero se produce la excepción administrada en el marco 0. Esto significa que el tiempo de ejecución Mono debe desenredarse los marcos nativos 0-7, lo que hace que un problema equivalente para el problema descrito anteriormente: aunque el tiempo de ejecución Mono desenredar los marcos nativos, no se ejecutará cualquier Objective-C `@catch` o `@finally` cláusulas .

Ejemplo de código:

```objc
-(id) setObject: (id) object forKey: (id) key
{
    @try {
        if (key == nil)
            [NSException raise: @"NSInvalidArgumentException"];
    } @finally {
        NSLog (@"This will not be executed");
    }
}
```

Y el `@finally` cláusula no se ejecutará porque el tiempo de ejecución Mono que se desenrede este marco no saberlo.

Es una variación de este producir una excepción administrada en código administrado y, a continuación, cuando se desenreda a través de los marcos nativos para obtener el primer administrados `catch` cláusula:

```csharp
class AppDelegate : UIApplicationDelegate {
    public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
    {
        throw new Exception ("An exception");
    }
    static void Main (string [] args)
    {
        try {
            UIApplication.Main (args, null, typeof (AppDelegate));
        } catch (Exception ex) {
            Console.WriteLine ("Managed exception caught.");
        }
    }
}
```

Los recursos administrados `UIApplication:Main` método llamará nativo `UIApplicationMain` método y, a continuación, en iOS hará un lote de ejecución de código nativo antes de llamar finalmente a los recursos administrados `AppDelegate:FinishedLaunching` método con aún una gran cantidad de marcos nativos en la pila cuando es la excepción administrada se produce:

     0: TestApp                 ExceptionMarshaling.IOS.AppDelegate:FinishedLaunching (UIKit.UIApplication,Foundation.NSDictionary)
     1: TestApp                 (wrapper runtime-invoke) <Module>:runtime_invoke_bool__this___object_object (object,intptr,intptr,intptr) 
     2: libmonosgen-2.0.dylib   mono_jit_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
     3: libmonosgen-2.0.dylib   do_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
     4: libmonosgen-2.0.dylib   mono_runtime_invoke [inlined] mono_runtime_invoke_checked(method=<unavailable>, obj=<unavailable>, params=<unavailable>, error=0xbff45758)
     5: libmonosgen-2.0.dylib   mono_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>)
     6: libxamarin-debug.dylib  xamarin_invoke_trampoline(type=<unavailable>, self=<unavailable>, sel="application:didFinishLaunchingWithOptions:", iterator=<unavailable>), context=<unavailable>)
     7: libxamarin-debug.dylib  xamarin_arch_trampoline(state=0xbff45ad4)
     8: libxamarin-debug.dylib  xamarin_i386_common_trampoline
     9: UIKit                   -[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:]
    10: UIKit                   -[UIApplication _callInitializationDelegatesForMainScene:transitionContext:]
    11: UIKit                   -[UIApplication _runWithMainScene:transitionContext:completion:]
    12: UIKit                   __84-[UIApplication _handleApplicationActivationWithScene:transitionContext:completion:]_block_invoke.3124
    13: UIKit                   -[UIApplication workspaceDidEndTransaction:]
    14: FrontBoardServices      __37-[FBSWorkspace clientEndTransaction:]_block_invoke_2
    15: FrontBoardServices      __40-[FBSWorkspace _performDelegateCallOut:]_block_invoke
    16: FrontBoardServices      __FBSSERIALQUEUE_IS_CALLING_OUT_TO_A_BLOCK__
    17: FrontBoardServices      -[FBSSerialQueue _performNext]
    18: FrontBoardServices      -[FBSSerialQueue _performNextFromRunLoopSource]
    19: FrontBoardServices      FBSSerialQueueRunLoopSourceHandler
    20: CoreFoundation          __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
    21: CoreFoundation          __CFRunLoopDoSources0
    22: CoreFoundation          __CFRunLoopRun
    23: CoreFoundation          CFRunLoopRunSpecific
    24: CoreFoundation          CFRunLoopRunInMode
    25: UIKit                   -[UIApplication _run]
    26: UIKit                   UIApplicationMain
    27: TestApp                 (wrapper managed-to-native) UIKit.UIApplication:UIApplicationMain (int,string[],intptr,intptr)
    28: TestApp                 UIKit.UIApplication:Main (string[],intptr,intptr)
    29: TestApp                 UIKit.UIApplication:Main (string[],string,string)
    30: TestApp                 ExceptionMarshaling.IOS.Application:Main (string[])

Marcos 0-1 y 27 al 30 se administran, mientras que todos aquellos entre son nativos. Si desenreda Mono a través de estos marcos, ningún Objective-C `@catch` o `@finally` cláusulas que se va a ejecutar.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Escenario 2: no se puede detectar las excepciones de Objective-c.

En el siguiente escenario, es _no_ posible detectar las excepciones de Objective-C mediante administrado `catch` controladores porque se controló la excepción de Objective-C de otro modo:

1. Se produce una excepción de C de objetivo.
2. El tiempo de ejecución Objective-C recorre la pila (pero la desenredo), buscando nativo `@catch` controlador que pueda controlar la excepción.
3. El tiempo de ejecución Objective-C encuentra un `@catch` desenreda la pila de controlador y comienza a ejecutarse el `@catch` controlador.

Este escenario se encuentra normalmente en aplicaciones de Xamarin.iOS, porque en el subproceso principal suele haber código similar al siguiente:

``` objective-c
void UIApplicationMain ()
{
    @try {
        while (true) {
            ExecuteRunLoop ();
        }
    } @catch (NSException *ex) {
        NSLog (@"An unhandled exception occured: %@", exc);
        abort ();
    }
}

```

Esto significa que en el subproceso principal nunca hay realmente una excepción no controlada de Objective-C y, por tanto, nunca se llama a la devolución de llamada que convierte las excepciones de Objective-C en las excepciones administradas.

También es bastante común al depurar aplicaciones de Xamarin.Mac en una versión anterior de macOS que Xamarin.Mac admite ya inspeccionando la mayoría de los objetos de interfaz de usuario en el depurador intentará obtener las propiedades que corresponden a los selectores que no existen en la ejecución (de plataforma ya que Xamarin.Mac incluye compatibilidad para una versión posterior de macOS). Llamar a estos selectores generará un `NSInvalidArgumentException` ("no reconocido selector enviada a..."), lo que finalmente provoca el bloqueo del proceso.

En resumen, tener el tiempo de ejecución Objective-C o las tramas de desenredo en tiempo de ejecución Mono que no hayan sido programados para identificador puede dar lugar a comportamientos no definidos, como bloqueos, pérdidas de memoria y otros tipos de comportamientos impredecible (mis).

## <a name="solution"></a>Soluciones

En el 10 de Xamarin.iOS y Xamarin.Mac 2.10, hemos agregado compatibilidad para detectar excepciones administradas y Objective-C en cualquier límite nativo administrado y para convertir esa excepción en el otro tipo.

En pseudocódigo, tendrá un aspecto similar al siguiente:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

P/Invoke para objc_msgSend se interceptan y esto se denomina en su lugar:

``` objective-c
void
xamarin_dyn_objc_msgSend (id obj, SEL sel)
{
    @try {
        objc_msgSend (obj, sel);
    } @catch (NSException *ex) {
        convert_to_and_throw_managed_exception (ex);
    }
}
```

Y algo similar se realiza para el caso inverso (cálculo de referencias de las excepciones administradas a las excepciones de Objective-C).

Detectar excepciones en el límite nativo administrado no es gratuito, por lo que no siempre está habilitado de forma predeterminada:

- Xamarin.iOS/tvOS: la intercepción de excepciones de Objective-C está habilitada en el simulador.
- Xamarin.watchOS: intercepción se aplica en todos los casos, ya que permite el desenredo de tiempo de ejecución Objective-C administrado marcos confundirá el recolector de elementos no utilizados y puede hacer falta de respuesta o un bloqueo.
- Xamarin.Mac: intercepción de excepciones de Objective-C está habilitada para las compilaciones de depuración.

El [marcas de tiempo de compilación](#build_time_flags) sección explica cómo habilitar la intercepción cuando no está habilitada de forma predeterminada.

## <a name="events"></a>Eventos

Hay dos nuevos eventos que se producen una vez que se ha interceptado una excepción: `Runtime.MarshalManagedException` y `Runtime.MarshalObjectiveCException`.

Se pasan ambos eventos un `EventArgs` objeto que contiene la excepción original que se ha producido (el `Exception` propiedad) y un `ExceptionMode` propiedad para definir cómo se debe serializar la excepción.

El `ExceptionMode` propiedad se puede cambiar en el evento controlador para cambiar el comportamiento según cualquier procesamiento personalizado que se realiza en el controlador. Un ejemplo sería anular el proceso si se produce una excepción determinada.

Cambiar el `ExceptionMode` propiedad se aplica al evento único, no afecta a todas las excepciones que se interceptan en el futuro.

Están disponibles los siguientes modos:

- `Default`: El valor predeterminado varía según la plataforma. Es `ThrowObjectiveCException` si el catálogo global está en modo cooperativo (watchOS), y `UnwindNativeCode` en caso contrario (iOS y watchOS / macOS). El valor predeterminado se puede cambiar en el futuro.
- `UnwindNativeCode`: Este es el comportamiento anterior (sin definir). No está disponible al usar el catálogo global en modo cooperativo (que es la única opción en watchOS, por lo tanto, esto no es una opción válida en watchOS), pero es la opción predeterminada para todas las demás plataformas.
- `ThrowObjectiveCException`: Convertir la excepción administrada en una excepción de Objective-C y la excepción de C de objetivo. Este es el valor predeterminado en watchOS.
- `Abort`: Anular el proceso.
- `Disable`: Deshabilita la intercepción de excepciones, por lo que no tiene sentido establecer este valor en el controlador de eventos, pero una vez que se genera el evento es demasiado tarde para deshabilitarlo. En cualquier caso, si se establece, se comportará como `UnwindNativeCode`.

Para la serialización de excepciones de Objective-C para código administrado, están disponibles los siguientes modos:

- `Default`: El valor predeterminado varía según la plataforma. Es `ThrowManagedException` si el catálogo global está en modo cooperativo (watchOS), y `UnwindManagedCode` en caso contrario (iOS y tvOS y macOS). El valor predeterminado se puede cambiar en el futuro.
- `UnwindManagedCode`: Este es el comportamiento anterior (sin definir). No está disponible al usar el catálogo global en modo cooperativo (que es el modo de GC solo es válido en watchOS; por tanto, esto no es una opción válida en watchOS), pero es el valor predeterminado para todas las demás plataformas.
- `ThrowManagedException`: Convertir la excepción de Objective-C en una excepción administrada y producir la excepción administrada. Este es el valor predeterminado en watchOS.
- `Abort`: Anular el proceso.
- `Disable`: Deshabilita la intercepción de excepciones, por lo que no tiene sentido establecer este valor en caso de controlador, pero una vez que el evento se genera, que es demasiado tarde para deshabilitarlo. En cualquier caso si se establece, anulará el proceso.

Por lo tanto, para ver cada vez que se calculan las referencias de una excepción, puede hacer esto:

``` csharp
Runtime.MarshalManagedException += (object sender, MarshalManagedExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling managed exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
    
};
Runtime.MarshalObjectiveCException += (object sender, MarshalObjectiveCExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling Objective-C exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
};
```

<a name="build_time_flags" />

## <a name="build-time-flags"></a>Marcas de tiempo de compilación

Es posible pasar de las siguientes opciones para **mtouch** (para aplicaciones de Xamarin.iOS) y **mmp** (para aplicaciones de Xamarin.Mac), que determinará si está habilitada la interceptación de la excepción y establecer que la acción predeterminada debería producirse:

- `--marshal-managed-exceptions=`
  - `default`
  - `unwindnativecode`
  - `throwobjectivecexception`
  - `abort`
  - `disable`

- `--marshal-objectivec-exceptions=`
  - `default`
  - `unwindmanagedcode`
  - `throwmanagedexception`
  - `abort`
  - `disable`

Excepto para `disable`, estos valores son idénticos a los `ExceptionMode` valores que se pasan a la `MarshalManagedException` y `MarshalObjectiveCException` eventos.

El `disable` opción will _principalmente_ deshabilitar intercepción, salvo que todavía se podrán interceptar las excepciones cuando no agrega ninguna sobrecarga de ejecución. Los eventos de serialización se generan para estas excepciones, con el modo predeterminado que se va al modo predeterminado para la plataforma que se está ejecutando.

## <a name="limitations"></a>Limitaciones

Solo se interceptar P/Invokes a la `objc_msgSend` familia de funciones cuando se intenta detectar las excepciones de Objective-C. Esto significa que P/Invoke a otra función de C, que, a continuación, genera las excepciones de Objective-C, se ejecutará en el comportamiento antiguo y no definido (Esto se puede mejorar en el futuro).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>Vínculos relacionados

- [Excepción de serialización (ejemplo)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
