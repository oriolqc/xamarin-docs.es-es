---
title: La interfaz de comandos de Xamarin.Forms
description: En este artículo se explica cómo implementar la propiedad Command con enlaces de datos de Xamarin.Forms. La interfaz de comandos proporciona un enfoque alternativo a la implementación de comandos que se adapta mucho mejor a la arquitectura MVVM.
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: b18d042e34146a72b488da9017648a430c9cd353
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996378"
---
# <a name="the-xamarinforms-command-interface"></a>La interfaz de comandos de Xamarin.Forms

En la arquitectura Model-View-ViewModel (MVVM), se definen los enlaces de datos entre las propiedades en ViewModel, que suele ser una clase que derive de `INotifyPropertyChanged`y las propiedades en la vista, que suele ser el archivo XAML. A veces, una aplicación tiene necesidades que van más allá de estos enlaces de propiedad al solicitar al usuario iniciar los comandos que influyen en el modelo de vista. Estos comandos generalmente se señalizan mediante clics de botón o el dedo derivaciones y tradicionalmente se procesan en el archivo de código subyacente en un controlador para el `Clicked` eventos de la `Button` o `Tapped` eventos de un `TapGestureRecognizer`.

La interfaz de comandos proporciona un enfoque alternativo a la implementación de comandos que se adapta mucho mejor a la arquitectura MVVM. El modelo de vista puede contener comandos, que son métodos que se ejecutan en respuesta a una actividad específica en la vista como una `Button` haga clic en. Enlaces de datos se definen entre estos comandos y el `Button`.

Para permitir que un enlace de datos entre un `Button` y un ViewModel, el `Button` define dos propiedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) de tipo <xref:System.Windows.Input.ICommand>
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) de tipo `Object`

Para usar la interfaz de comandos, define un enlace de datos que tenga como destino el `Command` propiedad de la `Button` donde el origen es una propiedad en el modelo de vista de tipo `ICommand`. El ViewModel contiene código asociado que `ICommand` propiedad que se ejecuta cuando se hace clic en el botón. Puede establecer `CommandParameter` para datos arbitrarios que se va a distinguir entre varios botones si todo se enlaza a la misma `ICommand` propiedad en el modelo de vista.

El `Command` y `CommandParameter` propiedades también se definen mediante las clases siguientes:

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) y por lo tanto, [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem), que se deriva de `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) y por lo tanto, [ `ImageCell` ](xref:Xamarin.Forms.ImageCell), que se deriva de `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) define un [ `SearchCommand` ](xref:Xamarin.Forms.SearchBar.SearchCommand) propiedad de tipo `ICommand` y un [ `SearchCommandParameter` ](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) propiedad. El [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand) propiedad de [ `ListView` ](xref:Xamarin.Forms.ListView) también es de tipo `ICommand`.

Todos estos comandos pueden administrarse dentro de una clase ViewModel de manera que no depende del objeto de interfaz de usuario concreta en la vista.

## <a name="the-icommand-interface"></a>La interfaz ICommand

