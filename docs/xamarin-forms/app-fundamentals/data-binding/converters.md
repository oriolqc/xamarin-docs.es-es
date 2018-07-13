---
title: Convertidores de valores de enlace de Xamarin.Forms
description: En este artículo se explica cómo convertir los valores dentro de un enlace de datos de Xamarin.Forms mediante la implementación de un convertidor de valores (que es también se denomina un conversor de enlaces o convertidor de valores de enlace).
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 28892692133020de1fa5a6eb007bb3f9bcf2612b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997491"
---
# <a name="xamarinforms-binding-value-converters"></a>Convertidores de valores de enlace de Xamarin.Forms

Enlaces de datos normalmente transferir datos desde una propiedad de origen a una propiedad de destino y, en algunos casos, desde la propiedad de destino para la propiedad de origen. Esta transferencia es sencilla cuando las propiedades de origen y destino son del mismo tipo, o cuando un tipo se puede convertir al tipo a través de una conversión implícita. Cuando no es así, debe realizarse una conversión de tipos.

En el [ **aplicarles un formato** ](string-formatting.md) artículo, vimos cómo puede usar el `StringFormat` propiedad de un enlace de datos para convertir cualquier tipo en una cadena. Para otros tipos de conversiones, deberá escribir algún código especializado en una clase que implementa el [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) interfaz. (La plataforma Universal de Windows contiene una clase similar denominada [ `IValueConverter` ](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) en el `Windows.UI.Xaml.Data` espacio de nombres, pero esto `IValueConverter` está en el `Xamarin.Forms` espacio de nombres.) Las clases que implementan `IValueConverter` se denominan *los convertidores de tipos de valor*, pero también a menudo se conocen como *enlace convertidores* o *convertidores de valores de enlace*.

## <a name="the-ivalueconverter-interface"></a>La interfaz de IValueConverter

Suponga que desea definir un enlace de datos donde la propiedad de origen es del tipo `int` pero la propiedad de destino es un `bool`. Desea que este enlace de datos para generar un `false` valor cuando el origen de entero es igual a 0, y `true` en caso contrario.  

Puede hacerlo con una clase que implementa el `IValueConverter` interfaz:

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value != 0;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? 1 : 0;
    }
}
```

Establecer una instancia de esta clase a la [ `Converter` ](xref:Xamarin.Forms.Binding.Converter) propiedad de la `Binding` clase o a la [ `Converter` ](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) propiedad de la `Binding` extensión de marcado. Esta clase se convierte en parte del enlace de datos.

El `Convert` se llama al método cuando los datos se mueven desde el origen al destino en `OneWay` o `TwoWay` enlaces. El `value` parámetro es el objeto o el valor del origen de enlace de datos. El método debe devolver un valor del tipo de destino de enlace de datos. El método que se muestra a continuación las conversiones el `value` parámetro a un `int` y, a continuación, se compara con 0 para un `bool` valor devuelto.

El `ConvertBack` se llama al método cuando los datos se mueven desde el destino al origen en `TwoWay` o `OneWayToSource` enlaces. `ConvertBack` realiza la conversión opuesta: supone el `value` parámetro es un `bool` desde el destino y lo convierte en un `int` devolver el valor para el origen.

Si el enlace de datos también incluye un `StringFormat` establecer, se invoca el convertidor de valores antes de que el resultado es el formato como una cadena.

El **habilitar botones** página en el [ **demostraciones de enlace de datos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) ejemplo muestra cómo utilizar este convertidor de valores en un enlace de datos. El `IntToBoolConverter` se crea una instancia en el diccionario de recursos de la página. A continuación, se hace referencia con un `StaticResource` extensión de marcado para establecer el `Converter` propiedad en enlaces de datos de dos. Es muy común para compartir los convertidores de tipos de datos entre varios enlaces de datos en la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.EnableButtonsPage"
             Title="Enable Buttons">
    <ContentPage.Resources>
        <ResourceDictionary>
            <local:IntToBoolConverter x:Key="intToBool" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <Entry x:Name="entry1"
               Text=""
               Placeholder="enter search term"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Search"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry1},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />

        <Entry x:Name="entry2"
               Text=""
               Placeholder="enter destination"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Submit"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry2},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />
    </StackLayout>
</ContentPage>
```

Si se usa un convertidor de valores en varias páginas de la aplicación, puede inicializarla en el diccionario de recursos en el **App.xaml** archivo.

El **habilitar botones** página muestra un común cuando necesita un `Button` realiza una operación basada en texto que el usuario escribe en un `Entry` vista. Si no se ha escrito en el `Entry`, el `Button` debe deshabilitarse. Cada `Button` contiene un enlace de datos en su `IsEnabled` propiedad. El origen de enlace de datos es el `Length` propiedad de la `Text` propiedad de los correspondientes `Entry`. Si ese `Length` propiedad no es 0, devuelve el convertidor de valores `true` y `Button` está habilitado:

