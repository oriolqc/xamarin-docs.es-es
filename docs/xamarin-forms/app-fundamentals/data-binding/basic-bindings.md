---
title: Enlaces básicos
description: El contexto de enlace, orígenes y destinos de enlace de datos
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 065258acacb8469b90dd2ca04286f0686dc90063
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="basic-bindings"></a>Enlaces básicos

Un enlace de datos de Xamarin.Forms vincula un par de propiedades entre dos objetos, al menos uno de los cuales es normalmente un objeto de interfaz de usuario. Estos dos objetos se denominan el *destino* y *origen*:

- El *destino* es el objeto (y la propiedad) en la que se establece el enlace de datos.
- El *origen* es el objeto (y la propiedad) al que hace referencia el enlace de datos.

En ocasiones puede resultar un poco confusa esta distinción: en el caso más simple, los datos fluyen desde el origen al destino, lo que significa que el valor de la propiedad de destino se establece el valor de la propiedad de origen. Sin embargo, en algunos casos, los datos también pueden fluir desde el destino al origen, o en ambas direcciones. Para evitar confusiones, tenga en cuenta que el destino siempre es el objeto en el que se establece el enlace de datos, incluso si está proporcionando los datos en lugar de recibir datos.

## <a name="bindings-with-a-binding-context"></a>Enlaces con un contexto de enlace

Aunque los enlaces de datos normalmente se especifica completamente en XAML, es instructivo para ver los enlaces de datos en el código. El **el enlace básico de código** página contiene un archivo XAML con un `Label` y `Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicCodeBindingPage"
             Title="Basic Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="48"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El `Slider` se establece para un intervalo de 0 a 360. El propósito de este programa es girar el `Label` manipulando el `Slider`.

Sin enlaces de datos, debe establecer el `ValueChanged` eventos de la `Slider` a un controlador de eventos que tiene acceso a la `Value` propiedad de la `Slider` y establece ese valor la `Rotation` propiedad de la `Label`. El enlace de datos automatiza ese trabajo; el controlador de eventos y el código en él ya no son necesarios. 

Puede establecer un enlace en una instancia de cualquier clase que deriva de [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/), que incluye `Element`, `VisualElement`, `View`, y `View` derivados.  El enlace siempre se establece en el objeto de destino. El enlace hace referencia al objeto de origen. Para establecer el enlace de datos, utilice a los siguientes dos miembros de la clase de destino:

- El [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) propiedad especifica el objeto de origen.
- El [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) método especifica las propiedades de destino y origen.

En este ejemplo, el `Label` es el destino de enlace y `Slider` es el origen de enlace. Cambios en el `Slider` origen afectan a la rotación de la `Label` destino. Los datos fluyen desde el origen al destino.

El `SetBinding` método definido por `BindableObject` tiene un argumento de tipo [ `BindingBase` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingBase/) desde el que el [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/) se deriva de la clase, pero hay otras `SetBinding` métodos definido por el [ `BindableObjectExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObjectExtensions/) clase. El archivo de código subyacente en el **el enlace básico de código** ejemplo usa una [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObjectExtensions.SetBinding/p/Xamarin.Forms.BindableObject/Xamarin.Forms.BindableProperty/System.String/) método de extensión de esta clase.

```csharp
public partial class BasicCodeBindingPage : ContentPage
{
    public BasicCodeBindingPage()
    {
        InitializeComponent();

        label.BindingContext = slider;
        label.SetBinding(Label.RotationProperty, "Value");
    }
}
```

La `Label` objeto es el destino de enlace, por lo que es el objeto en la que se establece esta propiedad y en el que se llama al método. El `BindingContext` propiedad indica el origen de enlace, que es el `Slider`.

El `SetBinding` método se llama en el destino de enlace, pero especifica la propiedad de destino y la propiedad de origen. La propiedad de destino se especifica como un `BindableProperty` objeto: `Label.RotationProperty`. La propiedad de origen se especifica como una cadena e indica la `Value` propiedad de `Slider`. 

El `SetBinding` método revela una de las reglas más importantes de los enlaces de datos:

*La propiedad de destino debe estar respaldada por una propiedad enlazable.*

Esta regla implica que el objeto de destino debe ser una instancia de una clase que deriva de `BindableObject`. Consulte la [ **propiedades enlazables** ](~/xamarin-forms/xaml/bindable-properties.md) artículo para obtener información general de objetos enlazables y propiedades enlazables.

No hay ninguna regla de este tipo para la propiedad de origen, que se especifica como una cadena. Internamente, se usa la reflexión para tener acceso a la propiedad real. En este caso concreto, sin embargo, la `Value` propiedad también está respaldada por una propiedad enlazable.

El código puede ser un poco simplificado: el `RotationProperty` propiedad enlazable se define mediante `VisualElement`y es heredada por `Label` y `ContentPage` así, por lo que no es necesario el nombre de clase en el `SetBinding` llamar a:

