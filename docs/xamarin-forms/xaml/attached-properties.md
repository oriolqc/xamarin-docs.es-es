---
title: Propiedades asociadas
description: Este artículo proporciona una introducción a las propiedades adjuntas y muestra cómo crear y consumirlos.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: e0ecff37eaf615321c7fcdce35e334db89ae631a
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245903"
---
# <a name="attached-properties"></a>Propiedades asociadas

_Una propiedad adjunta es un tipo especial de propiedad enlazable, definido en una clase pero adjunta a otros objetos y reconocible en XAML como un atributo que contiene una clase y un nombre de propiedad separados por puntos. Este artículo proporciona una introducción a las propiedades adjuntas y muestra cómo crear y consumirlos._

## <a name="overview"></a>Información general

Adjunta propiedades permiten un objeto para asignar un valor para una propiedad que no se define su propia clase. Por ejemplo, secundario, pueden usar elementos adjunta propiedades para informar a su elemento primario de cómo se van a presentar en la interfaz de usuario. El [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) control permite a la fila y columna de un elemento secundario que se especifica estableciendo el `Grid.Row` y `Grid.Column` propiedades adjuntas. `Grid.Row` y `Grid.Column` son propiedades adjuntas porque se establecen en los elementos que son elementos secundarios de un `Grid`, en lugar de en el `Grid` propio.

Propiedades enlazables deben implementarse como propiedades adjuntas en los escenarios siguientes:

- Cuando es necesario tener un mecanismo de configuración de propiedades disponible para las clases distinto de la definición de clase.
- Cuando la clase representa un servicio que necesita para integrarse fácilmente con otras clases.

Para obtener más información acerca de las propiedades enlazables, vea [propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="creating-and-consuming-an-attached-property"></a>Crear y utilizar una propiedad adjunta

El proceso de creación de una propiedad adjunta es como sigue:

1. Crear un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instancia con uno de los [ `CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) sobrecargas del método.
1. Proporcionar `static` `Get` *PropertyName* y `Set` *PropertyName* métodos como los descriptores de acceso para la propiedad adjunta.

### <a name="creating-a-property"></a>Crear una propiedad

Al crear una propiedad adjunta para su uso en otros tipos, la clase donde se crea la propiedad no tiene que derivan de [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/). Sin embargo, el *destino* propiedad para los descriptores de acceso debe ser de o derivarse de [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/).

Una propiedad adjunta puede crearse mediante la declaración de un `public static readonly` propiedad de tipo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/). La propiedad enlazable debe establecerse en el valor devuelto de uno de los [ `BindableProperty.CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) sobrecargas del método. La declaración debe estar dentro del cuerpo de la clase propietaria, pero fuera de las definiciones de miembros.

El código siguiente muestra un ejemplo de una propiedad asociada:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Esto crea una propiedad adjunta con el nombre `HasShadow`, del tipo `bool`. La propiedad pertenece a la `ShadowEffect` clase y tiene un valor predeterminado de `false`. La convención de nomenclatura para las propiedades asociadas es que el identificador de la propiedad adjunta debe coincidir con el nombre de propiedad especificado en el `CreateAttached` método, con "Property" anexada. Por lo tanto, en el ejemplo anterior, el identificador de la propiedad adjunta es `HasShadowProperty`.

Para obtener más información acerca de cómo crear propiedades enlazables, ni siquiera los parámetros que se pueden especificar durante la creación, consulte [crear y consumir una propiedad enlazable](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property).

### <a name="creating-accessors"></a>Crear descriptores de acceso

Estática `Get` *PropertyName* y `Set` *PropertyName* métodos son necesarios como descriptores de acceso de la propiedad adjunta, en caso contrario, el sistema de propiedades podrá usar el propiedad adjunta. El `Get` *PropertyName* descriptor de acceso debe ajustarse a la siguiente firma:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

El `Get` *PropertyName* descriptor de acceso debe devolver el valor que se encuentra en la correspondiente `BindableProperty` field para la propiedad adjunta. Esto puede lograrse mediante una llamada a la [ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/) método, pasando el identificador de propiedad enlazable en la que se va a obtener el valor y, a continuación, convertir el valor resultante al tipo requerido.

El `Set` *PropertyName* descriptor de acceso debe ajustarse a la siguiente firma:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

El `Set` *PropertyName* descriptor de acceso debe establecer el valor de los correspondientes `BindableProperty` field para la propiedad adjunta. Esto puede lograrse mediante una llamada a la [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método, pasando el identificador de propiedad enlazable en la que se va a establecer el valor y el valor que se va a establecer.

Para ambos descriptores de acceso, el *destino* objeto debe ser de o derivarse de [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/).

En el ejemplo de código siguiente se muestra los descriptores de acceso para el `HasShadow` propiedad asociada:

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consuming-an-attached-property"></a>Consumir una propiedad adjunta

Una vez creada una propiedad adjunta, se puede consumir desde XAML o código. En XAML, esto se logra mediante la declaración de un espacio de nombres con un prefijo con la declaración de espacio de nombres que indica el nombre de espacio de nombres de Common Language Runtime (CLR) y, opcionalmente, un nombre de ensamblado. Para obtener más información, consulte [espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md).

En el ejemplo de código siguiente se muestra un espacio de nombres XAML para un tipo personalizado que contiene una propiedad adjunta, que se define dentro del mismo ensamblado que el código de aplicación que hace referencia el tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

La declaración de espacio de nombres, a continuación, se utiliza cuando estableciendo la propiedad adjunta en un control determinado, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>Consumir una propiedad adjunta con un estilo

Las propiedades adjuntas también pueden agregarse a un control por un estilo. El ejemplo de código XAML siguiente muestra un *explícita* estilo que usa el `HasShadow` propiedad adjunta, que se pueden aplicar a [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controles:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

El [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) pueden aplicarse a un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) estableciendo su [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedad a la `Style` instancia mediante el `StaticResource`extensión de marcado, como se muestra en el ejemplo de código siguiente:

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Para obtener más información sobre los estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Escenarios avanzados

Al crear una propiedad adjunta, hay una serie de parámetros opcionales que se pueden establecer para habilitar escenarios avanzados de propiedad adjunta. Se trata de detectar los cambios de propiedad, validar los valores de propiedad y la conversión de valores de propiedad. Para obtener más información, consulte [escenarios avanzados](~/xamarin-forms/xaml/bindable-properties.md#advanced).

## <a name="summary"></a>Resumen

Este artículo proporciona una introducción a las propiedades asociadas y se ha mostrado cómo crear y consumir. Una propiedad adjunta es un tipo especial de propiedad enlazable, definido en una clase pero asociadas a otros objetos y reconocible en XAML como atributos que contienen una clase y un nombre de propiedad separados por puntos.


## <a name="related-links"></a>Vínculos relacionados

- [Propiedades enlazables](~/xamarin-forms/xaml/bindable-properties.md)
- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Efecto de sombra (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
