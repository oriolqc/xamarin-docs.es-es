---
title: Propiedades enlazables
description: En Xamarin.Forms, se amplía la funcionalidad de common language runtime (CLR) propiedades propiedades enlazables. Una propiedad enlazable es un tipo especial de propiedad, donde se realiza el seguimiento de valor de la propiedad por el sistema de propiedades de Xamarin.Forms. Este artículo proporciona una introducción a las propiedades enlazables y muestra cómo crear y consumirlos.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: 7e1d3c82036ef703014ae548a6719937e89d22f4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="bindable-properties"></a>Propiedades enlazables

_En Xamarin.Forms, se amplía la funcionalidad de common language runtime (CLR) propiedades propiedades enlazables. Una propiedad enlazable es un tipo especial de propiedad, donde se realiza el seguimiento de valor de la propiedad por el sistema de propiedades de Xamarin.Forms. Este artículo proporciona una introducción a las propiedades enlazables y muestra cómo crear y consumirlos._

## <a name="overview"></a>Información general

Propiedades enlazables amplían la funcionalidad de la propiedad CLR mediante copia de una propiedad con un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) tipo, en lugar de una propiedad con un campo de respaldo. El propósito de propiedades enlazables es proporcionar un sistema de propiedad que admite enlace de datos, estilos, plantillas y valores se establecen a través de relaciones de elementos primarios y secundarios. Además, las propiedades enlazables pueden proporcionar valores predeterminados, la validación de valores de propiedad y las devoluciones de llamada que supervisen los cambios de propiedad.

Propiedades deben implementarse como propiedades enlazables para admitir uno o más de las siguientes características:

- Actúa como válido *destino* propiedad para el enlace de datos.
- Establecer la propiedad a través de un [estilo](~/xamarin-forms/user-interface/styles/index.md).
- Proporcionar un valor de propiedad predeterminado que es diferente del valor predeterminado para el tipo de la propiedad.
- Validar el valor de la propiedad.
- Supervisión de los cambios de propiedad.

Ejemplos de propiedades enlazables Xamarin.Forms [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/), [ `Button.BorderRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/), y [ `StackLayout.Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/). Cada propiedad enlazable le corresponde una `public static readonly` propiedad de tipo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) que se expone en la misma clase y que es el identificador de la propiedad se puede enlazar. Por ejemplo, el identificador de propiedad enlazable correspondiente para el `Label.Text` propiedad es [ `Label.TextProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Label.TextProperty/).

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>Crear y usar una propiedad enlazable

El proceso de creación de una propiedad enlazable es como sigue:

1. Crear un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instancia con uno de los [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) sobrecargas del método.
1. Definir descriptores de acceso de propiedad para el [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instancia.

Tenga en cuenta que todos los [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) se deben crear instancias en el subproceso de interfaz de usuario. Esto significa que sólo el código que se ejecuta en el subproceso de interfaz de usuario puede obtener o establecer el valor de una propiedad enlazable. Sin embargo, `BindableProperty` pueden tener acceso a instancias de otros subprocesos mediante la serialización para el subproceso de interfaz de usuario con el [ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/) método.

### <a name="creating-a-property"></a>Crear una propiedad

Para crear un `BindableProperty` instancia, debe derivar la clase contenedora de la [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) clase. Sin embargo, la `BindableObject` clase es alta en la jerarquía de clases, por lo que la mayoría de las clases que se utiliza para propiedades enlazables de soporte técnico de funcionalidad de interfaz de usuario.

Puede crearse una propiedad enlazable declarando un `public static readonly` propiedad de tipo [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/). La propiedad enlazable debe establecerse en el valor devuelto de uno de los [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) sobrecargas del método. La declaración debe estar dentro del cuerpo de [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) clase derivada, pero fuera de las definiciones de miembros.

Como mínimo, debe especificarse un identificador al crear un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/), junto con los parámetros siguientes:

- El nombre de la [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/).
- Tipo de la propiedad.
- El tipo del objeto propietario.
- El valor predeterminado para la propiedad. Esto garantiza que la propiedad siempre devuelve un valor predeterminado concreto cuando está establecido, y puede ser diferente del valor predeterminado para el tipo de la propiedad. El valor predeterminado será restaurar cuando la [ `ClearValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.ClearValue/p/Xamarin.Forms.BindableProperty/) método se llama en la propiedad se puede enlazar.