[![Habilitar los botones](converters-images/enablebuttons-small.png "habilitar botones")](converters-images/enablebuttons-large.png#lightbox "habilitar los botones")

Tenga en cuenta que el `Text` propiedad en cada `Entry` se inicializa en una cadena vacía. El `Text` propiedad es `null` de forma predeterminada, y los datos de enlace no funcionará en ese caso.

Algunos convertidores de valores escritos específicamente para determinadas aplicaciones, mientras que otros están generalizados. Si sabe que un convertidor de valores solo se usará en `OneWay` enlaces, el `ConvertBack` método puede devolver simplemente `null`.

El `Convert` método mostrado anteriormente, implícitamente se da por supuesto que el `value` argumento es de tipo `int` y el valor devuelto debe ser de tipo `bool`. De forma similar, el `ConvertBack` método supone que la `value` argumento es de tipo `bool` y el valor devuelto es `int`. Si no es así, se producirá una excepción en tiempo de ejecución.

Puede escribir los convertidores de valores que se generalizó más y acepte diferentes tipos de datos. El `Convert` y `ConvertBack` métodos pueden usar el `as` o `is` operadores con la `value` parámetro, o puede llamar a `GetType` en el parámetro para determinar su tipo y, a continuación, realice algo adecuados. El tipo esperado del valor devuelto de cada método viene dado por la `targetType` parámetro. A veces, los convertidores de valores se utilizan con los enlaces de datos de diferentes tipos de destino; Puede usar el convertidor de valores el `targetType` argumento para realizar una conversión del tipo correcto.

Si la conversión se realiza es diferente para distintas referencias culturales, utilice el `culture` parámetro para este propósito. El `parameter` argumento `Convert` y `ConvertBack` se explica más adelante en este artículo.

## <a name="binding-converter-properties"></a>Propiedades de convertidor de tipos de enlace

Las clases de convertidor de tipos de valor pueden tener propiedades y los parámetros genéricos. Convierte este convertidor de valores determinado un `bool` desde el origen para un objeto de tipo `T` para el destino:

```csharp
public class BoolToObjectConverter<T> : IValueConverter
{
    public T TrueObject { set; get; }

    public T FalseObject { set; get; }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? TrueObject : FalseObject;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return ((T)value).Equals(TrueObject);
    }
}
```

El **conmutador indicadores** página muestra cómo se puede usar para mostrar el valor de un `Switch` vista. Aunque es común para crear instancias de los convertidores de valores como recursos en un diccionario de recursos, esta página muestra una alternativa: se crea una instancia de cada convertidor de valores entre `Binding.Converter` etiquetas de elemento de propiedad. El `x:TypeArguments` indica el argumento genérico, y `TrueObject` y `FalseObject` se establecen en objetos de ese tipo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SwitchIndicatorsPage"
             Title="Switch Indicators">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>

            <Style TargetType="Switch">
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Subscribe?" />
            <Switch x:Name="switch1" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch1}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Of course!"
                                                         FalseObject="No way!" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Allow popups?" />
            <Switch x:Name="switch2" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Yes"
                                                         FalseObject="No" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
                <Label.TextColor>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Color"
                                                         TrueObject="Green"
                                                         FalseObject="Red" />
                        </Binding.Converter>
                    </Binding>
                </Label.TextColor>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Learn more?" />
            <Switch x:Name="switch3" />
            <Label FontSize="18"
                   VerticalOptions="Center">
                <Label.Style>
                    <Binding Source="{x:Reference switch3}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Style">
                                <local:BoolToObjectConverter.TrueObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Indubitably!" />
                                        <Setter Property="FontAttributes" Value="Italic, Bold" />
                                        <Setter Property="TextColor" Value="Green" />
                                    </Style>                                    
                                </local:BoolToObjectConverter.TrueObject>

                                <local:BoolToObjectConverter.FalseObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Maybe later" />
                                        <Setter Property="FontAttributes" Value="None" />
                                        <Setter Property="TextColor" Value="Red" />
                                    </Style>
                                </local:BoolToObjectConverter.FalseObject>
                            </local:BoolToObjectConverter>
                        </Binding.Converter>
                    </Binding>
                </Label.Style>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

En el último de los tres `Switch` y `Label` pares, el argumento genérico se establece en `Style`y todo `Style` objetos se proporcionan para los valores de `TrueObject` y `FalseObject`. Estas ignorar el estilo implícito para `Label` se establece en el diccionario de recursos, por lo que las propiedades de ese estilo están asignadas explícitamente a la `Label`. Si activa o desactiva el `Switch` hace que el correspondiente `Label` para reflejar el cambio:

