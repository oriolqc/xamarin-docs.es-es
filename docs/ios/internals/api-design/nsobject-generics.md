---
title: "Subclases genéricas de NSObject"
ms.topic: article
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 76c35cfb993bade324b25f86e75db7eb028a7399
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="generic-subclasses-of-nsobject"></a>Subclases genéricas de NSObject

## <a name="using-generics-with-nsobjects"></a>Uso de genéricos con NSObjects

A partir de Xamarin.iOS 7.2.1 puede utilizar genéricos en subclases de `NSObject` (por ejemplo [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)).

Ahora puede crear clases genéricas, como esta:

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Ya que los objetos que subclase `NSObject` se registran con el tiempo de ejecución de C objetivo existen algunas limitaciones en cuanto a lo que es posible con las subclases genéricas de `NSObject` tipos.
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Consideraciones para que las subclases genéricas de NSObject

Este documento detallan las limitaciones de la compatibilidad limitada con subclases genéricas de `NSObjects` introducidos con Xamarin.iOS 7.2.1.
    
### <a name="generic-type-arguments-in-member-signatures"></a>Argumentos de tipo genéricos en firmas de miembro

Todos los argumentos de tipo genérico en una firma de miembro expuestos a Objective-C deben tener un `NSObject` restricción.

**Good**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: el parámetro de tipo genérico es un `NSObject`, por lo que la firma de selector para `myMethod:` pueden exponerse de forma segura a Objective-C (siempre será `NSObject` o una subclase de ella).

**Bad**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: no es posible crear una firma Objective-C para los miembros exportados que puede llamar código Objective-C ya que la firma sería diferentes según el tipo exacto del tipo genérico `T`.

**Good**:

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

**Motivo**: es posible ha sin restricciones argumentos de tipo genérico siempre que no toman parte de la firma de miembros exportados.

**Good**:

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

**Motivo**: el `T` parámetro en Objective-C exporta `MyMethod` esté restringido a ser un `NSObject`, el tipo sin restricciones `U` no forma parte de la firma.
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>Creaciones de instancias de tipos genéricos desde Objective-c.

No se permite la creación de instancias de tipos genéricos de C de objetivo. Esto suele ocurrir cuando se usa un tipo administrado en un xib.

Tenga en cuenta esta definición de clase, que expone un constructor que toma un `IntPtr` (la forma de Xamarin.iOS de construir un objeto de C# desde una instancia de Objective-C nativo):
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Mientras la construcción anterior es correcto, en tiempo de ejecución, se producirá una excepción en si Objective-C intenta crear una instancia del mismo.

Esto es ocurre porque Objective-C no tiene ningún concepto de tipos genéricos y no puede especificar el tipo exacto genérico para crear.

Puede que este problema puede solucionarse mediante la creación de una subclase especializada del tipo genérico.   Por ejemplo:
    
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

Ahora hay ninguna ambigüedad ya, la clase `GenericUIView` pueden utilizarse en xibs.

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

**Motivo**: esto no está permitido porque Xamarin.iOS no sabe cuál es el tipo que se utilizará para el argumento de tipo `T` cuando se invoca el método de C de objetivo.

Una alternativa es crear un método especializado y exportar en su lugar:

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

No puede exponer un miembros estáticos para Objective-c. Si está hospedado dentro de una subclase genérica de `NSObject`.

Ejemplo de un escenario no compatible:

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

**Motivo:** igual que los métodos genéricos, las necesidades de tiempo de ejecución de Xamarin.iOS para que pueda saber qué tipo que se utilizará para el argumento de tipo genérico T.

Por ejemplo los miembros que se usa la propia instancia (ya que nunca habrá una instancia genérica<T>, siempre será genérico<SomeSpecificClass>), pero esta información para los miembros estáticos no está presente.

Tenga en cuenta que esto se aplica incluso si el miembro en cuestión no usa el argumento de tipo T de ninguna manera.

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

La compatibilidad de genéricos requiere la nueva [sistema de registro](~/ios/internals/registrar.md).

Si intenta usar el antiguo sistema heredado registro le mostrará advertencias cuando encuentra tipos genéricos (además de para no generar código correcto, lo que produce un comportamiento no definido).
    
## <a name="performance"></a>Rendimiento

El registrador de estático no puede resolver un miembro exportado en un tipo genérico en tiempo de compilación tal y como lo normal, se va a buscar en tiempo de ejecución. Esto significa que invocar al método de Objective-C es ligeramente más lenta que la invocación de miembros de clases no genéricas.

