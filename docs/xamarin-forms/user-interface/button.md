---
title: Botón de Xamarin.Forms
description: El botón responde a un punteo o clic que dirige una aplicación para llevar a cabo una tarea determinada.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/01/2018
ms.openlocfilehash: d74e0b2aa5be6e8eee2ce5cb54572dd4113d4d7d
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244921"
---
# <a name="xamarinforms-button"></a>Botón de Xamarin.Forms

_El botón responde a un punteo o clic que dirige una aplicación para llevar a cabo una tarea determinada._

El [ `Button` ](xref:Xamarin.Forms.Button) es el control interactivo más fundamental de Xamarin.Forms todos. El `Button` normalmente muestra una cadena de texto corta que indica un comando, pero también puede mostrar una imagen de mapa de bits, o una combinación de texto y una imagen. El usuario presiona el `Button` con un dedo o hace clic en él con el mouse para iniciar ese comando.

La mayoría de los temas se describe a continuación corresponden a las páginas de la [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) ejemplo.

## <a name="handling-button-clicks"></a>Control de botón hace clic en

`Button` define un [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento que se desencadena cuando el usuario puntea el `Button` con un puntero dedo o un mouse (ratón). El evento se desencadena cuando se suelta el botón dedo o un mouse (ratón) en la superficie de la `Button`. El `Button` debe tener su [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad establecida en `true` para que responda a derivaciones.

El **haga clic en botón básica** página en el [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) ejemplo muestra cómo crear una instancia de un `Button` en XAML y el identificador de su `Clicked` eventos. El **BasicButtonClickPage.xaml** archivo contiene un `StackLayout` con ambos un `Label` y `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>

        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

El `Button` tiende a ocupar todo el espacio que se permite para él. Por ejemplo, si no establece la `HorizontalOptions` propiedad de `Button` a algo distinto de `Fill`, el `Button` ocupará todo el ancho de su elemento primario.

De forma predeterminada, el `Button` es rectangular, pero puede dar TI redondeada esquinas mediante la [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) propiedad, tal y como se describe a continuación, en la sección [ **botón apariencia** ](#button-appearance).

El [ `Text` ](xref:Xamarin.Forms.Button.Text) propiedad especifica el texto que aparece en el `Button`. El [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento está establecido en un controlador de eventos denominado `OnButtonClicked`. Este controlador se encuentra en el archivo de código subyacente, **BasicButtonClickPage.xaml.cs**:

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

Cuando el `Button` que se derivan, la `OnButtonClicked` el método se ejecuta. El `sender` argumento es el `Button` objeto responsable de este evento. Ya puede utilizarla para tener acceso a la `Button` objeto, o para distinguir entre varios `Button` los objetos que comparten el mismo `Clicked` eventos.

Esta `Clicked` controlador llama a una función de animación que gira la `Label` 360 grados en 1000 milisegundos. Este es el programa que se ejecuta en dispositivos iOS y Android y como una aplicación de plataforma Universal de Windows (UWP) en el escritorio de Windows 10:

[![Haga clic de botón básica](button-images/BasicButtonClick.png "clic de botón básica")](button-images/BasicButtonClick-Large.png#lightbox "clic de botón básica")

Tenga en cuenta que la `OnButtonClicked` método incluye la `async` modificador porque `await` se utiliza en el controlador de eventos. A `Clicked` controlador de eventos requiere la `async` modificador solo si usa el cuerpo del controlador de `await`.

Cada plataforma representa el `Button` de su propia forma específica. En el [ **botón apariencia** ](#button-appearance) sección, verá cómo establecer colores y realizar el `Button` borde visible para los aspectos más personalizadas. `Button` implementa el [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) interfaz, de modo que incluya [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), y [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)propiedades.

## <a name="creating-a-button-in-code"></a>Crear un botón en el código

Es habitual para crear instancias de un `Button` en XAML, pero también puede crear un `Button` en el código. Esto podría ser conveniente si la aplicación necesita crear varios botones basados en datos que son enumerables con un `foreach` bucle.

El **clic del botón código** página muestra cómo crear una página que es funcionalmente equivalente a la **haga clic en botón básica** página pero completamente en C#:

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

Todo lo que se realiza en el constructor de clase. Dado que el `Clicked` controlador es solo una instrucción larga, se puede adjuntar al evento modo muy sencillo:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Por supuesto, también puede definir el controlador de eventos como un método independiente (al igual que el `OnButtonClick` método **haga clic en botón básica**) y asociar ese método para el evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Deshabilitar el botón

A veces, una aplicación está en un estado determinado que un determinado `Button` haga clic en no es una operación válida. En esos casos, el `Button` debe deshabilitarse estableciendo su `IsEnabled` propiedad `false`. El ejemplo clásico es un `Entry` control para un nombre de archivo acompañado de un archivo / abrir `Button`: el `Button` debe habilitarse solo si se ha escrito algún texto en el `Entry`.
Puede usar un `DataTrigger` para esta tarea, tal y como se muestra en el [ **datos desencadenadores** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) artículo.

## <a name="using-the-command-interface"></a>Mediante la interfaz de comandos

Es posible que una aplicación responder a `Button` derivaciones sin control de la `Clicked` eventos. El `Button` implementa un mecanismo de notificación alternativo denominado el _comando_ o _estableciendo_ interfaz. Esto consta de dos propiedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) de tipo [ `ICommand` ](xref:System.Windows.Input.ICommand), una interfaz definida en el [ `System.Windows.Input` ](xref:System.Windows.Input) espacio de nombres.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propiedad de tipo [ `Object` ](xref:System.Object).

Este enfoque es especialmente adecuado en relación con el enlace de datos y especialmente al implementar la arquitectura Model-View-ViewModel (MVVM). Estos temas se tratan en los artículos [enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md), [de enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), y [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

En una aplicación MVVM, el modelo de vista define las propiedades de tipo `ICommand` , a continuación, que están conectados al XAML `Button` elementos con enlaces de datos. También define Xamarin.Forms [ `Command` ]((xref:Xamarin.Forms.Command`1)) y [ `Command<T>` ](xref:Xamarin.Forms.Command`1) las clases que implementan la `ICommand` de interfaz y ayudar a definir las propiedades de tipo ViewModel`ICommand`.

Los comandos se describen con más detalle en el artículo [ **la interfaz de comandos** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) pero la **básica de botón de comando** página en el [  **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) muestra el enfoque básico.

El `CommandDemoViewModel` clase es un ViewModel muy simple que define una propiedad de tipo `double` denominado `Number`y dos propiedades de tipo `ICommand` denominado `MultiplyBy2Command` y `DivideBy2Command`:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

Los dos `ICommand` propiedades se inicializan en el constructor de clase con dos objetos de tipo `Command`. El `Command` constructores incluyen una pequeña función (denominado el `execute` argumento del constructor) que duplica o mitades el `Number` propiedad.

El **BasicButtonCommand.xaml** archivo establece su `BindingContext` a una instancia de `CommandDemoViewModel`. El `Label` elemento y dos `Button` elementos contengan enlaces a las tres propiedades en `CommandDemoViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">

    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>

    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

Como los dos `Button` se puntea elementos, los comandos se ejecutan, y el número de cambios de valor:

[![Comando de botón básica](button-images/BasicButtonCommand.png "comando de botón básica")](button-images/BasicButtonCommand-Large.png#lightbox)

La ventaja de este enfoque sobre `Clicked` controladores es que toda la lógica que implican la funcionalidad de esta página se encuentra en el modelo de vista en lugar de en el archivo de código subyacente, para lograr una mejor separación de la interfaz de usuario de la lógica de negocios.

También es posible que el `Command` objetos que se va a controlar la habilitación y deshabilitación de la `Button` elementos. Por ejemplo, imagine que desea limitar el intervalo de valores numéricos entre 2<sup>10</sup> y 2<sup>&ndash;10</sup>. Puede agregar otra función al constructor (denominado el `canExecute` argumento) que devuelve `true` si el `Button` debe estar habilitada. Esta es la modificación de la `CommandDemoViewModel` constructor:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

Las llamadas a la `ChangeCanExecute` método de `Command` son necesarios para que la `Command` puede llamar al método el `canExecute` método y determine si el `Button` debe deshabilitarse o no. Con este cambio en el código, como el número alcanza el límite, el `Button` está deshabilitado:

[![Comando de botón básica - modificar](button-images/BasicButtonCommandModified.png "comando de botón básica: modificar")](button-images/BasicButtonCommandModified-Large.png#lightbox)

Es posible que dos o más `Button` elementos que se va a enlazar al mismo `ICommand` propiedad. El `Button` elementos se pueden distinguir utilizando la [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) propiedad de `Button`. En este caso, deseará usar la interfaz genérica [ `Command<T>` ](xref:Xamarin.Forms.Command`1) clase. El `CommandParameter` objeto, a continuación, se pasa como argumento a la `execute` y `canExecute` métodos. Esta técnica se muestra en detalle en la [ **estableciendo básica** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) sección de la [ **interfaz comandos** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) artículo.

El [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) ejemplo también utiliza esta técnica en su `MainPage` clase. El **MainPage.xaml** archivo contiene un `Button` para cada página del ejemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Cada `Button` tiene su `Command` propiedad enlazada a una propiedad denominada `NavigateCommand`y el `CommandParameter` está establecido en un [ `Type` ](xref:System.Type) objeto corresponde a una de las clases de página en el proyecto.

Que `NavigateCommand` propiedad es de tipo `ICommand` y se define en el archivo de código subyacente:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

El constructor inicializa la `NavigateCommand` propiedad a una `Command<Type>` objeto porque `Type` es el tipo de la `CommandParameter` objeto establecido en el archivo XAML. Esto significa que la `execute` método tiene un argumento de tipo `Type` que corresponde a este `CommandParameter` objeto. La función crea una instancia de la página y, a continuación, navega a ella.

Tenga en cuenta que el constructor concluye estableciendo su `BindingContext` a sí mismo. Esto es necesario para las propiedades en el archivo XAML para enlazar con el `NavigateCommand` propiedad.

## <a name="pressing-and-releasing-the-button"></a>Presione y suelte el botón

Además el `Clicked` eventos, `Button` también define [ `Pressed` ](xref:Xamarin.Forms.Button.Pressed) y [ `Released` ](xref:Xamarin.Forms.Button.Released) eventos. El `Pressed` evento tiene lugar cuando se presiona un dedo una `Button`, o se presiona un botón del mouse con el puntero colocado sobre el `Button`. El `Released` evento tiene lugar cuando se suelta el botón dedo o un mouse (ratón). Por lo general, un `Clicked` también se desencadena el evento al mismo tiempo que la `Released` evento, pero si el puntero dedo o un mouse (ratón) se desliza fuera de la superficie de la `Button` antes de que se libera, el `Clicked` no puede producir el evento.

El `Pressed` y `Released` eventos no se usan con frecuencia, pero puede usarse para fines especiales, como se muestra en el **presione y versión botón** página. El archivo XAML contiene una `Label` y un `Button` con controladores asociados para la `Pressed` y `Released` eventos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

El archivo de código subyacente se anima la `Label` cuando un `Pressed` evento se produce, pero se suspende la rotación cuando un `Released` evento tiene lugar:

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

El resultado es que la `Label` sólo se gira mientras un dedo está en contacto con el `Button`y se detiene cuando se libera el dedo:

[![Presione y suelte el botón](button-images/PressAndReleaseButton.png "presione y suelte el botón")](button-images/PressAndReleaseButton-Large.png)

Este tipo de comportamiento tiene aplicaciones para juegos: un dedo mantenido en una `Button` podría provocar que un objeto de la pantalla de mover en una dirección determinada.

<a name="button-appearance" />

## <a name="button-appearance"></a>Apariencia del botón

El `Button` hereda o define varias propiedades que afectan a su apariencia:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) es el color de la `Button` texto
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) es el color de fondo para que el texto
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) es el color de un área que rodea el `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) se usa la familia de fuentes para el texto
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) es el tamaño del texto
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) indica si el texto es negrita o cursiva
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) es el ancho del borde
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) redondea los vértices

