---
title: Compatibilidad con Objective-c.
ms.topic: article
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: f6d19f0f6573b17dfb3feb6bf131686413d4e68f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="objective-c-support"></a>Compatibilidad con Objective-c.

## <a name="specific-features"></a>Características específicas

La generación de ObjC tiene un algunas características especiales que vale la pena mencionar.

### <a name="automatic-reference-counting"></a>Recuento de referencias automático

El uso de automática referencia recuento (arco) es **necesario** para llamar a los enlaces generados. Proyecto con una biblioteca basada en embeddinator debe compilarse con `-fobjc-arc`.

### <a name="nsstring-support"></a>Compatibilidad con NSString

Las API que exponen `System.String` tipos se convierten en `NSString`. Esto facilita la administración de memoria que tratar con `char*`.

### <a name="protocols-support"></a>Compatibilidad de protocolos

Interfaces administradas se convierten en protocolos ObjC donde todos los miembros son `@required`.

### <a name="nsobject-protocol-support"></a>Compatibilidad con el protocolo de NSObject

De forma predeterminada, suponemos que el hash de forma predeterminada e igualdad entre .net y el tiempo de ejecución ObjC están bien e intercambiables que compartan una semántica muy similar.

Cuando se invalida un tipo administrado `Equals(Object)` o `GetHashCode` generalmente significa que el comportamiento de forma predeterminada (. NET) no era lo mejor. Suponemos que el comportamiento de Objective-C predeterminado no sería cualquiera.

