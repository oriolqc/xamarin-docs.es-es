---
title: Selectores de Objective-C en Xamarin.iOS
description: Este documento describe cómo interactuar con los selectores de Objective-C desde C#. Describe cómo invocar los selectores y las consideraciones técnicas que deben tenerse en cuenta al hacerlo.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/12/2017
ms.openlocfilehash: 15db59945f482728f760006095e294bc5628c8bd
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870183"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Selectores de Objective-C en Xamarin.iOS

Se basa en el lenguaje de Objective-C *selectores*. Un selector es un mensaje que se puede enviar a un objeto o un *clase*. [Xamarin.iOS](~/ios/internals/api-design/index.md) mapas selectores a los métodos de instancia de la instancia y los selectores de métodos estáticos de clase.

A diferencia de las funciones de C normales (y como funciones miembro de C++), no se puede invocar directamente un selector utilizando [P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/) en su lugar, los selectores se envían a una clase de Objective-C o la instancia mediante el [`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
función.

Para obtener más información acerca de los mensajes en Objective-C, eche un vistazo a Apple [trabajar con objetos](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) guía.

## <a name="example"></a>Ejemplo

Suponga que desea invocar el [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
Selector de [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring).
La declaración (de la documentación de Apple) es:

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Esta API tiene las siguientes características:

- El tipo de valor devuelto es `CGSize` para la API unificada.
- El `font` parámetro es un [UIFont](xref:UIKit.UIFont) (y un tipo derivado (indirectamente) [NSObject](xref:Foundation.NSObject)y se asigna a [System.IntPtr](xref:System.IntPtr).
- El `width` parámetro, un `CGFloat`, se asigna a `nfloat`.
- El `lineBreakMode` parámetro, un [ `UILineBreakMode` ](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc), ya se ha enlazado en Xamarin.iOS como la [`UILineBreakMode`](xref:UIKit.UILineBreakMode)
Enumeración.

En resumen, la `objc_msgSend` debe coincidir con la declaración:

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

Declare como sigue:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Para llamar a este método, use código como el siguiente:

```csharp
NSString target = ...
Selector selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont font = ...
nfloat width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, 
    selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode
);
```

El valor devuelto fue una estructura que era menor que 8 bytes de tamaño (al igual que el antiguo `SizeF` usa antes de cambiar a las API unificada), habría ejecutar el código anterior en el simulador, pero se ha bloqueado en el dispositivo. Para llamar a un selector que devuelve un valor de menos de 8 bits de tamaño, declare el `objc_msgSend_stret` función:

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Para llamar a este método, use código como el siguiente:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, 
        selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode
    );
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode
    );
```

## <a name="invoking-a-selector"></a>Invocar un selector

Invocar un selector consta de tres pasos:

1. Obtiene el destino del selector.
2. Obtiene el nombre de selector.
3. Llamar a `objc_msgSend` con los argumentos adecuados.

### <a name="selector-targets"></a>Destinos de selector

Un destino de selector es una instancia de objeto o una clase de C de objetivo. Si el destino es una instancia y procede de un tipo de Xamarin.iOS enlazado, use el [ `ObjCRuntime.INativeObject.Handle` ](xref:ObjCRuntime.INativeObject.Handle) propiedad.

Si el destino es una clase, use [ `ObjCRuntime.Class` ](xref:ObjCRuntime.Class) para obtener una referencia a la instancia de clase, a continuación, use el [ `Class.Handle` ](xref:ObjCRuntime.Class.Handle) propiedad.

### <a name="selector-names"></a>Nombres de selector

Nombres de los selectores se muestran en la documentación de Apple. Por ejemplo, [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring?language=objc) incluye [ `sizeWithFont:` ](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) y [ `sizeWithFont:forWidth:lineBreakMode:` ](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) selectores. Los dos puntos finales y embedded forman parte del nombre de selector y no se pueden omitir.

