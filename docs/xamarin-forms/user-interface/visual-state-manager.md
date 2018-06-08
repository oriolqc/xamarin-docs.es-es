---
title: El Administrador de estado Visual de Xamarin.Forms
description: Use el Administrador de estado Visual para realizar cambios en los elementos XAML basados en estados visuales establecer desde el código.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: 3c0330d8d6d07112350db007d0500d57c236dc24
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848029"
---
# <a name="the-xamarinforms-visual-state-manager"></a>El Administrador de estado Visual de Xamarin.Forms

_Use el Administrador de estado Visual para realizar cambios en los elementos XAML basados en estados visuales establecer desde el código._

Visual State Manager (VSM) es nuevo en Xamarin.Forms 3.0. VSM proporciona una forma estructurada para realizar cambios visuales en la interfaz de usuario desde el código. En la mayoría de los casos, la interfaz de usuario de la aplicación se define en XAML, y este código XAML incluye marcado que describe cómo afecta el Administrador de estado Visual a los objetos visuales de la interfaz de usuario.

La funcionalidad de VSM introduce el concepto de _estados visuales_. Una vista de Xamarin.Forms como un `Button` puede tener varios una apariencia visual distinta según su estado subyacente &mdash; si está deshabilitado, o presiona o, tiene foco de entrada. Se trata de los Estados del botón.

Estados visuales se recopilan en _grupos de estado visual_. Todos los estados visuales dentro de un grupo de estado visual son mutuamente excluyentes. Estados visuales y grupos de estado visual se identifican mediante cadenas de texto simple.

Xamarin.Forms Visual State Manager define un grupo de estado visual denominado "CommonStates" con tres estados visuales:

- "Normal"
- "Deshabilitado"
- "Centrado"

Este grupo de estados visuales se admite para todas las clases que derivan de [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), que es la clase base para [ `View` ](xref:Xamarin.Forms.View) y [ `Page` ](xref:Xamarin.Forms.Page). 

También puede definir sus propios grupos de estado visual y estados visuales, como en este artículo se demuestra.

> [!NOTE]
> Xamarin.Forms los programadores familiarizados con [desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md) son conscientes de que los desencadenadores también pueden realizar cambios en objetos visuales en la interfaz de usuario según los cambios en las propiedades de una vista o la activación de los eventos. Sin embargo, la utilización de desencadenadores para tratar con varias combinaciones de estos cambios puede ser algo confuso. Históricamente, el Administrador de estado Visual se introdujo en entornos basados en XAML de Windows para mitigar la confusión resultante de las combinaciones de estados visuales. VSM los estados visuales dentro de un grupo de estado visual siempre son mutuamente excluyentes. En cualquier momento, solo un estado de cada grupo es el estado actual.

## <a name="the-common-states"></a>Los Estados comunes

El Administrador de estado Visual permite incluir secciones en el archivo XAML que puede cambiar la apariencia visual de una vista si la vista es normal o deshabilitado, o tiene el foco de entrada. Se conocen como el _Estados comunes_.

Por ejemplo, suponga que tiene un `Entry` vista en la página, y desea que la apariencia visual de la `Entry` para cambiar de las maneras siguientes:

- El `Entry` debe tener un rosa en segundo plano cuando la `Entry` está deshabilitado.
- El `Entry` debe tener un fondo de color verde normalmente.
- El `Entry` debe expandir hasta dos veces su altura normal cuando tiene el foco de entrada.

Puede asociar el marcado VSM a una vista individual, o puede definir en un estilo si se aplica a varias vistas. Las dos secciones siguientes describen estos enfoques.

### <a name="vsm-markup-on-a-view"></a>Marcado VSM en una vista

Para asociar el marcado VSM a un `Entry` ver, en primer lugar separar el `Entry` en etiquetas inicial y final:

```xaml
<Entry FontSize="18">

</Entry>
```

Ha entregado un tamaño de fuente explícita porque uno de los Estados usará el `FontSize` propiedad duplicar el tamaño del texto en el `Entry`.

A continuación, inserte `VisualStateManager.VisualStateGroups` etiquetas entre esas etiquetas:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) es una propiedad enlazable adjunta definida por el [ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager) clase. (Para obtener más información sobre propiedades enlazables asociadas, vea el artículo [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md).) Se trata cómo la `VisualStateGroups` propiedad está asociada a la `Entry` objeto.

El `VisualStateGroups` propiedad es de tipo [ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList), que es una colección de [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) objetos. En el `VisualStateManager.VisualStateGroups` etiquetas, inserte un par de `VisualStateGroup` etiquetas para cada grupo de estados visuales que se va a incluir:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Tenga en cuenta que la `VisualStateGroup` etiqueta tiene una `x:Name` que indica el nombre del grupo de atributos. El `VisualStateGroup` clase define un `Name` propiedad que puede usar en su lugar:

