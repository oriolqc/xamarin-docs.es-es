---
title: Parte 5. Desde los enlaces de datos para MVVM
description: 'El patrón MVVM exige una separación entre tres capas de software: la interfaz de usuario XAML, llamada a la vista; los datos subyacentes, denominado el modelo; y llama a un intermediario entre la vista y el modelo, el modelo de vista.'
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: e83f8a585c4badc31bffaea53bb2f183e7b11fc9
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268867"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Parte 5. Desde los enlaces de datos para MVVM

_El modelo de arquitectura Model-View-ViewModel (MVVM) se inventaron con XAML en mente. El patrón exige una separación entre tres capas de software: la interfaz de usuario XAML, llamada a la vista; los datos subyacentes, denominado el modelo; y llama a un intermediario entre la vista y el modelo, el modelo de vista. La vista y el modelo de vista a menudo se conectan a través de enlaces de datos definidos en el archivo XAML. BindingContext para la vista normalmente es una instancia del modelo de vista._

## <a name="a-simple-viewmodel"></a>Un modelo de vista Simple

Como una introducción a ViewModels, veamos primero un programa sin uno.
Anteriormente, ha visto cómo definir una nueva declaración de espacio de nombres XML para permitir que un archivo XAML para las clases de referencia de otros ensamblados. Este es un programa que define una declaración de espacio de nombres XML para el `System` espacio de nombres:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Puede usar el programa `x:Static` para obtener la fecha y hora actuales desde el método estático `DateTime.Now` propiedad y establecer que `DateTime` valor para el `BindingContext` en un `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` es una propiedad muy especial: cuando se establece la `BindingContext` en un elemento, es heredada por todos los elementos secundarios de ese elemento. Esto significa que todos los elementos secundarios de la `StackLayout` tienen este mismo `BindingContext`, y pueden contener enlaces simples a las propiedades de ese objeto.

