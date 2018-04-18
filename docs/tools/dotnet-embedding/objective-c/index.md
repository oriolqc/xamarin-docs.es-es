---
title: Compatibilidad con Objective-c.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 515185ca7be8b6e24c92c9f44eb6dadbaf6d9219
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="objective-c-support"></a>Compatibilidad con Objective-c.

## <a name="specific-features"></a>Características específicas

La generación de Objective-C tiene algunas de las características especiales que se debe tener en cuenta.

### <a name="automatic-reference-counting"></a>Recuento de referencias automático

El uso de automática referencia recuento (arco) es **necesario** para llamar a los enlaces generados. Proyecto con una biblioteca basada en embeddinator debe compilarse con `-fobjc-arc`.

### <a name="nsstring-support"></a>Compatibilidad con NSString

Las API que exponen `System.String` tipos se convierten en `NSString`. Esto facilita la administración de memoria que cuando se trabaja con `char*`.

### <a name="protocols-support"></a>Compatibilidad de protocolos

Interfaces administradas se convierten en protocolos Objective-C donde todos los miembros son `@required`.

### <a name="nsobject-protocol-support"></a>Compatibilidad con el protocolo NSObject

De forma predeterminada, el hash de forma predeterminada y la igualdad entre .NET y el tiempo de ejecución de C de objetivo se supone son intercambiables, como comparten una semántica similar.

Cuando se invalida un tipo administrado `Equals(Object)` o `GetHashCode`, generalmente significa que el comportamiento predeterminado (. NET) no era suficiente; esto implica que es probable que el comportamiento de Objective-C predeterminado no es suficiente cualquiera.

En tales casos, se invalida el generador de la [ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) método y [ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) propiedad definida en la [ `NSObject` protocolo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Esto permite la implementación administrada personalizada que se usará de código Objective-C de forma transparente.

### <a name="exceptions-support"></a>Compatibilidad con excepciones

Pasar `--nativeexception` como argumento a `objcgen` convertirá las excepciones administradas en Objective C excepciones que se pueden detectar y procesar. 

### <a name="comparison"></a>Comparación

Tipos que implementan administrados `IComparable` (o su versión genérica `IComparable<T>`) generará Objective-C métodos descriptivos que devuelven un `NSComparisonResult` y aceptar un `nil` argumento. Esto hace que la API generada más descriptiva a los desarrolladores de C de objetivo. Por ejemplo:

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

crear una categoría Objective-C como este:

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Cuando un solo tipo administrado abarca varios tipos, se generan varias categorías de C de objetivo.

### <a name="subscripting"></a>Subíndices

Propiedades indizadas administradas se convierten en subíndices de objeto. Por ejemplo:

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

Crear objetivo C similar al:

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

que se puede utilizar a través de la sintaxis de subíndice Objective-C:

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

Según el tipo de su indizador, subíndices indizada o con clave se generará cuando sea necesario.

Esto [artículo](http://nshipster.com/object-subscripting/) es una buena introducción a subíndices.

## <a name="main-differences-with-net"></a>Principales diferencias con .NET

### <a name="constructors-vs-initializers"></a>Inicializadores de vs de constructores

En Objective-C, puede llamar a cualquiera de los inicializadores prototipos de cualquiera de las clases principales de la cadena de herencia, a menos que se marca como no disponible (`NS_UNAVAILABLE`).

En C# debe declarar explícitamente un miembro de constructor dentro de una clase, lo que significa que no se heredan los constructores.

Para exponer la representación de la derecha de la API de C# para Objective-C, `NS_UNAVAILABLE` se agrega a cualquier inicializador que no está presente en la clase secundaria de la clase primaria.

API DE C#:

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

Objective-C exhibe API:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

En este caso, `initWithId:` se ha marcado como no disponible.

### <a name="operator"></a>Operador

Objective-C no admite el operador de sobrecarga como C#, por lo que los operadores se convierten en los selectores de clase:

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

Sin embargo, algunos lenguajes de .NET no admiten sobrecarga de operadores, por lo que es común incluir también un ["descriptivo"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) denominado método además de la sobrecarga del operador.

Si la versión del operador y la versión "descriptiva" se encuentra, se generará sólo la versión descriptiva, tal y como se generarán en el mismo nombre de C de objetivo.

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

Se convierte en:

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operador de igualdad

In (operador) general `==` en C# se trata como un general operador tal y como se indicó anteriormente.

Sin embargo, si se encuentra el operador es igual a "descriptivo", ambos operador `==` and (operador) `!=` se omitirá en la generación.

### <a name="datetime-vs-nsdate"></a>Fecha y hora vs NSDate

Desde el [ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentación:

> `NSDate` los objetos encapsulan un único punto en el tiempo, independientemente de cualquier sistema calendrical determinado o la zona horaria. Objetos de fecha son inmutables, que representa un intervalo de tiempo invariable con respecto a una fecha de referencia absoluta (00: 00:00 UTC 1 de enero de 2001).

Debido a `NSDate` hacen referencia a fecha, todas las conversiones entre él y `DateTime` debe realizarse en UTC.

#### <a name="datetime-to-nsdate"></a>Fecha y hora para NSDate

Al convertir de `DateTime` a `NSDate`, `Kind` propiedad `DateTime` se tiene en cuenta:

|Tipo|Resultados|
|---|---|
|`Utc`|Se efectúa una conversión mediante la proporcionada `DateTime` objeto tal y como está.|
|`Local`|El resultado de llamar al método `ToUniversalTime()` en proporcionado `DateTime` objeto se usa para la conversión.|
|`Unspecified`|Proporcionado `DateTime` objeto se supone que la hora UTC, así mismo comportamiento cuando `Kind` es `Utc`.|

La conversión utiliza la fórmula siguiente:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

En esta fórmula: 

- `NSDateReferenceDateTicks` se calcula en función de la `NSDate` hacen referencia a la fecha de 00:00:00 UTC en 1 de enero de 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) se define en [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Para crear el `NSDate` objeto, el `TimeInterval` se utiliza con la `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) selector.

#### <a name="nsdate-to-datetime"></a>NSDate a DateTime

La conversión de `NSDate` a `DateTime` utiliza la fórmula siguiente:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

En esta fórmula: 

- `NSDateReferenceDateTicks` se calcula en función de la `NSDate` hacen referencia a la fecha de 00:00:00 UTC en 1 de enero de 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) se define en [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Después de calcular `DateTimeTicks`, `DateTime` [constructor](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) se invoca, establecer su `kind` a `DateTimeKind.Utc`.

> [!NOTE]
> `NSDate` puede ser `nil`, pero un `DateTime` es un struct en. NET, que, por definición, no puede ser `null`. Si asigna un `nil` `NSDate`, se convertirá en el valor predeterminado `DateTime` valor, que se asigna a `DateTime.MinValue`.

`NSDate` admite un máximo mayor y un valor mínimo inferior `DateTime`. Al convertir de `NSDate` a `DateTime`, estos valores superior e inferiores se cambian a la `DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) o [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue), respectivamente.