El <xref:System.Windows.Input.ICommand> interfaz no es parte de Xamarin.Forms. Se define en su lugar en el [System.Windows.Input](xref:System.Windows.Input) espacio de nombres y consta de dos métodos y un evento:

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Para usar la interfaz de comandos, el ViewModel contiene las propiedades de tipo `ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

El modelo de vista debe hacer referencia a una clase que implementa el `ICommand` interfaz. Esta clase se describirá en breve. En la vista, el `Command` propiedad de un `Button` está enlazado a esa propiedad:

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Cuando el usuario presiona el `Button`, el `Button` llamadas la `Execute` método en el `ICommand` objeto enlazado a su `Command` propiedad. Esa es la parte más sencilla de la interfaz de comandos.

El `CanExecute` método es más complejo. Cuando el enlace se define en primer lugar en el `Command` propiedad de la `Button`, y cuando el enlace de datos cambia de alguna manera, el `Button` llamadas la `CanExecute` método en el `ICommand` objeto. Si `CanExecute` devuelve `false`, la `Button` deshabilita a sí mismo. Esto indica que el comando concreto está actualmente disponible o no es válido.

El `Button` también adjunta un controlador en el `CanExecuteChanged` událostí `ICommand`. El evento se desencadena desde dentro de ViewModel. Cuando se desencadena ese evento, el `Button` llamadas `CanExecute` nuevo. El `Button` permite a sí mismo si `CanExecute` devuelve `true` y deshabilita a sí mismo si `CanExecute` devuelve `false`.

> [!IMPORTANT]
> No utilice el `IsEnabled` propiedad de `Button` si usa la interfaz de comandos.  

## <a name="the-command-class"></a>La clase de comando

Cuando el modelo de vista define una propiedad de tipo `ICommand`, ViewModel también debe contener o hacer referencia a una clase que implementa el `ICommand` interfaz. Esta clase debe contener o hacer referencia a la `Execute` y `CanExecute` métodos y fire el `CanExecuteChanged` evento cada vez que el `CanExecute` método podría devolver un valor diferente.

Puede escribir este tipo de clase, o puede usar una clase que otra persona ha escrito. Dado que `ICommand` forma parte de Microsoft Windows, se ha usado durante años, con las aplicaciones MVVM de Windows. Uso de una clase de Windows que implementa `ICommand` le permite compartir los ViewModel entre las aplicaciones de Windows y las aplicaciones de Xamarin.Forms.

Si uso compartido de ViewModels entre Windows y Xamarin.Forms no constituye un problema, puede usar el [ `Command` ](xref:Xamarin.Forms.Command) o [ `Command<T>` ](xref:Xamarin.Forms.Command`1) clase incluida en Xamarin.Forms para implementar el `ICommand`interfaz. Estas clases le permiten especificar los cuerpos de los `Execute` y `CanExecute` métodos en los constructores de clase. Usar `Command<T>` cuando se usa el `CommandParameter` propiedad para distinguir entre varias vistas que se enlaza a la misma `ICommand` propiedad y el más sencillo `Command` clase al que no es un requisito.

## <a name="basic-commanding"></a>Comandos básicos

El **entrada persona** página en el [ **demostraciones de enlace de datos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) programa muestra algunos comandos sencillos que se implementa en una clase ViewModel.

