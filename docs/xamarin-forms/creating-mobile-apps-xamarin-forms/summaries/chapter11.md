---
title: "Resumen del capítulo 11. La infraestructura enlazable"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 6e0f1abf04695dfb5348b631a9fbdbd2c81bc431
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Resumen del capítulo 11. La infraestructura enlazable

Cada programador de C# está familiarizado con C# *propiedades*. Propiedades contienen un *establecer* descriptor de acceso o un *obtener* descriptor de acceso. A menudo se les llama *propiedades CLR* para Common Language Runtime.

Xamarin.Forms define una definición de propiedad mejorada denominada una *propiedad enlazable* encapsulado por el [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) clase y admite la [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)clase. Estas clases son bastante diferentes pero relacionadas: el `BindableProperty` se utiliza para definir la propiedad en Sí; `BindableObject` es similar a `object` que se trata de una clase base para las clases que definen propiedades enlazables.

## <a name="the-xamarinforms-class-hierarchy"></a>La jerarquía de clases de Xamarin.Forms

El [ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) ejemplo utiliza la reflexión para mostrar una jerarquía de clases de Xamarin.Forms y mostrar el papel fundamental que desempeña `BindableObject` en esta jerarquía. `BindableObject` se deriva de `Object` y es la clase primaria para [ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/) desde el que [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) deriva. Se trata de la clase primaria de [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) y [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/), que es la clase primaria de [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/):

[![Captura de pantalla triple de la jerarquía de clases de uso compartido](images/ch11fg01-small.png "compartir de la jerarquía en la clase")](images/ch11fg01-large.png#lightbox "uso compartido de jerarquía de clase")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Un vistazo en BindableObject y BindableProperty

En las clases que derivan de `BindableObject` muchas propiedades CLR se dice que "respaldar" propiedades enlazables. Por ejemplo, el [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedad de la `Label` clase es una propiedad CLR, pero la `Label` clase también define un campo estático público de solo lectura denominado [ `TextProperty` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextProperty/) de tipo de `BindableProperty`.

Una aplicación puede establecer u obtener el `Text` propiedad de `Label` normalmente, o la aplicación puede establecer la `Text` mediante una llamada a la [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método definido por `BindableObject` con un `Label.TextProperty` argumento pasado. De forma similar, una aplicación puede obtener el valor de la `Text` propiedad mediante una llamada a la [ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/) método nuevo con un `Label.TextProperty` argumento. Esto se demuestra la [ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) ejemplo.

De hecho, el `Text` propiedad CLR se implementa por completo mediante la `SetValue` y `GetValue` métodos definidos por `BindableObject` junto con el `Label.TextProperty` propiedad estática.

`BindableObject` y `BindableProperty` proporcionan compatibilidad para:

- Que proporciona los valores predeterminados de propiedades
- Almacenar sus valores actuales
- Proporciona mecanismos para validar los valores de propiedad
- Mantiene la coherencia entre las propiedades relacionadas en una única clase
- Responder a los cambios de propiedad
- Activar las notificaciones cuando se va a cambiar o se ha cambiado una propiedad
- Compatibilidad con enlace de datos
- Compatibilidad con estilos
- Compatibilidad con recursos dinámicos

Cada vez que una propiedad que está respaldada por una propiedad enlazable cambia, `BindableObject` se activa un [ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/) evento identifica la propiedad que ha cambiado. No se desencadena este evento cuando la propiedad se establece en el mismo valor.

Algunas propiedades no están respaldadas por propiedades enlazables y algunas clases de Xamarin.Forms & #x 2014; como `Span` & #x 2014; no se derivan de `BindableObject`. Sólo una clase que deriva de `BindableObject` puede admitir propiedades enlazables porque `BindableObject` define la `SetValue` y `GetValue` métodos.

Dado que `Span` no se deriva de `BindableObject`, ninguno de sus propiedades & #x 2014; por ejemplo, `Text` & #x 2014; están respaldadas por una propiedad enlazable. Se trata de por qué un `DynamicResource` en el `Text` propiedad de `Span` genera una excepción en el [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) en el capítulo anterior. El [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) ejemplo muestra cómo establecer un recursos dinámicos en el código mediante el [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/p/Xamarin.Forms.BindableProperty/System.String/) método definido por `Element`. El primer argumento es un objeto de tipo `BindableProperty`.

De forma similar, el [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) método definido por `BindableObject` tiene un primer argumento de tipo `BindableProperty`.

## <a name="defining-bindable-properties"></a>Definir propiedades enlazables

Puede definir sus propias propiedades enlazables mediante el método estático [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) método o la cadena más corta ligeramente [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) sobrecarga para crear un campo estático de solo lectura de tipo `BindableProperty`.

Esto se muestra en el [ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. La clase se deriva de `Label` y le permite especificar un tamaño de fuente en puntos. Se muestra en el [ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) ejemplo.

Cuatro argumentos de la `BindableProperty.Create` método son necesarios:

- `propertyName`: el nombre de texto de la propiedad (igual que el nombre de propiedad CLR)
- `returnType`: el tipo de la propiedad CLR
- `declaringType`: el tipo de la clase que declara la propiedad
- `defaultValue`: valor predeterminado de la propiedad

Dado que `defaultValue` es de tipo `object`, el compilador debe ser capaz de determinar el tipo del valor predeterminado. Por ejemplo, si la `returnType` es `double`, el `defaultValue` debe establecerse en algo parecido a 0,0 en lugar de simplemente 0 o la falta de coincidencia de tipo desencadenarán una excepción en tiempo de ejecución.

También es muy común que una propiedad enlazable incluir:

- `propertyChanged`: un método estático que se llama cuando cambia la propiedad de valor. El primer argumento es la instancia de la clase cuya propiedad ha cambiado.

El resto de los argumentos a `BindableProperty.Create` no son tan comunes:

- `defaultBindingMode`: utilizado en relación con el enlace de datos (como se describe en [ **capítulo 16. Enlace de datos**](chapter16.md))
- `validateValue`: una devolución de llamada para comprobar si un valor válido
- `propertyChanging`: una devolución de llamada para indicar el momento en que la propiedad se va a cambiar
- `coerceValue`: una devolución de llamada para convertir un valor especificado con otro valor
- `defaultValueCreate`: una devolución de llamada para crear un valor predeterminado que no se pueden compartir entre instancias de la clase (por ejemplo, una colección)

### <a name="the-read-only-bindable-property"></a>La propiedad enlazable de solo lectura

Una propiedad enlazable puede ser de solo lectura. Crear una propiedad de solo lectura enlazable requiere llamar al método estático [ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) o un menor [ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) para definir un campo de solo lectura privado estático del tipo [ `BindablePropertyKey` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindablePropertyKey/).

A continuación, defina la propiedad CLR `set` descriptor de acceso como `private` para llamar a un [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindablePropertyKey/System.Object/) se puede sobrecargar con la `BindablePropertyKey` objeto. Esto impide que la propiedad que se va a establecer fuera de la clase.

Esto se muestra en el [ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) clase usada en el [ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) ejemplo.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 11 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Ejemplos de capítulo 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
