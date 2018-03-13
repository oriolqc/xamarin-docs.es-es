---
title: NSString
ms.topic: article
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 87cc1a95f250069310941e051dabe9ea588b4709
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="nsstring"></a>NSString

El diseño de Xamarin.iOS y Xamarin.Mac llama la API de uso exponer del tipo de cadena de .NET native, `string`, para la manipulación de cadenas en C# y otros lenguajes de programación de .NET y para exponer la cadena como el tipo de datos expuesto por la API en lugar de la `NSString` tipo de datos.


Esto significa que los desarrolladores no es necesario que mantener las cadenas que están diseñadas para usarse para llamar a Xamarin.iOS & Xamarin.Mac API (unificado) en un tipo especial (`Foundation.NSString`), pueden seguir usando de Mono `System.String` para todas las operaciones y cada vez que una API en Xamarin.iOS o Xamarin.Mac requiere una cadena, el enlace de API se encarga de la información de serialización.

Por ejemplo, la propiedad de "text" Objective-C en una `UILabel` de tipo `NSString`, se declara como el siguiente:

```csharp
@property(nonatomic, copy) NSString *text
```

Esto se expone en Xamarin.iOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

En segundo plano, la implementación de esta propiedad calcula las referencias de la cadena de C# en un `NSString` y llama a la `objc_msgSend` método de la misma manera que lo haría Objective-C.

Hay una serie de API de Objective-C de terceros que no consumen una `NSString`, pero en su lugar, utilizar una cadena de C (una "*char*"). En esos casos, todavía puede usar el tipo de datos de cadena de C#, pero debe utilizar el [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) atributo para informar el generador de enlace que esta cadena no debe calcularse como un `NSString`, pero en su lugar como una cadena de C.

 <a name="Exceptions_to_the_Rule" />


## <a name="exceptions-to-the-rule"></a>Excepciones a la regla

En Xamarin.iOS y Xamarin.Mac, hemos realizado una excepción a esta regla. La decisión entre cuando exponemos `string`s, y cuando se realiza un excepto y exponer `NSString`s, se realiza si el `NSString` método podría estar realizando una comparación de puntero en lugar de una comparación de contenido.


Esto puede suceder cuando una API Objective-C utiliza un complemento público `NSString` constante como un token que representa alguna acción en lugar de comparar el contenido real de la cadena.


En esos casos, `NSString` se exponen las API y hay una minoría de API que tienen este. También observará que se exponen propiedades NSString en algunas clases. Los `NSString` propiedades expuestas para elementos como las notificaciones. Éstas son propiedades suele tener el siguiente aspecto:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Las notificaciones son claves que se usan para la `NSNotification` clase si desea registrar un evento determinado que se va a difundir en tiempo de ejecución.

Claves suele ser algo parecido a esto:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Otro lugar donde `NSString`s se exponen en la API es como tokens que se usan como parámetros a ciertas API de iOS u OS X que toman `NSDictionary` objetos como parámetros. El diccionario contiene normalmente `NSString` claves. Xamarin.iOS, por convención, nombres de los estáticos `NSString` propiedades agregando el nombre de "Clave".