[![Cambiar indicadores](converters-images/switchindicators-small.png "cambiar indicadores")](converters-images/switchindicators-large.png#lightbox "cambiar indicadores")

También es posible usar [ `Triggers` ](~/xamarin-forms/app-fundamentals/triggers.md) para implementar cambios similares en la interfaz de usuario en función de otras vistas.

## <a name="binding-converter-parameters"></a>Enlazar parámetros de convertidor de tipos

El `Binding` clase define un [ `ConverterParameter` ](xref:Xamarin.Forms.Binding.ConverterParameter) propiedad y el `Binding` extensión de marcado también define un [ `ConverterParameter` ](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter) propiedad. Si se establece esta propiedad, el valor se pasa a la `Convert` y `ConvertBack` métodos como la `parameter` argumento. Incluso si la instancia de convertidor se comparte entre varios enlaces de datos, el `ConverterParameter` puede ser diferente para realizar conversiones algo diferentes.

El uso de `ConverterParameter` se muestra con un programa de selección de color. En este caso, el `RgbColorViewModel` tiene tres propiedades de tipo `double` denominado `Red`, `Green`, y `Blue` que usa para construir un `Color` valor:

```csharp
public class RgbColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Red
    {
        set
        {
            if (color.R != value)
            {
                Color = new Color(value, color.G, color.B);
            }
        }
        get
        {
            return color.R;
        }
    }

    public double Green
    {
        set
        {
            if (color.G != value)
            {
                Color = new Color(color.R, value, color.B);
            }
        }
        get
        {
            return color.G;
        }
    }

    public double Blue
    {
        set
        {
            if (color.B != value)
            {
                Color = new Color(color.R, color.G, value);
            }
        }
        get
        {
            return color.B;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Red"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Green"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Blue"));
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

El `Red`, `Green`, y `Blue` intervalo propiedades entre 0 y 1. Sin embargo, es preferible que los componentes se muestran como valores hexadecimales de dos dígitos.

Para mostrar estos elementos como valores hexadecimales en XAML, deben ser multiplicados por 255, puede convertir en un entero y, a continuación, el formato con la especificación de "X2" en el `StringFormat` propiedad. Las dos primeras tareas (multiplicar por 255 y convertir en un entero) pueden controlarse mediante el convertidor de valores. Para hacer que el convertidor de valores como generalizada como sea posible, se puede especificar el factor de multiplicación con el `ConverterParameter` propiedad, lo que significa que entra en el `Convert` y `ConvertBack` métodos como la `parameter` argumento:

```csharp
public class DoubleToIntConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)Math.Round((double)value * GetParameter(parameter));
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value / GetParameter(parameter);
    }

    double GetParameter(object parameter)
    {
        if (parameter is double)
            return (double)parameter;

        else if (parameter is int)
            return (int)parameter;

        else if (parameter is string)
            return double.Parse((string)parameter);

        return 1;
    }
}
```

El `Convert` convierte de un `double` a `int` al multiplicar por la `parameter` valor; la `ConvertBack` divide el entero `value` argumento por `parameter` y devuelve un `double` resultado. (En el programa se muestra a continuación, el convertidor de valores se usa solo en relación con la cadena de formato, por lo que `ConvertBack` no se usa.)

El tipo de la `parameter` argumento es probable que sea diferente en función de si se ha definido el enlace de datos en el código o XAML. Si el `ConverterParameter` propiedad de `Binding` está establecido en código, es probable que se establece en un valor numérico:

```csharp
binding.ConverterParameter = 255;
```

El `ConverterParameter` propiedad es de tipo `Object`, por lo que el compilador de C# interpreta el literal 255 como un entero y establece la propiedad en ese valor.

En XAML, sin embargo, el `ConverterParameter` es probable que se establecen de este modo:

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

El 255 parece un número, sino porque `ConverterParameter` es de tipo `Object`, el analizador XAML trata el 255 como una cadena.

Por ese motivo, el convertidor de valores mostrado anteriormente incluye otro `GetParameter` método que controla los casos para `parameter` perteneciente al tipo `double`, `int`, o `string`.  

El **Selector de colores RGB** crea una instancia de la página `DoubleToIntConverter` en su diccionario de recursos después de la definición de los dos estilos implícitos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.RgbColorSelectorPage"
             Title="RGB Color Selector">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <local:DoubleToIntConverter x:Key="doubleToInt" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:RgbColorViewModel Color="Gray" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Red}" />
            <Label Text="{Binding Red,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0:X2}'}" />

            <Slider Value="{Binding Green}" />
            <Label Text="{Binding Green,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0:X2}'}" />

            <Slider Value="{Binding Blue}" />
            <Label>
                <Label.Text>
                    <Binding Path="Blue"
                             StringFormat="Blue = {0:X2}"
                             Converter="{StaticResource doubleToInt}">
                        <Binding.ConverterParameter>
                            <x:Double>255</x:Double>
                        </Binding.ConverterParameter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Los valores de la `Red` y `Green` propiedades se muestran con un `Binding` extensión de marcado. El `Blue` sin embargo, crea una instancia de propiedad, el `Binding` clase para demostrar cómo explícita `double` se puede establecer el valor en `ConverterParameter` propiedad.

Este es el resultado:

[![Selector de colores RGB](converters-images/rgbcolorselector-small.png "Selector de colores RGB")](converters-images/rgbcolorselector-large.png#lightbox "Selector de colores RGB")


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