El `PersonViewModel` define tres propiedades denominadas `Name`, `Age`, y `Skills` que definen una persona. Esta clase hace *no* contener ninguno `ICommand` propiedades:

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    string name;
    double age;
    string skills;

    public event PropertyChangedEventHandler PropertyChanged;

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public double Age
    {
        set { SetProperty(ref age, value); }
        get { return age; }
    }

    public string Skills
    {
        set { SetProperty(ref skills, value); }
        get { return skills; }
    }

    public override string ToString()
    {
        return Name + ", age " + Age;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

El `PersonCollectionViewModel` se muestra a continuación crea nuevos objetos de tipo `PersonViewModel` y permite al usuario rellenar los datos. Para ello, la clase define propiedades `IsEditing` typu `bool` y `PersonEdit` de tipo `PersonViewModel`. Además, la clase define tres propiedades de tipo `ICommand` y una propiedad denominada `Persons` de tipo `IList<PersonViewModel>`:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    PersonViewModel personEdit;
    bool isEditing;

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public bool IsEditing
    {
        private set { SetProperty(ref isEditing, value); }
        get { return isEditing; }
    }

    public PersonViewModel PersonEdit
    {
        set { SetProperty(ref personEdit, value); }
        get { return personEdit; }
    }

    public ICommand NewCommand { private set; get; }

    public ICommand SubmitCommand { private set; get; }

    public ICommand CancelCommand { private set; get; }

    public IList<PersonViewModel> Persons { get; } = new ObservableCollection<PersonViewModel>();

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

Esta lista abreviada no incluye el constructor de la clase, que es donde las tres propiedades de tipo `ICommand` se definen, que se mostrará en breve. Tenga en cuenta que los cambios en las tres propiedades de tipo `ICommand` y `Persons` propiedad no dan lugar a `PropertyChanged` eventos que se activen. Estas propiedades están establecidos cuando se crea la clase y no cambian a partir de entonces.

Antes de examinar el constructor de la `PersonCollectionViewModel` class, echemos un vistazo a los archivos XAML para el **entrada persona** programa. Este archivo contiene un `Grid` con su `BindingContext` propiedad establecida en el `PersonCollectionViewModel`. El `Grid` contiene un `Button` con el texto **New** con su `Command` propiedad enlazada a la `NewCommand` propiedad ViewModel, dependiente de un formulario de entrada con las propiedades de la `IsEditing` propiedad, como así como las propiedades de `PersonViewModel`, y dos botones más enlazado a la `SubmitCommand` y `CancelCommand` propiedades de la clase ViewModel. El último `ListView` muestra la colección de las personas que ya escrito:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.PersonEntryPage"
             Title="Person Entry">
    <Grid Margin="10">
        <Grid.BindingContext>
            <local:PersonCollectionViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <!-- New Button -->
        <Button Text="New"
                Grid.Row="0"
                Command="{Binding NewCommand}"
                HorizontalOptions="Start" />

        <!-- Entry Form -->
        <Grid Grid.Row="1"
              IsEnabled="{Binding IsEditing}">

            <Grid BindingContext="{Binding PersonEdit}">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Label Text="Name: " Grid.Row="0" Grid.Column="0" />
                <Entry Text="{Binding Name}"
                       Grid.Row="0" Grid.Column="1" />

                <Label Text="Age: " Grid.Row="1" Grid.Column="0" />
                <StackLayout Orientation="Horizontal"
                             Grid.Row="1" Grid.Column="1">
                    <Stepper Value="{Binding Age}"
                             Maximum="100" />
                    <Label Text="{Binding Age, StringFormat='{0} years old'}"
                           VerticalOptions="Center" />
                </StackLayout>

                <Label Text="Skills: " Grid.Row="2" Grid.Column="0" />
                <Entry Text="{Binding Skills}"
                       Grid.Row="2" Grid.Column="1" />

            </Grid>
        </Grid>

        <!-- Submit and Cancel Buttons -->
        <Grid Grid.Row="2">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <Button Text="Submit"
                    Grid.Column="0"
                    Command="{Binding SubmitCommand}"
                    VerticalOptions="CenterAndExpand" />

            <Button Text="Cancel"
                    Grid.Column="1"
                    Command="{Binding CancelCommand}"
                    VerticalOptions="CenterAndExpand" />
        </Grid>

        <!-- List of Persons -->
        <ListView Grid.Row="3"
                  ItemsSource="{Binding Persons}" />
    </Grid>
</ContentPage>
```

Aquí mostramos cómo funciona: el usuario presiona primero la **New** botón. Esto permite que el formulario de entrada, pero se deshabilita la **New** botón. El usuario, a continuación, escribe un nombre, edad y habilidades. En cualquier momento durante la edición, el usuario puede presionar el **cancelar** botón para volver a empezar. Solo cuando se ha escrito un nombre y una vigencia válida es el **enviar** botón habilitado. Presione esto **enviar** botón transfiere la persona a la colección mostrada por el `ListView`. Después el **cancelar** o **enviar** se presiona el botón, se borra el formulario de entrada y la **New** botón se vuelva a habilitar.

La pantalla de iOS a la izquierda muestra el diseño antes de que se especifica una vigencia válida. El Android y UWP pantallas de presentación del **enviar** botón se habilita después de haber establecido una edad:

[![Entrada de persona](commanding-images/personentry-small.png "entrada persona")](commanding-images/personentry-large.png#lightbox "entrada persona")

El programa no tiene ninguna característica para editar entradas existentes y no guarda las entradas al salir de la página.

Toda la lógica para la **New**, **enviar**, y **cancelar** botones se controlan en `PersonCollectionViewModel` a través de definiciones de la `NewCommand`, `SubmitCommand`, y `CancelCommand` propiedades. El constructor de la `PersonCollectionViewModel` establece estas tres propiedades en objetos de tipo `Command`.  

Un [constructor](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de la `Command` clase le permite pasar argumentos de tipo `Action` y `Func<bool>` correspondiente a la `Execute` y `CanExecute` métodos. Es más fácil definir estas acciones y funciones como funciones de lambda directamente en el `Command` constructor. Esta es la definición de la `Command` de objeto para el `NewCommand` propiedad:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {
        NewCommand = new Command(
            execute: () =>
            {
                PersonEdit = new PersonViewModel();
                PersonEdit.PropertyChanged += OnPersonEditPropertyChanged;
                IsEditing = true;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return !IsEditing;
            });

        ···

    }

    void OnPersonEditPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        (SubmitCommand as Command).ChangeCanExecute();
    }

    void RefreshCanExecutes()
    {
        (NewCommand as Command).ChangeCanExecute();
        (SubmitCommand as Command).ChangeCanExecute();
        (CancelCommand as Command).ChangeCanExecute();
    }

    ···

}
```

Cuando el usuario hace clic en el **nuevo** botón, el `execute` función pasa el `Command` se ejecuta el constructor. Esto crea un nuevo `PersonViewModel` de objeto, se establece un controlador de ese objeto `PropertyChanged` establece el evento, `IsEditing` a `true`y llama a la `RefreshCanExecutes` método definido después del constructor.

Además de implementar el `ICommand` interfaz, el `Command` clase también define un método denominado `ChangeCanExecute`. Debe llamar ViewModel `ChangeCanExecute` para un `ICommand` propiedad cada vez que tiene algún problema que podría cambiar el valor devuelto de la `CanExecute` método. Una llamada a `ChangeCanExecute` hace que el `Command` clase para desencadenar la `CanExecuteChanged` método. El `Button` ha adjuntado un controlador para ese evento y responde mediante una llamada a `CanExecute` nuevo y, a continuación, habilitar basándose en el valor devuelto de ese método.

Cuando el `execute` método `NewCommand` llamadas `RefreshCanExecutes`, el `NewCommand` propiedad recibe una llamada a `ChangeCanExecute`y el `Button` llamadas la `canExecute` método, que ahora devuelve `false` porque el `IsEditing`propiedad ahora está `true`.

El `PropertyChanged` controlador para el nuevo `PersonViewModel` de objeto llama el `ChangeCanExecute` método `SubmitCommand`. Le mostramos cómo se implementa esa propiedad de comando:


```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        SubmitCommand = new Command(
            execute: () =>
            {
                Persons.Add(PersonEdit);
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return PersonEdit != null &&
                       PersonEdit.Name != null &&
                       PersonEdit.Name.Length > 1 &&
                       PersonEdit.Age > 0;
            });

        ···
    }

    ···

}
```

El `canExecute` funcionar para `SubmitCommand` se llama cada vez que hay una propiedad que cambió en el `PersonViewModel` de objeto que se está editando. Devuelve `true` solo cuando el `Name` propiedad es al menos un carácter de longitud, y `Age` es mayor que 0. En ese momento, el **enviar** se habilita el botón.

El `execute` funcionar para **enviar** quita el controlador de cambio de propiedad de la `PersonViewModel`, agrega el objeto a la `Persons` colección y todo lo devuelve a condiciones iniciales.

El `execute` funcionen para el **cancelar** botón hace todo lo que el **enviar** botón hace execept agregar el objeto a la colección:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        CancelCommand = new Command(
            execute: () =>
            {
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return IsEditing;
            });
    }

    ···

}
```

El `canExecute` devuelve del método `true` en cualquier momento una `PersonViewModel` se está editando.

Estas técnicas puede adaptarse para escenarios más complejos: una propiedad en `PersonCollectionViewModel` podría enlazarse a la `SelectedItem` propiedad de la `ListView` para editar los elementos existentes y un **eliminar** botón podría agregarse al eliminar esos elementos.

No es necesario definir la `execute` y `canExecute` métodos como las funciones lambda. Puede escribir métodos privados regulares como en el ViewModel y hacer referencia a ellos en el `Command` constructores. Sin embargo, este enfoque tienden a producir una gran cantidad de métodos que se hace referencia a una sola vez en el modelo de vista.

## <a name="using-command-parameters"></a>Uso de parámetros de comando

A veces es conveniente para uno o varios botones (u otros objetos de interfaz de usuario) compartir el mismo `ICommand` propiedad en el modelo de vista. En este caso, usa el `CommandParameter` propiedad para distinguir entre los botones.

Aún puede usar el `Command` clase estos compartido `ICommand` propiedades. La clase define un [constructor alternativo](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean})) que acepta `execute` y `canExecute` métodos con parámetros de tipo `Object`. Se trata cómo el `CommandParameter` se pasa a estos métodos.

Sin embargo, cuando se usa `CommandParameter`, resulta más fácil de utilizar el modelo genérico [ `Command<T>` ](xref:Xamarin.Forms.Command`1) clase para especificar el tipo del objeto establecido en `CommandParameter`. El `execute` y `canExecute` métodos que especifique tienen parámetros de ese tipo.