El código siguiente muestra un ejemplo de una propiedad enlazable, con un identificador y los valores de los cuatro parámetros obligatorios:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Esto crea una [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instancia denominada `EventName`, del tipo `string`. La propiedad pertenece a la `EventToCommandBehavior` clase y tiene un valor predeterminado de `null`. La convención de nomenclatura para las propiedades enlazables es que el identificador de propiedad enlazable debe coincidir con el nombre de propiedad especificado en el `Create` método, con "Property" anexada. Por lo tanto, en el ejemplo anterior, el identificador de propiedad enlazable es `EventNameProperty`.

Si lo desea, cuando se crea un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) de instancia, los siguientes parámetros se pueden especificar:

- El modo de enlace. Esto se utiliza para especificar la dirección en la que se propagarán cambios de valor de propiedad. En el modo de enlace predeterminada, los cambios se propagarán desde el *origen* a la *destino*.
- Un delegado de validación que se invocará cuando se establece el valor de propiedad. Para obtener más información, consulte [las devoluciones de llamada de validación](#validation).
- Un delegado de cambio de propiedad que se invocará cuando el valor de propiedad ha cambiado. Para obtener más información, consulte [detectar cambios de propiedad](#propertychanges).
- Una propiedad de cambio de delegado que se invoca cuando cambia el valor de propiedad. Este delegado tiene la misma firma que el delegado de la propiedad ha cambiado.
- Un delegado de valor forzado que se invocará cuando el valor de propiedad ha cambiado. Para obtener más información, consulte [Coerce devoluciones de llamada de valor](#coerce).
- Un `Func` que se usa para inicializar un valor de propiedad predeterminado. Para obtener más información, consulte [crear un valor predeterminado con un elemento Func](#defaultfunc).

### <a name="creating-accessors"></a>Crear descriptores de acceso

Descriptores de acceso de propiedad son necesarios para usar la sintaxis de la propiedad para tener acceso a una propiedad enlazable. El `Get` descriptor de acceso debe devolver el valor que se encuentra en la propiedad enlazable correspondiente. Esto puede lograrse mediante una llamada a la [ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/) método, pasando el identificador de propiedad enlazable en la que se va a obtener el valor y, a continuación, convertir el resultado al tipo requerido. El `Set` descriptor de acceso debe establecer el valor de la propiedad enlazable correspondiente. Esto puede lograrse mediante una llamada a la [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) método, pasando el identificador de propiedad enlazable en la que se va a establecer el valor y el valor que se va a establecer.

En el ejemplo de código siguiente se muestra los descriptores de acceso para el `EventName` propiedad enlazable:

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>Consumir una propiedad enlazable

Una vez creada una propiedad enlazable, se puede consumir desde XAML o código. En XAML, esto se logra mediante la declaración de un espacio de nombres con un prefijo con la declaración de espacio de nombres que indica el nombre del espacio de nombres CLR y, opcionalmente, un nombre de ensamblado. Para obtener más información, consulte [espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md).

En el ejemplo de código siguiente se muestra un espacio de nombres XAML para un tipo personalizado que contiene una propiedad enlazable, que se define dentro del mismo ensamblado que el código de aplicación que hace referencia el tipo personalizado:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La declaración de espacio de nombres se utiliza al establecer el `EventName` propiedad enlazable, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  ...
});
```

<a name="advanced" />

## <a name="advanced-scenarios"></a>Escenarios avanzados

Al crear un [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) de la instancia, hay una serie de parámetros opcionales que se pueden establecer para habilitar escenarios de propiedad enlazable avanzada. Esta sección explora estos escenarios.

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>Detectar cambios de propiedad

A `static` método de devolución de llamada de cambio de propiedad se puede registrar con una propiedad enlazable mediante la especificación de la `propertyChanged` parámetro para el [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) método. El método de devolución de llamada especificado se invocará cuando cambie el valor de la propiedad se puede enlazar.

El siguiente ejemplo de código muestra cómo el `EventName` propiedad enlazable registra el `OnEventNameChanged` método como un método de devolución de llamada de cambio de propiedad:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

En el método de devolución de llamada de cambio de propiedad, el [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) parámetro se usa para indicar qué instancia de la clase propietaria ha informado de un cambio y los valores de los dos `object` parámetros representan los valores antiguos y nuevos de la propiedad se puede enlazar.

<a name="validation" />

### <a name="validation-callbacks"></a>Devoluciones de llamada de validación

A `static` método de devolución de llamada de validación se puede registrar con una propiedad enlazable mediante la especificación de la `validateValue` parámetro para el [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) método. El método de devolución de llamada especificado se invocará cuando se establece el valor de la propiedad se puede enlazar.

El siguiente ejemplo de código muestra cómo el `Angle` propiedad enlazable registra el `IsValidValue` método como un método de devolución de llamada de validación:

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

Las devoluciones de llamada de validación se proporcionan con un valor y debe devolver `true` si el valor es válido para la propiedad, en caso contrario, `false`. Se producirá una excepción si una devolución de llamada de validación devuelve `false`, que debe controlarse con el desarrollador. Un uso típico de un método de devolución de llamada de validación es restringir los valores de números enteros o dobles cuando se establece la propiedad se puede enlazar. Por ejemplo, el `IsValidValue` método comprueba que el valor de propiedad es una `double` dentro del intervalo de 0 a 360.

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>Coerce las devoluciones de llamada de valor

A `static` convertir el valor del método de devolución de llamada se puede registrar con una propiedad enlazable mediante la especificación de la `coerceValue` parámetro para el [ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/) método. El método de devolución de llamada especificado se invocará cuando cambie el valor de la propiedad se puede enlazar.

Convertir el valor que se usan las devoluciones de llamada para forzar una reevaluación de una propiedad enlazable cuando cambia el valor de la propiedad. Por ejemplo, una devolución de llamada de valor forzado puede usarse para asegurarse de que el valor de una propiedad enlazable no es mayor que el valor de otra propiedad enlazable.

El siguiente ejemplo de código muestra cómo el `Angle` propiedad enlazable registra el `CoerceAngle` método como un método de devolución de llamada de valor forzado:

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle) {
    input = homePage.MaximumAngle;
  }

  return input;
}
```

El `CoerceAngle` método comprueba el valor de la `MaximumAngle` propiedad y si la `Angle` es mayor el valor de propiedad, convierte el valor en el `MaximumAngle` valor de propiedad.

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>Crear un valor predeterminado con un elemento Func

Un `Func` puede usarse para inicializar el valor predeterminado de una propiedad enlazable, como se muestra en el ejemplo de código siguiente:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

El `defaultValueCreator` parámetro está establecido en un `Func` que invoca la [ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/) método para devolver un `double` que representa el tamaño de la fuente que se usa en un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) en la plataforma nativa.

## <a name="summary"></a>Resumen

Este artículo proporciona una introducción a las propiedades enlazables y se ha mostrado cómo crear y consumirlos. Una propiedad enlazable es un tipo especial de propiedad, donde se realiza el seguimiento de valor de la propiedad por el sistema de propiedades de Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Evento al comportamiento del comando (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Devolución de llamada de validación (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/validationcallback/)
- [Convertir el valor de devolución de llamada (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/coercevaluecallback/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
