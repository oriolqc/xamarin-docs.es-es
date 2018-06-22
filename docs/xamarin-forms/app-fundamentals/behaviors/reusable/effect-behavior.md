---
title: EffectBehavior reutilizable
description: Los comportamientos son un enfoque útil para agregar un efecto a un control, quitar el efecto de modelo de control de código de archivos de código subyacente. Este artículo muestra cómo utilizar un comportamiento de Xamarin.Forms para agregar un efecto a un control.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: a1612d1e87f0e05c859babd93fd03ac9a5736b47
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30785060"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior reutilizable

_Los comportamientos son un enfoque útil para agregar un efecto a un control, quitar el efecto de modelo de control de código de archivos de código subyacente. Este artículo muestra cómo utilizar un comportamiento de Xamarin.Forms para agregar un efecto a un control._

## <a name="overview"></a>Información general

El `EffectBehavior` clase es un comportamiento personalizado Xamarin.Forms reutilizable que agrega un [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) instancia a un control cuando el comportamiento está asociado al control y quita el `Effect` cuando es el comportamiento de instancia Desconectar del control.

Las siguientes propiedades de comportamiento deben establecerse para usar el comportamiento:

- **Grupo** : el valor de la [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) atributo para la clase de efecto.
- **Nombre** : el valor de la [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributo para la clase de efecto.

Para obtener más información acerca de los efectos, consulte [efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="creating-the-behavior"></a>Crear el comportamiento

El `EffectBehavior` clase se deriva de la [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (clase), donde `T` es un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/). Esto significa que la `EffectBehavior` clase puede asociarse a cualquier control de Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Implementar propiedades enlazables

El `EffectBehavior` clase define dos [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instancias, que se usan para agregar una [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) a un control cuando se adjunta el comportamiento del control. Estas propiedades se muestran en el ejemplo de código siguiente:

```csharp
public class EffectBehavior : Behavior<View>
{
  public static readonly BindableProperty GroupProperty =
    BindableProperty.Create ("Group", typeof(string), typeof(EffectBehavior), null);
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(EffectBehavior), null);

  public string Group {
    get { return (string)GetValue (GroupProperty); }
    set { SetValue (GroupProperty, value); }
  }

  public string Name {
    get { return(string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }
  ...
}
```

Cuando el `EffectBehavior` se consume, el `Group` propiedad debe establecerse en el valor de la [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) atributo para el efecto. Además, el `Name` propiedad debe establecerse en el valor de la [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributo para la clase de efecto.

### <a name="implementing-the-overrides"></a>Implementar las invalidaciones

El `EffectBehavior` clase invalidaciones el [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) y [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) métodos de la [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) de la clase, como se muestra en el código siguiente ejemplo:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  protected override void OnAttachedTo (BindableObject bindable)
  {
    base.OnAttachedTo (bindable);
    AddEffect (bindable as View);
  }

  protected override void OnDetachingFrom (BindableObject bindable)
  {
    RemoveEffect (bindable as View);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

El [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método realiza el programa de instalación mediante una llamada a la `AddEffect` método, pasando el control adjunto como un parámetro. El [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método realiza la limpieza mediante una llamada a la `RemoveEffect` método, pasando el control adjunto como un parámetro.

### <a name="implementing-the-behavior-functionality"></a>Implementar la funcionalidad del comportamiento

El propósito del comportamiento es agregar el [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) definido en el `Group` y `Name` propiedades a un control cuando el comportamiento está asociado al control y quitar el `Effect` cuando es el comportamiento Desconectar del control. La funcionalidad de comportamiento principal se muestra en el ejemplo de código siguiente:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  void AddEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Add (GetEffect ());
    }
  }

  void RemoveEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Remove (GetEffect ());
    }
  }

  Effect GetEffect ()
  {
    if (!string.IsNullOrWhiteSpace (Group) && !string.IsNullOrWhiteSpace (Name)) {
      return Effect.Resolve (string.Format ("{0}.{1}", Group, Name));
    }
    return null;
  }
}
```

El `AddEffect` método se ejecuta en respuesta a la `EffectBehavior` va a adjuntar a un control y recibe el control adjunto como un parámetro. El método, a continuación, agrega el efecto recuperado para el control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección. El `RemoveEffect` método se ejecuta en respuesta a la `EffectBehavior` separarse de un control y recibe el control adjunto como un parámetro. El método, a continuación, quita el efecto del control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección.

El `GetEffect` método usa la [ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/) método para recuperar el [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/). El efecto se encuentra a través de una concatenación de la `Group` y `Name` valores de propiedad. Si no le proporciona una plataforma el efecto, el `Effect.Resolve` método devolverá no`null` valor.

## <a name="consuming-the-behavior"></a>Consumir el comportamiento

El `EffectBehavior` clase se pueden adjuntar a la [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) colección de un control, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Behaviors>
    <local:EffectBehavior Group="Xamarin" Name="LabelShadowEffect" />
  </Label.Behaviors>
</Label>
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};
label.Behaviors.Add (new EffectBehavior {
  Group = "Xamarin",
  Name = "LabelShadowEffect"
});
```

El `Group` y `Name` propiedades del comportamiento se establecen en los valores de la [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) y [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributos para la clase de efecto de cada uno específico de plataforma proyecto.

En tiempo de ejecución, cuando se adjunta el comportamiento a la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) (control), el `Xamarin.LabelShadowEffect` se agregarán al control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección. Esto da como resultado una sombra que se agrega el texto mostrado por el `Label` controlar, como se muestra en las capturas de pantalla siguiente:

![](effect-behavior-images/screenshots.png "Aplicación de ejemplo EffectsBehavior")

La ventaja de usar este comportamiento para agregar y quitar los efectos de controles es que el código de control de efecto de modelo puede quitarse de archivos de código subyacente.

## <a name="summary"></a>Resumen

Este artículo se muestra mediante un comportamiento para agregar un efecto a un control. El `EffectBehavior` clase es un comportamiento personalizado Xamarin.Forms reutilizable que agrega un [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) instancia a un control cuando el comportamiento está asociado al control y quita el `Effect` cuando es el comportamiento de instancia Desconectar del control.


## <a name="related-links"></a>Vínculos relacionados

- [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamiento de un efecto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Behavior](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Behavior<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
