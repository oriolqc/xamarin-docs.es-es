---
title: Comportamientos de Xamarin.Forms
description: Comportamientos de Xamarin.Forms se crean mediante la derivación desde el comportamiento o<T> clase. Este artículo demuestra cómo crear y utilizar los comportamientos de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 3a86e7713620eff90db995941eb35df7bc393a76
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848296"
---
# <a name="xamarinforms-behaviors"></a>Comportamientos de Xamarin.Forms

_Comportamientos de Xamarin.Forms se crean mediante la derivación desde el comportamiento o<T> clase. Este artículo demuestra cómo crear y utilizar los comportamientos de Xamarin.Forms._

## <a name="overview"></a>Información general

El proceso de creación de un comportamiento de Xamarin.Forms es como sigue:

1. Cree una clase que hereda de la [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (clase), donde `T` es el tipo del control al que debe aplicarse el comportamiento.
1. Invalidar el [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método para realizar cualquier configuración necesaria.
1. Invalidar el [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método para realizar cualquier limpieza necesaria.
1. Implementar la funcionalidad básica del comportamiento.

El resultado es la estructura que se muestra en el ejemplo de código siguiente:

```csharp
public class CustomBehavior : Behavior<View>
{
    protected override void OnAttachedTo (View bindable)
    {
        base.OnAttachedTo (bindable);
        // Perform setup
    }

    protected override void OnDetachingFrom (View bindable)
    {
        base.OnDetachingFrom (bindable);
        // Perform clean up
    }

    // Behavior implementation
}
```

El [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método se activa inmediatamente después de que el comportamiento asociado a un control. Este método recibe una referencia al control a la que se ha adjuntado y puede usarse para registrar los controladores de eventos o realizar otra instalación que se necesita para admitir la funcionalidad del comportamiento. Por ejemplo, puede suscribirse a un evento en un control. A continuación, se implementa la funcionalidad del comportamiento en el controlador de eventos para el evento.

El [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método se desencadena cuando se quita el comportamiento del control. Este método recibe una referencia al control al que está conectado y se utiliza para realizar cualquier limpieza necesaria. Por ejemplo, puede cancelar la suscripción a un evento en un control para evitar pérdidas de memoria.

Puede usar el comportamiento si se adjunta a la [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) colección del control adecuado.

## <a name="creating-a-xamarinforms-behavior"></a>Crear un comportamiento de Xamarin.Forms

La aplicación de ejemplo muestra un `NumericValidationBehavior`, que resalta el valor especificado por el usuario en un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control en rojo, si no es un `double`. El comportamiento se muestra en el ejemplo de código siguiente:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    protected override void OnAttachedTo(Entry entry)
    {
        entry.TextChanged += OnEntryTextChanged;
        base.OnAttachedTo(entry);
    }

    protected override void OnDetachingFrom(Entry entry)
    {
        entry.TextChanged -= OnEntryTextChanged;
        base.OnDetachingFrom(entry);
    }

    void OnEntryTextChanged(object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

El `NumericValidationBehavior` se deriva de la [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (clase), donde `T` es un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). El [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método registra un controlador de eventos para el [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) evento, con el [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método Anular registro el `TextChanged`pérdidas de evento con el fin de evitar que la memoria. Proporciona la funcionalidad básica del comportamiento del `OnEntryTextChanged` método, que analiza el valor especificado por el usuario en el `Entry`y establece la [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) propiedad a color rojo si el valor no es un `double`.

> [!NOTE]
> Xamarin.Forms no establece la `BindingContext` de un comportamiento, porque pueden compartirse y aplicar a diversos controles mediante los estilos de comportamientos.

## <a name="consuming-a-xamarinforms-behavior"></a>Consumir un comportamiento de Xamarin.Forms

Cada control Xamarin.Forms tiene un [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) colección, al que se pueden agregar uno o varios comportamientos tal como se muestra en el siguiente ejemplo de código XAML:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

El equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) en C# se muestra en el ejemplo de código siguiente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

En tiempo de ejecución el comportamiento responderá a la interacción con el control, según la implementación de comportamiento. Las capturas de pantalla siguientes muestran el comportamiento que responde a las entradas no válidas:

[![](creating-images/screenshots-sml.png "Ejemplo de aplicación con el comportamiento de Xamarin.Forms")](creating-images/screenshots.png#lightbox "aplicación con el comportamiento de Xamarin.Forms de ejemplo")

> [!NOTE]
> Comportamientos están escritos para un tipo de control (o una superclase que puede aplicar a muchos controles), y solo se debe agregar a un control compatible. Si intenta adjuntar un comportamiento a un control incompatible producirá una excepción.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Consumir un comportamiento de Xamarin.Forms con un estilo

Comportamientos también pueden utilizarse un estilo explícito o implícito. Sin embargo, crear un estilo que establece el [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) propiedad de un control no es posible porque la propiedad es de solo lectura. La solución es agregar una propiedad adjunta a la clase de comportamiento que controla agregando y quitando el comportamiento. El proceso es como sigue:

1. Agregar una propiedad adjunta a la clase de comportamiento que se usará para controlar la adición o eliminación del comportamiento para el control al que se le el comportamiento asociado. Asegúrese de que registra la propiedad adjunta un `propertyChanged` delegado que se ejecutará cuando cambia el valor de la propiedad.
1. Crear un `static` captador y establecedor de la propiedad adjunta.
1. Implementar la lógica en el `propertyChanged` delegado para agregar y quitar el comportamiento.

En el ejemplo de código siguiente se muestra una propiedad adjunta que controla agregando y quitando el `NumericValidationBehavior`:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached ("AttachBehavior", typeof(bool), typeof(NumericValidationBehavior), false, propertyChanged: OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.Behaviors.Add (new NumericValidationBehavior ());
        } else {
            var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
            if (toRemove != null) {
                entry.Behaviors.Remove (toRemove);
            }
        }
    }
    ...
}
```

El `NumericValidationBehavior` clase contiene una propiedad adjunta con el nombre `AttachBehavior` con un `static` captador y establecedor, que controla la adición o eliminación del comportamiento del control al que se adjuntará. Esto adjunta los registros de propiedad del `OnAttachBehaviorChanged` método que se ejecutará cuando cambia el valor de la propiedad. Este método agrega o quita el comportamiento para el control, en función del valor de la `AttachBehavior` propiedad adjunta.

El siguiente ejemplo de código muestra un *explícita* de estilo para el `NumericValidationBehavior` que usa el `AttachBehavior` adjunta la propiedad y que pueden aplicarse a [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controles:

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

El [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) pueden aplicarse a un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control estableciendo su [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedad a la `Style` instancia mediante el `StaticResource` extensión de marcado, como se muestra en el ejemplo de código siguiente:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Para obtener más información sobre los estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Si bien puede agregar propiedades enlazables a un comportamiento que se establece o se consultan en XAML, si creas comportamientos que tienen el estado no se comparten entre los controles en un `Style` en un `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Quitar un comportamiento de un Control

El [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método se activa cuando un comportamiento se quita de un control y se utiliza para realizar cualquier limpieza necesaria como cancelar la suscripción de un evento para evitar una pérdida de memoria. Sin embargo, los comportamientos no implícitamente quitan de controles, a menos que el control [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) colección se modifica mediante una `Remove` o `Clear` método. En el ejemplo de código siguiente se muestra cómo quitar un comportamiento concreto de un control `Behaviors` colección:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Del como alternativa, el control [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) puede borrar, como se muestra en el ejemplo de código siguiente:

```csharp
entry.Behaviors.Clear();
```

Además, tenga en cuenta que no se quitan implícitamente comportamientos de controles cuando las páginas se extraen de la pila de navegación. En su lugar, debe quitarse explícitamente antes de páginas que salen de ámbito.

## <a name="summary"></a>Resumen

Este artículo muestra cómo crear y utilizar los comportamientos de Xamarin.Forms. Comportamientos de Xamarin.Forms se crean derivando de la [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) clase.


## <a name="related-links"></a>Vínculos relacionados

- [Comportamiento de Xamarin.Forms (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Comportamiento de Xamarin.Forms aplicado con un estilo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [Comportamiento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamiento<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
