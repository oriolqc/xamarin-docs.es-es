---
title: EffectBehavior reutilizable
description: Los comportamientos son un enfoque útil para agregar un efecto a un control, quitar el efecto de modelo código de archivos de código subyacente del control. En este artículo muestra cómo utilizar un comportamiento de Xamarin.Forms para agregar un efecto a un control.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 1ce7eda6f556041cbffc3793b00e8e2cba44d3d0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995786"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior reutilizable

_Los comportamientos son un enfoque útil para agregar un efecto a un control, quitar el efecto de modelo código de archivos de código subyacente del control. En este artículo muestra cómo utilizar un comportamiento de Xamarin.Forms para agregar un efecto a un control._

## <a name="overview"></a>Información general

El `EffectBehavior` clase es un comportamiento personalizado Xamarin.Forms reutilizable que agrega un [ `Effect` ](xref:Xamarin.Forms.Effect) instancia a un control cuando el comportamiento está asociado al control y quita el `Effect` cuando es el comportamiento de instancia Desasociar del control.

Las siguientes propiedades de comportamiento deben establecerse para usar el comportamiento:

- **Grupo** : el valor de la [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo para la clase del efecto.
- **Nombre** : el valor de la [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributo para la clase del efecto.

Para obtener más información acerca de los efectos, vea [efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="creating-the-behavior"></a>Crear el comportamiento

El `EffectBehavior` clase se deriva de la [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (clase), donde `T` es un [ `View` ](xref:Xamarin.Forms.View). Esto significa que el `EffectBehavior` clase puede asociarse a cualquier control de Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Implementar propiedades enlazables

El `EffectBehavior` clase define dos [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instancias, que se usan para agregar un [ `Effect` ](xref:Xamarin.Forms.Effect) a un control cuando el comportamiento está asociado al control. Estas propiedades se muestran en el ejemplo de código siguiente:

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

Cuando el `EffectBehavior` consumido, la `Group` propiedad debe establecerse en el valor de la [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo para el efecto. Además, el `Name` propiedad debe establecerse en el valor de la [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributo para la clase del efecto.

### <a name="implementing-the-overrides"></a>Implementación de las invalidaciones

El `EffectBehavior` clase invalidaciones el [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) y [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) métodos de la [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) clase, como se muestra en el código siguiente ejemplo:

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

El [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método realiza la instalación mediante una llamada a la `AddEffect` método, pasando el control adjunto como un parámetro. El [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método realiza la limpieza mediante una llamada a la `RemoveEffect` método, pasando el control adjunto como un parámetro.

### <a name="implementing-the-behavior-functionality"></a>Implementación de la funcionalidad del comportamiento

El propósito del comportamiento es agregar el [ `Effect` ](xref:Xamarin.Forms.Effect) definido en el `Group` y `Name` propiedades a un control cuando el comportamiento está asociado al control y quite el `Effect` cuando es el comportamiento Desasociar del control. La funcionalidad básica de comportamiento se muestra en el ejemplo de código siguiente:

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

El `AddEffect` método se ejecuta en respuesta a la `EffectBehavior` que se asocia a un control y recibe el control adjunto como un parámetro. El método, a continuación, agrega el efecto recuperado para el control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección. El `RemoveEffect` método se ejecuta en respuesta a la `EffectBehavior` desasociarse de un control y recibe el control adjunto como un parámetro. El método, a continuación, quita el efecto del control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección.

El `GetEffect` método usa la [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) método para recuperar el [ `Effect` ](xref:Xamarin.Forms.Effect). El efecto se encuentra a través de una concatenación de la `Group` y `Name` los valores de propiedad. Si una plataforma no proporciona el efecto, el `Effect.Resolve` devolverá el método que no es`null` valor.

## <a name="consuming-the-behavior"></a>Consumir el comportamiento

El `EffectBehavior` clase puede asociarse a la [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) colección de un control, como se muestra en el ejemplo de código XAML siguiente:

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

El `Group` y `Name` se establecen las propiedades del comportamiento en los valores de la [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) y [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributos para la clase de efecto de cada uno específico de plataforma proyecto.

En tiempo de ejecución, cuando el comportamiento está asociado a la [ `Label` ](xref:Xamarin.Forms.Label) (control), el `Xamarin.LabelShadowEffect` se agregarán al control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección. Esto da como resultado una sombra que se agregan al texto mostrado por el `Label` controlar, como se muestra en las capturas de pantalla siguiente:

![](effect-behavior-images/screenshots.png "Aplicación de ejemplo con EffectsBehavior")

La ventaja de usar este comportamiento para agregar y quitar los efectos de los controles es que se puede quitar cantidad efecto control de código reutilizable de archivos de código subyacente.

## <a name="summary"></a>Resumen

En este artículo se muestra utilizando un comportamiento para agregar un efecto a un control. El `EffectBehavior` clase es un comportamiento personalizado Xamarin.Forms reutilizable que agrega un [ `Effect` ](xref:Xamarin.Forms.Effect) instancia a un control cuando el comportamiento está asociado al control y quita el `Effect` cuando es el comportamiento de instancia Desasociar del control.


## <a name="related-links"></a>Vínculos relacionados

- [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportamiento de un efecto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Comportamiento](xref:Xamarin.Forms.Behavior)
- [Comportamiento<T>](xref:Xamarin.Forms.Behavior`1)
