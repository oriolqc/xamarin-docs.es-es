---
title: Casilla de verificación de Xamarin.Forms
description: La casilla de verificación de Xamarin.Forms es un tipo de botón que o bien se puede comprobar o está vacío. Cuando se activa una casilla de verificación, se considera en. Cuando una casilla de verificación está vacía, se considera estar apagado.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: 42631b1b67dc1d342e9f8666916604e68ee158d8
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517924"
---
# <a name="xamarinforms-checkbox"></a>Casilla de verificación de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)

Xamarin.Forms `CheckBox` es un tipo de botón que puede ser activado ni estar vacío. Cuando se activa una casilla de verificación, se considera en. Cuando una casilla de verificación está vacía, se considera estar apagado.

`CheckBox` define un `bool` propiedad denominada `IsChecked`, lo que indica si el `CheckBox` está activada. Esta propiedad también está respaldada por un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede cambiar el estilo y ser el destino de los enlaces de datos.

> [!NOTE]
> El `IsChecked` propiedad enlazable tiene un modo de enlace predeterminada de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay).

`CheckBox` define un `CheckedChanged` evento que se desencadena cuando el `IsChecked` los cambios de propiedad, mediante la manipulación de usuario o cuando una aplicación establece el `IsChecked` propiedad. El `CheckedChangedEventArgs` objeto que acompaña a la `CheckedChanged` el evento tiene una propiedad única denominada `Value`, del tipo `bool`. Cuando se desencadena el evento, el valor de la `Value` propiedad está establecida en el nuevo valor de la `IsChecked` propiedad.

## <a name="create-a-checkbox"></a>Crear un control CheckBox

El ejemplo siguiente muestra cómo crear una instancia de un `CheckBox` en XAML:

```xaml
<CheckBox />
```

Este XAML hace que aparezca en la que se muestra en las capturas de pantalla siguiente:

![Captura de pantalla de una casilla vacía, en iOS y Android](checkbox-images/checkbox-empty.png "casilla vacía")

De forma predeterminada, el `CheckBox` está vacío. El `CheckBox` puede comprobarse la manipulación del usuario, o estableciendo la `IsChecked` propiedad `true`:

```xaml
<CheckBox IsChecked="true" />
```

Este XAML hace que aparezca en la que se muestra en las capturas de pantalla siguiente:

![Captura de pantalla de una casilla activada, en iOS y Android](checkbox-images/checkbox-checked.png "activada la casilla de verificación")

Como alternativa, un `CheckBox` pueden crearse en el código:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Responder al cambio de estado de una casilla de verificación

Cuando el `IsChecked` los cambios de propiedad, mediante la manipulación de usuario o cuando una aplicación establece el `IsChecked` propiedad, el `CheckedChanged` desencadena el evento. Puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

El archivo de código subyacente contiene el controlador para el `CheckedChanged` eventos:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

El `sender` argumento es el `CheckBox` responsable de este evento. Se puede usar para tener acceso a la `CheckBox` objeto, o para distinguir entre varias `CheckBox` objetos que comparten el mismo `CheckedChanged` eventos.

Como alternativa, un controlador de eventos para el `CheckedChanged` se pueden registrar eventos en el código:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Enlazar datos de un control CheckBox

El `CheckedChanged` controlador de eventos se puede eliminar mediante el uso de enlace de datos y desencadenadores para responder a un `CheckBox` está activado o está vacío:

```xaml
<CheckBox x:Name="checkBox" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

En este ejemplo, el [ `Label` ](xref:Xamarin.Forms.Label) usa una expresión de enlace en un desencadenador de datos para supervisar la `IsChecked` propiedad de la `CheckBox`. Cuando esta propiedad se convierte en `true`, `FontAttributes` y `FontSize` propiedades de la `Label` cambiar. Cuando el `IsChecked` propiedad devuelve al `false`, el `FontAttributes` y `FontSize` propiedades de la `Label` se restablecen a su estado inicial.

En las capturas de pantalla siguiente, se muestra la captura de pantalla de iOS la [ `Label` ](xref:Xamarin.Forms.Label) formato cuando el `CheckBox` está vacío, mientras que Android la captura de pantalla muestra la `Label` formato cuando el `CheckBox` está activado:

[![Captura de pantalla de los datos enlazados de casilla de verificación, en iOS y Android](checkbox-images/checkbox-databinding.png "casilla enlazado a datos")](checkbox-images/checkbox-databinding-large.png#lightbox "casilla enlazado a datos")

Para obtener más información acerca de los desencadenadores, consulte [Xamarin.Forms desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Deshabilitar un control Checkbox

A veces, una aplicación entra en un estado donde un `CheckBox` que se está comprobando no es una operación válida. En tales casos, el `CheckBox` puede deshabilitarse estableciendo su `IsEnabled` propiedad `false`.

## <a name="checkbox-appearance"></a>Apariencia de casilla de verificación

Además de las propiedades que `CheckBox` hereda el [ `View` ](xref:Xamarin.Forms.View) (clase), `CheckBox` también define un `Color` propiedad que establece el color en un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

Las capturas de pantalla siguientes muestran una serie de activado `CheckBox` objetos, donde cada objeto tiene su `Color` propiedad establecida en otro [ `Color` ](xref:Xamarin.Forms.Color):

![Captura de pantalla de las casillas de verificación coloreada en iOS y Android](checkbox-images/checkbox-colors.png "casilla de verificación de color")

## <a name="checkbox-visual-states"></a>Casilla de verificación estados visuales

`CheckBox` tiene un `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState) que puede utilizarse para iniciar un cambio visual en el `CheckBox` cuando se activa.

En el siguiente ejemplo XAML se muestra cómo definir un estado visual para el `IsChecked` estado:

```xaml
<CheckBox ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="IsChecked">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Green" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</CheckBox>
```

En este ejemplo, el `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState) especifica que, cuando el `CheckBox` está activada, su `Color` propiedad se establecerá en verde. El `Normal` `VisualState` especifica que, cuando el `CheckBox` está en estado normal, su `Color` propiedad se establecerá en rojo. Por lo tanto, el efecto general es que el `CheckBox` está en rojo cuando está vacía y verde cuando se activa.

Para obtener más información acerca de los estados visuales, vea [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de casilla de verificación (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)
- [Desencadenadores de Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Administrador de estado Visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
