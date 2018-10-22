---
title: Modo de enlace de Xamarin.Forms
description: En este artículo se explica cómo controlar el flujo de información entre el origen y destino mediante un modo de enlace, que se especifica con un miembro de la enumeración BindingMode. Todas las propiedades enlazables tienen un modo de enlace predeterminada, que indica el modo en vigor cuando esa propiedad es un destino de enlace de datos.
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/01/2018
ms.openlocfilehash: a6eaf08d17f70c43f451361e27555a09c39f26a9
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935672"
---
# <a name="xamarinforms-binding-mode"></a>Modo de enlace de Xamarin.Forms

En el [artículo anterior](basic-bindings.md), el **enlace de código alternativa** y **alternativa XAML enlace** páginas destacadas un `Label` con su `Scale` propiedad enlazado a la `Value` propiedad de un `Slider`. Porque el `Slider` valor inicial es 0, esto produce el `Scale` propiedad de la `Label` para establecerse en 0 en lugar de 1 y el `Label` ha desaparecido.

En el [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) ejemplo, el **enlace inverso** página es similar a los programas en el artículo anterior, salvo que el enlace de datos se define en el `Slider` en lugar de en el `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.ReverseBindingPage"
             Title="Reverse Binding">
    <StackLayout Padding="10, 0">

        <Label x:Name="label"
               Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                VerticalOptions="CenterAndExpand"
                Value="{Binding Source={x:Reference label},
                                Path=Opacity}" />
    </StackLayout>
</ContentPage>
```

En primer lugar, esto podría parecer con versiones anteriores: ahora el `Label` es el origen de enlace de datos y el `Slider` es el destino. Las referencias de enlace el `Opacity` propiedad de la `Label`, que tiene un valor predeterminado de 1.

Como cabría esperar, el `Slider` se inicializa en el valor 1 de la inicial `Opacity` valor `Label`. Esto se muestra en la captura de pantalla de iOS de la izquierda:

