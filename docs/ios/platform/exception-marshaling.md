---
title: Serialización en Xamarin.iOS (excepción)
description: Este documento describe cómo trabajar con excepciones nativas y administradas en una aplicación de Xamarin.iOS. Describe una solución para estos problemas y problemas que pueden producirse.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/05/2017
ms.openlocfilehash: dcf1074aacb6d139d107dac01fa86f459831d5f9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786748"
---
# <a name="exception-marshaling-in-xamarinios"></a>Serialización en Xamarin.iOS (excepción)

_Xamarin.iOS contiene nuevos eventos para ayudar a responder a las excepciones, especialmente en código nativo._

Código administrado y Objective-C tienen compatibilidad con excepciones de tiempo de ejecución (try/catch/finally (cláusulas)).

Sin embargo, sus implementaciones son diferentes, lo que significa que las bibliotecas en tiempo de ejecución (el tiempo de ejecución Mono y las bibliotecas en tiempo de ejecución de Objective-C) tienen problemas cuando tienen que administrar excepciones y, a continuación, ejecutar código escrito en otros lenguajes.

Este documento explican los problemas que pueden producirse y las posibles soluciones.

También incluye un proyecto de ejemplo, [excepción serialización](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), que puede utilizarse para probar diferentes escenarios y sus soluciones.

## <a name="problem"></a>Problema

El problema se produce cuando una excepción se produce y se detecta durante un período de desenredo de pila que no coincide con el tipo de excepción que se produjo.

Un ejemplo típico de esto para Xamarin.iOS o Xamarin.Mac es cuando una API nativa produce una excepción de C de objetivo y, a continuación, esa excepción Objective-C algún modo se debe controlar cuando el proceso de desenredo de pila alcanza un marco administrado.

La acción predeterminada es no hacer nada. Para el ejemplo anterior, esto significa que permite a los marcos de desenredado administrado Objective-C en tiempo de ejecución. Esto es problemático, ya que el tiempo de ejecución de C de objetivo no sabe cómo desenredar marcos administrados; Por ejemplo no ejecute cualquier `catch` o `finally` cláusulas en dicho marco.

### <a name="broken-code"></a>Código roto

Considere el ejemplo de código siguiente:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Esto producirá un NSInvalidArgumentException Objective-C en código nativo:

    NSInvalidArgumentException *** setObjectForKey: key cannot be nil

Y el seguimiento de pila tendrá un aspecto similar al siguiente:

    0   CoreFoundation          __exceptionPreprocess + 194
    1   libobjc.A.dylib         objc_exception_throw + 52
    2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
    3   libobjc.A.dylib         objc_msgSend + 102
    4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
    5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
    6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()

Marcos 0-3 son marcos nativos y el desenredado de pila en el tiempo de ejecución de C objetivo _puede_ las tramas de desenredado. En concreto, ejecutará cualquier Objective-C `@catch` o `@finally` cláusulas.

Sin embargo, es el responsable del desenredado Objective-C pila _no_ capaz de desenredo correctamente el administrado tramas (4-6), en que los fotogramas será desenredos, pero no se ejecutarán la lógica de excepción administrada.

Lo que significa que normalmente no es posible detectar estas excepciones de la siguiente manera:

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

Esto es porque el desenredado de pila Objective-C no conozca los recursos administrados `catch` cláusula y no se la `finally` cláusula se ejecuta.

Cuando el ejemplo de código anterior _es_ eficaz, es porque Objective-C tiene un método de notificación de las excepciones no controladas de Objective-C, [`NSSetUncaughtExceptionHandler`][2], que Xamarin.iOS y el uso de Xamarin.Mac y en ese momento intenta convertir las excepciones Objective-C en las excepciones administradas.

## <a name="scenarios"></a>Escenarios

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Escenario 1: detectar excepciones Objective-C con un controlador catch administrado

En el siguiente escenario, es posible detectar las excepciones de C objetivo usando administrado `catch` controladores:

1. Se produce una excepción de C de objetivo.
2. El tiempo de ejecución de C objetivo recorre la pila (pero no desenredo), buscando nativo `@catch` controlador que pueda controlar la excepción.
3. El tiempo de ejecución de C de objetivo no encontró ninguna `@catch` controladores, llamadas `NSGetUncaughtExceptionHandler`y se invoca el controlador instalado por Xamarin.iOS/Xamarin.Mac.
4. Controlador de Xamarin.iOS/Xamarin.Mac's se debe convertir la excepción Objective-C en una excepción administrada y la producen. Desde el objetivo-C en tiempo de ejecución no desenredar la pila (solo lo visto), el marco actual es el mismo donde se produjo la excepción de C de objetivo.

