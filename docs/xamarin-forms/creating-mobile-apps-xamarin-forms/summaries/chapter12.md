---
title: Resumen de capítulo 12. Estilos
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen de capítulo 12. Estilos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: d69c6c18a28c89742b186474656ee666b1e6a0ee
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241681"
---
# <a name="summary-of-chapter-12-styles"></a>Resumen de capítulo 12. Estilos

De Xamarin.Forms, los estilos permiten varias vistas compartir una colección de valores de propiedad. Esto reduce el marcado y permite mantener coherente temas visuales.

Estilos casi siempre se definen y consumidos en el marcado. Un objeto de tipo [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) es crear instancias en un diccionario de recursos y, a continuación, se establece en el [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedad de un elemento visual mediante un `StaticResource` o `DyanamicResource` marcado extensión.

## <a name="the-basic-style"></a>El estilo básico

A `Style` requiere que su [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) se establece en el tipo del objeto visual se aplica a. Cuando un `Style` se crea una instancia en un diccionario de recursos (como es habitual) también requiere un `x:Key` atributo.

El `Style` tiene una propiedad de contenido de tipo [ `Setters` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Setters/), que es una colección de [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/) objetos. Cada `Setter` asocia un [ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) con un [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/).

En XAML el `Property` configuración es el nombre de una propiedad CLR (como el `Text` propiedad de `Button`), pero la propiedad de estilo debe estar respaldada por una propiedad enlazable. Además, la propiedad debe definirse en la clase indicada por el `TargetType` configuración o heredado por esa clase.

Puede especificar el `Value` configuración utilizando el elemento de propiedad `<Setter.Value>`. Esto le permite establecer `Value` a un objeto que no se pueden expresar en una cadena de texto o a un `OnPlatform` de objeto o a un objeto, crea una instancia mediante `x:Arguments` o `x:FactoryMethod`. El `Value` propiedad también se puede establecer con un `StaticResource` expresión a otro elemento en el diccionario.

El [ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) programa muestra la sintaxis básica y cómo hacer referencia a la `Style` con un `StaticResource` extensión de marcado:

[![Captura de pantalla triple de estilo básico](images/ch12fg01-small.png "estilos básicos")](images/ch12fg01-large.png#lightbox "estilos básicos")

El `Style` objeto y cualquier objeto creado en el `Style` objeto como un `Value` configuración se comparten entre todas las vistas que hacen referencia a que `Style`. El `Style` no puede contener cualquier elemento que no se pueden compartir como una `View` derivado.

Controladores de eventos no se puede establecer un `Style`. El `GestureRecognizers` no se puede establecer la propiedad un `Style` porque no está respaldada por una propiedad enlazable.

## <a name="styles-in-code"></a>Estilos de código

Aunque no es muy común, puede crear instancias e inicializar `Style` objetos en el código. Esto se demuestra la [ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) ejemplo.

## <a name="style-inheritance"></a>Herencia de estilos

`Style` tiene una [ `BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) propiedad que puede establecer en un `StaticResource` otro estilo de referencia de extensión de marcado. Esto permite estilos heredar de estilos anteriores y agregar o reemplazar valores de propiedades. El [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) ejemplo se muestra cómo hacerlo.

Si `Style2` se basa en `Style1`, `TargetType` de `Style2` debe ser el mismo que `Style1` o derivado de `Style1`. El diccionario de recursos en el que `Style1` se almacena debe ser el mismo diccionario de recursos como `Style2` o un diccionario de recursos situado más arriba en el árbol visual.

## <a name="implicit-styles"></a>Estilos implícitos

Si un `Style` en un recurso de diccionario no tiene un `x:Key` configuración, del atributo se asigna automáticamente una clave de diccionario y la `Style` objeto se convierte en una *estilo implícitas*. Una vista sin un `Style` configuración y cuyo tipo coincide con el `TargetType` exactamente encontrarán ese estilo, como el [ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) muestra.

Puede derivar un estilo implícito de un `Style` con un `x:Key` configuración pero no al revés. No se puede hacer referencia explícita a un estilo implícito.

Puede implementar tres tipos de jerarquía con estilos y `BasedOn`:

- De los estilos definidos en el `Application` y `Page` hacia abajo hasta los estilos definidos en los diseños más abajo en el árbol visual.
- De los estilos definidos para las clases base como `VisualElement` y `View` en los estilos definidos para las clases específicas.
- De estilos con las claves del diccionario explícita en los estilos implícitos.

Estas jerarquías se muestran en la [ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) ejemplo.

## <a name="dynamic-styles"></a>Estilos dinámicos

Puede hacer referencia a un estilo en un diccionario de recursos por `DynamicResource` en lugar de `StaticResource`. Esto hace que el estilo de un *estilo dinámico*. Si ese estilo se reemplaza en el diccionario de recursos por otro estilo con la misma clave, las vistas que hacen referencia a ese estilo con `DynamicResource` cambia automáticamente. Además, hará que la ausencia de una entrada con la clave especificada del diccionario `StaticResource` para generar una excepción pero no `DynamicResource`.

Puede utilizar esta técnica para cambiar dinámicamente un estilo o temas como la [ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) muestra.

Sin embargo, no puede establecer la `BasedOn` propiedad a una `DynamicResource` extensión composición porque `BasedOn` no está respaldado por una propiedad enlazable. Para derivar un estilo dinámicamente, no establezca `BasedOn`. En su lugar, establezca el [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propiedad a la clave de diccionario del estilo que desee derivar de. El [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) ejemplo muestra esta técnica.

## <a name="device-styles"></a>Estilos de dispositivo

El [ `Device.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) clase anidada define doce campos estáticos de solo lectura para los seis estilos con un `TargetType` de `Label` que puede usar para los tipos comunes de los usos de texto.

Seis de estos campos son de tipo `Style` que puede establecer directamente en un `Style` propiedad en el código:

- [`BodyStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)
- [`TitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyle/)
- [`SubtitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyle/)
- [`CaptionStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyle/)
- [`ListItemTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyle/)
- [`ListItemDetailTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyle/)

Los seis campos son de tipo `string` y se pueden usar como claves de diccionario para estilos dinámicos:

- [`BodyStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyleKey/) igual que "BodyStyle"
- [`TitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyleKey/) igual que "TitleStyle"
- [`SubtitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyleKey/) igual que "SubtitleStyle"
- [`CaptionStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyleKey/) igual que "CaptionStyle"
- [`ListItemTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyleKey/) igual que "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyleKey/) igual que "ListItemDetailTextStyle"

Estos estilos se muestran en el [ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) ejemplo.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 12 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Ejemplos de capítulo 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
