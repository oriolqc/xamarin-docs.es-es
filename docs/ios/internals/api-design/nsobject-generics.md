---
title: Subclases genéricas de NSObject en Xamarin.iOS
description: Este documento describe cómo crear crear subclases genéricas de NSObject. Examina lo que puede y no se puede realizar, analiza al registrador estático y echa un vistazo a rendimiento.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 39faa4670b17cdf4853bfe24ff104765ca541b9f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106226"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Subclases genéricas de NSObject en Xamarin.iOS

## <a name="using-generics-with-nsobjects"></a>Utilizar genéricos con NSObjects

Comenzando con Xamarin.iOS 7.2.1 puede usar tipos genéricos en las subclases de `NSObject` (por ejemplo [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)).

Ahora puede crear las clases genéricas, como la siguiente:

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Dado que los objetos de esa subclase `NSObject` están registrados con el tiempo de ejecución Objective-C, existen algunas limitaciones sobre lo que es posible con subclases genéricas de `NSObject` tipos.
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Consideraciones para subclases genéricas de NSObject

Este documento detallan las limitaciones en la compatibilidad limitada con subclases genéricas de `NSObjects` introducidos con Xamarin.iOS 7.2.1.
    
### <a name="generic-type-arguments-in-member-signatures"></a>Argumentos de tipo genéricos en firmas de miembro

Todos los argumentos de tipo genérico en una signatura de miembro expuesto a Objective-C deben tener un `NSObject` restricción.

**Buena**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: el parámetro de tipo genérico es un `NSObject`, por lo que la firma del selector de `myMethod:` se pueden exponer de forma segura y Objective-C (siempre será `NSObject` o una subclase de ella).

**Incorrecta**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: no es posible crear una firma de Objective-C para los miembros exportados que puede llamar a código de Objective-C, ya que la firma podría diferir según el tipo exacto del tipo genérico `T`.

**Buena**:

```csharp
class Generic<T> : NSObject
{
    T storage;

    [Export ("myMethod:")]
    public void MyMethod (NSObject value)
    {
    }
}
```

**Motivo**: es posible que haya sin restricciones argumentos de tipo genérico, siempre no forman parte de la firma del miembro exportado.

**Buena**:

```csharp
class Generic<T, U> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
        Console.WriteLine (typeof (U));
    }
}
```

**Motivo**: el `T` parámetro en Objective C exporta `MyMethod` esté restringido a ser un `NSObject`, el tipo sin restricciones `U` no forma parte de la firma.
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>Creaciones de instancias de tipos genéricos desde Objective-C

No se permite la creación de instancias de tipos genéricos desde Objective-C. Esto suele ocurrir cuando se usa un tipo administrado en un xib.

Tenga en cuenta esta definición de clase, que expone un constructor que toma un `IntPtr` (la forma de Xamarin.iOS de construir un C# objeto a partir de una instancia de Objective-C nativa):
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Mientras que la construcción anterior está bien, en tiempo de ejecución, se producirá una excepción en si trata de Objective-C crear una instancia de ella.

Se trata de sucede porque Objective-C no tiene ningún concepto de tipos genéricos, y no puede especificar el tipo exacto genérico para crear.

Este problema puede solucionarse mediante la creación de una subclase especializada del tipo genérico.   Por ejemplo:
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

Ahora hay ninguna ambigüedad, la clase `GenericUIView` puede usarse en xib.

## <a name="no-support-for-generic-methods"></a>No se admite para métodos genéricos

### <a name="generic-methods-are-not-allowed"></a>No se permiten métodos genéricos.

No se compilará el código siguiente:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**Motivo**: no se permite porque Xamarin.iOS no sabe qué tipo para usar para el argumento de tipo `T` cuando el método se invoca desde Objective-C.

Una alternativa es crear un método especializado y exportarlas en su lugar:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyUIViewMethod (UIView argument)
    {
        MyMethod<UIView> (argument);
    }
    public void MyMethod<T> (T argument)
    {
    }
}
```

### <a name="no-exported-static-members-allowed"></a>No hay miembros estáticos exportados permitidos

No puede exponer un miembros estáticos y Objective-C si está hospedado dentro de una subclase genérica de `NSObject`.

Ejemplo de un escenario no admitido:

```csharp
class Generic<T> : NSObject where T : NSObject
{
    [Export ("myMethod:")]
    public static void MyMethod ()
    {
    }

    [Export ("myProperty")]
    public static T MyProperty { get; set; }
}
```

**Motivo:** al igual que los métodos genéricos, las necesidades de tiempo de ejecución de Xamarin.iOS para poder saber qué tipo usar para el argumento de tipo genérico T.

Por ejemplo los miembros que se usa la propia instancia (, ya que nunca habrá una instancia genérico<T>, siempre será genérico<SomeSpecificClass>), pero esta información para los miembros estáticos no está presente.

Tenga en cuenta que esto se aplica incluso si el miembro en cuestión no utiliza el argumento de tipo T de ninguna manera.

En este caso, la alternativa es crear una subclase especializada:

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIVIewProperty {
        get { return MyProperty; }
        set { MyProperty = value; }
    }
}

class Generic<T> : NSObject where T : NSObject
{
    public static void MyMethod () {}
    public static T MyProperty { get; set; }
}
```

### <a name="requires-new-static-registrar"></a>Requiere a Registrar estática nueva

La compatibilidad de genéricos requiere el nuevo [sistema de registro](~/ios/internals/registrar.md).

Si intenta usar el antiguo sistema de registro antiguos mostrará advertencias cuando encuentra tipos genéricos (además de para no generar código correcto, lo que produce un comportamiento indefinido).
    
## <a name="performance"></a>Rendimiento

El registrador estático no puede resolver un miembro exportado en un tipo genérico en tiempo de compilación que lo hace, se va a buscar en tiempo de ejecución. Esto significa que invocar al método desde Objective-C es ligeramente más lenta que la invocación de miembros de clases no genéricas.

