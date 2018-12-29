---
title: Lenguaje de marcado de aplicaciones eXtensible (XAML)
description: XAML es un lenguaje de marcado declarativo que puede utilizarse para definir las interfaces de usuario. La interfaz de usuario se define en un archivo XML utilizando la sintaxis XAML, mientras que el comportamiento de tiempo de ejecución se define en un archivo de código subyacente independiente.
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 9924e588808783fe35dbd830bbc9af288f37e7ea
ms.sourcegitcommit: f890b5ec9b7c2702875070859e1a8cbf6e870e46
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/28/2018
ms.locfileid: "53813965"
---
# <a name="extensible-application-markup-language-xaml"></a>Lenguaje de marcado de aplicaciones eXtensible (XAML)

_XAML es un lenguaje de marcado declarativo que puede utilizarse para definir las interfaces de usuario. La interfaz de usuario se define en un archivo XML utilizando la sintaxis XAML, mientras que el comportamiento de tiempo de ejecución se define en un archivo de código subyacente independiente._

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evolve 2016: Convertirse en un patrón de XAML**

> [!NOTE]
> Pruebe el [versión preliminar de XAML estándar](standard/index.md)

<a name="xaml" />

## <a name="xaml-basicsxaml-basicsindexmd"></a>[XAML Basics](xaml-basics/index.md) (Conceptos básicos de XAML)

XAML permite a los desarrolladores definir interfaces de usuario en aplicaciones de Xamarin.Forms mediante marcado en lugar de código. XAML nunca es necesario en un programa de Xamarin.Forms, pero es dispone de herramientas y, a menudo es más coherente visualmente y más concisa que el código equivalente. XAML es especialmente adecuada para su uso con la arquitectura de aplicaciones populares de Model-View-ViewModel (MVVM): XAML define la vista que esté vinculada a ViewModel código a través de enlaces de datos basados en XAML.

## <a name="xaml-compilationxamlcmd"></a>[Compilación de XAML](xamlc.md)

XAML se puede compilar de forma opcional directamente en lenguaje intermedio (IL) con el compilador XAML (XAMLC). En este artículo se describe cómo utilizar XAMLC y sus ventajas.

## <a name="xaml-previewerxaml-previewermd"></a>[Controlador de vista previa de XAML](xaml-previewer.md)

El [controlador de vista previa de XAML](~/xamarin-forms/xaml/xaml-previewer.md) presenta una vista previa dinámica de una página side-by-side con el marcado XAML, lo que permite ver la interfaz de usuario representan a medida que escribe.

## <a name="xaml-namespacesnamespacesmd"></a>[Espacios de nombres XAML](namespaces.md)

XAML usa la `xmlns` atributo XML para las declaraciones de espacio de nombres. Este artículo presenta la sintaxis del espacio de nombres XAML y muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo.

## <a name="xaml-markup-extensionsmarkup-extensionsindexmd"></a>[Extensiones de marcado XAML](markup-extensions/index.md)

XAML incluye las extensiones de marcado para establecer atributos en los valores u objetos más allá de lo que se puede expresar con cadenas simples. Estos incluyen que hacen referencia a constantes, campos y propiedades estáticas, los diccionarios de recursos y enlaces de datos.

## <a name="field-modifiersfield-modifiersmd"></a>[Modificadores de campo](field-modifiers.md)

El `x:FieldModifier` namespace (atributo) especifica el nivel de acceso para los campos generados para los elementos XAML con nombre.

## <a name="passing-argumentspassing-argumentsmd"></a>[Paso de argumentos](passing-arguments.md)

XAML puede utilizarse para pasar argumentos a métodos de fábrica o constructores no predeterminados. En este artículo muestra cómo utilizar los atributos XAML que se pueden usar para pasar argumentos a los constructores para llamar a métodos de fábrica y para especificar el tipo de argumento genérico.

## <a name="bindable-propertiesbindable-propertiesmd"></a>[Propiedades enlazables](bindable-properties.md)

En Xamarin.Forms, la funcionalidad de las propiedades de common language runtime (CLR) se extiende por las propiedades enlazables. Una propiedad enlazable es un tipo especial de propiedad, donde el valor de propiedad se realiza el seguimiento por el sistema de propiedades de Xamarin.Forms. Este artículo proporciona una introducción a las propiedades enlazables y muestra cómo crear y consumirlos.

## <a name="attached-propertiesattached-propertiesmd"></a>[Propiedades asociadas](attached-properties.md)

Una propiedad adjunta es un tipo especial de propiedad enlazable, definido en una clase pero conectado a otros objetos y reconocible en XAML como un atributo que contiene una clase y un nombre de propiedad separados por un punto. Este artículo proporciona una introducción a las propiedades adjuntas y muestra cómo crear y consumirlos.

## <a name="resource-dictionariesresource-dictionariesmd"></a>[Diccionarios de recursos](resource-dictionaries.md)

Recursos XAML son definiciones de objetos que se pueden usar más de una vez. Un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) permite que los recursos definidos en una sola ubicación y volver a utilizarse en toda una aplicación de Xamarin.Forms. En este artículo se muestra cómo crear y consumir un `ResourceDictionary`y cómo combinar una `ResourceDictionary` a otro.

## <a name="loading-xaml-at-runtimeruntime-loadmd"></a>[Cargar XAML en tiempo de ejecución](runtime-load.md)

XAML se puede cargar y analizar en tiempo de ejecución con el [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensión.
