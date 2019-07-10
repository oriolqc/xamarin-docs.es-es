---
title: Conmutador de Xamarin.Forms
description: El modificador de Xamarin.Forms es un tipo de botón que se puede manipular por el usuario para alternar entre activar y desactivar los Estados. Este artículo explica cómo usar la clase de modificador para mostrar un elemento de interfaz de usuario de alternancia.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/03/2019
ms.openlocfilehash: 22a17f9a916d94a3a0f44a451512de43c943e95a
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675041"
---
# <a name="xamarinforms-switch"></a>Conmutador de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)

Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch) es un botón de alternancia horizontal que se puede manipular por el usuario para alternar entre activar y desactivar los Estados, que se representan mediante un `boolean` valor. El `Switch` clase hereda de [ `View` ](xref:Xamarin.Forms.View).

La siguiente captura de pantalla muestra un `Switch` en controlar su **en** y **desactivar** alternar los Estados en iOS y Android:

![Captura de pantalla de conmutadores de activar y desactivar los Estados, en iOS y Android](switch-images/switch-states-default.png "cambia en iOS y Android")

El `Switch` control define dos propiedades:

* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) es un `Color` que afecta a cómo el `Switch` está representado en el estado de alternancia, o **en**, estado.
* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) es un `boolean` valor que indica si el `Switch` es **en**.

Estas propiedades están respaldadas por un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa el `Switch` puede cambiar el estilo y ser el destino de los enlaces de datos.

El `Switch` control define un `Toggled` evento que se desencadena cuando el `IsToggled` los cambios de propiedad, mediante la manipulación de usuario o cuando una aplicación establece el `IsToggled` propiedad. El `ToggledEventArgs` objeto que acompaña a la `Toggled` el evento tiene una propiedad única denominada `Value`, del tipo `bool`. Cuando se desencadena el evento, el valor de la `Value` propiedad refleja el nuevo valor de la `IsToggled` propiedad.

## <a name="create-a-switch"></a>Crear un conmutador

Un `Switch` se pueden crear instancias en XAML. Su `IsToggled` propiedad puede establecerse para activar o desactivar el `Switch`. De forma predeterminada, el `IsToggled` propiedad es `false`. El ejemplo siguiente muestra cómo crear una instancia de un `Switch` en XAML con el elemento opcional `IsToggled` conjunto de propiedades:

```xaml
<Switch IsToggled="true"/>
```

Un `Switch` también se pueden crear en el código:

```csharp
Switch switch = new Switch { IsToggled = true };
```

### <a name="switch-style-properties"></a>Cambiar las propiedades de estilo

El `OnColor` propiedad puede establecerse para definir el `Switch` de color cuando se cambia a su **en** estado. El ejemplo siguiente muestra cómo crear una instancia de un `Switch` en XAML con el `OnColor` conjunto de propiedades:

```xaml
<Switch OnColor="Orange" />
```

El `OnColor` propiedad también se puede establecer al crear un `Switch` en el código:

```csharp
Switch switch = new Switch { OnColor = Color.Orange };
```

La siguiente captura de pantalla muestra la `Switch` en su **en** y **desactivar** alternar los Estados, con el `OnColor` propiedad establecida en `Color.Orange` en iOS y Android:

![Captura de pantalla de conmutadores de activar y desactivar los Estados, en iOS y Android](switch-images/switch-states-oncolor.png "cambia en iOS y Android")

## <a name="respond-to-a-switch-state-change"></a>Responder a un cambio de estado del conmutador

Cuando el `IsToggled` los cambios de propiedad, mediante la manipulación de usuario o cuando una aplicación establece el `IsToggled` propiedad, el `Toggled` desencadena el evento. Puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<Switch Toggled="OnToggled" />
```

El archivo de código subyacente contiene el controlador para el `Toggled` eventos:

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

El `sender` argumento en el controlador de eventos es el `Switch` responsable de activar este evento. Puede usar el `sender` propiedad para tener acceso a la `Switch` objeto, o para distinguir entre varias `Switch` objetos que comparten el mismo `Toggled` controlador de eventos.

El `Toggled` también se puede asignar el controlador de eventos en el código:

```csharp
Switch switch = new Switch {...};
switch.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Enlazar datos de un conmutador

El `Toggled` controlador de eventos se puede eliminar mediante el uso de enlace de datos y desencadenadores para responder a un `Switch` cambiar los Estados de alternancia.

```xaml
<Switch x:Name="styleSwitch" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference styleSwitch}, Path=IsToggled}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

En este ejemplo, el [ `Label` ](xref:Xamarin.Forms.Label) utiliza una expresión de enlace en un `DataTrigger` para supervisar la `IsToggled` propiedad de la `Switch` denominado `styleSwitch`. Cuando esta propiedad se convierte en `true`, `FontAttributes` y `FontSize` propiedades de la `Label` cambian. Cuando el `IsToggled` propiedad devuelve al `false`, el `FontAttributes` y `FontSize` propiedades de la `Label` se restablecen a su estado inicial.

Para obtener información acerca de los desencadenadores, vea [Xamarin.Forms desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Deshabilitar un conmutador

Una aplicación puede entrar en un estado donde el `Switch` no es una operación válida que se va a activar o desactivar. En tales casos, el `Switch` puede deshabilitarse estableciendo su `IsEnabled` propiedad `false`. Esto impedirá que los usuarios puedan manipular la `Switch`.

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de conmutador](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)
* [Desencadenadores de Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