El **teclado Decimal** página ilustra esta técnica mostrándole cómo implementar un teclado numérico para escribir números decimales. El `BindingContext` para el `Grid` es un `DecimalKeypadViewModel`. El `Entry` propiedad de este ViewModel está enlazada a la `Text` propiedad de un `Label`. Todos los `Button` objetos están enlazados a varios comandos en el modelo de vista: `ClearCommand`, `BackspaceCommand`, y `DigitCommand`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.DecimalKeypadPage"
             Title="Decimal Keyboard">

    <Grid WidthRequest="240"
          HeightRequest="480"
          ColumnSpacing="2"
          RowSpacing="2"
          HorizontalOptions="Center"
          VerticalOptions="Center">

        <Grid.BindingContext>
            <local:DecimalKeypadViewModel />
        </Grid.BindingContext>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Button">
                    <Setter Property="FontSize" Value="32" />
                    <Setter Property="BorderWidth" Value="1" />
                    <Setter Property="BorderColor" Value="Black" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Label Text="{Binding Entry}"
               Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3"
               FontSize="32"
               LineBreakMode="HeadTruncation"
               VerticalTextAlignment="Center"
               HorizontalTextAlignment="End" />

        <Button Text="CLEAR"
                Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding ClearCommand}" />

        <Button Text="&#x21E6;"
                Grid.Row="1" Grid.Column="2"
                Command="{Binding BackspaceCommand}" />

        <Button Text="7"
                Grid.Row="2" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="7" />

        <Button Text="8"
                Grid.Row="2" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="8" />

        <Button Text="9"
                Grid.Row="2" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="9" />

        <Button Text="4"
                Grid.Row="3" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="4" />

        <Button Text="5"
                Grid.Row="3" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="5" />

        <Button Text="6"
                Grid.Row="3" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="6" />

        <Button Text="1"
                Grid.Row="4" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="1" />

        <Button Text="2"
                Grid.Row="4" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="2" />

        <Button Text="3"
                Grid.Row="4" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="3" />

        <Button Text="0"
                Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding DigitCommand}"
                CommandParameter="0" />

        <Button Text="&#x00B7;"
                Grid.Row="5" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="." />
    </Grid>
