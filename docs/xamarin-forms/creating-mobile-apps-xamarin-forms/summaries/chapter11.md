---
title: Resumen del capítulo 11. La infraestructura enlazable
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 11. La infraestructura enlazable'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53054241"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Resumen del capítulo 11. La infraestructura enlazable

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Cada programador de C# está familiarizado con C# *propiedades*. Las propiedades contienen un *establecer* descriptor de acceso o un *obtener* descriptor de acceso. A menudo se denominan *propiedades CLR* para Common Language Runtime.

Xamarin.Forms define una definición de propiedad mejorada denominada una *propiedad enlazable* encapsulado por el [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) clase y compatible con la [ `BindableObject` ](xref:Xamarin.Forms.BindableObject)clase. Estas clases son bastante diferentes pero relacionados: la `BindableProperty` se utiliza para definir la propiedad en Sí; `BindableObject` es similar a `object` que se trata de una clase base para las clases que definen las propiedades enlazables.

## <a name="the-xamarinforms-class-hierarchy"></a>La jerarquía de clases de Xamarin.Forms

El [ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) ejemplo utiliza la reflexión para mostrar una jerarquía de clases de Xamarin.Forms y mostrar el papel fundamental desempeñado por `BindableObject` en esta jerarquía. `BindableObject` se deriva de `Object` y es la clase primaria para [ `Element` ](xref:Xamarin.Forms.Element) desde el que [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) se deriva. Se trata de la clase primaria para [ `Page` ](xref:Xamarin.Forms.Page) y [ `View` ](xref:Xamarin.Forms.View), que es la clase primaria para [ `Layout` ](xref:Xamarin.Forms.Layout):

[![Captura de pantalla triple de la jerarquía de clases de uso compartido](images/ch11fg01-small.png "compartir de la jerarquía en la clase")](images/ch11fg01-large.png#lightbox "uso compartido de jerarquía de clase")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Un vistazo en BindableObject y BindableProperty

En las clases que derivan de `BindableObject` muchas propiedades CLR se dice que "backup" propiedades enlazables. Por ejemplo, el [ `Text` ](xref:Xamarin.Forms.Label.Text) propiedad de la `Label` clase es una propiedad de CLR, pero la `Label` clase también define un campo estático público de solo lectura denominado [ `TextProperty` ](xref:Xamarin.Forms.Label.TextProperty) de tipo `BindableProperty`.

Una aplicación puede establecer u obtener el `Text` propiedad de `Label` normalmente, o la aplicación puede establecer la `Text` mediante una llamada a la [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) definido por el método `BindableObject` con un `Label.TextProperty` argumento. De forma similar, una aplicación puede obtener el valor de la `Text` propiedad mediante una llamada a la [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) método nuevo con un `Label.TextProperty` argumento. Esto se demuestra el [ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) ejemplo.

De hecho, el `Text` propiedad CLR se implementa por completo mediante la `SetValue` y `GetValue` métodos definidos por `BindableObject` junto con el `Label.TextProperty` propiedad estática.

`BindableObject` y `BindableProperty` proporcionan compatibilidad para:

- Lo que proporciona los valores predeterminados de propiedades
- Almacenar sus valores actuales
- Proporciona mecanismos para validar los valores de propiedad
- Mantener la coherencia entre las propiedades relacionadas en una única clase
- Responder a los cambios de propiedad
- Activación de notificaciones cuando se va a cambiar o ha cambiado una propiedad
- Compatibilidad con enlace de datos
- Compatibilidad con estilos
- Compatibilidad con los recursos dinámicos

Cada vez que una propiedad que está respaldada por una propiedad enlazable cambia, `BindableObject` se activa un [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) eventos que identifica la propiedad que ha cambiado. Este evento no se desencadena cuando la propiedad está establecida en el mismo valor.

Algunas propiedades no están respaldadas por propiedades enlazables y algunas clases de Xamarin.Forms &mdash; como `Span` &mdash; no se derivan de `BindableObject`. Sólo una clase que deriva de `BindableObject` puede admitir propiedades enlazables porque `BindableObject` define la `SetValue` y `GetValue` métodos.

Dado que `Span` no se deriva de `BindableObject`, ninguno de sus propiedades &mdash; como `Text` &mdash; están respaldadas por una propiedad enlazable. Se trata de por qué un `DynamicResource` en el `Text` propiedad de `Span` provoca una excepción en el [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) ejemplo en el capítulo anterior. El [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) ejemplo muestra cómo establecer un recurso dinámico en el código mediante el [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) definido por el método `Element`. El primer argumento es un objeto de tipo `BindableProperty`.

De forma similar, el [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) definido por el método `BindableObject` tiene un primer argumento de tipo `BindableProperty`.

## <a name="defining-bindable-properties"></a>Definir propiedades enlazables

Puede definir sus propias propiedades enlazables con estático [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) método para crear un campo estático de solo lectura de tipo `BindableProperty`.

Esto se muestra en el [ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. La clase se deriva de `Label` y le permite especificar un tamaño de fuente en puntos. Se muestra en el [ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) ejemplo.

Cuatro argumentos de la `BindableProperty.Create` método son necesarios:

- `propertyName`: el nombre de texto de la propiedad (igual que el nombre de propiedad CLR)
- `returnType`: el tipo de la propiedad CLR
- `declaringType`: el tipo de la clase que declara la propiedad
- `defaultValue`: valor predeterminado de la propiedad

Dado que `defaultValue` es de tipo `object`, el compilador debe ser capaz de determinar el tipo del valor predeterminado. Por ejemplo, si la `returnType` es `double`, el `defaultValue` debe establecerse en algo parecido a 0.0 en lugar de simplemente 0, o la falta de coincidencia de tipo activará una excepción en tiempo de ejecución.

También es muy común para una propiedad enlazable incluir:

- `propertyChanged`: un método estático que se llama cuando cambia la propiedad de valor. El primer argumento es la instancia de la clase cuya propiedad ha cambiado.

Los otros argumentos `BindableProperty.Create` no son tan comunes:

- `defaultBindingMode`: utilizado en relación con el enlace de datos (como se describe en [ **capítulo 16. Enlace de datos**](chapter16.md))
- `validateValue`: una devolución de llamada para comprobar si un valor válido
- `propertyChanging`: una devolución de llamada para indicar el momento en que la propiedad se va a cambiar
- `coerceValue`: una devolución de llamada para convertir un valor establecido por otro valor
- `defaultValueCreate`: una devolución de llamada para crear un valor predeterminado que no se pueden compartir entre instancias de la clase (por ejemplo, una colección)

### <a name="the-read-only-bindable-property"></a>La propiedad enlazable de solo lectura

Una propiedad enlazable puede ser de solo lectura. Creación de una propiedad enlazable de solo lectura requiere llamar al método estático [ `BindableProperty.CreateReadOnly` ](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) para definir un campo de solo lectura privado estático de tipo [ `BindablePropertyKey` ](xref:Xamarin.Forms.BindablePropertyKey).

A continuación, defina la propiedad CLR `set` descriptor de acceso como `private` para llamar a un [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) sobrecarga con el `BindablePropertyKey` objeto. Esto impide que la propiedad que se establece fuera de la clase.

Esto se muestra en el [ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) clase usada en el [ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) ejemplo.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 11 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Ejemplos de capítulo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md)
