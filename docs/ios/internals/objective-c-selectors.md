---
title: Selectores de Objective-c.
ms.topic: article
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 3fa01d8f28dc1c86f9d4a8ee4d9fc0a9cdb8ee9c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="objective-c-selectors"></a>Selectores de Objective-c.

El lenguaje C de objetivo se basa en *selectores*. Un selector es un mensaje que puede enviarse a un objeto o un *clase*. [Xamarin.iOS](~/ios/internals/api-design/index.md) mapas selectores para métodos de instancia de la instancia y los selectores para métodos estáticos de clase.

A diferencia de las funciones normales de C (y como funciones miembro de C++), no se puede invocar directamente un selector utilizando [P/Invoke](http://www.mono-project.com/Dllimport).
(*Reserva*: en teoría se podría utilizar P/Invoke para las funciones miembro de C++ no virtual, pero deberá preocuparse por cada compilador la eliminación de nombres, que es una gran variedad de dolor mejor pasa por alto.) En su lugar, los selectores se envían a una clase de objetivo-C o la instancia mediante la [ `objc_msgSend` función](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend).

Es posible [esta guía útil acerca de la mensajería Objective-C](http://developer.apple.com/iphone/library/documentation/cocoa/conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html) útil.

<a name="Example" />

## <a name="example"></a>Ejemplo

Imagine que desea invocar la [-[NSString sizeWithFont:forWidth:lineBreakMode:]](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:) selector.
La declaración (de la documentación de Apple) es:

```csharp
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

-  El tipo de valor devuelto es *CGSize* para la API unificada.
-  El *fuente* parámetro es un [UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (y un tipo derivado (indirectamente) [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) ) y, por tanto, se asigna a [System.IntPtr](https://developer.xamarin.com/api/type/System.IntPtr/) .
-  El *ancho* parámetro, un *CGFloat* , se asigna a *nfloat*.
-  El *lineBreakMode* parámetro, un *UILineBreakMode* , ya se ha enlazado en Xamarin.iOS como el [UILineBreakMode enumeración](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/) .


Reunir todas las y queremos que una declaración de objc_msgSend que coincida con:

```csharp
CGSize objc_msgSend(IntPtr target, IntPtr selector,
    IntPtr font, nfloat width, UILineBreakMode mode);
```

Necesitamos declararla:

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

Si el valor devuelto se hubiera una estructura que era menos de 8 bytes de tamaño (al igual que el antiguo archivo `SizeF` usa antes de pasar a las API unificada) habría ejecutar el código anterior en el simulador pero se ha bloqueado en el dispositivo. Por lo que para llamar a un Selector que devuelve un valor de menos de 8 bits de tamaño, por lo tanto se *también* necesario declarar el `objc_msgSend_stret()` función:

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

A continuación, se volverían invocación:

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

1.  Obtiene el destino de selector.
1.  Obtener el nombre de selector.
1.  Llame a objc_msgSend() con los argumentos apropiados.


<a name="Selector_Targets" />

### <a name="selector-targets"></a>Destinos de selector

Un destino de selector es una instancia de objeto o una clase de C de objetivo. Si el destino es una instancia y procede de un tipo de Xamarin.iOS enlazado, use la [ObjCRuntime.INativeObject.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) propiedad.

Si el destino es una clase, use [ObjCRuntime.Class](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) para obtener una referencia a la instancia de clase, a continuación, use la [Class.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) propiedad.


<a name="Selector_Names" />

### <a name="selector-names"></a>Nombres de selector

Nombres de selector se muestran en la documentación de Apple. Por ejemplo, el [métodos de extensión de UIKit NSString](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html) incluyen [sizeWithFont:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:) y [sizeWithFont:forWidth:lineBreakMode:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:). Los dos puntos finales e incrustados son importantes y forman parte del nombre del selector.

Una vez que tenga un nombre de selector, puede crear un [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) instancia para él.


<a name="Calling_objc_msgSend()" />

### <a name="calling-objcmsgsend"></a>Al llamar a objc_msgSend()

 `objc_msgSend()` se utiliza para enviar un mensaje (selector) a un objeto. Esta familia de funciones tiene al menos dos argumentos necesarios: el destino de selector (una instancia o clase controlar), el selector de sí mismo y, a continuación, los argumentos necesarios para el selector determinado. La instancia y selector de argumento deben ser `System.IntPtr`, y todos los argumentos restantes deben coincidir con el tipo de espera el selector, por ejemplo, un `nint` para un `int`, o un `System.IntPtr` para todos los `NSObject`-tipos derivados. Use la [NSObject.Handle](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propiedad que se va a obtener un `IntPtr` para una instancia de tipo C de objetivo.

Lamentablemente, no hay más de un `objc_msgSend()` función.

Use [ `objc_msgSend_stret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) para selectores que devuelven una estructura.
Para mantener las cosas "interesante", en ARM Esto incluye tipos de todos los valores devueltos que son *no* una enumeración o cualquiera de los tipos integrados de C (char, short, int, long, float, double). En x86 (el simulador), esto es necesario que se usará para todas las estructuras de mayores tamaño de 8 bytes. (CGSize: utilizado en el ejemplo anterior, es de 8 bytes exactamente y, por tanto, no utilice `objc_msgSend_stret()` en el simulador.)

Use [ `objc_msgSend_fpret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_fpret) para valor de punto de selectores que devuelven flotante en x86 solo. Esta función no tienen que usarse en ARM; en su lugar, use `objc_msgSend()`.

El método main [objc_msgSend()](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend) función se utiliza para todos los demás selectores.

Una vez que haya decidido que `objc_msgSend()` funciones debe llamar a (y puede ser más de uno, por ejemplo, para el simulador y el dispositivo), puede utilizar una normal [ `[DllImport]` ](https://developer.xamarin.com/api/type/System.Runtime.InteropServices.DllImportAttribute/) método para declarar la función para invocaciones posteriores.

Un conjunto de rolecapability `objc_msgSend()` declaraciones pueden encontrarse en [ `ObjCRuntime.Messaging` ](https://developer.xamarin.com/api/type/ObjCRuntime.Messaging/).


<a name="ugly" />

## <a name="the-ugly"></a>El feo

Objective-C tiene tres tipos de `objc_msgSend` métodos: uno para invocaciones regulares, uno para las llamadas que devuelven valores de punto flotante (sólo x86) y otro para las llamadas que devuelven valores de las estructuras. Ésta incluye el sufijo `_stret` en `ObjCRuntime.Messaging`.

Si va a invocar un método que devolverá determinadas estructuras (las reglas se describen a continuación), debe invocar el método con el valor devuelto como primer parámetro como un valor de salida, similar al siguiente:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

Cosas que no resulte atractivo aquí y dado que la regla de cuando se debe usar _stret_ es diferente en X86 y ARM, si desea que sus enlaces para trabajar en el simulador y el dispositivo, debe agregar código que el siguiente aspecto:

```csharp
if (Runtime.Arch == Arch.DEVICE){
    PointF ret;

    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);

    return ret;
} else
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
```

### <a name="using-the-objcmsgsendstret-method"></a>Mediante el objc\_msgSend\_stret (método)

La regla para cuándo se debe utilizar el [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) son iguales que para **ARM**:

-  Cualquier tipo de valor que no es una enumeración o cualquiera de los tipos base para una enumeración (int, byte, short, long, double, float).


La regla para cuándo se debe utilizar el [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) son iguales que para **X86**:

-  Cualquier tipo de valor que no es una enumeración, o cualquiera de los tipos base para una enumeración (int, byte, short, long, double, float) y cuyo tamaño nativo es superior a 8 bytes.


### <a name="creating-your-own-signatures"></a>Crear sus propias firmas.

El siguiente [general](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) puede utilizarse para crear sus propias firmas, si es necesario.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de selectores](https://developer.xamarin.com/samples/mac-ios/Objective-C/Selectors/)