</ContentPage>
```

El 11 botones para los 10 dígitos y el separador decimal comparten un enlace a `DigitCommand`. El `CommandParameter` distingue entre estos botones. El valor establecido en `CommandParameter` suele ser el mismo que el texto que se muestra en el botón, excepto el separador decimal, que, para una mayor claridad, se muestra con un carácter de punto central.

Este es el programa en acción:

[![Teclado decimal](commanding-images/decimalkeyboard-small.png "teclado Decimal")](commanding-images/decimalkeyboard-large.png#lightbox "teclado Decimal")

Observe que el botón de la coma decimal de tres todas las capturas de pantalla está deshabilitado porque el número introducido ya contiene un separador decimal.

El `DecimalKeypadViewModel` define un `Entry` propiedad de tipo `string` (que es la única propiedad que se desencadena una `PropertyChanged` evento) y tres propiedades de tipo `ICommand`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{
    string entry = "0";

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public string Entry
    {
        private set
        {
            if (entry != value)
            {
                entry = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Entry"));
            }
        }
        get
        {
            return entry;
        }
    }

    public ICommand ClearCommand { private set; get; }

    public ICommand BackspaceCommand { private set; get; }

    public ICommand DigitCommand { private set; get; }
}
```

El botón correspondiente a la `ClearCommand` siempre está habilitada y simplemente establece la entrada a "0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {
        ClearCommand = new Command(
            execute: () =>
            {
                Entry = "0";
                RefreshCanExecutes();
            });

        ···

    }

    void RefreshCanExecutes()
    {
        ((Command)BackspaceCommand).ChangeCanExecute();
        ((Command)DigitCommand).ChangeCanExecute();
    }

    ···

}
```

Dado que el botón siempre está habilitado, no es necesario especificar un `canExecute` argumento en el `Command` constructor.

La lógica para escribir números y del retroceso es un poco complicado, porque si no se ha especificado ningún dígito, el `Entry` propiedad es la cadena "0". Si el usuario escribe más ceros, la `Entry` todavía contiene sólo un cero. Si el usuario escribe cualquier otro dígito, ese dígito reemplaza el cero. Sin embargo, si el usuario escribe un punto decimal antes de cualquier otro dígito, a continuación, `Entry` es la cadena "0.".

El **retroceso** botón está habilitado solo cuando la longitud de la entrada es mayor que 1, o si `Entry` no es igual a la cadena "0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        BackspaceCommand = new Command(
            execute: () =>
            {
                Entry = Entry.Substring(0, Entry.Length - 1);
                if (Entry == "")
                {
                    Entry = "0";
                }
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return Entry.Length > 1 || Entry != "0";
            });

        ···

    }

    ···

}
```

