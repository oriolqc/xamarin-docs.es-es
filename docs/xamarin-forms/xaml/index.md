---
title: Lenguaje de marcado de aplicaciones eXtensible (XAML)
description: "XAML es un lenguaje de marcado declarativo que puede utilizarse para definir las interfaces de usuario. La interfaz de usuario se define en un archivo XML utilizando la sintaxis XAML, mientras que el comportamiento de tiempo de ejecución se define en un archivo de código subyacente independiente."
ms.topic: article
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/24/2016
ms.openlocfilehash: a6e31fa9da7a5764d9a7fd04aa73d7d246143384
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="extensible-application-markup-language-xaml"></a>Lenguaje de marcado de aplicaciones eXtensible (XAML)

_XAML es un lenguaje de marcado declarativo que puede utilizarse para definir las interfaces de usuario. La interfaz de usuario se define en un archivo XML utilizando la sintaxis XAML, mientras que el comportamiento de tiempo de ejecución se define en un archivo de código subyacente independiente._

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evolucionar 2016: Se convierta en un patrón de XAML**

> [!NOTE]
> Pruebe el [previsualización estándar de XAML](standard/index.md)

<a name="xaml" />

## <a name="xaml-basicsxaml-basicsindexmd"></a>[Conceptos básicos XAML](xaml-basics/index.md)

XAML permite a los desarrolladores definir las interfaces de usuario en aplicaciones de Xamarin.Forms mediante marcado en lugar del código. XAML nunca es necesario en un programa de Xamarin.Forms pero herramientas y, a menudo es más visualmente coherente y más concisa que el código equivalente. XAML es especialmente adecuado para su uso con la arquitectura de aplicaciones populares de Model-View-ViewModel (MVVM): XAML define la vista que está vinculada al código de modelo de vista a través de enlaces de datos basadas en XAML.

## <a name="xaml-compilationxamlcmd"></a>[Compilación de XAML](xamlc.md)

XAML se puede compilar de forma opcional directamente en lenguaje intermedio (IL) con el compilador XAML (XAMLC). En este artículo se describe cómo usar XAMLC y sus ventajas.

## <a name="xaml-previewerxaml-previewermd"></a>[Controlador de vista previa XAML](xaml-previewer.md)

El [controlador de vista previa de XAML](~/xamarin-forms/xaml/xaml-previewer.md) anunciadas en Xamarin evolucionar 2016 está disponible para las pruebas en el canal alfa.

## <a name="xaml-namespacesnamespacesmd"></a>[Espacios de nombres XAML](namespaces.md)

XAML usa el `xmlns` atributo XML para las declaraciones de espacio de nombres. Este artículo presenta la sintaxis de espacio de nombres XAML y muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo.

## <a name="xaml-markup-extensionsmarkup-extensionsindexmd"></a>[Extensiones de marcado XAML](markup-extensions/index.md)

XAML incluye las extensiones de marcado para establecer atributos en valores u objetos más allá de lo que se puede expresar con cadenas simples. Esto incluye hacer referencia a constantes, propiedades estáticas y campos, los diccionarios de recursos y enlaces de datos.

## <a name="passing-argumentspassing-argumentsmd"></a>[Pasar argumentos](passing-arguments.md)

XAML se puede usar para pasar argumentos a los métodos de fábrica o constructores no predeterminados. Este artículo muestra cómo utilizar los atributos XAML que pueden utilizarse para pasar argumentos a los constructores para llamar a métodos de generador y para especificar el tipo de argumento genérico.

## <a name="bindable-propertiesbindable-propertiesmd"></a>[Propiedades enlazables](bindable-properties.md)

En Xamarin.Forms, se amplía la funcionalidad de common language runtime (CLR) propiedades propiedades enlazables. Una propiedad enlazable es un tipo especial de propiedad, donde se realiza el seguimiento de valor de la propiedad por el sistema de propiedades de Xamarin.Forms. Este artículo proporciona una introducción a las propiedades enlazables y muestra cómo crear y consumirlos.

## <a name="attached-propertiesattached-propertiesmd"></a>[Propiedades adjuntas](attached-properties.md)

Una propiedad adjunta es un tipo especial de propiedad enlazable, definido en una clase pero adjunta a otros objetos y reconocible en XAML como un atributo que contiene una clase y un nombre de propiedad separados por puntos. Este artículo proporciona una introducción a las propiedades adjuntas y muestra cómo crear y consumirlos.

## <a name="resource-dictionariesresource-dictionariesmd"></a>[Diccionarios de recursos](resource-dictionaries.md)

Recursos XAML son definiciones de objetos que se pueden usar más de una vez. A [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) permite a los recursos definidos en una sola ubicación y volver a utilizar en una aplicación de Xamarin.Forms. En este artículo se muestra cómo crear y consumir un `ResourceDictionary`y cómo combinar una `ResourceDictionary` a otro.
