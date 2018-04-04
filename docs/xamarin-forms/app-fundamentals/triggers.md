---
title: Desencadenadores
description: Responder a los cambios de la interfaz de usuario con XAML
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 3b0cba75c8a62d571e82abb3bd2e27cac1e0d075
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="triggers"></a>Desencadenadores

Desencadenadores permiten expresar acciones mediante declaración en XAML que cambiar la apariencia de los controles basados en eventos o cambios de propiedad.

Puede asignar un desencadenador directamente a un control, o agregar a un diccionario de recursos de nivel de aplicación o página se aplique a varios controles.

Hay cuatro tipos de desencadenador:

* [Desencadenador de propiedad](#property) -se produce cuando se establece una propiedad en un control en un valor determinado.

* [Desencadenador de datos](#data) : utiliza enlace de datos para el desencadenamiento basándose en las propiedades de otro control.

* [Desencadenador de eventos](#event) -se produce cuando se produce un evento en el control.

* [Desencadenador de múltiples](#multi) -permite varias condiciones de desencadenador para establecerse antes de que se produce una acción.

<a name="property" />

## <a name="property-triggers"></a>Desencadenadores de propiedad

Un desencadenador simple se puede expresar puramente en XAML, agregar un `Trigger` elemento a un control desencadena la recopilación.
Este ejemplo muestra un desencadenador que cambia un `Entry` color de fondo cuando reciben el foco:

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
             Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Son las partes importantes de la declaración del desencadenador:

* **TargetType** -el tipo de control que se aplica el desencadenador.

* **Propiedad** : la propiedad en el control que se supervisa.

* **Valor** -el valor, cuando se produce por la propiedad supervisada, que hace que Active el desencadenador.

* **Establecedor** -una colección de `Setter` se pueden agregar elementos y cuando se cumple la condición desencadenadora. Debe especificar el `Property` y `Value` para establecer.

* **EnterActions y ExitActions** (no mostrado) - se escriben en código y puede usarse además (o instead of) `Setter` elementos. Únicamente son [se describe a continuación](#enterexit).

### <a name="applying-a-trigger-using-a-style"></a>Aplicar un desencadenador mediante un estilo

Los desencadenadores también pueden agregarse a un `Style` declaración de un control, en una página o una aplicación `ResourceDictionary`. Este ejemplo declara un estilo implícito (es decir. ningún `Key` está establecido) lo que significa que se aplicará a todos los `Entry` controles de la página.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

<a name="data" />

## <a name="data-triggers"></a>Desencadenadores de datos

Desencadenadores de datos utilizan el enlace de datos para supervisar otro control para hacer que el `Setter`s a llamar. En lugar de la `Property` de atributo en un desencadenador de propiedad, establezca el `Binding` atributo para supervisar con el valor especificado.

El ejemplo siguiente usa la sintaxis de enlace de datos `{Binding Source={x:Reference entry}, Path=Text.Length}` que es ¿cómo nos referimos a las propiedades de otro control. Cuando la longitud de la `entry` es cero, se activa el desencadenador. En este ejemplo el desencadenador deshabilita el botón cuando la entrada está vacía.

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </Button.Triggers>
</Button>
```

Sugerencia: al evaluar `Path=Text.Length` siempre proporciona un valor predeterminado para la propiedad de destino (p. ej. `Text=""`) porque de lo contrario, será `null` y el desencadenador no funcionará como se espera.

Además de especificar `Setter`s también puede proporcionar [ `EnterActions` y `ExitActions` ](#enterexit).

<a name="event" />

## <a name="event-triggers"></a>Desencadenadores de eventos

El `EventTrigger` elemento solo requiere un `Event` propiedad, como `"Clicked"` en el ejemplo siguiente.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Observe que hay ningún `Setter` elementos, pero en su lugar una referencia a una clase definida por `local:NumericValidationTriggerAction` que requiere el `xmlns:local` deben declararse en la página de XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

La propia clase implementa `TriggerAction` lo que significa que debe proporcionar un nombre para invalidar el `Invoke` método al que se llama cada vez que se produce el evento de desencadenador.

Debe ser una implementación de la acción de desencadenador:

* Implementar la interfaz genérica `TriggerAction<T>` (clase), con el parámetro genérico correspondiente con el tipo de control que se aplicará el desencadenador. Puede usar superclases como `VisualElement` para escribir las acciones del desencadenador que funcionan con una variedad de controles, o especifican un tipo de control, como `Entry`.

* Invalidar el `Invoke` método - Esto se llama cada vez que se cumplen los criterios del desencadenador.

* Si lo desea exponer propiedades que se pueden establecer en el código XAML cuando se declara el desencadenador (como `Anchor`, `Scale`, y `Length` en este ejemplo).

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

Las propiedades expuestas por la acción del desencadenador pueden establecerse en la declaración de XAML como se indica a continuación:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Tenga cuidado al uso compartido de desencadenadores en una `ResourceDictionary`, por lo que cualquier estado que se configura una vez se aplicará a todos ellos, se compartirá entre controles de una instancia.

Tenga en cuenta que los desencadenadores de eventos no admiten `EnterActions` y `ExitActions` [se describe a continuación](#enterexit).

<a name="multi" />

## <a name="multi-triggers"></a>Desencadenadores múltiples

A `MultiTrigger` es similar a una `Trigger` o `DataTrigger` salvo que puede haber más de una condición. Todas las condiciones deben cumplirse antes de la `Setter`s se activan.

Este es un ejemplo de un desencadenador para un botón que se enlaza a dos entradas diferentes (`email` y `phone`):

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>

  <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

El `Conditions` también podría contener la colección `PropertyCondition` elementos de la forma siguiente:

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>Creación de un desencadenador de múltiples "requerir a todos"

El desencadenador de múltiples sólo actualiza su control cuando se cumplen todas las condiciones. Pruebas para "las longitudes de campo son cero" (por ejemplo, una página de inicio de sesión que deben cumplirse todas las entradas) son complicado porque desea que una condición "donde Text.Length > 0", pero esto no se puede expresar en XAML.

Esto puede hacerse con un `IValueConverter`. El código del convertidor debajo la transformaciones el `Text.Length` enlace en un `bool` que indica si un campo está vacío o no:


```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;           // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Para utilizar este convertidor en un desencadenador de múltiples, primero agréguelo al diccionario de recursos de la página (junto con un personalizado `xmlns:local` definición de espacio de nombres):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

A continuación se muestra el código XAML. Tenga en cuenta las siguientes diferencias en el primer ejemplo de desencadenador de múltiples:

* El botón tiene `IsEnabled="false"` establecido de forma predeterminada.
* Las condiciones de desencadenador múltiples usan el convertidor para activar el `Text.Length` valor en un valor booleano.
* Cuando todas las condiciones son `true`, el establecedor hace que el botón `IsEnabled` propiedad `true`.

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

Estas capturas de pantalla muestran la diferencia entre los dos ejemplos de desencadenador de múltiples anteriores. En la parte superior de las pantallas, texto de entrada en una sola `Entry` es suficiente para habilitar la **guardar** botón.
En la parte inferior de las pantallas, el **inicio de sesión** botón permanece inactivo hasta que ambos campos contienen datos.


![](triggers-images/multi-requireall.png "Ejemplos multiTrigger")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions y ExitActions

Otra manera de implementar cambios cuando se produce un desencadenador es mediante la adición de `EnterActions` y `ExitActions` colecciones y especificando `TriggerAction<T>` implementaciones.

Puede proporcionar *ambos* `EnterActions` y `ExitActions` como `Setter`s en un desencadenador, pero tenga en cuenta que la `Setter`s se denominan inmediatamente (no esperan a la `EnterAction` o `ExitAction` a Complete). O bien puede realizar todo el contenido en el código y no utilizar `Setter`s en absoluto.

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
                        <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Como siempre, cuando una clase se hace referencia en XAML debe declarar un espacio de nombres como `xmlns:local` como se muestra aquí:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

La `FadeTriggerAction` código se muestra a continuación:

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public FadeTriggerAction() {}

    public int StartsFrom { set; get; }

    protected override void Invoke (VisualElement visual)
    {
            visual.Animate("", new Animation( (d)=>{
                var val = StartsFrom==1 ? d : 1-d;
                visual.BackgroundColor = Color.FromRgb(1, val, 1);

            }),
            length:1000, // milliseconds
            easing: Easing.Linear);
    }
}
```

Nota: `EnterActions` y `ExitActions` se omiten en **desencadenadores de eventos**.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de desencadenadores](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Documentación de API de Xamarin.Forms](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/)
