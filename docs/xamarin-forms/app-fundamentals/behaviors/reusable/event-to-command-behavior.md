---
title: Reusable EventToCommandBehavior
description: "Pueden utilizar comportamientos para asociar comandos a los controles que no se diseñaron para interactuar con los comandos. Este artículo muestra cómo utilizar un comportamiento de Xamarin.Forms para invocar un comando cuando se activa un evento."
ms.topic: article
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: d82a1391feca9187cf2aca4394509447aeac6a18
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="reusable-eventtocommandbehavior"></a>Reusable EventToCommandBehavior

_Pueden utilizar comportamientos para asociar comandos a los controles que no se diseñaron para interactuar con los comandos. Este artículo muestra cómo utilizar un comportamiento de Xamarin.Forms para invocar un comando cuando se activa un evento._

## <a name="overview"></a>Información general

El `EventToCommandBehavior` clase es un comportamiento personalizado Xamarin.Forms reutilizable que se ejecuta un comando como respuesta a *cualquier* desencadenamiento de eventos. De forma predeterminada, los argumentos de evento para el evento se pasará al comando y puede tener opcionalmente convierten por un [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementación.

Las siguientes propiedades de comportamiento deben establecerse para usar el comportamiento:

- **EventName** : el nombre del evento que escucha el comportamiento.
- **Comando** : el **ICommand** va a ejecutar. El comportamiento espera encontrar el `ICommand` a la instancia en la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del control adjunto, que se puede heredar de un elemento primario.

También se pueden establecer las siguientes propiedades de comportamiento opcional:

- **CommandParameter** : un `object` que se pasará al comando.
- **Convertidor de** : un [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementación que cambiará el formato de los datos del argumento de evento que se pasan entre *origen* y *destino*por el motor de enlace.

## <a name="creating-the-behavior"></a>Crear el comportamiento

El `EventToCommandBehavior` clase se deriva de la `BehaviorBase<T>` (clase), que a su vez se deriva de la [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) clase. El propósito de la `BehaviorBase<T>` clase es proporcionar una clase base para cualquier comportamiento de Xamarin.Forms que requiere la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del comportamiento se establezca en el control asociado. Esto garantiza que el comportamiento puede enlazar a y ejecutar la `ICommand` especificado por el `Command` propiedad cuando se consume el comportamiento.

El `BehaviorBase<T>` clase proporciona un reemplazable [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método que establece la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del comportamiento y un reemplazable [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)método que limpie el `BindingContext`. Además, la clase almacena una referencia para el control asociado en la `AssociatedObject` propiedad.

### <a name="implementing-bindable-properties"></a>Implementar propiedades enlazables

El `EventToCommandBehavior` clase define cuatro [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) comando definan las instancias, que se ejecutan en un usuario cuando se activa un evento. Estas propiedades se muestran en el ejemplo de código siguiente:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  public static readonly BindableProperty EventNameProperty =
    BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
  public static readonly BindableProperty CommandProperty =
    BindableProperty.Create ("Command", typeof(ICommand), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty CommandParameterProperty =
    BindableProperty.Create ("CommandParameter", typeof(object), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty InputConverterProperty =
    BindableProperty.Create ("Converter", typeof(IValueConverter), typeof(EventToCommandBehavior), null);

  public string EventName { ... }
  public ICommand Command { ... }
  public object CommandParameter { ... }
  public IValueConverter Converter { ...  }
  ...
}
```

Cuando el `EventToCommandBehavior` se consume la clase, el `Command` propiedad debe estar enlazado a datos un `ICommand` que se ejecutará en respuesta a la activación de eventos que se define en el `EventName` propiedad. El comportamiento esperará hasta que encuentra la `ICommand` en el [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del control adjunto.

De forma predeterminada, los argumentos de evento para el evento se pasará al comando. Estos datos se pueden convertir, opcionalmente, cuando se pasan entre *origen* y *destino* por el motor de enlace, especificando un [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementación como el `Converter` valor de propiedad. Como alternativa, se puede pasar un parámetro al comando especificando el `CommandParameter` valor de propiedad.

### <a name="implementing-the-overrides"></a>Implementar las invalidaciones

El `EventToCommandBehavior` clase invalidaciones el [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) y [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) métodos de la `BehaviorBase<T>` de la clase, como se muestra en el ejemplo de código siguiente:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  protected override void OnAttachedTo (View bindable)
  {
    base.OnAttachedTo (bindable);
    RegisterEvent (EventName);
  }

  protected override void OnDetachingFrom (View bindable)
  {
    DeregisterEvent (EventName);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

El [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) método realiza el programa de instalación mediante una llamada a la `RegisterEvent` método, pasando el valor de la `EventName` propiedad como un parámetro. El [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método realiza la limpieza mediante una llamada a la `DeregisterEvent` método, pasando el valor de la `EventName` propiedad como un parámetro.

### <a name="implementing-the-behavior-functionality"></a>Implementar la funcionalidad del comportamiento

El propósito del comportamiento es ejecutar el comando definido por el `Command` propiedad en respuesta a la activación de eventos definido por el `EventName` propiedad. La funcionalidad de comportamiento principal se muestra en el ejemplo de código siguiente:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  void RegisterEvent (string name)
  {
    if (string.IsNullOrWhiteSpace (name)) {
      return;
    }

    EventInfo eventInfo = AssociatedObject.GetType ().GetRuntimeEvent (name);
    if (eventInfo == null) {
      throw new ArgumentException (string.Format ("EventToCommandBehavior: Can't register the '{0}' event.", EventName));
    }
    MethodInfo methodInfo = typeof(EventToCommandBehavior).GetTypeInfo ().GetDeclaredMethod ("OnEvent");
    eventHandler = methodInfo.CreateDelegate (eventInfo.EventHandlerType, this);
    eventInfo.AddEventHandler (AssociatedObject, eventHandler);
  }

  void OnEvent (object sender, object eventArgs)
  {
    if (Command == null) {
      return;
    }

    object resolvedParameter;
    if (CommandParameter != null) {
      resolvedParameter = CommandParameter;
    } else if (Converter != null) {
      resolvedParameter = Converter.Convert (eventArgs, typeof(object), null, null);
    } else {
      resolvedParameter = eventArgs;
    }       

    if (Command.CanExecute (resolvedParameter)) {
      Command.Execute (resolvedParameter);
    }
  }
  ...
}
```

El `RegisterEvent` método se ejecuta en respuesta a la `EventToCommandBehavior` va a adjuntar a un control y recibe el valor de la `EventName` propiedad como un parámetro. El método, a continuación, intenta localizar el evento definido en el `EventName` propiedad, en el control asociado. Siempre que el evento se puede encontrar, el `OnEvent` se registra el método para que sea el método de controlador para el evento.

El `OnEvent` método se ejecuta en respuesta a la activación de eventos que se define en el `EventName` propiedad. Siempre que el `Command` propiedad hace referencia a válido `ICommand`, el método intenta recuperar un parámetro que se pasa a la `ICommand` como se indica a continuación:

- Si el `CommandParameter` propiedad define un parámetro, se recuperará.
- De lo contrario, si la `Converter` propiedad define una [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) implementación, el convertidor se ejecuta y convierte los datos del argumento de evento que se pasan entre *origen* y *destino* por el motor de enlace.
- En caso contrario, los argumentos de evento se supone que el parámetro.

Los datos enlazados `ICommand` y, a continuación, se ejecuta, pasando el parámetro al comando, siempre que el [ `CanExecute` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Command.CanExecute/p/System.Object/) método `true`.

Aunque no se muestra aquí, el `EventToCommandBehavior` también incluye una `DeregisterEvent` método que es ejecutado por el [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) método. El `DeregisterEvent` método se utiliza para buscar y anular el registro de los eventos definidos en el `EventName` propiedad para realizar la limpieza de las pérdidas de memoria posible.

## <a name="consuming-the-behavior"></a>Consumir el comportamiento

El `EventToCommandBehavior` clase se pueden adjuntar a la [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) colección de un control, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ListView ItemsSource="{Binding People}">
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}"
                                  Converter="{StaticResource SelectedItemConverter}" />
  </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var listView = new ListView ();
listView.SetBinding (ItemsView<Cell>.ItemsSourceProperty, "People");
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
  Converter = new SelectedItemEventArgsToSelectedItemConverter ()
});

var selectedItemLabel = new Label ();
selectedItemLabel.SetBinding (Label.TextProperty, "SelectedItemText");
```

El `Command` propiedad del comportamiento está enlazado a datos la `OutputAgeCommand` propiedad de ViewModel asociado, mientras el `Converter` propiedad está establecida en el `SelectedItemConverter` de instancia, que devuelve el [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/)de la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) desde el [ `SelectedItemChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SelectedItemChangedEventArgs/).

En tiempo de ejecución, el comportamiento responderá a la interacción con el control. Cuando se selecciona un elemento en el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) se desencadenará el evento, que se ejecutará el `OutputAgeCommand` en el modelo de vista. A su vez, se actualizan lo ViewModel `SelectedItemText` propiedad que la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) enlaza a, como se muestra en las capturas de pantalla siguiente:

[![](event-to-command-behavior-images/screenshots-sml.png "Ejemplo de aplicación con EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "aplicación con EventToCommandBehavior de ejemplo")

La ventaja de usar este comportamiento para ejecutar un comando cuando se activa un evento, es que se pueden asociados con controles que no se han diseñado para interactuar con los comandos de comandos. Además, esto quita código de control de eventos-modelo de archivos de código subyacente.

## <a name="summary"></a>Resumen

Este artículo se muestra mediante un comportamiento de Xamarin.Forms para invocar un comando cuando se activa un evento. Pueden utilizar comportamientos para asociar comandos a los controles que no se diseñaron para interactuar con los comandos.


## <a name="related-links"></a>Vínculos relacionados

- [Comportamiento de EventToCommand (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Behavior](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Behavior<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
