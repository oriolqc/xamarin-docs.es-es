---
title: Selectores de Objective-C en Xamarin.iOS
description: Este documento describe cómo interactuar con los selectores de Objective-C desde C#. Describe cómo invocar los selectores y las consideraciones técnicas que deben tenerse en cuenta al hacerlo.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: d8708e518e967083bcfb95c88f8c20fddbb44b53
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998799"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Selectores de Objective-C en Xamarin.iOS

Se basa en el lenguaje de Objective-C *selectores*. Un selector es un mensaje que se puede enviar a un objeto o un *clase*. [Xamarin.iOS](~/ios/internals/api-design/index.md) mapas selectores a los métodos de instancia de la instancia y los selectores de métodos estáticos de clase.

A diferencia de las funciones de C normales (y como funciones miembro de C++), no se puede invocar directamente un selector utilizando [P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
(*Reservar*: en teoría podría usar P/Invoke para las funciones miembro de C++ no virtual, pero deberá preocuparse por cada compilador daños en los nombres, que es un mundo de mejor omite el dolor.) En su lugar, los selectores se envían a una clase de Objective-C o la instancia mediante el [ `objc_msgSend` función](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend).

Es posible [esta guía útil sobre la mensajería de Objective-C](http://developer.apple.com/iphone/library/documentation/cocoa/conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html) útil.

<a name="Example" />

## <a name="example"></a>Ejemplo

Suponga que desea invocar la [-[NSString sizeWithFont:forWidth:lineBreakMode:]](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:) selector.
La declaración (de la documentación de Apple) es:

```csharp
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

-  El tipo de valor devuelto es *CGSize* para la API unificada.
-  El *fuente* parámetro es un [UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (y un tipo derivado (indirectamente) [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) ) y, por tanto, se asigna a [System.IntPtr](xref:System.IntPtr) .
-  El *ancho* parámetro, un *CGFloat* , se asigna a *nfloat*.
-  El *lineBreakMode* parámetro, un *UILineBreakMode* , ya se ha enlazado en Xamarin.iOS como el [UILineBreakMode enumeración](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/) .


Juntar todo, y queremos una declaración objc_msgSend que coincide con:

```csharp
CGSize objc_msgSend(IntPtr target, IntPtr selector,
    IntPtr font, nfloat width, UILineBreakMode mode);
```

Debemos declararlo:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

Una vez declarado, que podemos llamar una vez que tenemos los parámetros adecuados:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode);
```

El valor devuelto fue una estructura que era menor que 8 bytes de tamaño (al igual que el antiguo `SizeF` usa antes de cambiar a las API unificada) habría ejecutar el código anterior en el simulador, pero se ha bloqueado en el dispositivo. Por lo que para llamar a un Selector que devuelve un valor de menos de 8 bits de tamaño, por lo tanto nos *también* debe declarar el `objc_msgSend_stret()` función:

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

A continuación, se convertiría en invocación:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode);
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode);
```


<a name="Invoking_a_Selector" />

## <a name="invoking-a-selector"></a>Invocar un Selector

Invocar un selector consta de tres pasos:

1.  Obtiene el destino del selector.
1.  Obtiene el nombre de selector.
1.  Llame a objc_msgSend() con los argumentos adecuados.


<a name="Selector_Targets" />

### <a name="selector-targets"></a>Destinos de selector

Un destino de selector es una instancia de objeto o una clase de C de objetivo. Si el destino es una instancia y procede de un tipo de Xamarin.iOS enlazado, use el [ObjCRuntime.INativeObject.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) propiedad.

Si el destino es una clase, use [ObjCRuntime.Class](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) para obtener una referencia a la instancia de clase, a continuación, use el [Class.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) propiedad.


<a name="Selector_Names" />

### <a name="selector-names"></a>Nombres de selector

Los nombres de selector aparecen dentro de la documentación de Apple. Por ejemplo, el [métodos de extensión de UIKit NSString](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html) incluyen [sizeWithFont:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:) y [sizeWithFont:forWidth:lineBreakMode:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:). Los dos puntos incrustados y finales son importantes y forman parte del nombre de selector.

Una vez que tenga un nombre de selector, puede crear un [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) instancia para él.


<a name="Calling_objc_msgSend()" />

### <a name="calling-objcmsgsend"></a>Una llamada a objc_msgSend()

 `objc_msgSend()` se usa para enviar un mensaje (selector) a un objeto. Esta familia de funciones tarda al menos dos argumentos necesarios: el destino del selector (una instancia o clase controlar), el selector de sí mismo y, a continuación, los argumentos necesarios para el selector determinado. Los argumentos de la instancia y el selector deben ser `System.IntPtr`, y todos los argumentos restantes deben coincidir con el tipo que espera el selector, por ejemplo, un `nint` para un `int`, o un `System.IntPtr` para todos los `NSObject`-tipos derivados. Use la [NSObject.Handle](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propiedad para obtener un `IntPtr` para una instancia de tipo C de objetivo.

Lamentablemente, no hay más de uno `objc_msgSend()` función.

Use [ `objc_msgSend_stret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) para selectores que devuelven una estructura.
Mantener las cosas "interesante", en ARM Esto incluye devuelven tipos que son *no* una enumeración o cualquiera de los tipos integrados de C (char, short, int, long, float, double). En x86 (el simulador), esto debe utilizarse para todas las estructuras de mayores tamaño de 8 bytes. (CGSize--utilizado en el ejemplo anterior, es exactamente de 8 bytes y, por tanto, no use `objc_msgSend_stret()` en el simulador.)