En este caso, el generador de invalida la [ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) método y [ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) propiedad definida en la [ `NSObject` protocolo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Esto permite la implementación administrada personalizada puede utilizarse desde ObjC código transparente.

### <a name="comparison"></a>Comparación

Tipos que implementan administrados `IComparable` o es una versión genérica `IComparable<T>` generará ObjC métodos descriptivos que devuelve un `NSComparisonResult` y aceptar un `nil` argumento. Esto hace que la API generada más descriptiva a los desarrolladores de ObjC, p. ej.

```csharp
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categorías

Administrar las extensiones de los métodos se convierten en categorías. Por ejemplo los siguientes métodos de extensión en `Collection`:

```csharp
    public static class SomeExtensions {

        public static int CountNonNull (this Collection collection) { ... }

        public static int CountNull (this Collection collection) { ... }
    }
```

crear una categoría Objective-C como este:

```csharp
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;
@end
```

Cuando administra un solo tipo extiende varios tipos, a continuación, se generan varias categorías de C de objetivo.

### <a name="subscripting"></a>Subíndices

Propiedades indizadas administradas se convierten en subíndices de objeto. Por ejemplo:

```csharp
    public bool this[int index] {
        get { return c[index]; }
        set { c[index] = value; }
    }
```

Crear objetivo C similar al:

```csharp
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

que se puede utilizar a través de la sintaxis de subíndice Objective-C:

```csharp
    if ([intCollection [0] isEqual:@42])
        intCollection[0] = @13;
```

Según el tipo de su indizador, subíndices indizada o con clave se generará cuando sea necesario.

Esto [artículo](http://nshipster.com/object-subscripting/) es una buena introducción a subíndices.

## <a name="main-differences-with-net"></a>Principales diferencias con .NET

### <a name="constructors-vs-initializers"></a>Constructores vs inicializadores

En Objective-C, se puede llamar a cualquiera de los inicializadores prototipos de cualquiera de las clases principales en la cadena de herencia, a menos que esté marcado como no disponible (NS_UNAVAILABLE).

En C# debe declarar explícitamente un miembro de constructor dentro de una clase, esto significa que no se heredan los constructores.

Para exponer la representación de la derecha de la API de C# para Objective-C, agregamos `NS_UNAVAILABLE` a cualquier inicializador que no está presente en la clase secundaria de la clase primaria.

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

```objectivec
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

Aquí podemos ver que `initWithId:` se ha marcado como no disponible.

### <a name="operator"></a>Operador

ObjC no admite el operador de sobrecarga como C#, por lo que los operadores se convierten en los selectores de clase:

```csharp
    public static AllOperators operator + (AllOperators c1, AllOperators c2)
    {
        return new AllOperators (c1.Value + c2.Value);
    }
```

por

```csharp
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

Sin embargo, algunos lenguajes de .NET no admiten sobrecarga de operadores, por lo que es común incluir también un ["descriptivo"](https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx) denominado método además de la sobrecarga del operador.

Si la versión del operador y la versión "descriptiva" se encuentra, se generará sólo la versión descriptiva, tal y como se generarán en el mismo nombre de c de objetivo.

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

```csharp
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operador de igualdad

En general operador == de C# se trata como un operador general como se indicó anteriormente.

Sin embargo, si se encuentra el operador es igual a "descriptivo", ambos operador == y el operador! = will omitirá en la generación.

### <a name="datetime-vs-nsdate"></a>Fecha y hora vs NSDate

De [del NSDate](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentación:

> Objetos de NSDate encapsulan un único punto en el tiempo, independientemente de cualquier sistema calendrical determinado o la zona horaria. Objetos de fecha son inmutables, que representa un intervalo de tiempo invariable con respecto a una fecha de referencia absoluta (00: 00:00 UTC 1 de enero de 2001).

Debido a `NSDate` hacen referencia a fecha, todas las conversiones entre él y `DateTime` debe realizarse en UTC.

#### <a name="datetime-to-nsdate"></a>Fecha y hora para NSDate

Al convertir de `DateTime` a `NSDate` la fecha y hora `Kind` tener en cuenta la propiedad.

<table>
<tr><th> Tipo         </th><th> Resultados                                                                                            </th></tr>
<!--tr><td> ------------ </td><td> -------------------------------------------------------------------------------------------------- </td></tr-->
<tr><td> Hora UTC          </td><td> Se efectúa una conversión utilizando el objeto de fecha y hora proporcionado tal cual.                                  </td></tr>
<tr><td> Local        </td><td> El resultado de llamar al método `ToUniversalTime ()` en la fecha y hora proporcionada objeto se usa para la conversión. </td></tr>
<tr><td> Sin especificar  </td><td> El objeto de fecha y hora proporcionado se supone que la hora UTC, así mismo comportamiento que el tipo == Utc.                </td></tr>
</table>

La conversión se realiza mediante la fórmula siguiente:

> [!NOTE]
> **TimeInterval** = DateTimeObjectTicks - NSDateReferenceDateTicks[dt] / [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx)

Una vez que tenemos la TimeInterval usamos del NSDate [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) selector para crearla.

#### <a name="nsdate-to-datetime"></a>NSDate a DateTime

Es de instancia que es la fecha de referencia va de NSDate a DateTime, suponemos que obtenemos un NSDate **UTC de 00:00:00 del 1 de enero de 2001** y utilice la siguiente fórmula:

> [!NOTE]
> **DateTimeTicks** = NSDateTimeIntervalSinceReferenceDate * [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx) + NSDateReferenceDateTicks [dt]

Una vez que se calcula la **DateTimeTicks** usamos la fecha y hora siguiente [constructor](https://msdn.microsoft.com/en-us/library/w0d47c9c(v=vs.110).aspx) configuración su `kind` a `DateTimeKind.Utc`.

Existen algunas consideraciones que debe tener en cuenta, puede ser NSDate `nil` pero una fecha y hora es un struct en .NET y por definición no puede ser `null`. Si asigna un `nil` NSDate se traducirá, con el valor de fecha y hora predeterminado que se asigna a `DateTime.MinValue`.

MinValue y MaxValue también son diferentes, NSDate puede admitir los límites superior e inferiores del DateTime, por lo que cada vez que se proporcione a un valor superior o inferior se establecerá en del valor DateTime [MaxValue](https://msdn.microsoft.com/en-us/library/system.datetime.maxvalue(v=vs.110).aspx) o [MinValue](https://msdn.microsoft.com/en-us/library/system.datetime.minvalue(v=vs.110).aspx) respectivamente.

**DT**: fecha de referencia de NSDate **UTC de 00:00:00 del 1 de enero de 2001** => `new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;`
