---
title: Comportamientos de Xamarin.Forms
description: Comportamientos de Xamarin.Forms se crean mediante la derivación desde el comportamiento o el comportamiento<T> clase. En este artículo se muestra cómo crear y usar comportamientos de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 7e057567ec0bb72e9bcc016d4a9fef3af78a3ea1
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998900"
---
# <a name="xamarinforms-behaviors"></a>Comportamientos de Xamarin.Forms

_Comportamientos de Xamarin.Forms se crean mediante la derivación desde el comportamiento o el comportamiento<T> clase. En este artículo se muestra cómo crear y consumir los comportamientos de Xamarin.Forms._

## <a name="overview"></a>Información general

El proceso de creación de un comportamiento de Xamarin.Forms es como sigue:

1. Cree una clase que hereda de la [ `Behavior` ](xref:Xamarin.Forms.Behavior) o [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (clase), donde `T` es el tipo del control al que debe aplicarse el comportamiento.
1. Invalidar el [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método para realizar cualquier configuración necesaria.
1. Invalidar el [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método para realizar cualquier limpieza necesaria.
1. Implementar la funcionalidad básica del comportamiento.

Esto da como resultado la estructura que se muestra en el ejemplo de código siguiente:

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

El [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método se activa justo después de que está asociado el comportamiento a un control. Este método recibe una referencia al control al que está conectado y puede usarse para registrar los controladores de eventos o realizar otra instalación necesaria para admitir la funcionalidad del comportamiento. Por ejemplo, podría suscribirse a un evento en un control. A continuación, se implementaría la funcionalidad del comportamiento del controlador de eventos para el evento.

El [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método se desencadena cuando se quita el comportamiento del control. Este método recibe una referencia al control al que se adjunta y se usa para realizar cualquier limpieza necesaria. Por ejemplo, pudo cancelar la suscripción a un evento en un control para evitar pérdidas de memoria.

El comportamiento, a continuación, puede utilizarse en adjuntarlo a la [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) colección del control adecuado.

## <a name="creating-a-xamarinforms-behavior"></a>Creación de un comportamiento de Xamarin.Forms

La aplicación de ejemplo se muestra un `NumericValidationBehavior`, que resalta el valor especificado por el usuario en un [ `Entry` ](xref:Xamarin.Forms.Entry) controlar en rojo, si no es un `double`. El comportamiento se muestra en el ejemplo de código siguiente:

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

El `NumericValidationBehavior` deriva el [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (clase), donde `T` es un [ `Entry` ](xref:Xamarin.Forms.Entry). El [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método registra un controlador de eventos para el [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) evento, con el [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método anular el registro de la `TextChanged`pérdidas de eventos para evitar que la memoria. Proporciona la funcionalidad básica del comportamiento del `OnEntryTextChanged` método, que analiza el valor especificado por el usuario en el `Entry`y establece el [ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor) propiedad a rojo si el valor no es un `double`.

> [!NOTE]
> Xamarin.Forms no establece la `BindingContext` de un comportamiento, ya que los comportamientos pueden compartir y aplicar a varios controles mediante estilos.

## <a name="consuming-a-xamarinforms-behavior"></a>Consumir un comportamiento de Xamarin.Forms

Cada control Xamarin.Forms tiene una [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) colección, al que se pueden agregar uno o varios comportamientos como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

El equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) en C# se muestra en el ejemplo de código siguiente:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

En tiempo de ejecución el comportamiento responderá a la interacción con el control, según la implementación del comportamiento. Las capturas de pantalla siguientes muestran el comportamiento que responder a la entrada no válida:

[![](creating-images/screenshots-sml.png "Ejemplo de aplicación con el comportamiento de Xamarin.Forms")](creating-images/screenshots.png#lightbox "aplicación con el comportamiento de Xamarin.Forms de ejemplo")

> [!NOTE]
> Comportamientos están escritos para un tipo de control (o superclase que puede aplicar a muchos controles), y solo se debe agregar a un control compatible. Intenta adjuntar un comportamiento a un control incompatible se producirá una excepción producida.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Consumir un comportamiento de Xamarin.Forms con un estilo

Los comportamientos también pueden utilizarse en un estilo explícito o implícito. Sin embargo, crear un estilo que establece el [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) propiedad de un control no es posible porque la propiedad es de solo lectura. La solución es agregar una propiedad adjunta a la clase de comportamiento que controla agregando y quitando el comportamiento. El proceso es como sigue:

1. Agregue una propiedad adjunta a la clase de comportamiento que se usará para controlar la adición o eliminación del comportamiento al control al que se le el comportamiento asociado. Asegúrese de que registra la propiedad adjunta un `propertyChanged` delegado que se ejecutará cuando cambia el valor de la propiedad.
1. Crear un `static` captador y establecedor de la propiedad adjunta.
1. Implementar la lógica en el `propertyChanged` delegado para agregar y quitar el comportamiento.

En el ejemplo de código siguiente se muestra una propiedad adjunta que controla la adición y eliminación de la `NumericValidationBehavior`:

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

El `NumericValidationBehavior` clase contiene una propiedad adjunta mencionada `AttachBehavior` con un `static` captador y establecedor, que controla la adición o eliminación del comportamiento para el control al que se adjuntará. Esto adjunta la propiedad registra el `OnAttachBehaviorChanged` método que se ejecutará cuando cambia el valor de la propiedad. Este método agrega o quita el comportamiento para el control, en función del valor de la `AttachBehavior` propiedad adjunta.

El siguiente ejemplo de código muestra un *explícita* de estilo para el `NumericValidationBehavior` que usa el `AttachBehavior` adjunta la propiedad y que se pueden aplicar a [ `Entry` ](xref:Xamarin.Forms.Entry) controles:

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

El [ `Style` ](xref:Xamarin.Forms.Style) puede aplicarse a un [ `Entry` ](xref:Xamarin.Forms.Entry) control estableciendo su [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propiedad a la `Style` instancia mediante el `StaticResource` extensión de marcado, como se muestra en el ejemplo de código siguiente:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Para obtener más información sobre los estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Aunque puede agregar propiedades enlazables a un comportamiento que se establece o se consultan en XAML, si crea los comportamientos que tienen el estado no se comparten entre los controles en un `Style` en un `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Quitar un comportamiento de un Control

El [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método se desencadena cuando se quita de un control de un comportamiento y se usa para realizar cualquier limpieza necesaria como cancelar la suscripción de un evento para evitar una pérdida de memoria. Sin embargo, los comportamientos no implícitamente dejaron de controles a menos que el control [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) colección se modifica mediante una `Remove` o `Clear` método. En el ejemplo de código siguiente se muestra cómo quitar un comportamiento específico de un control `Behaviors` colección:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Del como alternativa, el control [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) colección se puede borrar, como se muestra en el ejemplo de código siguiente:

```csharp
entry.Behaviors.Clear();
```

Además, tenga en cuenta que no se quitan implícitamente los comportamientos de los controles cuando las páginas se extraen de la pila de navegación. En su lugar, debe quitarse explícitamente antes de quede fuera del ámbito de las páginas.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo crear y consumir los comportamientos de Xamarin.Forms. Los comportamientos de Xamarin.Forms se crean mediante la derivación de las clases [`Behavior`](xref:Xamarin.Forms.Behavior) o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1).


## <a name="related-links"></a>Vínculos relacionados

- [Comportamiento de Xamarin.Forms (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Aplicar el comportamiento de Xamarin.Forms con un estilo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [Comportamiento](xref:Xamarin.Forms.Behavior)
- [Comportamiento<T>](xref:Xamarin.Forms.Behavior`1)