Otro problema se produce en este caso, porque el tiempo de ejecución Mono no sabe cómo desenredar Objective-C fotogramas correctamente.

Cuando se llama a devolución de llamada de excepción de C de objetivo no detectada de Xamarin.iOS, la pila es similar al siguiente:

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

En este caso, los marcos administrados solo son fotogramas 8-10, pero se produce la excepción administrada en el marco de 0. Esto significa que el tiempo de ejecución Mono debe desenredar los marcos nativos 0-7, lo que produce un problema equivalente para el problema descrito anteriormente: aunque el tiempo de ejecución Mono desenredará los marcos nativos, no ejecute cualquier Objective-C `@catch` o `@finally` cláusulas .

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

Y el `@finally` cláusula no se ejecutará porque el tiempo de ejecución de Mono que se desenrede este marco no saberlo.

Una variación de este consiste en producir una excepción administrada en código administrado y, a continuación, cuando se desenreda a través de los marcos nativos para obtener en el primer servidor administrado `catch` cláusula:

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

Los recursos administrados `UIApplication:Main` método llamará nativo `UIApplicationMain` método y, a continuación, iOS llevará a cabo una gran parte de la ejecución de código nativo antes de llamar finalmente a los recursos administrados de `AppDelegate:FinishedLaunching` método todavía a tener muchos de los marcos nativos en la pila una vez la excepción administrada se produce:

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

Marcos 0-1 y 27-30 se administran, mientras que todos los en entre son nativos. Si desenreda Mono a través de estos marcos, ningún objetivo-C `@catch` o `@finally` las cláusulas que se va a ejecutar.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Escenario 2: no se puede detectar las excepciones de Objective-c.

En el siguiente escenario es _no_ posible detectar las excepciones de C objetivo mediante administrado `catch` controladores porque se controló la excepción Objective-C de otro modo:

1. Se produce una excepción de C de objetivo.
2. El tiempo de ejecución de C objetivo recorre la pila (pero no desenredo), buscando nativo `@catch` controlador que pueda controlar la excepción.
3. El tiempo de ejecución de C objetivo busca un `@catch` controlador, se desenreda la pila y empieza a ejecutarse el `@catch` controlador.

Este escenario se encuentra normalmente en aplicaciones de Xamarin.iOS, porque en el subproceso principal normalmente hay código similar al siguiente:

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

Esto significa que en el subproceso principal realmente no hay nunca una excepción no controlada de Objective-C y, por tanto, nunca se llama a la devolución de llamada que convierte las excepciones de C de objetivo a las excepciones administradas.

Esto también es bastante común al depurar aplicaciones de Xamarin.Mac en una versión anterior de macOS Xamarin.Mac admite ya inspeccionando la mayoría de objetos de interfaz de usuario en el depurador intentará obtener las propiedades que corresponden a selectores que no existen en la ejecución (de plataforma Dado que Xamarin.Mac incluye compatibilidad con una versión posterior de Mac OS). Llamar a este tipo selectores generará un `NSInvalidArgumentException` ("selector no reconocido envía a..."), lo que finalmente hace que el proceso se bloquee.

En resumen, con el tiempo de ejecución de C de objetivo o los marcos de desenredado de Mono en tiempo de ejecución no se programan para identificador puede provocar comportamientos no definidos, como bloqueos, pérdidas de memoria y otros tipos de comportamientos impredecibles (mis).

## <a name="solution"></a>Soluciones

En 10 Xamarin.iOS y Xamarin.Mac 2.10, hemos agregado compatibilidad para capturar las excepciones administradas y Objective-C en cualquier límite nativo administrado y para convertir esa excepción en el otro tipo.

En seudocódigo, parece algo parecido a esto:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

P/Invoke para objc_msgSend se interceptan, y esto se llama en su lugar:

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

Y algo similar se realiza en el caso inverso (el cálculo de referencias de las excepciones administradas a las excepciones de Objective-C).

Detectar excepciones en el límite nativo administrado no es gratuito, por lo que no siempre está habilitado de forma predeterminada:

