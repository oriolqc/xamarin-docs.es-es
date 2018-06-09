---
title: Resumen de capítulo 10. Extensiones de marcado XAML
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen de capítulo 10. Extensiones de marcado XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: cc6c3154b7e6535fa7528032fb7a91ad90a0a7f8
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241105"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Resumen de capítulo 10. Extensiones de marcado XAML

Normalmente, el analizador de XAML convierte cualquier cadena que se establece como un valor de atributo para el tipo de la propiedad en función de las conversiones estándar para los tipos de datos básicos. NET, o un [ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/) derivado se asocia a la propiedad o su tipo con un [`TypeConverterAttribute`](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/).

Pero a veces resulta práctico establecer un atributo de un origen diferente, por ejemplo, un elemento en un diccionario, o si el valor de una propiedad estática o un campo, o de un cálculo de algún tipo.

Este es el trabajo de un *extensión de marcado XAML*. A pesar del nombre, extensiones de marcado XAML son *no* una extensión a XML. XAML siempre es XML válido.

## <a name="the-code-infrastructure"></a>La infraestructura de código

Una extensión de marcado XAML es una clase que implementa el [ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/) interfaz. Esta clase a menudo tiene la palabra `Extension` al final de su nombre pero normalmente aparece en XAML sin ese sufijo.

Las siguientes extensiones de marcado XAML son compatibles con todas las implementaciones de XAML:

- `x:Static` Compatible con [`StaticExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/)
- `x:Reference` Compatible con [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/)
- `x:Type` Compatible con [`TypeExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/)
- `x:Null` Compatible con [`NullExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/)
- `x:Array` Compatible con [`ArrayExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/)

Estas cuatro extensiones de marcado XAML son compatibles con muchas implementaciones de XAML, incluidos los Xamarin.Forms:

- `StaticResource` Compatible con [`StaticResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/)
- `DynamicResource` Compatible con [`DynamicResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/)
- `Binding` compatible con [ `BindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) &mdash;se describe en [capítulo 16. Data binding](#chapter16)
- `TemplateBinding` compatible con [ `TemplateBindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/) &mdash;no se tratan en el libro

Una extensión de marcado XAML adicional se incluye en Xamarin.Forms en relación con [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/):

- [`ConstraintExpression`](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/)&mdash;no se tratan en el libro

## <a name="accessing-static-members"></a>Obtener acceso a miembros estáticos

Use la [ `x:Static` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) elemento que se va a definir un atributo con el valor de un miembro de propiedad, campo o enumeración estático público. Establecer el [ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/) propiedad al miembro estático. Generalmente resulta más sencillo especificar `x:Static` y el nombre de miembro entre llaves. El nombre de la `Member` no es necesario que la propiedad incluirán, simplemente el propio miembro. Esta sintaxis común se muestra en el [ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) ejemplo. Los campos estáticos a sí mismos se definen en el [ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) clase. Esta técnica permite establecer las constantes se utilizan a través de un programa.

Con una declaración de espacio de nombres XML adicional, puede hacer referencia a propiedades estáticas públicas, campos o los miembros de enumeración definidos en .NET framework, como se muestra en el [ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) ejemplo .

## <a name="resource-dictionaries"></a>Diccionarios de recursos

El `VisualElement` clase define una propiedad denominada [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) que puede establecer en un objeto de tipo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). En XAML, puede almacenar los elementos de este diccionario e identificarlos con el `x:Key` atributo. Los elementos almacenados en el diccionario de recursos se comparten entre todas las referencias al elemento.

### <a name="staticresource-for-most-purposes"></a>StaticResource para la mayoría de los casos

En la mayoría de los casos usará el [ `StaticResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/) extensión de marcado para hacer referencia a un elemento del diccionario de recursos, como se muestra en el [ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) ejemplo . Puede usar un `StaticResourceExtension` elemento o `StaticResource` entre llaves:

[![Captura de pantalla triple de uso compartido de recursos](images/ch10fg03-small.png "uso compartido de recursos")](images/ch10fg03-large.png#lightbox "uso compartido de recursos")

No confunda la `x:Static` extensión de marcado y el `StaticResource` extensión de marcado.

### <a name="a-tree-of-dictionaries"></a>Un árbol de diccionarios

Cuando el analizador XAML encuentra un `StaticResource`, se comienza a buscar el árbol visual de una clave de búsqueda de coincidencias y, a continuación, busca en el `ResourceDictionary` en la aplicación `App` clase. Esto permite que elementos en un diccionario de recursos en el árbol visual para invalidar un diccionario de recursos situado más arriba en el árbol visual. Esto se muestra en el [ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) ejemplo.

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource para fines especiales

El `StaticResource` extensión de marcado hace que un elemento se puede recuperar desde el diccionario cuando se crea un árbol visual durante la `InitializeComponent` llamar. Una alternativa a `StaticResource` es [ `DynamicResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/), que mantiene un vínculo a la clave del diccionario y se actualiza el destino cuando el elemento hace referencia a los cambios clave.

La diferencia entre `StaticResource` y `DynamicResource` se muestra en el [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) ejemplo.

Una propiedad definida por `DynamicResource` debe estar respaldada por una propiedad enlazable como se describe en [capítulo 11, la infraestructura enlazable](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensiones de marcado de menos usado

Use la [ `x:Null` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/) extensión de marcado para establecer una propiedad en `null`.

Use la [ `x:Type` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/) extensión de marcado para establecer una propiedad en .NET `Type` objeto.

Use [ `x:Array` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/) para definir una matriz. Especificar el tipo de los miembros de la matriz estableciendo el [`Type`] propiedad a un `x:Type` extensión de marcado.

## <a name="a-custom-markup-extension"></a>Una extensión de marcado personalizada

Puede crear sus propias extensiones de marcado XAML mediante la escritura de una clase que implementa el [ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/) interactuar con un [ `ProvideValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue/p/System.IServiceProvider/) método.

El [ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) clase cumple los requisitos. Crea un valor de tipo `Color` basados en valores de propiedades denominadas `H`, `S`, `L`, y `A`. Esta clase es el primer elemento de una biblioteca de Xamarin.Forms denominada [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) que se ha creado y utilizado en el transcurso de este libro.

El [ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) ejemplo muestra cómo hacer referencia a esta biblioteca y utilizar la extensión de marcado personalizada.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 10 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Ejemplos de capítulo 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