[![Invertir enlace](binding-mode-images/reversebinding-small.png "invertir enlace")](binding-mode-images/reversebinding-large.png#lightbox "invertir enlace")

Pero quizá le sorprende que el `Slider` seguirá funcionando, como se muestran en las capturas de pantalla de Android y UWP. Esto parece sugerir que el enlace de datos funciona mejor cuando el `Slider` es el destino de enlace en lugar de `Label` porque la inicialización funciona como se podría esperar.

La diferencia entre el **enlace inverso** ejemplo y los ejemplos anteriores implica la *modo de enlace*.

## <a name="the-default-binding-mode"></a>El modo de enlace predeterminada

El modo de enlace se especifica con un miembro de la [ `BindingMode` ](xref:Xamarin.Forms.BindingMode) enumeración:

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; datos van en ambas direcciones entre el origen y destino
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; datos van de origen a destino
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; van de datos de destino a origen
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; datos van desde el origen al destino, pero solo cuando el `BindingContext` cambios (nuevos con Xamarin.Forms 3.0)

Todas las propiedades enlazables tienen valor predeterminado es el enlace de modo que se establece cuando se crea la propiedad enlazable, y que está disponible en el [ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) propiedad de la `BindableProperty` objeto. Este modo de enlace predeterminada indica el modo en vigor cuando esa propiedad es un destino de enlace de datos.

El modo de enlace predeterminada para la mayoría de las propiedades como `Rotation`, `Scale`, y `Opacity` es `OneWay`. Cuando estas propiedades son destinos de enlace de datos, a continuación, se establece la propiedad de destino desde el origen.

Sin embargo, el modo de enlace predeterminado para el `Value` propiedad de `Slider` es `TwoWay`. Esto significa que, cuando el `Value` propiedad es un destino de enlace de datos, a continuación, el destino se establece desde el origen (como es habitual), pero también se establece el origen del destino. Esto es lo que permite el `Slider` debe establecerse en inicial `Opacity` valor.

Este enlace bidireccional puede parecer que crean un bucle infinito, pero eso no sucede. Propiedades enlazables no señalar un cambio de propiedad, a menos que la propiedad realmente cambia. Esto evita que un bucle infinito.

### <a name="two-way-bindings"></a>Enlaces bidireccionales

Propiedades enlazables más tienen un modo de enlace predeterminada de `OneWay` , pero las siguientes propiedades tienen un modo de enlace predeterminada de `TwoWay`:

- `Date` propiedad de `DatePicker`
- `Text` propiedad de `Editor`, `Entry`, `SearchBar`, y `EntryCell`
- `IsRefreshing` propiedad de `ListView`
- `SelectedItem` propiedad de `MultiPage`
- `SelectedIndex` y `SelectedItem` propiedades de `Picker`
- `Value` propiedad de `Slider` y `Stepper`
- `IsToggled` propiedad de `Switch`
- `On` propiedad de `SwitchCell`
- `Time` propiedad de `TimePicker`

Estas propiedades concretas se definen como `TwoWay` por una muy buena razón:

Cuando se usan enlaces de datos con la arquitectura de aplicación Model-View-ViewModel (MVVM), la clase ViewModel es el origen de enlace de datos y la vista, que consta de las vistas como `Slider`, son destinos de enlace de datos. Enlaces de MVVM son similares a la **enlace inverso** ejemplo más que los enlaces en los ejemplos anteriores. Es muy probable que desee que cada vista en la página que se inicialice con el valor de la propiedad correspondiente en el modelo de vista, pero los cambios en la vista también deberían afectar a la propiedad ViewModel.

Las propiedades con los modos de enlace predeterminada de `TwoWay` son las más probable que se usarán en escenarios MVVM.

### <a name="one-way-to-source-bindings"></a>Enlaces solo-bidireccional al origen

Propiedades enlazables de solo lectura tienen un modo de enlace predeterminada de `OneWayToSource`. Hay sólo una propiedad enlazable de lectura/escritura que tiene un modo de enlace predeterminada de `OneWayToSource`:

- `SelectedItem` propiedad de `ListView`

La razón es que un enlace en el `SelectedItem` debe dar como resultado de propiedad en la configuración del origen de enlace. Un ejemplo más adelante en este artículo invalida este comportamiento.

### <a name="one-time-bindings"></a>Enlaces de un solo uso

Varias propiedades tienen un modo de enlace predeterminada de `OneTime`. Estos son:

- `IsTextPredictionEnabled` propiedad de `Entry`
- `Text`, `BackgroundColor`, y `Style` propiedades de `Span`.

Destino de propiedades con un modo de enlace de `OneTime` sólo se actualizan cuando cambia el contexto de enlace. Para bindings en estas propiedades de destino, esto simplifica la infraestructura de enlace ya no es necesario supervisar los cambios en las propiedades del origen.

## <a name="viewmodels-and-property-change-notifications"></a>ViewModels y notificaciones de cambio de propiedad

El **Selector de colores Simple** página muestra el uso de un modelo de vista simple. Enlaces de datos permiten al usuario seleccionar un color mediante tres `Slider` elementos para el matiz, saturación y luminosidad.

El ViewModel es el origen de enlace de datos. El ViewModel *no* definir propiedades enlazables, pero implementa un mecanismo de notificación que permite que la infraestructura de enlace recibir una notificación cuando cambia el valor de una propiedad. Este mecanismo de notificación es la [ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) interfaz, que define una propiedad única denominada [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged). Una clase que implementa esta interfaz normalmente desencadena el evento cuando cambia el valor de uno de sus propiedades públicas. El evento no necesita activarse si la propiedad no cambia nunca. (El `INotifyPropertyChanged` interfaz también se implementa mediante `BindableObject` y un `PropertyChanged` evento se desencadena cada vez que una propiedad enlazable cambie el valor.)

El `HslColorViewModel` clase define cinco propiedades: la `Hue`, `Saturation`, `Luminosity`, y `Color` propiedades están interrelacionadas. Cuando cambia el valor, de cualquiera de los tres colores componentes la `Color` se vuelve a calcular la propiedad, y `PropertyChanged` eventos se activan para las cuatro propiedades:

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

Cuando el `Color` los cambios de propiedad, estático `GetNearestColorName` método en el `NamedColor` clase (también se incluye en el **DataBindingDemos** solución) Obtiene el color con nombre más cercano y establece el `Name` propiedad. Esto `Name` propiedad tiene una privada `set` descriptor de acceso, por lo que no se puede establecer desde fuera de la clase.

Cuando se establece una clase ViewModel como un origen de enlace, la infraestructura de enlace asocia un controlador para el `PropertyChanged` eventos. De esta manera, el enlace puede recibir una notificación de cambios en las propiedades y, a continuación, puede establecer las propiedades de destino de los valores cambiados.

Sin embargo, cuando una propiedad de destino (o la `Binding` definición en una propiedad de destino) tiene un `BindingMode` de `OneTime`, no es necesario para la infraestructura de enlace asociar un controlador en el `PropertyChanged` eventos. La propiedad de destino se actualiza solo cuando el `BindingContext` los cambios y no cuando se cambia la propiedad de origen.

El **Selector de colores Simple** archivo XAML crea una instancia el `HslColorViewModel` en el diccionario de recursos de la página y se inicializa el `Color` propiedad. El `BindingContext` propiedad de la `Grid` está establecido en un `StaticResource` extensión para hacer referencia a ese recurso de enlace:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SimpleColorSelectorPage">

    <ContentPage.Resources>
        <ResourceDictionary>
            <local:HslColorViewModel x:Key="viewModel"
                                     Color="MediumTurquoise" />

            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <Grid BindingContext="{StaticResource viewModel}">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <BoxView Color="{Binding Color}"
                 Grid.Row="0" />

        <StackLayout Grid.Row="1"
                     Margin="10, 0">

            <Label Text="{Binding Name}"
                   HorizontalTextAlignment="Center" />

            <Slider Value="{Binding Hue}" />

            <Slider Value="{Binding Saturation}" />

            <Slider Value="{Binding Luminosity}" />
        </StackLayout>
    </Grid>
</ContentPage>
```

El `BoxView`, `Label`y tres `Slider` vistas heredan el contexto de enlace desde el `Grid`. Estas vistas son todos los destinos de enlace que hacen referencia a las propiedades de origen en el modelo de vista. Para el `Color` propiedad de la `BoxView`y el `Text` propiedad de la `Label`, los enlaces de datos son `OneWay`: se establecen las propiedades de la vista de las propiedades en ViewModel.

El `Value` propiedad de la `Slider`, sin embargo, es `TwoWay`. Esto permite que cada `Slider` debe establecerse de ViewModel y también del modelo de vista debe establecerse de cada uno `Slider`.

Cuando el programa se ejecuta en primer lugar, el `BoxView`, `Label`y tres `Slider` elementos están establecidos de ViewModel en función de la inicial `Color` propiedad establecida cuando se crea una instancia de ViewModel. Esto se muestra en la captura de pantalla de iOS a la izquierda:

[![Selector de colores simple](binding-mode-images/simplecolorselector-small.png "Selector de colores Simple")](binding-mode-images/simplecolorselector-large.png#lightbox "Selector de colores Simple")

Al manipular los controles deslizantes, el `BoxView` y `Label` se actualizan en consecuencia, como se muestra en las capturas de pantalla de Android y UWP.

Iniciar el ViewModel en el diccionario de recursos es un enfoque común. También es posible crear instancias de ViewModel dentro de etiquetas de elemento de propiedad para el `BindingContext` propiedad. En el **Selector de colores Simple** XAML de archivos, pruebe a quitar el `HslColorViewModel` desde el diccionario de recursos y establézcalo en la `BindingContext` propiedad de la `Grid` similar al siguiente:

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

El contexto de enlace se puede establecer en una variedad de formas. En ocasiones, el archivo de código subyacente se crea una instancia de ViewModel y lo establece en el `BindingContext` propiedad de la página. Estos son enfoques válidos.

## <a name="overriding-the-binding-mode"></a>Reemplazar el modo de enlace

Si el modo de enlace predeterminada en la propiedad de destino no es adecuado para un enlace de datos determinado, es posible invalidar estableciendo la [ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode) propiedad de `Binding` (o la [ `Mode` ](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) propiedad de la `Binding` extensión de marcado) en uno de los miembros de la `BindingMode` enumeración.

Sin embargo, establecer el `Mode` propiedad `TwoWay` no siempre funciona como cabría esperar. Por ejemplo, pruebe a modificar el **alternativa XAML enlace** archivo XAML para incluir `TwoWay` en la definición de enlace:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

Se podría esperar que la `Slider` se inicializaría en el valor inicial de la `Scale` propiedad, que es 1, pero eso no sucede. Cuando un `TwoWay` enlace se ha inicializado, el destino se establece desde el origen en primer lugar, lo que significa que el `Scale` propiedad está establecida en el `Slider` valor predeterminado de 0. Cuando el `TwoWay` enlace se configura en el `Slider`, el `Slider` inicialmente se establece desde el origen.

Puede establecer el modo de enlace en `OneWayToSource` en el **alternativa XAML enlace** ejemplo:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

Ahora el `Slider` se inicializa en 1 (el valor predeterminado de `Scale`) pero manipulando el `Slider` no afecta a la `Scale` propiedad, por lo que esto no es muy útil.

Una aplicación muy útil de cómo reemplazar el modo de enlace predeterminada con `TwoWay` implica la `SelectedItem` propiedad de `ListView`. El modo de enlace predeterminada es `OneWayToSource`. Cuando se establece un enlace de datos en el `SelectedItem` propiedad para hacer referencia a una propiedad de origen en un modelo de vista, se configurará esa propiedad de origen desde el `ListView` selección. Sin embargo, en algunas circunstancias, puede que le interese el `ListView` van a inicializar desde el modelo de vista.

El **la configuración de ejemplo** página muestra esta técnica. Esta página representa una implementación simple de la configuración de la aplicación, que se definen muy a menudo en una clase ViewModel, por ejemplo, esto `SampleSettingsViewModel` archivo:

```csharp
public class SampleSettingsViewModel : INotifyPropertyChanged
{
    string name;
    DateTime birthDate;
    bool codesInCSharp;
    double numberOfCopies;
    NamedColor backgroundNamedColor;

    public event PropertyChangedEventHandler PropertyChanged;

    public SampleSettingsViewModel(IDictionary<string, object> dictionary)
    {
        Name = GetDictionaryEntry<string>(dictionary, "Name");
        BirthDate = GetDictionaryEntry(dictionary, "BirthDate", new DateTime(1980, 1, 1));
        CodesInCSharp = GetDictionaryEntry<bool>(dictionary, "CodesInCSharp");
        NumberOfCopies = GetDictionaryEntry(dictionary, "NumberOfCopies", 1.0);
        BackgroundNamedColor = NamedColor.Find(GetDictionaryEntry(dictionary, "BackgroundNamedColor", "White"));
    }

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public DateTime BirthDate
    {
        set { SetProperty(ref birthDate, value); }
        get { return birthDate; }
    }

    public bool CodesInCSharp
    {
        set { SetProperty(ref codesInCSharp, value); }
        get { return codesInCSharp; }
    }

    public double NumberOfCopies
    {
        set { SetProperty(ref numberOfCopies, value); }
        get { return numberOfCopies; }
    }

    public NamedColor BackgroundNamedColor
    {
        set
        {
            if (SetProperty(ref backgroundNamedColor, value))
            {
                OnPropertyChanged("BackgroundColor");
            }
        }
        get { return backgroundNamedColor; }
    }

    public Color BackgroundColor
    {
        get { return BackgroundNamedColor?.Color ?? Color.White; }
    }

    public void SaveState(IDictionary<string, object> dictionary)
    {
        dictionary["Name"] = Name;
        dictionary["BirthDate"] = BirthDate;
        dictionary["CodesInCSharp"] = CodesInCSharp;
        dictionary["NumberOfCopies"] = NumberOfCopies;
        dictionary["BackgroundNamedColor"] = BackgroundNamedColor.Name;
    }

    T GetDictionaryEntry<T>(IDictionary<string, object> dictionary, string key, T defaultValue = default(T))
    {
        return dictionary.ContainsKey(key) ? (T)dictionary[key] : defaultValue;
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

Cada configuración de la aplicación es una propiedad que se guarda en el diccionario de propiedades de Xamarin.Forms en un método denominado `SaveState` y se carga desde ese diccionario en el constructor. Hacia la parte inferior de la clase son dos métodos que ayudan a simplificar modelos de vista y que sean menos propenso a errores. El `OnPropertyChanged` método en la parte inferior tiene un parámetro opcional que se establece en la propiedad que realiza la llamada. Esto evita errores ortográficos, al especificar el nombre de la propiedad como una cadena.

El `SetProperty` método en la clase hace incluso más: se compara el valor se establece en la propiedad con el valor almacenado como un campo y solo llama `OnPropertyChanged` cuando los dos valores no son iguales.

El `SampleSettingsViewModel` clase define dos propiedades para el color de fondo: el `BackgroundNamedColor` propiedad es de tipo `NamedColor`, que una clase también se incluye en el **DataBindingDemos** solución. El `BackgroundColor` propiedad es de tipo `Color`y se obtiene de la `Color` propiedad de la `NamedColor` objeto.

El `NamedColor` clase usa la reflexión de .NET para enumerar todos los campos públicos estáticos en Xamarin.Forms `Color` estructura y almacenarlas con sus nombres en una colección accesible desde estático `All` propiedad:

```csharp
public class NamedColor : IEquatable<NamedColor>, IComparable<NamedColor>
{
    // Instance members
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    public bool Equals(NamedColor other)
    {
        return Name.Equals(other.Name);
    }

    public int CompareTo(NamedColor other)
    {
        return Name.CompareTo(other.Name);
    }

    // Static members
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof(Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                                (int)(255 * color.R),
                                                (int)(255 * color.G),
                                                (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        all.Sort();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }

    public static NamedColor Find(string name)
    {
        return ((List<NamedColor>)All).Find(nc => nc.Name == name);
    }

    public static string GetNearestColorName(Color color)
    {
        double shortestDistance = 1000;
        NamedColor closestColor = null;

        foreach (NamedColor namedColor in NamedColor.All)
        {
            double distance = Math.Sqrt(Math.Pow(color.R - namedColor.Color.R, 2) +
                                        Math.Pow(color.G - namedColor.Color.G, 2) +
                                        Math.Pow(color.B - namedColor.Color.B, 2));

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                closestColor = namedColor;
            }
        }
        return closestColor.Name;
    }
}
```

El `App` clase en el **DataBindingDemos** proyecto define una propiedad denominada `Settings` de tipo `SampleSettingsViewModel`. Esta propiedad se inicializa cuando la `App` se crea una instancia de clase y el `SaveState` método se llama cuando el `OnSleep` se llama al método:

```csharp
public partial class App : Application
{
    public App()
    {
        InitializeComponent();

        Settings = new SampleSettingsViewModel(Current.Properties);

        MainPage = new NavigationPage(new MainPage());
    }

    public SampleSettingsViewModel Settings { private set; get; }

    protected override void OnStart()
    {
        // Handle when your app starts
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Settings.SaveState(Current.Properties);
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
    }
}
```

Para obtener más información sobre los métodos de ciclo de vida de aplicación, consulte el artículo [ **ciclo de vida de aplicación**](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

Casi todo lo demás se controla en el **SampleSettingsPage.xaml** archivo. El `BindingContext` de la página se establece mediante un `Binding` extensión de marcado: el origen de enlace es estático `Application.Current` propiedad, que es la instancia de la `App` clase en el proyecto y el `Path` está establecido en el `Settings` propiedad, que es el `SampleSettingsViewModel` objeto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SampleSettingsPage"
             Title="Sample Settings"
             BindingContext="{Binding Source={x:Static Application.Current},
                                      Path=Settings}">

    <StackLayout BackgroundColor="{Binding BackgroundColor}"
                 Padding="10"
                 Spacing="10">

        <StackLayout Orientation="Horizontal">
            <Label Text="Name: "
                   VerticalOptions="Center" />

            <Entry Text="{Binding Name}"
                   Placeholder="your name"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Birth Date: "
                   VerticalOptions="Center" />

            <DatePicker Date="{Binding BirthDate}"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Do you code in C#? "
                   VerticalOptions="Center" />

            <Switch IsToggled="{Binding CodesInCSharp}"
                    VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Number of Copies: "
                   VerticalOptions="Center" />

            <Stepper Value="{Binding NumberOfCopies}"
                     VerticalOptions="Center" />

            <Label Text="{Binding NumberOfCopies}"
                   VerticalOptions="Center" />
        </StackLayout>

        <Label Text="Background Color:" />

        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
                  VerticalOptions="FillAndExpand"
                  RowHeight="40">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     HeightRequest="32"
                                     WidthRequest="32"
                                     VerticalOptions="Center" />

                            <Label Text="{Binding FriendlyName}"
                                   FontSize="24"
                                   VerticalOptions="Center" />
                        </StackLayout>                        
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Todos los elementos secundarios de la página heredan el contexto de enlace. La mayoría de los otros enlaces en esta página son para las propiedades de `SampleSettingsViewModel`. El `BackgroundColor` propiedad se usa para establecer el `BackgroundColor` propiedad de la `StackLayout`y el `Entry`, `DatePicker`, `Switch`, y `Stepper` propiedades se enlazan a otras propiedades en ViewModel.

El `ItemsSource` propiedad de la `ListView` está establecido en estático `NamedColor.All` propiedad. Esto rellena el `ListView` con todos los `NamedColor` instancias. Para cada elemento de la `ListView`, el contexto de enlace para el elemento está establecido en un `NamedColor` objeto. El `BoxView` y `Label` en el `ViewCell` están enlazados a las propiedades de `NamedColor`.

El `SelectedItem` propiedad de la `ListView` es de tipo `NamedColor`y se enlaza a la `BackgroundNamedColor` propiedad de `SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

El modo de enlace predeterminado para `SelectedItem` es `OneWayToSource`, que establece la propiedad ViewModel desde el elemento seleccionado. El `TwoWay` modo permite el `SelectedItem` van a inicializar desde el modelo de vista.

Sin embargo, cuando el `SelectedItem` está establecido en este modo, el `ListView` no se desplaza automáticamente para mostrar el elemento seleccionado. Es necesario un poco de código en el archivo de código subyacente:

```csharp
public partial class SampleSettingsPage : ContentPage
{
    public SampleSettingsPage()
    {
        InitializeComponent();

        if (colorListView.SelectedItem != null)
        {
            colorListView.ScrollTo(colorListView.SelectedItem,
                                   ScrollToPosition.MakeVisible,
                                   false);
        }
    }
}
```

La captura de pantalla de iOS a la izquierda muestra el programa cuando se ejecuta en primer lugar. El constructor en `SampleSettingsViewModel` inicializa el color de fondo a blanco, y eso es lo que está seleccionado en el `ListView`:

[![Ejemplo de configuración](binding-mode-images/samplesettings-small.png "ejemplo de configuración")](binding-mode-images/samplesettings-large.png#lightbox "ejemplo de configuración")

Las otras dos capturas de pantalla muestran la configuración modificada. Al experimentar con esta página, recuerde colocar el programa en modo de suspensión o finalización en el dispositivo o emulador que se está ejecutando. Finalizar el programa desde el depurador de Visual Studio no provocará la `OnSleep` invalidar en el `App` llamar a la clase.

En el siguiente artículo verá cómo especificar [ **aplicarles un formato** ](string-formatting.md) de enlaces de datos que se establecen en el `Text` propiedad de `Label`.


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