- Xamarin.iOS/tvOS: intercepción de excepciones Objective C está habilitada en el simulador.
- Xamarin.watchOS: intercepción no se aplica en todos los casos, ya que permite el desenredo de en tiempo de ejecución de C objetivo administrado fotogramas confundirá el recolector de elementos no utilizados y puede hacer una falta de respuesta o bloqueo.
- Xamarin.Mac: intercepción de excepciones Objective C está habilitada para las compilaciones de depuración.

El [marcas de tiempo de compilación](#build_time_flags) sección explica cómo habilitar la interceptación cuando no está habilitada de forma predeterminada.

## <a name="events"></a>Eventos

Hay dos nuevos eventos que se produzcan una vez que se ha interceptado una excepción: `Runtime.MarshalManagedException` y `Runtime.MarshalObjectiveCException`.

Los dos eventos se pasan un `EventArgs` objeto que contiene la excepción original que se produjo (la `Exception` propiedad) y un `ExceptionMode` propiedad para definir cómo se debería serializar la excepción.

El `ExceptionMode` propiedad se puede cambiar en el evento controlador para cambiar el comportamiento según cualquier procesamiento personalizado que se realiza en el controlador. Un ejemplo sería el proceso se anulará si se produce una excepción de determinados.

Cambiar el `ExceptionMode` propiedad se aplica al evento único, no afecta a las excepciones que se interceptan en el futuro.

Están disponibles los siguientes modos:

- `Default`: El valor predeterminado varía según la plataforma. Es `ThrowObjectiveCException` si el catálogo global está en modo cooperativo (watchOS), y `UnwindNativeCode` en caso contrario (iOS / watchOS / macOS). El valor predeterminado se puede cambiar en el futuro.
- `UnwindNativeCode`: Se trata el comportamiento anterior (undefined). No está disponible al usar el catálogo global en modo cooperativo (que es la única opción en watchOS; por lo tanto, esto no es una opción válida en watchOS), pero es la opción predeterminada para todas las demás plataformas.
- `ThrowObjectiveCException`: Convertir la excepción administrada en una excepción de C de objetivo y producir la excepción de C de objetivo. Este es el valor predeterminado en watchOS.
- `Abort`: Permite anular el proceso.
- `Disable`: Deshabilita la intercepción de excepciones, por lo que no tiene sentido establecer este valor en el controlador de eventos, pero una vez que se genera el evento es demasiado tarde para deshabilitarlo. En cualquier caso, si se establece, se comportarán como `UnwindNativeCode`.

Para una serialización excepciones Objective-C a código administrado, están disponibles los siguientes modos:

- `Default`: El valor predeterminado varía según la plataforma. Es `ThrowManagedException` si el catálogo global está en modo cooperativo (watchOS), y `UnwindManagedCode` en caso contrario (iOS / tvOS / macOS). El valor predeterminado se puede cambiar en el futuro.
- `UnwindManagedCode`: Se trata el comportamiento anterior (undefined). No está disponible al usar el catálogo global en modo cooperativo (que es el modo de GC solo es válido en watchOS, lo que esto no es una opción válida en watchOS), pero es el valor predeterminado para todas las demás plataformas.
- `ThrowManagedException`: Convertir la excepción Objective-C en una excepción administrada y producir la excepción administrada. Este es el valor predeterminado en watchOS.
- `Abort`: Permite anular el proceso.
- `Disable`: Deshabilita la intercepción de excepciones, por lo que no tiene sentido establecer este valor en el evento se genera el controlador, pero una vez el evento, es demasiado tarde para deshabilitarlo. En cualquier caso si establece, se anulará el proceso.

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

Es posible pasar las siguientes opciones para **mtouch** (para aplicaciones de Xamarin.iOS) y **mmp** (para aplicaciones de Xamarin.Mac), que determinará si está habilitada la interceptación de excepción y establecer la acción predeterminada que se realizarán:

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

El `disable` opción will _principalmente_ deshabilitar intercepción, pero todavía se podrá interceptar las excepciones cuando no agrega ninguna sobrecarga de ejecución. Todavía se provocan los eventos de cálculo de referencias de estas excepciones, con el modo predeterminado que se va al modo predeterminado para la plataforma que se está ejecutando.

## <a name="limitations"></a>Limitaciones

Solo se interceptan P/Invokes a la `objc_msgSend` familia de funciones al intentar detectar excepciones de C de objetivo. Esto significa que P/Invoke a otra función de C, que, a continuación, inicia las excepciones de Objective-C, seguirá ejecutándose en el comportamiento anterior y no definido (Esto puede mejorar en el futuro).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>Vínculos relacionados

- [Serialización de excepción (ejemplo)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