```csharp
label.SetBinding(RotationProperty, "Value");
```

Sin embargo, incluido el nombre de clase es un buena recordatorio del objeto de destino.

Mientras se manipulan los `Slider`, el `Label` gira en consecuencia:

[![Código de Basice enlace](basic-bindings-images/basiccodebinding-small.png "enlace código básico")](basic-bindings-images/basiccodebinding-large.png#lightbox "enlace código básico")

El **enlace Xaml básico** es idéntica a la página **enlace código básico** salvo que define el enlace de datos completo en XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicXamlBindingPage"
             Title="Basic XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Al igual que en código, se establece el enlace de datos en el objeto de destino, que es el `Label`. Intervienen dos extensiones de marcado XAML. Estos son al instante reconocibles por los delimitadores de llave de apertura:

- El `x:Reference` extensión de marcado es necesaria para hacer referencia al objeto de origen, que es el `Slider` denominado `slider`.
- El `Binding` vínculos de extensión de marcado el `Rotation` propiedad de la `Label` a la `Value` propiedad de la `Slider`. 

Vea el artículo [las extensiones de marcado de XAML](~/xamarin-forms/xaml/markup-extensions/index.md) para obtener más información acerca de las extensiones de marcado XAML. El `x:Reference` extensión de marcado es compatible con la [ `ReferenceExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) clase; `Binding` es compatible con la [ `BindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) clase. Como el código XML se indican los prefijos de espacio de nombres, `x:Reference` forma parte de la especificación de XAML 2009, mientras que `Binding` forma parte de Xamarin.Forms. Tenga en cuenta que no aparece ninguna marca de comillas entre las llaves.

Es fácil olvidarse el `x:Reference` extensión de marcado cuando se establece la `BindingContext`. Es común a erróneamente establezca la propiedad directamente en el nombre del origen del enlace similar al siguiente:

```xaml
BindingContext="slider"
```

Pero eso no es correcto. Establece ese marcado el `BindingContext` propiedad a un `string` objeto cuyos caracteres deletreen "slider".

Tenga en cuenta que la propiedad de origen se especifica con el [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Path/) propiedad de `BindingExtension`, que se corresponde con el [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) propiedad de la [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/) clase.

El marcado que se muestra en el **enlace de XAML básico** se puede simplificar la página: las extensiones de marcado XAML como `x:Reference` y `Binding` puede tener *propiedad de contenido* definido de atributos, que para XAML las extensiones de marcado significa que el nombre de propiedad no tiene que aparecen. El `Name` propiedad es la propiedad de contenido de `x:Reference`y el `Path` propiedad es la propiedad de contenido de `Binding`, lo que significa que se pueden eliminar de las expresiones:

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Enlaces sin un contexto de enlace

El `BindingContext` propiedad es un componente importante de los enlaces de datos, pero no siempre es necesario. En su lugar, se puede especificar el objeto de origen en el `SetBinding` llamar o `Binding` extensión de marcado.

Esto se muestra en el **alternativa código enlace** ejemplo. El archivo XAML es similar a la **el enlace básico de código** ejemplo salvo que la `Slider` se define para controlar la `Scale` propiedad de la `Label`. Por esta razón, el `Slider` está establecido para un intervalo de &ndash;2 a 2:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeCodeBindingPage"
             Title="Alternative Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente establece el enlace con el [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) método definido por `BindableObject`. El argumento es un [constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Binding.Binding/p/System.String/Xamarin.Forms.BindingMode/Xamarin.Forms.IValueConverter/System.Object/System.String/System.Object/) para el [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/) clase:

```csharp
public partial class AlternativeCodeBindingPage : ContentPage
{
    public AlternativeCodeBindingPage()
    {
        InitializeComponent();

        label.SetBinding(Label.ScaleProperty, new Binding("Value", source: slider));
    }
}
```

El `Binding` constructor tiene 6 parámetros, por lo que el `source` parámetro se especifica con un argumento con nombre. El argumento es el `slider` objeto. 

Ejecutar este programa puede resultar un poco sorprendente:

[![Código alternativo enlace](basic-bindings-images/alternativecodebinding-small.png "enlace código alternativo")](basic-bindings-images/alternativecodebinding-large.png#lightbox "enlace código alternativo")

La pantalla de iOS de la izquierda muestra el aspecto de la pantalla cuando la página aparece por primera vez. ¿Donde es el `Label`? 

El problema es que el `Slider` tiene un valor inicial de 0. Esto hace que la `Scale` propiedad de la `Label` también se establezca en 0, reemplazar el valor predeterminado de 1. Esto da como resultado la `Label` siendo invisible inicialmente. Como demuestran las capturas de pantalla de plataforma Universal de Windows (UWP) y Android, puede manipular el `Slider` para realizar la `Label` aparecer de nuevo, pero su desaparición inicial es desconcertante.

Descubrirá que en el [artículo siguiente](binding-mode.md) cómo evitar este problema mediante la inicialización de la `Slider` desde el valor predeterminado de la `Scale` propiedad.

El **enlace XAML de alternativa** página muestra el enlace equivalente completamente en XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeXamlBindingPage"
             Title="Alternative XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="{Binding Source={x:Reference slider},
                               Path=Value}" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Ahora el `Binding` extensión de marcado tiene dos propiedades establecido, `Source` y `Path`, separados por punto y coma. Si lo prefiere pueden aparecer en la misma línea:

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

El `Source` propiedad está establecida en un embedded `x:Reference` extensión de marcado que de lo contrario, tiene la misma sintaxis que la configuración de la `BindingContext`. Observe que no aparece ninguna marca de comillas entre las llaves, y que las dos propiedades deben estar separadas por una coma.

La propiedad de contenido de la `Binding` extensión de marcado es `Path`, pero la `Path=` parte de la extensión de marcado solo se puede eliminar si es la primera propiedad en la expresión. Para eliminar la `Path=` parte, es necesario intercambiar las dos propiedades:

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Aunque las extensiones de marcado XAML normalmente están delimitadas por llaves, también puede expresarse como elementos de objeto:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Source="{x:Reference slider}"
                 Path="Value" />
    </Label.Scale>
</Label>
``` 

Ahora el `Source` y `Path` propiedades son atributos normales de XAML: los valores aparecen entre comillas y los atributos no están separados por una coma. El `x:Reference` extensión de marcado también puede convertirse en un elemento de objeto:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Path="Value">
            <Binding.Source>
                <x:Reference Name="slider" />
            </Binding.Source>
        </Binding>
    </Label.Scale>
</Label>
```

Esta sintaxis no es común, pero a veces es necesario cuando se trata de objetos complejos.

Establecen los ejemplos mostrados hasta ahora la `BindingContext` propiedad y el `Source` propiedad de `Binding` a una `x:Reference` extensión de marcado para hacer referencia a otra vista en la página. Estas dos propiedades son de tipo `Object`, y se pueden establecer en cualquier objeto que incluye propiedades que son adecuados para orígenes de enlace. 

En los artículos con antelación, descubrirá que puede establecer el `BindingContext` o `Source` propiedad a una `x:Static` extensión de marcado para hacer referencia al valor de una propiedad estática o un campo, o un `StaticResource` extensión de marcado para hacer referencia a un objeto almacenado en un diccionario de recursos, o directamente a un objeto, que suele ser (pero no siempre) una instancia de un modelo de vista. 

El `BindingContext` propiedad también puede establecerse en un `Binding` objeto para que la `Source` y `Path` propiedades de `Binding` definir el contexto de enlace.

## <a name="binding-context-inheritance"></a>Herencia de contexto de enlace

En este artículo, ha visto que puede especificar el objeto de origen mediante el `BindingContext` propiedad o el `Source` propiedad de la `Binding` objeto. Si ambas están establecidas, el `Source` propiedad de la `Binding` tiene prioridad sobre la `BindingContext`.

El `BindingContext` propiedad tiene una característica muy importante:

*La configuración de la `BindingContext` propiedad se hereda a través del árbol visual.*

Como verá, esto puede resultar muy útil para simplificar las expresiones de enlace y en algunos casos &mdash; especialmente en escenarios de Model-View-ViewModel (MVVM) &mdash; es fundamental.

El **herencia de contexto de enlace** ejemplo es una simple demostración de la herencia de contexto de enlace:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BindingContextInheritancePage"
             Title="BindingContext Inheritance">
    <StackLayout Padding="10">

        <StackLayout VerticalOptions="FillAndExpand"
                     BindingContext="{x:Reference slider}">
            
            <Label Text="TEXT"
                   FontSize="80"
                   HorizontalOptions="Center"
                   VerticalOptions="EndAndExpand"
                   Rotation="{Binding Value}" />

            <BoxView Color="#800000FF"
                     WidthRequest="180"
                     HeightRequest="40"
                     HorizontalOptions="Center"
                     VerticalOptions="StartAndExpand"
                     Rotation="{Binding Value}" />
        </StackLayout>

        <Slider x:Name="slider" 
                Maximum="360" />
        
    </StackLayout>
</ContentPage>
```

El `BindingContext` propiedad de la `StackLayout` está establecido en el `slider` objeto. Este contexto de enlace es heredada por ambos el `Label` y `BoxView`, ambos de que tienen sus `Rotation` propiedades establecidas en el `Value` propiedad de la `Slider`: 

[![Herencia de contexto de enlace](basic-bindings-images/bindingcontextinheritance-small.png "herencia de contexto de enlace")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "herencia de contexto de enlace")

En el [artículo siguiente](binding-mode.md), verá cómo la *modo de enlace* puede cambiar el flujo de datos entre los objetos de origen y de destino.


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos de la libreta de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