La lógica para la `execute` funcionen para el **retroceso** botón garantiza que el `Entry` es al menos una cadena de "0".

El `DigitCommand` propiedad está enlazada a los botones de 11, cada uno de los cuales identifica a sí mismo con el `CommandParameter` propiedad. El `DigitCommand` podrían establecerse en una instancia de la normal `Command` clase, pero la más fácil de usar el `Command<T>` clase genérica. Cuando se usa la interfaz de comandos con XAML, el `CommandParameter` propiedades suelen ser cadenas, y que es el tipo del argumento genérico. El `execute` y `canExecute` funciones, a continuación, tienen argumentos de tipo `string`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        DigitCommand = new Command<string>(
            execute: (string arg) =>
            {
                Entry += arg;
                if (Entry.StartsWith("0") && !Entry.StartsWith("0."))
                {
                    Entry = Entry.Substring(1);
                }
                RefreshCanExecutes();
            },
            canExecute: (string arg) =>
            {
                return !(arg == "." && Entry.Contains("."));
            });
    }

    ···

}
```

El `execute` método anexa el argumento de cadena para el `Entry` propiedad. Sin embargo, si el resultado comienza con un cero (pero no un cero y un separador decimal), a continuación, ese cero inicial debe quitarse mediante el `Substring` función.

El `canExecute` devuelve del método `false` sólo si el argumento es el separador decimal (lo que indica que se presiona el separador decimal) y `Entry` ya contiene un separador decimal.

Todos los `execute` métodos llamada `RefreshCanExecutes`, que llama a `ChangeCanExecute` para ambos `DigitCommand` y `ClearCommand`. Esto garantiza que se habilitan los botones de retroceso y separador decimal o deshabilitada en función de la secuencia actual de dígitos especificados.

## <a name="adding-commands-to-existing-views"></a>Agregar comandos a las vistas existentes

Si desea usar la interfaz de comandos con las vistas que no son compatibles con, es posible utilizar un comportamiento de Xamarin.Forms que convierte un evento en un comando. Esto se describe en el artículo [ **reutilizable EventToCommandBehavior**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md).

## <a name="asynchronous-commanding-for-navigation-menus"></a>Asincrónica comandos de menús de navegación

Los comandos es útil para la implementación de menús de navegación, como los de la [ **demostraciones de enlace de datos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) propio programa. Aquí es parte de **MainPage.xaml**:


```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MainPage"
             Title="Data Binding Demos"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection Title="Basic Bindings">

                <TextCell Text="Basic Code Binding"
                          Detail="Define a data-binding in code"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicCodeBindingPage}" />

                <TextCell Text="Basic XAML Binding"
                          Detail="Define a data-binding in XAML"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicXamlBindingPage}" />

                <TextCell Text="Alternative Code Binding"
                          Detail="Define a data-binding in code without a BindingContext"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:AlternativeCodeBindingPage}" />

                ···

            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Al usar comandos con XAML, `CommandParameter` normalmente se establecen propiedades en cadenas. En este caso, sin embargo, se usa una extensión de marcado XAML para que la `CommandParameter` es de tipo `System.Type`.

