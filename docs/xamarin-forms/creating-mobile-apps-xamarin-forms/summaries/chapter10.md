---
title: Resumen del capítulo 10. Extensiones de marcado XAML
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 10. Extensiones de marcado XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334348"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Resumen del capítulo 10. Extensiones de marcado XAML

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

Normalmente, el analizador XAML convierte cualquier cadena que se establece como valor de atributo en el tipo de la propiedad en función de las conversiones estándar para los tipos de datos de .NET básicas, o un [ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter) derivado se asocia a la propiedad o su tipo con un [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

Pero a veces es conveniente establecer un atributo de un origen diferente, por ejemplo, un elemento en un diccionario, o el valor de una propiedad estática o un campo, o de un cálculo de algún tipo.

Este es el trabajo de un *extensión de marcado XAML*. A pesar del nombre, son extensiones de marcado XAML *no* una extensión de XML. XAML es siempre el código XML válido.

## <a name="the-code-infrastructure"></a>La infraestructura de código

Una extensión de marcado XAML es una clase que implementa el [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) interfaz. A menudo, esta clase tiene la palabra `Extension` al final de su nombre, pero aparece normalmente en XAML sin dicho sufijo.

Las extensiones de marcado XAML siguientes son compatibles con todas las implementaciones de XAML:

- `x:Static` Compatible con [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` Compatible con [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` Compatible con [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` Compatible con [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` Compatible con [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Estas cuatro extensiones de marcado XAML son compatibles con muchas implementaciones de XAML, incluido Xamarin.Forms:

- `StaticResource` Compatible con [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` Compatible con [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` compatible con [ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash;descritos en [capítulo 16. Data binding](chapter16.md)
- `TemplateBinding` compatible con [ `TemplateBindingExtension` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash;no se tratan en el libro

Se incluye una extensión de marcado XAML adicional en Xamarin.Forms en conexión con [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;no se tratan en el libro

## <a name="accessing-static-members"></a>Acceso a miembros estáticos

Use la [ `x:Static` ](xref:Xamarin.Forms.Xaml.StaticExtension) elemento para establecer un atributo en el valor de un miembro de enumeración, campo o propiedad estático público. Establecer el [ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member) propiedad al miembro estático. Generalmente resulta más sencillo especificar `x:Static` y el nombre de miembro entre llaves. El nombre de la `Member` propiedad no necesita incluirse, solo el propio miembro. Esta sintaxis común que se muestra en el [ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) ejemplo. Los campos estáticos a sí mismos se definen en el [ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) clase. Esta técnica permite establecer las constantes utilizadas a través de un programa.

Con una declaración de espacio de nombres XML adicional, puede hacer referencia a propiedades estáticas públicas, campos o los miembros de enumeración definidos en .NET framework, como se muestra en el [ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) ejemplo .

## <a name="resource-dictionaries"></a>Diccionarios de recursos

El `VisualElement` clase define una propiedad denominada [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) que se pueden establecer para un objeto de tipo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Dentro de XAML, puede almacenar elementos en este diccionario e identificarlos con el `x:Key` atributo. Los elementos almacenados en el diccionario de recursos se comparten entre todas las referencias al elemento.

### <a name="staticresource-for-most-purposes"></a>StaticResource para la mayoría de los casos

En la mayoría de los casos usará el [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) markup extension para hacer referencia a un elemento desde el diccionario de recursos, tal como lo demuestra el [ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) ejemplo . Puede usar un `StaticResourceExtension` elemento o `StaticResource` entre llaves:

[![Captura de pantalla triple de uso compartido de recursos](images/ch10fg03-small.png "uso compartido de recursos")](images/ch10fg03-large.png#lightbox "uso compartido de recursos")

No confunda la `x:Static` extensión de marcado y el `StaticResource` extensión de marcado.

### <a name="a-tree-of-dictionaries"></a>Un árbol de diccionarios

Cuando el analizador XAML encuentra un `StaticResource`, comienza a buscar el árbol visual para una clave coincidente y, después, busca en el `ResourceDictionary` en la aplicación `App` clase. Esto permite a los elementos en un diccionario de recursos más profundo del árbol visual para invalidar un diccionario de recursos superior en el árbol visual. Esto se muestra en el [ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) ejemplo.

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource para fines especiales

El `StaticResource` extensión de marcado hace que un elemento que se recuperará desde el diccionario cuando se crea un árbol visual durante la `InitializeComponent` llamar. Una alternativa a `StaticResource` es [ `DynamicResource` ](xref:Xamarin.Forms.Xaml.DynamicResourceExtension), que mantiene un vínculo a la clave del diccionario y se actualiza el destino cuando el elemento hace referencia a los cambios clave.

La diferencia entre `StaticResource` y `DynamicResource` se muestra en el [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) ejemplo.

Una propiedad establecida por `DynamicResource` debe estar respaldada por una propiedad enlazable, como se describe en [capítulo 11, la infraestructura enlazable](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensiones de marcado usa menor

Use la [ `x:Null` ](xref:Xamarin.Forms.Xaml.NullExtension) extensión de marcado para establecer una propiedad en `null`.

Use la [ `x:Type` ](xref:Xamarin.Forms.Xaml.TypeExtension) extensión de marcado para establecer una propiedad en un .NET `Type` objeto.

Use [ `x:Array` ](xref:Xamarin.Forms.Xaml.ArrayExtension) para definir una matriz. Especificar el tipo de los miembros de la matriz estableciendo el [`Type`] propiedad a un `x:Type` extensión de marcado.

## <a name="a-custom-markup-extension"></a>Una extensión de marcado personalizada

Puede crear sus propias extensiones de marcado XAML escribiendo una clase que implementa el [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) interfaz con un [ `ProvideValue` ](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) método.

El [ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) clase cumple esos requisitos. Crea un valor de tipo `Color` según los valores de propiedades denominadas `H`, `S`, `L`, y `A`. Esta clase es el primer elemento en una biblioteca de Xamarin.Forms denominada [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) que se ha creado y utilizado en el transcurso de este libro.

El [ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) ejemplo muestra cómo hacer referencia a esta biblioteca y usar la extensión de marcado personalizada.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 10 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Capítulo 10 muestras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