En el **One-Shot DateTime** programa, dos de los nodos secundarios contienen enlaces a las propiedades de ese `DateTime` valor, pero los otros dos secundarios contienen enlaces que parecen que falta una ruta de acceso de enlace. Esto significa que la `DateTime` propio valor se usa para la `StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

Por supuesto, el problema principal es que la fecha y hora son conjunto una vez cuando se crea en primer lugar la página y nunca cambio:

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "Vista que muestra la fecha y hora")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "vista que muestra la fecha y hora")

Un archivo XAML puede mostrar un reloj de que siempre se muestra la hora actual, pero necesita código para ayudar a. Al pensar en MVVM, el modelo y ViewModel son clases que se escriben completamente en código. La vista suele ser un archivo XAML que hace referencia a propiedades definidas en el modelo de vista a través de enlaces de datos.

Un modelo adecuado está escrito en el modelo de vista y un modelo de vista adecuado está escrito en la vista. Sin embargo, muy a menudo un programador adapte los tipos de datos expuestos por el modelo de vista para los tipos de datos asociados con las interfaces de usuario en particular. Por ejemplo, si un modelo tiene acceso a una base de datos que contiene cadenas ASCII de caracteres de 8 bits, el modelo de vista necesitaría convertir entre esas cadenas para cadenas Unicode para alojar el uso exclusivo de Unicode en la interfaz de usuario.

En ejemplos sencillos de MVVM (como los mostrados aquí), a menudo hay ningún modelo en absoluto y el modelo consiste en sólo una vista y ViewModel vinculadas con enlaces de datos.

Este es un modelo de vista para un reloj con una propiedad única denominada `DateTime`, sino que las actualizaciones que `DateTime` propiedad cada segundo:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

ViewModels generalmente implementar la `INotifyPropertyChanged` interfaz, lo que significa que la clase desencadena un `PropertyChanged` evento cada vez que cambia una de sus propiedades. El mecanismo de enlace de datos de Xamarin.Forms asocia un controlador a este `PropertyChanged` por lo que puede recibir una notificación cuando cambia una propiedad de evento y mantener el destino que se actualiza con el nuevo valor.

Un reloj basado en este modelo de vista puede ser tan simple como esta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

Observe cómo la `ClockViewModel` está establecido en el `BindingContext` de la `Label` mediante etiquetas de elemento de propiedad. Como alternativa, puede crear instancias de la `ClockViewModel` en un `Resources` colección y establézcalo en la `BindingContext` a través de un `StaticResource` extensión de marcado. O bien, el archivo de código subyacente puede crear una instancia en el modelo de vista.

El `Binding` extensión de marcado en el `Text` propiedad de la `Label` formatos el `DateTime` propiedad. Esta es la pantalla:

[![](data-bindings-to-mvvm-images/clock.png "Vista que muestra la fecha y hora a través de ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "vista que muestra la fecha y hora a través del modelo de vista")

También es posible tener acceso a las propiedades individuales de la `DateTime` propiedad de ViewModel, separe las propiedades con períodos:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interactivo

MVVM con bastante frecuencia se utiliza con enlaces de datos bidireccional para una vista interactiva basada en un modelo de datos subyacente.

Esta es una clase denominada `HslViewModel` que convierte un `Color` valor en `Hue`, `Saturation`, y `Luminosity` valores y viceversa:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Cambia a la `Hue`, `Saturation`, y `Luminosity` propiedades causa el `Color` propiedad que se va a cambiar y cambia a `Color` hace que las tres propiedades cambiar. Esto puede parecer un bucle infinito, salvo que la clase no invocar el `PropertyChanged` eventos a menos que realmente ha cambiado la propiedad. Esto coloca un extremo para el bucle de comentarios en caso contrario, no controlado.

El archivo XAML siguiente contiene un `BoxView` cuyo `Color` propiedad está enlazada a la `Color` propiedad de ViewModel y tres `Slider` y tres `Label` vistas enlazado a la `Hue`, `Saturation`y `Luminosity` propiedades:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

El enlace en cada `Label` es el valor predeterminado `OneWay`. Solo se necesita para mostrar el valor. Pero el enlace en cada `Slider` es `TwoWay`. Esto permite la `Slider` se inicialicen desde el modelo de vista. Tenga en cuenta que la `Color` propiedad está establecida en `Aqua` cuando se crea una instancia del modelo de vista. Pero un cambio en el `Slider` también es necesario establecer un nuevo valor para la propiedad en el modelo de vista, que, a continuación, calcula un nuevo color.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM mediante enlaces de datos bidireccional")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM mediante enlaces de datos bidireccional")

## <a name="commanding-with-viewmodels"></a>Estableciendo con ViewModels

En muchos casos, el patrón MVVM está restringido a la manipulación de elementos de datos: objetos de interfaz de usuario en la vista de objetos de datos en el modelo de vista en paralelo.

Sin embargo, en ocasiones, la vista debe contener botones que desencadenan las diversas acciones en el modelo de vista. Pero no debe contener el modelo de vista `Clicked` controladores para los botones ya que podría asociar el modelo de vista para un paradigma de interfaz de usuario concreta.

Para permitir que ViewModels ser más independiente de objetos de la interfaz de usuario en particular, pero seguirá permitiendo a los métodos para que se llame en el modelo de vista, un *comando* interfaz existe. Esta interfaz de comandos es compatible con los siguientes elementos de Xamarin.Forms:

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (y, por lo que también `ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

Con la excepción de la `SearchBar` y `ListView` elemento, estos elementos definen dos propiedades:

-  `Command` de tipo  `System.Windows.Input.ICommand`
-  `CommandParameter` de tipo  `Object`

El `SearchBar` define `SearchCommand` y `SearchCommandParameter` propiedades, mientras que la `ListView` define un `RefreshCommand` propiedad de tipo `ICommand`.

El `ICommand` interfaz define dos métodos y un evento:

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

El modelo de vista puede definir propiedades de tipo `ICommand`. A continuación, puede enlazar estas propiedades para el `Command` propiedad de cada `Button` u otro elemento, o quizás una vista personalizada que implementa esta interfaz. Opcionalmente, puede establecer la `CommandParameter` propiedad para identificar individuales `Button` objetos (u otros elementos) que están enlazados a esta propiedad Perspective. Internamente, la `Button` llamadas el `Execute` método cada vez que el usuario puntea el `Button`, pasando a la `Execute` método su `CommandParameter`.

