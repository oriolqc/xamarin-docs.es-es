---
title: Prácticas recomendadas de inserción de .NET para Objective-C
description: Este documento describen varios procedimientos recomendados para usar la inserción de .NET con Objective-C. Describe exponer un subconjunto del código administrado, exponer una API chunkier, nomenclatura y mucho más.
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 33138b7858b8bc04a5be30f9fad1709e916f5575
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364155"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>Prácticas recomendadas de inserción de .NET para Objective-C

Se trata de un borrador y pueden no ser sincronizados con las características actualmente compatibles con la herramienta. Esperamos que este documento evolucionará por separado y, finalmente, coincide con el final de la herramienta, es decir, le sugeriremos a largo plazo mejores enfoques - soluciones alternativas no inmediatas.

Gran parte de este documento también se aplica a otros idiomas admitidos. Sin embargo, todos proporcionan algunos ejemplos son en C# y Objective-C.

## <a name="exposing-a-subset-of-the-managed-code"></a>Exponer un subconjunto del código administrado

La biblioteca nativa generada/framework contiene código de Objective-C para llamar a cada una de las API administradas que se exponen. La API más que de superficie (hacer público), a continuación, mayor nativo _adherencia_ se convertirá en la biblioteca.

Sería una buena idea para crear un ensamblado diferente y más pequeño, para exponer solo las API necesarias para el desarrollador nativo. Ese fachada también le permitirá tener más control sobre la visibilidad, nomenclatura, comprobación de errores... del código generado.

## <a name="exposing-a-chunkier-api"></a>Exponer una API chunkier

Hay un precio para realizar la transición de código nativo a administrado (y posterior). Por lo tanto, es mejor exponer _pesado en lugar de chatty_ API para los programadores de aplicaciones nativas, p. ej.

**Chatty**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**Chunky**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

Puesto que el número de transiciones es menor, el rendimiento será mejor. También requiere menos código que se va a generar, por lo que esto creará una biblioteca nativa más pequeña.

## <a name="naming"></a>nomenclatura

Asignación de nombres es uno de estos dos problemas más difíciles en informática, las demás que se va a caché de errores de invalidación y off-por-1. Espero que inserción de .NET puede protegerse de todos excepto de nomenclatura.

### <a name="types"></a>Tipos

Objective-C no admite espacios de nombres. En general, sus tipos van precedidos de un 2 (para Apple) o 3 (de 3 partes) como prefijo, de caracteres `UIView` para la vista de UIKit, que indica el marco de trabajo.

Para tipos de .NET el omitiendo el espacio de nombres no es posible que pueden presentar nombres duplicados o confusos. Esto hace que los tipos .NET existentes muy largos, por ejemplo

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

se usará como:

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

Sin embargo puede exponer el tipo como volver a:

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

lo que más descriptiva de Objective-C para usar, por ejemplo:

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>Métodos

Incluso buenos nombres de .NET podrían no ser ideales para una API de C de objetivo.

Convenciones de nomenclatura en Objective-C son diferentes de .NET (mayúsculas y minúsculas camel en lugar de mayúsculas y minúsculas pascal, más detallado).
Lea la [directrices de programación para cacao](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF).

Desde el punto de vista de un desarrollador Objective-C, un método con un `Get` prefijo implica que no pertenece la instancia, es decir, el [obtener regla](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1).

Esta regla de nomenclatura no tiene ninguna coincidencia en el mundo de GC. NET; un método de .NET con un `Create` prefijo se comportará de forma idéntica en. NET. Sin embargo, para desarrolladores de Objective-C, normalmente significa que posee la instancia devuelta, es decir, el [crear regla](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).

## <a name="exceptions"></a>Excepciones

Es bastante común en .NET para usar las excepciones de forma extensa para informar de errores. Sin embargo, son lentas y no idéntica en Objective-C. Siempre que sea posible se debe ocultar del desarrollador de Objective-C.

Por ejemplo, .NET `Try` patrón será mucho más fácil de consumir desde código de Objective-C:

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

versus

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>Excepciones dentro de `init*`

En .NET un constructor debe tener éxito y devolver un (_espero_) instancia válida o produce una excepción.

En cambio, Objective-C permite `init*` para devolver `nil` cuando no se puede crear una instancia. Se trata de un patrón común, pero no general, utilizado en muchos de los marcos de Apple. En otros casos un `assert` puede ocurrir (y eliminar el proceso actual).

El generador de seguir los mismos `return nil` de patrón de generan `init*` métodos. Si se produce una excepción administrada, a continuación, se imprimirá (mediante `NSLog`) y `nil` se devolverá al llamador.

## <a name="operators"></a>Operadores

Objective-C no permitir que los operadores se puede sobrecargar como C# funciona, de modo que estos se convierten en los selectores de clase.

["Descriptivo"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) métodos con nombre se generan con preferencia a las sobrecargas de operador cuando se encuentra y puede producir un más fáciles de consumir API.

Las clases que reemplazan los operadores `==` o `!=` debe invalidar también el método Equals (Object) estándar.
