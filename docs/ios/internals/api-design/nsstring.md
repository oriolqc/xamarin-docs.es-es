---
title: NSString en Xamarin.iOS y Xamarin.Mac
description: Este documento describe cómo Xamarin.iOS transparente convierte objetos NSString C# cadena de objetos, cuando esto no sucede.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: cc9e3f992642f3cdc3d16fe6f829b6a6c06b50fc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115040"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString en Xamarin.iOS y Xamarin.Mac

Requiere el uso de una API exponer del tipo de cadena de .NET native, el diseño de Xamarin.iOS y Xamarin.Mac `string`, para manipular las cadenas en C# y otros lenguajes de programación de .NET y exponer la cadena como el tipo de datos expuesto por la API en lugar de el `NSString` tipo de datos.

Esto significa que los desarrolladores no deberían tener que mantener las cadenas que están diseñadas para usarse para llamar a Xamarin.iOS y Xamarin.Mac API (unificada) en un tipo especial (`Foundation.NSString`), pueden seguir usando de Mono `System.String` para todas las operaciones y en cualquier momento una API de Xamarin.iOS o Xamarin.Mac requiere una cadena, el enlace de API se encarga de la información de serialización.

Por ejemplo, la propiedad de "text" Objective-C en una `UILabel` de tipo `NSString`, se declara como esta:

```objc
@property(nonatomic, copy) NSString *text
```

Esto se expone en Xamarin.iOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

En segundo plano, la implementación de esta propiedad calcula las referencias de la C# de cadena en un `NSString` y llama a la `objc_msgSend` método de la misma manera que lo haría Objective-C.

Hay una serie de API de Objective-C de terceros que no consumen una `NSString`, pero en su lugar, utilizar una cadena de C (una "*char*"). En esos casos, puede seguir utilizando el C# la cadena de tipo de datos, pero debe usar el [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) atributo para informar el generador de enlace que no se debe serializar esta cadena como un `NSString`, pero en su lugar como una cadena de C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Excepciones a la regla

En Xamarin.iOS y Xamarin.Mac, hemos realizado una excepción a esta regla. La decisión entre cuando se exponen `string`s, y cuando realizamos una excepto y exponer `NSString`s, se realiza si el `NSString` método podría estar realizando una comparación de punteros en lugar de una comparación de contenido.

Esto podría suceder cuando una API de Objective-C usa una pública `NSString` constante como un token que representa alguna acción, en lugar de comparar el contenido real de la cadena.

En esos casos, `NSString`  se exponen las API, y hay una minoría de las API que tienen este. También observará que se exponen propiedades NSString en algunas clases. Los `NSString` se exponen propiedades de elementos tales como notificaciones. Esos son propiedades suele tener este aspecto:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```
Las notificaciones son claves que se usan para la `NSNotification` clase cuando desea registrar un evento determinado que se va a difundir el tiempo de ejecución.

Claves suele tener un aspecto similar al siguiente:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Otro lugar donde `NSString`s se exponen en la API es como los tokens que se usan como parámetros a determinadas API de iOS u OS X que toman `NSDictionary` objetos como parámetros. El diccionario contiene normalmente `NSString` claves. Xamarin.iOS, por convención, los nombres de los estáticos `NSString` propiedades agregando el nombre de "Clave".