El `CanExecute` método y `CanExecuteChanged` se usan para los casos donde un `Button` tap puede no ser actualmente válido, en cuyo caso el `Button` debe deshabilitar propio. El `Button` llamadas `CanExecute` cuando el `Command` propiedad se establece primero y siempre que el `CanExecuteChanged` desencadena el evento. Si `CanExecute` devuelve `false`, `Button` deshabilita a sí mismo y no genera `Execute` llamadas.

Para obtener ayuda para agregar comandos a su ViewModels, Xamarin.Forms se definen dos clases que implementan `ICommand`: `Command` y `Command<T>` donde `T` es el tipo de los argumentos `Execute` y `CanExecute`. Estas dos clases definen varios constructores más una `ChangeCanExecute` método que el modelo de vista puede llamar para forzar la `Command` objeto que se va a desencadenar la `CanExecuteChanged` eventos.

Este es un modelo de vista para un teclado simple que sirve para escribir números de teléfono. Tenga en cuenta que la `Execute` y `CanExecute` método se definen como derecha de las funciones lambda en el constructor:

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Este modelo de vista se da por supuesto que el `AddCharCommand` propiedad está enlazada a la `Command` propiedad de varios botones (o cualquier otra cosa que tiene una interfaz de comandos), cada uno de los cuales se identifica por el `CommandParameter`. Estos botones agregan caracteres a un `InputString` propiedad, que, a continuación, se da formato como un número de teléfono para el `DisplayText` propiedad.

También hay una segunda propiedad de tipo `ICommand` denominado `DeleteCharCommand`. Esto se enlaza a un botón Atrás espaciado, pero el botón debería deshabilitarse si no hay ningún carácter a eliminar.

El teclado siguiente no es sofisticado como visualmente como es posible. En su lugar, el marcado se ha reducido al mínimo para mostrar más claramente el uso de la interfaz de comandos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

El `Command` propiedad del primer `Button` que aparece en este marcado está enlazado a la `DeleteCharCommand`; el resto se enlazan a la `AddCharCommand` con un `CommandParameter` que es el mismo que el carácter que aparece en el `Button` cara. Aquí es el programa en acción:

[![](data-bindings-to-mvvm-images/keypad.png "Calculadora mediante comandos y MVVM")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "calculadora mediante comandos y MVVM")

### <a name="invoking-asynchronous-methods"></a>Invocar métodos asincrónicos

Comandos también pueden invocar métodos asincrónicos. Esto se consigue mediante la `async` y `await` palabras clave al especificar el `Execute` método:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Esto indica que la `DownloadAsync` método es un `Task` y debe esperar:

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>Implementación de un menú de navegación

El [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/) programa que contiene todo el código fuente de esta serie de artículos utiliza un modelo de vista para su página de inicio. Este modelo de vista es una definición de una clase corta con tres propiedades denominadas `Type`, `Title`, y `Description` que contiene el tipo de cada una de las páginas de ejemplo, un título y una breve descripción. Además, el modelo de vista define una propiedad estática denominada `All` que es una colección de todas las páginas en el programa:

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; }
}
```

El archivo XAML de `MainPage` define un `ListBox` cuyo `ItemsSource` propiedad está establecida en el que `All` propiedad y que contiene un `TextCell` para mostrar la `Title` y `Description` propiedades de cada página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Las páginas se muestran en una lista desplazable:

[![](data-bindings-to-mvvm-images/mainpage.png "Lista desplazable de páginas")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "lista desplazable de páginas")

El controlador en el archivo de código subyacente se desencadena cuando el usuario selecciona un elemento. Los conjuntos de controlador la `SelectedItem` propiedad de la `ListBox` a `null` y, a continuación, crea una instancia de la página seleccionada y navega a ella:

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Xamarin evolucionar 2016: MVVM simplificación con Xamarin.Forms y Prism**

## <a name="summary"></a>Resumen

XAML es una herramienta eficaz para definir las interfaces de usuario en aplicaciones de Xamarin.Forms, especialmente cuando el enlace de datos y se usan MVVM. El resultado es una representación limpia, elegante y potencialmente herramientas de una interfaz de usuario con todo el soporte de segundo plano en el código.


## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introducción a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxis XAML esencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
