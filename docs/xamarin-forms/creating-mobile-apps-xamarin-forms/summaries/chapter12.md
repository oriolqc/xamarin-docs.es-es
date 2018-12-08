---
title: Resumen del capítulo 12. Estilos
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 12. Estilos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bb2cd1c97cc588923e0da1a8793f16445c111f0e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058852"
---
# <a name="summary-of-chapter-12-styles"></a>Resumen del capítulo 12. Estilos

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

En Xamarin.Forms, los estilos permiten varias vistas compartir una colección de valores de propiedad. Esto reduce el marcado y permite mantener coherentes temas visuales.

Estilos casi siempre se definen y se consumen en el marcado. Un objeto de tipo [ `Style` ](xref:Xamarin.Forms.Style) es crear una instancia en un diccionario de recursos y, a continuación, se establece en el [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propiedad de un elemento visual mediante un `StaticResource` o `DynamicResource` marcado extensión.

## <a name="the-basic-style"></a>El estilo básico

Un `Style` requiere que su [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) se establece en el tipo del objeto visual se aplica a. Cuando un `Style` se crea una instancia en un diccionario de recursos (como es habitual) también requiere un `x:Key` atributo.

El `Style` tiene una propiedad de contenido de tipo [ `Setters` ](xref:Xamarin.Forms.Style.Setters), que es una colección de [ `Setter` ](xref:Xamarin.Forms.Setter) objetos. Cada `Setter` asocia un [ `Property` ](xref:Xamarin.Forms.Setter.Property) con un [ `Value` ](xref:Xamarin.Forms.Setter.Value).

En XAML la `Property` configuración es el nombre de una propiedad de CLR (como el `Text` propiedad de `Button`), pero la propiedad de estilo debe estar respaldada por una propiedad enlazable. Además, la propiedad debe definirse en la clase indicada por el `TargetType` configuración o heredado por esa clase.

Puede especificar el `Value` si se configura con el elemento de propiedad `<Setter.Value>`. Esto le permite establecer `Value` a un objeto que no se pueden expresar en una cadena de texto, o a un `OnPlatform` de objeto o a un objeto, crea una instancia mediante `x:Arguments` o `x:FactoryMethod`. El `Value` también se puede establecer la propiedad con un `StaticResource` expresión a otro elemento en el diccionario.

El [ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) muestra la sintaxis básica de programa y se muestra cómo hacer referencia a la `Style` con un `StaticResource` extensión de marcado:

[![Captura de pantalla triple de estilo básico](images/ch12fg01-small.png "estilos básicos")](images/ch12fg01-large.png#lightbox "estilos básicos")

El `Style` objeto y cualquier objeto creado en el `Style` objeto como un `Value` configuración se comparten entre todas las vistas que hacen referencia a la que `Style`. El `Style` no puede contener cualquier cosa que no se puede compartir, como un `View` derivado.

No se puede establecer controladores de eventos un `Style`. El `GestureRecognizers` no se puede establecer la propiedad un `Style` porque no está respaldada por una propiedad enlazable.

## <a name="styles-in-code"></a>Estilos de código

Aunque no es común, puede crear una instancia e inicializar `Style` objetos en el código. Esto se demuestra el [ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) ejemplo.

## <a name="style-inheritance"></a>Herencia de estilo

`Style` tiene un [ `BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) propiedad que se puede establecer para un `StaticResource` otro estilo de referencia de extensión de marcado. Esto permite que los estilos heredar de estilos anteriores y agregar o reemplazar los valores de propiedad. El [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) muestra esto.

Si `Style2` se basa en `Style1`, `TargetType` de `Style2` debe ser el mismo que `Style1` o derivado de `Style1`. El diccionario de recursos en el que `Style1` se almacena debe ser el mismo diccionario de recursos como `Style2` o un diccionario de recursos superior en el árbol visual.

## <a name="implicit-styles"></a>Estilos implícitos

Si un `Style` en un recurso de diccionario no tiene un `x:Key` atributo de configuración, se asigna una clave de diccionario automáticamente y el `Style` objeto se convierte en un *estilo implícito*. Una vista sin un `Style` configuración y cuyo tipo coincide con el `TargetType` exactamente encontrarán ese estilo, como el [ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) muestra.

Puede derivar un estilo implícito de un `Style` con un `x:Key` configuración pero no al revés. No se puede hacer referencia explícitamente a un estilo implícito.

Puede implementar tres tipos de jerarquía con estilos y `BasedOn`:

- Desde los estilos definidos en el `Application` y `Page` hacia abajo hasta los estilos definidos en los diseños más abajo en el árbol visual.
- Los estilos definidos para las clases base, como de `VisualElement` y `View` para los estilos definidos para las clases específicas.
- De estilos con las claves del diccionario explícitas para los estilos implícitos.

Estas jerarquías se muestran en el [ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) ejemplo.

## <a name="dynamic-styles"></a>Estilos dinámicos

Se puede hacer referencia a un estilo en un diccionario de recursos por `DynamicResource` lugar `StaticResource`. Esto hace que el estilo de un *estilo dinámico*. Si ese estilo se sustituye en el diccionario de recursos por otro estilo con la misma clave, las vistas que hacen referencia a ese estilo con `DynamicResource` cambia automáticamente. Además, hará que la ausencia de una entrada con la clave especificada del diccionario `StaticResource` para generar una excepción, pero no `DynamicResource`.

Puede usar esta técnica para cambiar dinámicamente la aplicación de estilos o temas como la [ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) muestra.

Sin embargo, no puede establecer el `BasedOn` propiedad a un `DynamicResource` extensión maquillaje porque `BasedOn` no está respaldada por una propiedad enlazable. Para derivar un estilo de forma dinámica, no establezca `BasedOn`. En su lugar, establezca el [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propiedad a la clave del diccionario del estilo que desee derivar. El [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) muestra esta técnica.

## <a name="device-styles"></a>Estilos de dispositivo

El [ `Device.Styles` ](xref:Xamarin.Forms.Device.Styles) clase anidada define doce campos estáticos de sólo lectura para los seis estilos con un `TargetType` de `Label` que puede usar para los tipos comunes de usos de texto.

Seis de estos campos son de tipo `Style` que puede establecer directamente a un `Style` propiedad en el código:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Los seis campos son de tipo `string` y puede utilizarse como las claves del diccionario de estilos dinámicos:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) igual que "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) igual que "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) igual que "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) igual que "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) igual que "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) igual que "ListItemDetailTextStyle"

Estos estilos se muestran en el [ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) ejemplo.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 12 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Ejemplos de capítulo 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