Cada `Command` propiedad está enlazada a una propiedad denominada `NavigateCommand`. Que la propiedad está definida en el archivo de código subyacente, **MainPage.xaml.cs**:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(
            async (Type pageType) =>
            {
                Page page = (Page)Activator.CreateInstance(pageType);
                await Navigation.PushAsync(page);
            });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

El constructor establece la `NavigateCommand` propiedad a un `execute` método que crea una instancia de la `System.Type` parámetro y, a continuación, navega a ella. Dado que el `PushAsync` llamada requiere un `await` operador, el `execute` método debe estar marcado como asincrónicas. Esto se consigue con la `async` palabra clave antes de la lista de parámetros.

El constructor establece también el `BindingContext` de la página a sí mismo para que los enlaces haga referencia el `NavigateCommand` en esta clase.

El orden del código de este constructor establece una diferencia: los `InitializeComponent` llamada hace que el XAML que se va a analizar, pero en ese momento, el enlace a una propiedad denominada `NavigateCommand` no se puede resolver porque `BindingContext` está establecido en `null`. Si el `BindingContext` se establece en el constructor *antes* `NavigateCommand` está establecido, el enlace puede resolver cuándo `BindingContext` está establecida, pero en ese momento, `NavigateCommand` sigue siendo `null`. Establecer `NavigateCommand` después `BindingContext` no tendrá ningún efecto en el enlace porque un cambio en `NavigateCommand` no se activa un `PropertyChanged` eventos y el enlace no sabe que `NavigateCommand` ahora es válido.

Si se establecen `NavigateCommand` y `BindingContext` (en cualquier orden) antes de llamar a `InitializeComponent` funcionará porque ambos componentes del enlace se establecen cuando el analizador XAML encuentra la definición de enlace.

Enlaces de datos pueden resultar complicados, pero como ha visto en esta serie de artículos, son eficaces y versátiles y ayudar enormemente a organizar el código mediante la separación lógica subyacente de la interfaz de usuario.



## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