Los efectos de seis de estas propiedades (excepto `FontFamily` y `FontAttributes`) se muestran en la **apariencia del botón** página. Otra propiedad, [ `Image` ](xref:Xamarin.Forms.Button.Image), se describe en la sección [ **con mapas de bits de botón**](#image-button).

Todos los enlaces de datos y vistas en el **apariencia del botón** página se definen en el archivo XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">

            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />

            <Label Text="{Binding Source={x:Reference borderWidthSlider},
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider},
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

El `Button` en la parte superior de la página tiene sus tres `Color` propiedades se enlazan a `Picker` elementos en la parte inferior de la página. Los elementos de la `Picker` elementos son los colores de la `NamedColor` clase incluida en el proyecto. Tres `Slider` elementos contienen enlaces bidireccionales para la `FontSize`, `BorderWidth`, y `CornerRadius` propiedades de la `Button`.

Este programa le permite experimentar con combinaciones de todas estas propiedades:

[![Botón apariencia](button-images/ButtonAppearance.png "botón apariencia")](button-images/ButtonAppearance-Large.png)

Para ver el `Button` borde, deberá establecer un `BorderColor` a algo distinto de `Default`y `BorderWidth` en un valor positivo.

En iOS, observará que los anchos de borde grandes interfieren en el interior de la `Button` e interferir con la presentación del texto. Si decide usar un borde con un iOS `Button`, probablemente deseará comienza y termina el `Text` propiedad con espacios para conservar su visibilidad.

