---
title: Consumir las extensiones de marcado XAML
description: Este artículo explica cómo utilizar las extensiones de marcado de XAML de Xamarin.Forms para mejorar la eficacia y flexibilidad de XAML al permitir que los atributos del elemento que se pueden establecer desde una variedad de orígenes.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: 278677d45f997ac446c2a20967dc3501179bf8da
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245942"
---
# <a name="consuming-xaml-markup-extensions"></a>Consumir las extensiones de marcado XAML

Las extensiones de marcado XAML ayudar a mejorar la eficacia y flexibilidad de XAML al permitir que los atributos del elemento que se pueden establecer desde una variedad de orígenes. Varias extensiones de marcado XAML son parte de la especificación de XAML 2009. Estos aparecen en los archivos XAML con la costumbre `x` prefijo de espacio de nombres y se conocen con este prefijo. Se describen en las secciones siguientes:

- [`x:Static`](#static) &ndash; hacer referencia a propiedades estáticas, campos o miembros de la enumeración.
- [`x:Reference`](#reference) &ndash; referencia de elementos en la página.
- [`x:Type`](#type) &ndash; Establezca un atributo en un `System.Type` objeto.
- [`x:Array`](#array) &ndash; construir una matriz de objetos de un tipo determinado.
- [`x:Null`](#null) &ndash; Establezca un atributo en un `null` valor.

Tres otras extensiones de marcado XAML históricamente han admitido por otras implementaciones de XAML y también son compatibles con Xamarin.Forms. Se describen más detalladamente en otros artículos:

- `StaticResource` &ndash; hacer referencia a objetos de un diccionario de recursos, como se describe en el artículo [ **diccionarios de recursos**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource` &ndash; responder a los cambios en objetos en un diccionario de recursos, como se describe en el artículo [ **estilos dinámicos**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding` &ndash; establecer un vínculo entre las propiedades de dos objetos, como se describe en el artículo [ **enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding` &ndash; realiza el enlace de datos de una plantilla de control, como se describe en el artículo [**enlace a partir de una plantilla de Control**] / guías/xamarin-forms/aplicación-fundamentals/plantillas/control-plantillas/enlace a plantilla /)

El [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) diseño hace uso de la extensión de marcado personalizada [ `ConstraintExpression` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/). Esta extensión de marcado se describe en el artículo [ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>Extensiones de marcado x:Static

El `x:Static` extensión de marcado es compatible con la [ `StaticExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) clase. La clase tiene una propiedad única denominada [ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/) de tipo `string` que se establece en el nombre de una constante pública, una propiedad estática, un campo estático o un miembro de enumeración.

Una manera común de utilizar `x:Static` consiste en definir una clase con algunas constantes o variables estáticas, como este pequeño `AppConstants` clase en el [ **MarkupExtensions** ](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/) programa:

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

El **x: Static demostración** página muestra varias maneras de utilizar el `x:Static` extensión de marcado. El enfoque más detallado crea instancias el `StaticExtension` clase entre `Label.FontSize` etiquetas de elemento de propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

El analizador de XAML también permite la `StaticExtension` clase se abrevia como `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Esto puede simplificar aún más, pero el cambio, introduce algunos nueva sintaxis: consta de colocar la `StaticExtension` clase y la configuración entre llaves del miembro. La expresión resultante se establece directamente en el `FontSize` atributo:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Tenga en cuenta que hay *sin* comillas dentro de las llaves. El `Member` propiedad de `StaticExtension` ya no es un atributo XML. En su lugar es parte de la expresión para la extensión de marcado.

Tal y como se puede abreviar `x:StaticExtension` a `x:Static` cuando se use como un elemento de objeto, también puede abreviar en la expresión entre llaves:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

El `StaticExtension` clase tiene un `ContentProperty` que hacen referencia a la propiedad de atributo `Member`, que marca esta propiedad como propiedad de contenido de la clase predeterminada. Para las extensiones de marcado XAML expresadas con llaves, puede eliminar el `Member=` forma parte de la expresión:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Esta es la forma más habitual de la `x:Static` extensión de marcado.

El **demostración estático** página contiene dos ejemplos de otros. La etiqueta raíz del archivo XAML contiene una declaración de espacio de nombres XML para .NET `System` espacio de nombres:

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Esto permite la `Label` tamaño de fuente se establezca en el campo estático `Math.PI`. Esto da lugar a texto pequeño en su lugar, por lo que la `Scale` propiedad está establecida en `Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

El último ejemplo se muestra la `Device.RuntimePlatform` valor. El `Environment.NewLine` propiedad estática se utiliza para insertar un carácter de nueva línea entre los dos `Span` objetos:

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Este es el ejemplo que se ejecutan en las tres plataformas:

[![x: Static demostración](consuming-images/staticdemo-small.png "x: Static demostración")](consuming-images/staticdemo-large.png#lightbox "demostración x: Static")

<a name="reference" />

## <a name="xreference-markup-extension"></a>x:Reference (extensión de marcado)

El `x:Reference` extensión de marcado es compatible con la [ `ReferenceExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) clase. La clase tiene una propiedad única denominada [ `Name` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.ReferenceExtension.Name/) de tipo `string` que se establece en el nombre de un elemento en la página que se ha proporcionado un nombre con `x:Name`. Esto `Name` propiedad es la propiedad de contenido de `ReferenceExtension`, por lo que `Name=` no es necesaria cuando `x:Reference` aparece entre llaves.

El `x:Reference` extensión de marcado se utiliza exclusivamente con enlaces de datos, que se describen con más detalle en el artículo [ **enlace de datos**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

El **x: Reference demostración** página muestra dos usos de `x:Reference` con enlaces de datos, la primera que se usa para establecer el `Source` propiedad de la `Binding` objeto y el segundo donde se usa para establecer el `BindingContext` propiedad de enlaces de dos datos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

Ambos `x:Reference` expresiones utilizan la versión abreviada de la `ReferenceExtension` nombre de clase y eliminar el `Name=` forma parte de la expresión. En el primer ejemplo, el `x:Reference` extensión de marcado se incrusta en el `Binding` extensión de marcado. Tenga en cuenta que la `Source` y `StringFormat` configuración está separada por comas. Este es el programa que se ejecuta en las tres plataformas:

[![Demostración de x: Reference](consuming-images/referencedemo-small.png "x: Reference demostración")](consuming-images/referencedemo-large.png#lightbox "x: Reference demostración")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type (Extensión de marcado)

El `x:Type` extensión de marcado es el equivalente XAML de C# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/) palabra clave. Es compatible con la [ `TypeExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/) (clase), que define una propiedad denominada [ `TypeName` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.TypeExtension.TypeName/) de tipo `string` que se establece en un nombre de clase o estructura. El `x:Type` devuelve de la extensión de marcado el [ `System.Type` ](https://developer.xamarin.com/api/type/System.Type/) objeto de esa clase o estructura. `TypeName` es la propiedad de contenido de `TypeExtension`, por lo que `TypeName=` no es necesaria cuando `x:Type` aparece con las llaves.

Dentro de Xamarin.Forms, hay varias propiedades que tienen argumentos de tipo `Type`. Algunos ejemplos son la [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propiedad de `Style`y el [x: TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) atributo utilizado para especificar argumentos en las clases genéricas. Sin embargo, el analizador XAML se realiza el `typeof` operación automáticamente y el `x:Type` extensión de marcado no se usa en estos casos.

Un lugar donde `x:Type` *es* necesario es con el `x:Array` extensión de marcado, que se describe en el [próxima sección](#array).

El `x:Type` extensión de marcado también es útil al construir un menú donde cada elemento de menú corresponde a un objeto de un tipo determinado. Puede asociar un `Type` objeto con cada elemento de menú y, a continuación, crea una instancia del objeto cuando se selecciona el elemento de menú.

Se trata cómo el menú de navegación en `MainPage` en el **las extensiones de marcado** programa works. El **MainPage.xaml** archivo contiene un `TableView` con cada `TextCell` corresponde a una página determinada en el programa:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

Ésta es la página principal de apertura **las extensiones de marcado**:

[![Main página](consuming-images/mainpage-small.png "Main página")](consuming-images/mainpage-large.png#lightbox "Main página")

Cada `CommandParameter` propiedad está establecida en un `x:Type` extensión de marcado que se hace referencia a una de las otras páginas. El `Command` propiedad se enlaza a una propiedad denominada `NavigateCommand`. Esta propiedad se define en el `MainPage` archivo de código subyacente:

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

El `NavigateCommand` propiedad es una `Command` objeto que implementa un comando execute con un argumento de tipo `Type` &mdash; el valor de `CommandParameter`. El método usa `Activator.CreateInstance` para crear instancias de la página y, a continuación, navega a ella. El constructor concluye estableciendo la `BindingContext` de la página a sí misma, lo que permite el `Binding` en `Command` para que funcione. Consulte la [ **enlace de datos** ](~/xamarin-forms/app-fundamentals/data-binding/index.md) artículo y especialmente los [ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) artículo para obtener más detalles sobre este tipo de código.

El **x: Type demostración** página usa una técnica similar para crear instancias de elementos de Xamarin.Forms y agregarlos a un `StackLayout`. El archivo XAML inicialmente consta de tres `Button` elementos con sus `Command` propiedades establecidas en un `Binding` y `CommandParameter` se establecen en los tipos de las tres vistas de Xamarin.Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente define e inicializa la `CreateCommand` propiedad:

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

El método que se ejecuta cuando un `Button` se presiona crea una nueva instancia del argumento, se establece su `VerticalOptions` propiedad y lo agrega a la `StackLayout`. Los tres `Button` elementos, a continuación, compartan la página con vistas creadas de forma dinámica:

[![x: Type demostración](consuming-images/typedemo-small.png "x: Type demostración")](consuming-images/typedemo-large.png#lightbox "demostración x: Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array (Extensión de marcado)

El `x:Array` extensión de marcado permite definir una matriz en el marcado. Es compatible con la [ `ArrayExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/) (clase), que define dos propiedades:

- `Type` de tipo `Type`, lo que indica el tipo de los elementos de la matriz.
- `Items` de tipo `IList`, que es una colección de los mismos elementos. Se trata de la propiedad de contenido de `ArrayExtension`.

El `x:Array` propia extensión de marcado nunca aparece entre llaves. En su lugar, `x:Array` etiquetas de inicio y finalización delimitan la lista de elementos. Establecer el `Type` propiedad a un `x:Type` extensión de marcado.

El **x: Array demostración** página muestra cómo usar `x:Array` para agregar elementos a un `ListView` estableciendo el `ItemsSource` propiedad en una matriz:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

El `ViewCell` crea un sencillo `BoxView` para cada entrada de color:

[![Demostración de x: Array](consuming-images/arraydemo-small.png "x: Array demostración")](consuming-images/arraydemo-large.png#lightbox "x: Array demostración")

Hay varias maneras de especificar el individuo `Color` elementos de esta matriz. Puede usar un `x:Static` extensión de marcado:

```xaml
<x:Static Member="Color.Blue" />
```

O bien, puede usar `StaticResource` para recuperar un color de un diccionario de recursos:

```xaml
<StaticResource Key="myColor" />
```

Hacia el final de este artículo, verá una extensión de marcado XAML personalizada que también crea un nuevo valor de color:

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

Al definir matrices de tipos comunes como cadenas o números, use las etiquetas que aparecen en la [ **pasar argumentos de Constructor** ](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) artículo para delimitar los valores.

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null (Extensión de marcado)

El `x:Null` extensión de marcado es compatible con la [ `NullExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/) clase. No tiene ninguna propiedad y simplemente el equivalente XAML de C# [ `null` ](/dotnet/csharp/language-reference/keywords/null/) palabra clave.

El `x:Null` extensión de marcado es rara vez se necesita y se usan en contadas ocasiones, pero si lo encuentra lo necesita, podrá muy contento de que existe.

El **x: Null demostración** página ilustra un escenario cuando `x:Null` podría ser conveniente. Supongamos que define un modo implícito `Style` para `Label` que incluye un `Setter` que establece el `FontFamily` propiedad a un nombre de familia depende de la plataforma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

A continuación, verá que para uno de los `Label` elementos, desea que todos los valores de propiedades en la parte implícita `Style` excepto para el `FontFamily`, que desea que el valor predeterminado. Puede definir otra `Style` para ese fin, pero un enfoque más sencillo consiste simplemente en establecer el `FontFamily` propiedad de ese `Label` a `x:Null`, como se muestra en el centro de `Label`.

Este es el programa que se ejecuta en las tres plataformas:

[![x: Null demostración](consuming-images/nulldemo-small.png "x: Null demostración")](consuming-images/nulldemo-large.png#lightbox "demostración x: Null")

Observe que cuatro de las `Label` elementos tienen una fuente serif, pero el centro de `Label` tiene la fuente sans-serif de forma predeterminada.

## <a name="define-your-own-markup-extensions"></a>Definir sus propias extensiones de marcado

Si se ha encontrado una necesidad de una extensión de marcado XAML que no está disponible en Xamarin.Forms, puede [crear los suyos propios](creating.md).


## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capítulo de extensiones de marcado XAML de libreta de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
