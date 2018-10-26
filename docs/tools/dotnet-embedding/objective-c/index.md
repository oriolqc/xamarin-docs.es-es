---
title: Soporte técnico de Objective-c.
description: Este documento proporciona una descripción de la compatibilidad para Objective-C en la inserción de. NET. Describe el recuento de referencias automático, NSString, protocolos, NSObject protocolo, excepciones y mucho más.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 48caa70cf2bd408f8afc673b400f7d5a4369e108
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110731"
---
# <a name="objective-c-support"></a>Soporte técnico de Objective-c.

## <a name="specific-features"></a>Características específicas

La generación de Objective-C tiene algunas características especiales que debe tener en cuenta.

### <a name="automatic-reference-counting"></a>Recuento de referencias automático

El uso de automático referencia recuento (ARC) es **requiere** para llamar a los enlaces generados. Se debe compilar el proyecto con una biblioteca basada en inserción de .NET con `-fobjc-arc`.

### <a name="nsstring-support"></a>Compatibilidad con NSString

Las API que exponen `System.String` tipos se convierten en `NSString`. Esto facilita la administración de memoria que cuando se trabaja con `char*`.

### <a name="protocols-support"></a>Compatibilidad con protocolos

Interfaces administradas se convierten en los protocolos de Objective-C donde todos los miembros son `@required`.

### <a name="nsobject-protocol-support"></a>Soporte de protocolo de NSObject

De forma predeterminada, el hash de forma predeterminada y la igualdad entre .NET y el tiempo de ejecución Objective-C se supone que se pueda intercambiar, mientras comparten una semántica similar.

Cuando se invalida un tipo administrado `Equals(Object)` o `GetHashCode`, generalmente significa que el comportamiento predeterminado (. NET) no era suficiente; esto implica que es probable que el comportamiento de Objective-C predeterminado no es suficiente cualquiera.