Una vez que tenga un nombre de selector, puede crear un [ `ObjCRuntime.Selector` ](xref:ObjCRuntime.Selector) instancia para él.

### <a name="calling-objcmsgsend"></a>Una llamada a objc_msgSend

`objc_msgSend` envía un mensaje (selector) a un objeto. Esta familia de funciones tarda al menos dos argumentos necesarios: el destino del selector (una instancia o clase controlar), el selector de sí mismo y los argumentos necesarios para el selector. Los argumentos de la instancia y el selector deben ser `System.IntPtr`, y todos los argumentos restantes deben coincidir con el tipo que espera el selector, por ejemplo un `nint` para un `int`, o un `System.IntPtr` para todos los `NSObject`-tipos derivados. Use el [`NSObject.Handle`](xref:Foundation.NSObject.Handle)
propiedad que se va a obtener un `IntPtr` para una instancia de tipo C de objetivo.

Hay más de un `objc_msgSend` función:

- Use [ `objc_msgSend_stret` ](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) para selectores que devuelven un struct. En ARM, esto incluye devuelven tipos que no es una enumeración o cualquiera de los tipos integrados de C (`char`, `short`, `int`, `long`, `float`, `double`). En x86 (el simulador), este método debe utilizarse para todas las estructuras de mayores tamaño de 8 bytes (`CGSize` tiene 8 bytes y no usa `objc_msgSend_stret` en el simulador). 
- Use [ `objc_msgSend_fpret` ](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) para selectores que devuelven un flotante del punto de valor en x86 solo. Esta función no es necesario que se usará en ARM; en su lugar, use `objc_msgSend`. 
- El método main [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) función se utiliza para todos los demás selectores.

Una vez que haya decidido que `objc_msgSend` o funciones que se debe llamar a (simulador y el dispositivo es posible que cada uno de ellos requieren un método diferente), puede usar un normal [ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute) método para declarar la función para invocaciones posteriores.

Un conjunto de prefabricados `objc_msgSend` declaraciones pueden encontrarse en `ObjCRuntime.Messaging`.

## <a name="different-invocations-on-simulator-and-device"></a>Distintas invocaciones de simulador y dispositivo

Como se describió anteriormente, Objective-C tiene tres tipos de `objc_msgSend` métodos: uno para las invocaciones regulares, uno para las llamadas que devuelven valores de punto flotante (solo x86) y otro para las llamadas que devuelven valores de las estructuras. El último incluye el sufijo `_stret` en `ObjCRuntime.Messaging`.

Si invoca un método que devolverá ciertas estructuras (reglas que se describen a continuación), debe invocar el método con el valor devuelto como primer parámetro como un `out` valor:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

La regla de cuándo usar el `_stret_` método difiere en x86 y ARM.
Si desea que los enlaces para que funcione en el simulador y el dispositivo, agregue código como el siguiente:

```csharp
if (Runtime.Arch == Arch.DEVICE)
{
    PointF ret;
    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);
    return ret;
} 
else
{
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
}
```

### <a name="using-the-objcmsgsendstret-method"></a>Mediante el método objc_msgSend_stret

Al compilar para ARM, utilice el [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para cualquier tipo de valor que no es una enumeración o cualquiera de los tipos base para una enumeración (`int`, `byte`, `short`, `long`, `double`, `float`).

Al compilar para x86, usar [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para cualquier tipo de valor que no es una enumeración o cualquiera de los tipos base para una enumeración (`int`, `byte`, `short`, `long`, `double`, `float`) y cuyo tamaño nativo es mayor que 8 bytes.

### <a name="creating-your-own-signatures"></a>Crear sus propias firmas

La siguiente [gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) puede utilizarse para crear sus propias firmas, si es necesario.

## <a name="related-links"></a>Vínculos relacionados

- [Los selectores de Objective-C](https://developer.xamarin.com/samples/mac-ios/Objective-C/) ejemplo
