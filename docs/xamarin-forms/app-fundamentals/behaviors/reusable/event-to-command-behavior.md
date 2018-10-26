---
title: EventToCommandBehavior reutilizable
description: Comportamientos pueden utilizarse para asociar comandos a los controles que no se han diseñado para interactuar con los comandos. En este artículo muestra cómo utilizar un comportamiento de Xamarin.Forms para invocar un comando cuando se activa un evento.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 92d0f393ff8d96ac9c0040ab7c46a364418cf956
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109619"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior reutilizable

_Comportamientos pueden utilizarse para asociar comandos a los controles que no se han diseñado para interactuar con los comandos. En este artículo muestra cómo utilizar un comportamiento de Xamarin.Forms para invocar un comando cuando se activa un evento._

## <a name="overview"></a>Información general

El `EventToCommandBehavior` clase es un comportamiento personalizado Xamarin.Forms reutilizable que se ejecuta un comando como respuesta a *cualquier* desencadenamiento de eventos. De forma predeterminada, los argumentos de evento para el evento se pasará al comando y puede, opcionalmente, puede convertir mediante una [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementación.

Las siguientes propiedades de comportamiento deben establecerse para usar el comportamiento:

- **EventName** : el nombre del evento escucha el comportamiento.
- **Comando** : el **ICommand** que se ejecutará. El comportamiento que espera encontrar la `ICommand` a la instancia en el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del control adjunto, que puede heredar de un elemento primario.

También se pueden establecer las siguientes propiedades de comportamiento opcional:

- **CommandParameter** : un `object` que se pasarán al comando.
- **Convertidor** : un [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementación que cambiará el formato de los datos del argumento de evento mientras se pasa entre *origen* y *destino*por el motor de enlace.

## <a name="creating-the-behavior"></a>Crear el comportamiento

El `EventToCommandBehavior` clase se deriva de la `BehaviorBase<T>` (clase), que a su vez se deriva de la [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) clase. El propósito de la `BehaviorBase<T>` clase es proporcionar una clase base para cualquier comportamiento de Xamarin.Forms que requieren la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamiento para establecerse en el control adjunto. Esto garantiza que el comportamiento puede enlazar a y ejecute el `ICommand` especificado por el `Command` propiedad cuando se consume el comportamiento.

El `BehaviorBase<T>` clase proporciona un reemplazable [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método que establece el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamiento y un reemplazable [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))método que limpia la `BindingContext`. Además, la clase almacena una referencia al control adjunto en el `AssociatedObject` propiedad.

### <a name="implementing-bindable-properties"></a>Implementar propiedades enlazables

El `EventToCommandBehavior` clase define cuatro [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) comando cuando se activa un evento definido por el instancias, que se ejecutan en un usuario. Estas propiedades se muestran en el ejemplo de código siguiente:

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

Cuando el `EventToCommandBehavior` se consume la clase, el `Command` propiedad debe estar enlazado a datos con un `ICommand` que se ejecutará en respuesta a la activación de eventos que se define en el `EventName` propiedad. El comportamiento esperará hasta que encuentre el `ICommand` en el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del control adjunto.

De forma predeterminada, los argumentos de evento para el evento se pasarán al comando. Estos datos se pueden convertir, opcionalmente, cuando se pasa entre *origen* y *destino* por el motor de enlace, especificando un [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementación como el `Converter` valor de propiedad. Como alternativa, se puede pasar un parámetro para el comando especificando el `CommandParameter` valor de propiedad.

### <a name="implementing-the-overrides"></a>Implementación de las invalidaciones

El `EventToCommandBehavior` clase invalidaciones el [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) y [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) métodos de la `BehaviorBase<T>` clase, como se muestra en el ejemplo de código siguiente:

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

El [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) método realiza la instalación mediante una llamada a la `RegisterEvent` método, pasando el valor de la `EventName` propiedad como un parámetro. El [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método realiza la limpieza mediante una llamada a la `DeregisterEvent` método, pasando el valor de la `EventName` propiedad como un parámetro.

### <a name="implementing-the-behavior-functionality"></a>Implementación de la funcionalidad del comportamiento

El propósito del comportamiento es ejecutar el comando definido por el `Command` propiedad en respuesta a la activación del evento definido por el `EventName` propiedad. La funcionalidad básica de comportamiento se muestra en el ejemplo de código siguiente:

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

El `RegisterEvent` método se ejecuta en respuesta a la `EventToCommandBehavior` se adjunta a un control y recibe el valor de la `EventName` propiedad como un parámetro. El método intenta a continuación, busque el evento definido en el `EventName` propiedad en el control adjunto. Siempre que el evento puede encontrarse, el `OnEvent` se registra el método para que sea el método de controlador para el evento.

El `OnEvent` método se ejecuta en respuesta a la activación de eventos que se define en el `EventName` propiedad. Siempre que el `Command` propiedad hace referencia a un válido `ICommand`, el método intenta recuperar un parámetro para pasarlo a la `ICommand` como sigue:

- Si el `CommandParameter` propiedad define un parámetro, se recuperará.
- De lo contrario, si la `Converter` propiedad define un [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) implementación, el convertidor se ejecuta y convierte los datos del argumento de evento mientras se pasa entre *origen* y *destino* por el motor de enlace.
- En caso contrario, los argumentos del evento se supone que el parámetro.

Los datos enlazados `ICommand` y, a continuación, se ejecuta, pasando el parámetro del comando, siempre que el [ `CanExecute` ](xref:Xamarin.Forms.Command.CanExecute(System.Object)) devuelve del método `true`.

Aunque no aparece aquí, el `EventToCommandBehavior` también incluye un `DeregisterEvent` método ejecutado por el [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) método. El `DeregisterEvent` método se utiliza para localizar y anular el registro de evento definido en el `EventName` propiedad, al limpiar las pérdidas de memoria posibles.

## <a name="consuming-the-behavior"></a>Consumir el comportamiento

El `EventToCommandBehavior` clase puede asociarse a la [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) colección de un control, como se muestra en el ejemplo de código XAML siguiente:

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

El `Command` propiedad del comportamiento está enlazado a datos la `OutputAgeCommand` propiedad del ViewModel asociado, mientras que el `Converter` propiedad está establecida en el `SelectedItemConverter` de instancia, que devuelve el [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)de la [ `ListView` ](xref:Xamarin.Forms.ListView) desde el [ `SelectedItemChangedEventArgs` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs).

En tiempo de ejecución, el comportamiento responderá a la interacción con el control. Cuando se selecciona un elemento en el [ `ListView` ](xref:Xamarin.Forms.ListView), [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) se desencadenará el evento, que se ejecutará el `OutputAgeCommand` en ViewModel. A su vez actualiza el modelo de vista `SelectedItemText` propiedad que el [ `Label` ](xref:Xamarin.Forms.Label) enlaza a, como se muestra en las capturas de pantalla siguiente:

[![](event-to-command-behavior-images/screenshots-sml.png "Ejemplo de aplicación con EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "aplicación con EventToCommandBehavior de ejemplo")

La ventaja de usar este comportamiento para ejecutar un comando cuando se activa un evento, es que se pueden asociados con controles que no se han diseñado para interactuar con los comandos de comandos. Además, esto quita cantidad control de eventos de código reutilizable de archivos de código subyacente.

## <a name="summary"></a>Resumen

En este artículo se muestra utilizando un comportamiento de Xamarin.Forms para invocar un comando cuando se activa un evento. Comportamientos pueden utilizarse para asociar comandos a los controles que no se han diseñado para interactuar con los comandos.


## <a name="related-links"></a>Vínculos relacionados

- [Comportamiento de EventToCommand (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Comportamiento](xref:Xamarin.Forms.Behavior)
- [Comportamiento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