```xaml
<VisualStateGroup Name="CommonStates">
```

Puede usar `x:Name` o `Name` , pero no ambos en el mismo elemento.

El `VisualStateGroup` clase define una propiedad denominada [ `States` ](xref:Xamarin.Forms.VisualStateGroup.States), que es una colección de [ `VisualState` ](xref:Xamarin.Forms.VisualState) objetos. `States` es el _propiedad de contenido_ de `VisualStateGroups` por lo que puede incluir la `VisualState` etiquetas directamente entre el `VisualStateGroup` etiquetas. (Contenido de propiedades se describen en el artículo [sintaxis XAML esencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

El paso siguiente es incluir un par de etiquetas para cada estado visual en ese grupo. También pueden identificarse mediante `x:Name` o `Name`:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` define una propiedad denominada [ `Setters` ](xref:Xamarin.Forms.VisualState.Setters), que es una colección de [ `Setter` ](xref:Xamarin.Forms.Setter) objetos. Estos son los mismos `Setter` objetos que se usan en un [ `Style` ](xref:Xamarin.Forms.Style) objeto.

`Setters` es _no_ la propiedad de contenido de `VisualState`, por lo que es necesario incluir etiquetas de elemento de propiedad para el `Setters` propiedad:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
    
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Ahora puede insertar uno o varios `Setter` objetos entre cada par de `Setters` etiquetas. Estos son los `Setter` objetos que definen los estados visuales se ha descrito anteriormente:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Cada `Setter` etiqueta indica el valor de una propiedad determinada cuando ese estado está activo. Cualquier propiedad que se hace referencia a un `Setter` objeto debe estar respaldado por una propiedad enlazable.

Marcado similar al siguiente es la base de la **VSM en la vista** página en el **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** programa de ejemplo. La página incluye tres `Entry` vistas, pero solo la segunda se tiene la marca de VSM asociada a él:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />

        <Label Text="Entry with VSM: " />

        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Tenga en cuenta que el segundo `Entry` también tiene un `DataTrigger` como parte de su `Trigger` colección. Esto hace que la `Entry` deshabilitará hasta que algo se escribe en la tercera `Entry`. Ésta es la página en el inicio que se ejecuta en la plataforma Universal de Windows (UWP), iOS y Android:

[![VSM en la vista: deshabilitado](vsm-images/VsmOnViewDisabled.png "VSM en vista - deshabilitada")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

El estado visual actual es "deshabilitado" por lo que el fondo de la segunda `Entry` es rosa en iOS y Android pantallas. La implementación de UWP de `Entry` no permite establecer el fondo de color cuando la `Entry` está deshabilitado. 

Cuando escriba algún texto en la tercera `Entry`, el segundo `Entry` modificadores en el estado "Normal" y el fondo es ahora CAL:

[![VSM en la vista: Normal](vsm-images/VsmOnViewNormal.png "VSM en vista - normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Al tocar el segundo `Entry`, obtiene el foco de entrada. Se activa en el estado de "Enfocado" y se expande hasta dos veces su altura:

[![VSM en la vista: centra](vsm-images/VsmOnViewFocused.png "VSM en vista - enfocada")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Tenga en cuenta que el `Entry` no conserva el fondo de color verde cuando recibe el foco de entrada. Como el Administrador de estado Visual cambia entre los estados visuales, no están establecidas las propiedades establecidas por el estado anterior. Tenga en cuenta que los estados visuales son mutuamente excluyentes. El estado "Normal" no significa únicamente que el `Entry` está habilitado. Significa que el `Entry` está habilitada y no tiene el foco de entrada. 

Si desea que la `Entry` para que tenga un fondo de color verde en el estado de "Enfocado", agregue otro `Setter` a ese estado visual:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

En orden para estos `Setter` objetos que funcione correctamente, un `VisualStateGroup` debe contener `VisualState` objetos para todos los Estados de ese grupo. Si no hay un estado visual que no tenga ningún `Setter` objetos, incluirla como una etiqueta vacía:

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>Marcado de administrador de estado visual en un estilo

A menudo resulta necesaria para compartir el mismo marcado Visual State Manager entre dos o más vistas. En este caso, es conveniente colocar el marcado en una `Style` definición.

Aquí es existente implícito `Style` para el `Entry` elementos en la **VSM en vista** página:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

Agregar `Setter` etiquetas para el `VisualStateManager.VisualStateGroups` propiedad enlazable adjunta:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

La propiedad de contenido para `Setter` es `Value`, por lo que el valor de la `Value` propiedad se puede especificar directamente dentro de estas etiquetas. Que es propiedad de tipo `VisualStateGroupList`:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style> 
```

Dentro de estas etiquetas pueden incluir uno o varios `VisualStateGroup` objetos:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style> 
```

El resto del marcado VSM es el mismo que antes.

Este es el **VSM en estilo** página que muestra el marcado VSM completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">

                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />
        
        <Label Text="Entry with VSM: " />

        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Ahora todos los `Entry` vistas en esta página responden del mismo modo para sus estados visuales. Observe también que el estado de "Enfocado" incluye ahora un segundo `Setter` que proporciona cada `Entry` una CAL en segundo plano también cuando tiene foco de entrada:

[![VSM en estilo](vsm-images/VsmInStyle.png "VSM en estilo")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Definir sus propios estados visuales

Cada clase que deriva de `VisualElement` admite los tres estados comunes "Normal", "Enfocado" y "Deshabilitado". Internamente, la [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) clase detecta cuando se está convirtiendo en habilitado o deshabilitado, o con y sin foco y llama el método estático [ `VisualStateManager.GoToState` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualStateManager.GoToState/p/Xamarin.Forms.VisualElement/System.String/) método:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Éste es el único código Visual State Manager que encontrará en la `VisualElement` clase. Dado que `GoToState` se llama para cada objeto en función de cada clase que deriva de `VisualElement`, puede usar el Administrador de estado Visual con cualquier `VisualElement` objeto para responder a estos cambios.

Curiosamente, el nombre del grupo de estado visual "CommonStates" no está referenciado explícitamente en `VisualElement`. El nombre del grupo no es parte de la API para el Administrador de estado Visual. Dentro de uno de los dos muestras muestra hasta ahora, puede cambiar el nombre del grupo de "CommonStates" para todo lo demás, y el programa seguirá funcionando. El nombre del grupo es simplemente una descripción general de los Estados de ese grupo. Implícitamente se entiende que los estados visuales en todos los grupos son mutuamente excluyentes: un estado y el estado de un único es actual en cualquier momento.

Si va a implementar sus propios estados visuales, debe llamar a `VisualStateManager.GoToState` desde el código. Con mayor frecuencia realizará esta llamada desde el archivo de código subyacente de la clase de página.

El **VSM validación** página en el **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** ejemplo muestra cómo utilizar el Administrador de estado Visual en relación con la validación de entrada. El archivo XAML consta de dos `Label` elementos, un `Entry`, y `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout Padding="10, 10">
        
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />

        <Entry Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />

        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter Property="TextColor" Value="Transparent" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Invalid" />
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Label>

        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid" />

                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter Property="IsEnabled" Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

Marcado VSM está conectado a la segunda `Label` (denominado `helpLabel`) y la `Button` (denominado `submitButton`). Hay dos estados se excluyen mutuamente, denominados "Valid" y "No válido". Tenga en cuenta que cada uno de los dos grupos de "ValidationState" contiene `VisualState` las etiquetas "Valid" y "No válido", aunque uno de ellos está vacía en cada caso. 

Si el `Entry` no contiene un número de teléfono válido, a continuación, el estado actual es "No válido" por lo que el segundo `Label` está visible y el `Button` está deshabilitado:

[![Validación de VSM: Estado válido](vsm-images/VsmValidationInvalid.png "validación VSM - no válido")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Cuando se escribe un número de teléfono válido, a continuación, el estado actual se convierte en "Válido". El segundo `Entry` desaparece y `Button` ahora está habilitado:

[![Validación de VSM: Estado válido](vsm-images/VsmValidationValid.png "validación VSM - válido")](vsm-images/VsmValidationValid-Large.png#lightbox)

El archivo de código subyacente es responsable de control de la `TextChanged` eventos desde el `Entry`. El controlador utiliza una expresión regular para determinar si la cadena de entrada es válida o no. El método en el archivo de código subyacente denominado `GoToState` llama el método estático `VisualStateManager.GoToState` método para ambos `helpLabel` y `submitButton`:

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage ()
    {
        InitializeComponent ();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(helpLabel, visualState);
        VisualStateManager.GoToState(submitButton, visualState);
    }
}
```

Observe también que el `GoToState` método se llama desde el constructor para inicializar el estado. Siempre debe haber un estado actual. Pero en ningún sitio en el código hay una referencia al nombre del grupo de estado visual, aunque se hace referencia en el código XAML como "ValidationStates" para fines de claridad. 

Tenga en cuenta que el archivo de código subyacente debe tener en cuenta todos los objetos en la página que se ven afectada por estos estados visuales y llamar a `VisualStateManager.GoToState` para cada uno de estos objetos. En este ejemplo, es solo dos objetos (el `Label` y `Button`), pero también podría ser varios más.

Tal vez se pregunte: el archivo de código subyacente debe hacer referencia a todos los objetos en la página que se ve afectado por estos estados visuales, ¿por qué no el archivo de código subyacente simplemente tendrán acceso a los objetos directamente? Seguramente, podría. Sin embargo, la ventaja de usar la funcionalidad de VSM es que puede controlar elementos visuales cómo reaccionar frente a un estado diferente completamente en XAML, que conserva todo el diseño de interfaz de usuario en una ubicación. Esto evita la apariencia visual de configuración mediante el acceso a los elementos visuales directamente desde el código subyacente.

Puede resultar tentador considere derivar una clase de `Entry` y es posible definir una propiedad que puede establecer en una función de validación externo. La clase que deriva de `Entry` , a continuación, puede llamar a la `VisualStateManager.GoToState` método. Este esquema funcionaría correctamente, pero solo si el `Entry` eran el único objeto afectado por los distintos estados visuales. En este ejemplo, un `Label` y `Button` se también se ven afectados. No hay ninguna manera de marcado VSM conectado a un `Entry` para controlar otros objetos en la página y no hay manera de marcado VSM agregados a estos otros objetos para hacer referencia a un cambio de estado visual de otro objeto.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Mediante el Administrador de estado Visual de diseño adaptable

Puede cambiar el tamaño de una aplicación que se ejecuta en un teléfono normalmente se puede ver en un retrato o relación de aspecto horizontal y Xamarin.Forms está ejecutando un programa en el escritorio de Xamarin.Forms para que asuma diferentes tamaños y relaciones de aspecto. Una aplicación bien diseñada puede mostrar su contenido diferente para estos diversos factores de forma de ventana o página. 

Esta técnica se conoce a veces como _diseño adaptable_. Dado que diseño adaptable implica únicamente objetos visuales de un programa, es una aplicación ideal de Visual State Manager.

Un ejemplo sencillo es una aplicación que muestra una pequeña colección de botones que afectan al contenido de la aplicación. En modo vertical, podrían mostrarse estos botones en una fila horizontal en la parte superior de la página:

[![VSM diseño adaptable: Vertical](vsm-images/VsmAdaptiveLayoutPortrait.png "diseño adaptable de VSM - vertical")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

En modo horizontal, la matriz de botones puede mover a un lado y muestra en una columna:

[![VSM diseño adaptable: Panorama](vsm-images/VsmAdaptiveLayoutLandscape.png "diseño adaptable de VSM - horizontal")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De arriba a abajo, se ejecuta el programa en la plataforma Universal de Windows, iOS y Android.

El **diseño adaptable VSM** página en el [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/) ejemplo define un grupo denominado "OrientationStates" con dos estados visuales denominados "Vertical" y "Horizontal". (Un enfoque más complejo podría basarse en varios anchos de ventana o página diferentes). 

Marcado VSM se produce en cuatro lugares en el archivo XAML. El `StackLayout` denominado `mainStack` contiene el menú y el contenido, que es un `Image` elemento. Esto `StackLayout` debe tener una orientación vertical en modo vertical y una orientación horizontal en modo horizontal:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">

                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>

                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>
        
        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
            
            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">

                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>

                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">

                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>

                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

Interna `ScrollView` denominado `menuScroll` y `StackLayout` denominado `menuStack` implementar el menú de botones. La orientación de estos diseños es opuesta de `mainStack`. El menú debe ser horizontal en modo vertical y vertical en modo horizontal.

En la cuarta sección del marcado VSM está en un estilo implícito para los botones por sí mismos. Este marcado establece `VerticalOptions`, `HorizontalOptions`, y `Margin` propiedades específicas de las orientaciones portait y horizontal.

Los conjuntos de archivos de código subyacente el `BindingContext` propiedad de `menuStack` para implementar `Button` comandos y también asocia un controlador para el `SizeChanged` eventos de la página:

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

El `SizeChanged` las llamadas del controlador `VisualStateManager.GoToState` para los dos `StackLayout` y `ScrollView` elementos y, a continuación, recorre en bucle los elementos secundarios de `menuStack` para llamar a `VisualStateManager.GoToState` para el `Button` elementos.

Puede parecer como si el archivo de código subyacente puede controlar los cambios de orientación más fácilmente mediante establecer las propiedades de elementos en el archivo XAML, pero el Administrador de estado Visual definitivamente es un enfoque más estructurado. Todos los objetos visuales se mantienen en el archivo XAML, donde se convierten en más fáciles examinar, mantienen y modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Administrador de estado visual con Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**El Administrador de estado Visual Xamarin.Forms 3.0, por [Universidad de Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Vínculos relacionados

- [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)