En UWP, al seleccionar un `CornerRadius` que supera la mitad del alto de la `Button` produce una excepción.

## <a name="creating-a-toggle-button"></a>Crear un botón de alternancia

Es posible subclase `Button` para que funciona como un conmutador de encendido y apagado: pulse el botón una vez para el botón de alternancia en y pulse de nuevo para desactivarlo.

El siguiente `ToggleButton` clase se deriva de `Button` y define un nuevo evento denominado `Toggled` y una propiedad booleana denominada `IsToggled`. Estas propiedades son las mismas dos definidas por el Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

El `ToggleButton` constructor asocia un controlador para el `Clicked` eventos por lo que TI puede cambiar el valor de la `IsToggled` propiedad. El `OnIsToggledChanged` método activa el `Toggled` eventos.

La última línea de la `OnIsToggledChanged` llamadas de método estático `VisualStateManager.GoToState` método con el texto de dos cadenas "ToggledOn" y "ToggledOff". Puede leer acerca de este método y cómo la aplicación puede responder a estados visuales en el artículo [ **Xamarin.Forms Visual State Manager**](~/xamarin-forms/user-interface/visual-state-manager.md).

Dado que `ToggleButton` realiza la llamada a `VisualStateManager.GoToState`, la propia clase no tiene que incluir las funciones adicionales para cambiar la apariencia del botón en función de su `IsToggled` estado. Es decir, la responsabilidad del código XAML que hospeda el `ToggleButton`.