Use [ `objc_msgSend_fpret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_fpret) para selectores que devuelven un flotante del punto de valor en x86 solo. Esta función no es necesario que se usará en ARM; en su lugar, use `objc_msgSend()`.

El método main [objc_msgSend()](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend) función se utiliza para todos los demás selectores.

Una vez que haya decidido que `objc_msgSend()` o funciones se debe llamar a (y puede ser más de uno, por ejemplo, para el simulador y dispositivo), puede usar un normal [ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute) método para declarar la función para invocaciones posteriores.

Un conjunto de prefabricados `objc_msgSend()` declaraciones pueden encontrarse en [ `ObjCRuntime.Messaging` ](https://developer.xamarin.com/api/type/ObjCRuntime.Messaging/).


<a name="ugly" />

## <a name="the-ugly"></a>Lo feo

Objective-C tiene tres tipos de `objc_msgSend` métodos: uno para las invocaciones regulares, uno para las llamadas que devuelven valores de punto flotante (solo x86) y otro para las llamadas que devuelven valores de las estructuras. El último incluye el sufijo `_stret` en `ObjCRuntime.Messaging`.

Si invoca un método que devolverá ciertas estructuras (las reglas se describen a continuación), debe invocar el método con el valor devuelto como primer parámetro como valor out, similar al siguiente:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

Las cosas se ponen feas aquí y porque la regla para cuándo se deben utilizar _stret_ es diferente en X86 y ARM, si desea que los enlaces para trabajar en el simulador y el dispositivo, deberá agregar código similar al siguiente:

```csharp
if (Runtime.Arch == Arch.DEVICE){
    PointF ret;

    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);

    return ret;
} else
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
```

### <a name="using-the-objcmsgsendstret-method"></a>Mediante el objc\_msgSend\_stret (método)

La regla de cuándo usar el [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) son similares a esto para **ARM**:

-  Cualquier tipo que no es una enumeración o cualquiera de los tipos bases para una enumeración de valor (int, byte, short, long, double, float).


La regla de cuándo usar el [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) son similares a esto para **X86**:

-  Cualquier tipo que no es una enumeración, o cualquiera de los tipos bases para una enumeración de valor (int, byte, short, long, double, float) y cuyo tamaño nativo es mayor que 8 bytes.


### <a name="creating-your-own-signatures"></a>Crear sus propias firmas.

La siguiente [gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) puede utilizarse para crear sus propias firmas, si es necesario.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de selectores](https://developer.xamarin.com/samples/mac-ios/Objective-C/Selectors/)
