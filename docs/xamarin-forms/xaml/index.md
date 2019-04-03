---
title: Lenguaje de marcado de aplicaciones eXtensible (XAML)
description: XAML es un lenguaje de marcado declarativo que puede utilizarse para definir las interfaces de usuario. La interfaz de usuario se define en un archivo XML utilizando la sintaxis XAML, mientras que el comportamiento de tiempo de ejecución se define en un archivo de código subyacente independiente.
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2019
ms.openlocfilehash: fa93cb86867cb8539fb7ce4db45ad4751bfe6e04
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854229"
---
# <a name="extensible-application-markup-language-xaml"></a>Lenguaje de marcado de aplicaciones eXtensible (XAML)

_XAML es un lenguaje de marcado declarativo que puede utilizarse para definir las interfaces de usuario. La interfaz de usuario se define en un archivo XML utilizando la sintaxis XAML, mientras que el comportamiento de tiempo de ejecución se define en un archivo de código subyacente independiente._

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evolve 2016: Convertirse en un patrón de XAML**

> [!NOTE]
> Pruebe el [versión preliminar de XAML estándar](standard/index.md)

<a name="xaml" />

## [<a name="xaml-basics"></a>XAML Basics (Conceptos básicos de XAML)](xaml-basics/index.md)

XAML permite a los desarrolladores definir interfaces de usuario en aplicaciones de Xamarin.Forms mediante marcado en lugar de código. XAML nunca es necesario en un programa de Xamarin.Forms, pero dispone de herramientas y, a menudo es más coherente visualmente y más concisa que el código equivalente. XAML es especialmente adecuada para su uso con la arquitectura de aplicaciones populares de Model-View-ViewModel (MVVM): XAML define la vista que esté vinculada a ViewModel código a través de enlaces de datos basados en XAML.

## [<a name="xaml-compilation"></a>Compilación de XAML](xamlc.md)

XAML se puede compilar de forma opcional directamente en lenguaje intermedio (IL) con el compilador XAML (XAMLC). En este artículo se describe cómo utilizar XAMLC y sus ventajas.

## [<a name="xaml-previewer"></a>Controlador de vista previa XAML](xaml-previewer/index.md)

El [controlador de vista previa de XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) presenta una vista previa dinámica de una página side-by-side con el marcado XAML, lo que permite ver la interfaz de usuario representada a medida que escribe.

## [<a name="xaml-namespaces"></a>Espacios de nombres XAML](namespaces.md)

XAML usa la `xmlns` atributo XML para las declaraciones de espacio de nombres. Este artículo presenta la sintaxis del espacio de nombres XAML y muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo.

## [<a name="xaml-custom-namespace-schemas"></a>Esquemas XAML Namespace personalizado](custom-namespace-schemas.md)

Se puede definir un esquema del espacio de nombres personalizado XAML con la clase `XmlnsDefinitionAttribute`, que especifica una asignación entre una dirección URL personalizada y uno o varios espacios de nombres CLR. El esquema del espacio de nombres personalizado se puede usar en las declaraciones del espacio de nombres XAML.

## [<a name="xaml-namespace-recommended-prefixes"></a>XAML Namespace recomienda prefijos](custom-prefix.md)

La `XmlnsPrefixAttribute` clase puede ser usada por los autores de controles para especificar un prefijo recomendado para asociar a un espacio de nombres XAML para el uso XAML.

## [<a name="xaml-markup-extensions"></a>Extensiones de marcado XAML](markup-extensions/index.md)

XAML incluye las extensiones de marcado para establecer atributos en los valores u objetos más allá de lo que se puede expresar con cadenas simples. Estos incluyen que hacen referencia a constantes, campos y propiedades estáticas, los diccionarios de recursos y enlaces de datos.

## [<a name="field-modifiers"></a>Modificadores de campo](field-modifiers.md)

El `x:FieldModifier` namespace (atributo) especifica el nivel de acceso para los campos generados para los elementos XAML con nombre.

## [<a name="passing-arguments"></a>Pasar argumentos](passing-arguments.md)

XAML puede utilizarse para pasar argumentos a métodos de fábrica o constructores no predeterminados. En este artículo muestra cómo utilizar los atributos XAML que se pueden usar para pasar argumentos a los constructores para llamar a métodos de fábrica y para especificar el tipo de argumento genérico.

## [<a name="bindable-properties"></a>Propiedades enlazables](bindable-properties.md)

En Xamarin.Forms, la funcionalidad de las propiedades de common language runtime (CLR) se extiende por las propiedades enlazables. Una propiedad enlazable es un tipo especial de propiedad, donde el valor de propiedad se realiza el seguimiento por el sistema de propiedades de Xamarin.Forms. Este artículo proporciona una introducción a las propiedades enlazables y muestra cómo crear y consumirlos.

## [<a name="attached-properties"></a>Propiedades asociadas](attached-properties.md)

Una propiedad adjunta es un tipo especial de propiedad enlazable, definido en una clase pero conectado a otros objetos y reconocible en XAML como un atributo que contiene una clase y un nombre de propiedad separados por un punto. Este artículo proporciona una introducción a las propiedades adjuntas y muestra cómo crear y consumirlos.

## [<a name="resource-dictionaries"></a>Diccionarios de recursos](resource-dictionaries.md)

Recursos XAML son definiciones de objetos que se pueden usar más de una vez. Un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) permite que los recursos definidos en una sola ubicación y volver a utilizarse en toda una aplicación de Xamarin.Forms. En este artículo se muestra cómo crear y consumir un `ResourceDictionary`y cómo combinar una `ResourceDictionary` a otro.

## [<a name="loading-xaml-at-runtime"></a>Cargar XAML en tiempo de ejecución](runtime-load.md)

XAML se puede cargar y analizar en tiempo de ejecución con el [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensión.