El **demostración de botón de alternancia** página contiene dos instancias de `ToggleButton`, incluido el marcado Visual State Manager que establece el `Text`, `BackgroundColor`, y `TextColor` del botón en función del estado visual:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">

    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

El `Toggled` controladores de eventos están en el archivo de código subyacente. Son responsables de la configuración de la `FontAttributes` propiedad de la `Label` en función del estado de los botones:

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

Este es el programa que se ejecuta en iOS, Android y UWP:

[![Demostración de botón de alternar](button-images/ToggleButtonDemo.png "demostración de botón de alternar")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>Usar mapas de bits con botones

El `Button` clase define un [ `Image` ](xref:Xamarin.Forms.Button.Image) propiedad que permite mostrar una imagen de mapa de bits en el `Button`, por sí solas o en combinación con texto. También puede especificar el modo en que se organizan el texto e imagen.

El `Image` propiedad es de tipo [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), lo que significa que los mapas de bits deben almacenarse como recursos en los proyectos de plataforma individuales y no en el proyecto de biblioteca de .NET estándar.

Cada plataforma compatible con Xamarin.Forms permite que las imágenes que se almacenará en varios tamaños para las resoluciones de píxeles diferente de los diversos dispositivos que puede ejecutar la aplicación. Estos se denomina varios mapas de bits o se almacenan de tal manera que el sistema operativo puede elegir a la mejor coincidencia para el dispositivo de vídeo resolución de pantalla.

Para un mapa de bits en un `Button`, el tamaño recomendado es normalmente entre 32 y 64 unidades independientes del dispositivo, según el tamaño que desee que sea. Las imágenes utilizadas en este ejemplo se basan en un tamaño de 48 unidades independientes del dispositivo.

En el proyecto de iOS, el **recursos** carpeta contiene tres tamaños de esta imagen:

- Un mapa de bits 48 píxeles cuadrado almacenado como **/Resources/MonkeyFace.png**
- Almacenadas como un mapa de bits cuadrado 96 píxeles **/Resource/MonkeyFace@2x.png**
- Almacenadas como un mapa de bits cuadrado 144 píxeles **/Resource/MonkeyFace@3x.png**

Todos los tres mapas de bits se han proporcionado una **acción de compilación** de **BundleResource**.

Para el proyecto Android, los mapas de bits todos tienen el mismo nombre, pero se almacenan en distintas subcarpetas de la **recursos** carpeta:

- Un mapa de bits de cuadrado 72 píxeles almacenado como **/Resources/drawable-hdpi/MonkeyFace.png**
- Un mapa de bits de cuadrado 96 píxeles almacenado como **/Resources/drawable-xhdpi/MonkeyFace.png**
- Un mapa de bits de cuadrado 144 píxeles almacenado como **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Un mapa de bits de cuadrado 192 píxeles almacenado como **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Estos se han proporcionado una **acción de compilación** de **AndroidResource**.

En el proyecto de UWP, mapas de bits se pueden almacenar en cualquier lugar en el proyecto, pero generalmente se almacenan en una carpeta personalizada o **activos** carpeta existente. El proyecto de UWP contiene estos mapas de bits:

- Un mapa de bits 48 píxeles cuadrado almacenado como **/Assets/MonkeyFace.scale-100.png**
- Un mapa de bits de cuadrado 96 píxeles almacenado como **/Assets/MonkeyFace.scale-200.png**
- Un mapa de bits de cuadrado 192 píxeles almacenado como **/Assets/MonkeyFace.scale-400.png**

Todos se asignó un **acción de compilación** de **contenido**.

Puede especificar cómo la `Text` y `Image` propiedades están organizadas en el `Button` mediante la [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) propiedad de `Button`. Esta propiedad es de tipo [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), que es una clase incrustada en `Button`. El [constructor](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) tiene dos argumentos:

- Un miembro de la [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) enumeración: `Left`, `Top`, `Right`, o `Bottom` que indica cómo aparece el mapa de bits en relación con el texto.
- Un `double` valor para el espaciado entre el mapa de bits y el texto.

Los valores predeterminados son `Left` y 10 unidades. Dos propiedades de solo lectura de `ButtonContentLayout` denominado [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) y [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) proporcionar los valores de esas propiedades.

En el código, puede crear un `Button` y establezca el `ContentLayout` propiedad similar al siguiente:

```csharp
Button button = new Button
{
    Text = "button text",
    Image = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

En XAML, es necesario especificar el miembro de la enumeración o el espaciado o en cualquier orden separan por comas:

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

El **demostración de botón de imagen** página utiliza `OnPlatform` para especificar nombres de archivo distintos para iOS, Android y UWP en archivos de mapa de bits. Si desea utilizar el mismo nombre de archivo para las tres plataformas y evitar el uso de `OnPlatform`, debe almacenar los mapas de bits UWP en el directorio raíz del proyecto.

La primera `Button` en el **demostración de botón de imagen** página conjuntos el `Image` propiedad pero no la `Text` propiedad:

```xaml
<Button>
    <Button.Image>
        <OnPlatform x:TypeArguments="FileImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.Image>
</Button>
```

Si los mapas de bits UWP se almacenan en el directorio raíz del proyecto, se puede simplificar considerablemente este marcado:

```xaml
<Button Image="MonkeyFace.png" />
```

Para evitar muchas marcado redundante en la **ImageButtonDemo.xaml** archivo implícita `Style` también se define para establecer el `Image` propiedad. Esto `Style` se aplica automáticamente a otros cinco `Button` elementos. Este es el archivo XAML completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="Image">
                    <OnPlatform x:TypeArguments="FileImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.Image>
                <OnPlatform x:TypeArguments="FileImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.Image>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20"
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

Los cuatro finales `Button` elementos hacen uso de la `ContentLayout` propiedad para especificar una posición y espaciado del texto y mapa de bits:

[![Demostración de botón de imagen](button-images/ImageButtonDemo.png "demostración de botón de imagen")](button-images/ImageButtonDemo-Large.png#lightbox)

Ahora que ha visto las distintas formas en que puede controlar `Button` eventos y cambiar la `Button` apariencia.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ButtonDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [Botón API](xref:Xamarin.Forms.Button)