En tales casos, el generador anula el [ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) método y [ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) propiedad definida en el [ `NSObject` protocolo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Esto permite la implementación administrada personalizada que se usará desde código de Objective-C de forma transparente.

### <a name="exceptions-support"></a>Compatibilidad con excepciones

Pasar `--nativeexception` como argumento a `objcgen` convertirá las excepciones administradas en las excepciones de Objective-C que se pueden detectar y procesar. 

### <a name="comparison"></a>Comparación

Tipos que implementan administrados `IComparable` (o su versión genérica `IComparable<T>`) generará los métodos descriptivos Objective-C que devuelven un `NSComparisonResult` y acepte un `nil` argumento. Esto hace más descriptiva para desarrolladores de Objective-C a la API generada. Por ejemplo:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categorías

Administrar las extensiones de los métodos se convierten en categorías. Por ejemplo, los siguientes métodos de extensión en `Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

crear una categoría de Objective-C como esta:

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Cuando un solo tipo administrado extiende varios tipos, se generan varias categorías de Objective-C.

### <a name="subscripting"></a>Subíndices

Propiedades indizadas administradas se convierten en los subíndices de objeto. Por ejemplo:

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

crearía Objective-C como:

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

que se puede usar mediante la sintaxis de subíndice de Objective-C:

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

Según el tipo de su indizador, se generarán los subíndices indizado o con clave en su caso.

Esto [artículo](http://nshipster.com/object-subscripting/) es una excelente introducción a los subíndices.

## <a name="main-differences-with-net"></a>Principales diferencias con .NET

### <a name="constructors-vs-initializers"></a>Inicializadores de vs de constructores

En Objective-C, se puede llamar a cualquiera del inicializador de prototipos de cualquiera de las clases principales de la cadena de herencia, a menos que se marca como no disponible (`NS_UNAVAILABLE`).

En C# debe declarar explícitamente un miembro de constructor dentro de una clase, lo que significa que los constructores no se heredan.

Para exponer la representación de la derecha de la C# API y Objective-C, `NS_UNAVAILABLE` se agrega a cualquier inicializador que no está presente en la clase secundaria de la clase primaria.

C#API:

```csharp
public class Unique {
    public Unique () : this (1)
    {
    }

    public Unique (int id)
    {
    }
}

public class SuperUnique : Unique {
    public SuperUnique () : base (911)
    {
    }
}
```

Objective-C expone API:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

En este caso, `initWithId:` se ha marcado como no disponible.

### <a name="operator"></a>Operador

Objective-C no admite la sobrecarga de operadores como C# funciona, de modo que los operadores se convierten en los selectores de clase:

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

por

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

Sin embargo, algunos lenguajes de .NET no admiten sobrecarga de operadores, por lo que es común para incluir también un ["descriptivo"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) denominado método además de la sobrecarga del operador.

Si la versión del operador y la versión "descriptiva" se encuentran, se generará sólo la versión descriptiva, tal y como se generan en el mismo nombre de Objective-C.

```csharp
public static AllOperatorsWithFriendly operator + (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}

public static AllOperatorsWithFriendly Add (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}
```

se convierte en:

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operador de igualdad

En general operador `==` en C# se trata como un operador general como se indicó anteriormente.

Sin embargo, si se encuentra el operador es igual a "descriptivo", ambos operador `==` y operador `!=` se omitirá en la generación.

### <a name="datetime-vs-nsdate"></a>Fecha y hora vs NSDate

Desde el [ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentación:

> `NSDate` los objetos encapsulan un único punto en el tiempo, independientemente de cualquier sistema calendrical particular o la zona horaria. Los objetos de fecha son inmutables, que representa un intervalo de tiempo invariable con respecto a una fecha de referencia absoluta (00: 00:00 UTC 1 de enero de 2001).

Debido a `NSDate` hacen referencia a fecha, todas las conversiones entre él y `DateTime` debe realizarse en UTC.

#### <a name="datetime-to-nsdate"></a>Fecha y hora en NSDate

Al convertir de `DateTime` a `NSDate`, `Kind` propiedad `DateTime` se tiene en cuenta:

|Tipo|Resultados|
|---|---|
|`Utc`|Se efectúa una conversión proporciona `DateTime` objeto tal cual.|
|`Local`|El resultado de llamar a `ToUniversalTime()` suministrados `DateTime` objeto se usa para la conversión.|
|`Unspecified`|Proporcionado `DateTime` objeto se supone que es la hora UTC, lo mismo comportamiento cuando `Kind` es `Utc`.|

La conversión utiliza la fórmula siguiente:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

En esta fórmula: 

- `NSDateReferenceDateTicks` se calcula en función de la `NSDate` hacen referencia a la fecha de 00:00:00 UTC el 1 de enero de 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) se define en [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Para crear el `NSDate` objeto, el `TimeInterval` se usa con el `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) selector.

#### <a name="nsdate-to-datetime"></a>NSDate a DateTime

La conversión de `NSDate` a `DateTime` utiliza la fórmula siguiente:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

En esta fórmula: 

- `NSDateReferenceDateTicks` se calcula en función de la `NSDate` hacen referencia a la fecha de 00:00:00 UTC el 1 de enero de 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) se define en [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Después de calcular `DateTimeTicks`, `DateTime` [constructor](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) se invoca, establecer su `kind` a `DateTimeKind.Utc`.

> [!NOTE]
> `NSDate` puede ser `nil`, pero un `DateTime` es un struct en. NET, que, por definición, no puede ser `null`. Si da un `nil` `NSDate`, se traducirá en el valor predeterminado `DateTime` valor, que se asigna a `DateTime.MinValue`.

`NSDate` admite un máximo superior y un valor mínimo menor que `DateTime`. Al convertir de `NSDate` a `DateTime`, estos valores superiores e inferiores se cambian a la `DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) o [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue), respectivamente.
