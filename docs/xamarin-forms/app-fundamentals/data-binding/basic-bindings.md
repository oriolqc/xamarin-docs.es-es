---
title: Enlaces de Xamarin.Forms Basic
description: En este artículo se explica cómo usar Xamarin.Forms enlace de datos, lo que vincula un par de propiedades entre dos objetos, al menos uno de los cuales suele ser un objeto de interfaz de usuario. Estos dos objetos se denominan el destino y el origen.
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 16d1970b5e9d8f9c2b7c8be875c81136525c4fb7
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998077"
---
# <a name="xamarinforms-basic-bindings"></a>Enlaces de Xamarin.Forms Basic

Un enlace de datos de Xamarin.Forms vincula un par de propiedades entre dos objetos, al menos uno de los cuales suele ser un objeto de interfaz de usuario. Estos dos objetos se denominan el *destino* y *origen*:

- El *destino* es el objeto (y la propiedad) en la que se establece el enlace de datos.
- El *origen* es el objeto (y la propiedad) al que hace referencia el enlace de datos.

Esta distinción a veces puede ser un poco confusa: en el caso más simple, los datos fluyen desde el origen al destino, lo que significa que el valor de la propiedad de destino se establece en el valor de la propiedad de origen. Sin embargo, en algunos casos, pueden también flujo de datos desde el destino en el origen o en ambas direcciones. Para evitar confusiones, tenga en cuenta que el destino es siempre el objeto en el que se establece el enlace de datos, incluso si está ofreciendo datos en lugar de recibir datos.

## <a name="bindings-with-a-binding-context"></a>Enlaces con un contexto de enlace

Aunque normalmente se especifican los enlaces de datos completamente en XAML, es instructivo para ver los enlaces de datos en el código. El **enlace básico de código** página contiene un archivo XAML con un `Label` y un `Slider`:

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

El `Slider` está establecido para un intervalo de 0 a 360. El propósito de este programa es se va a girar el `Label` manipulando el `Slider`.

Sin enlaces de datos, establecería el `ValueChanged` eventos de la `Slider` a un controlador de eventos que tiene acceso a la `Value` propiedad de la `Slider` y establece ese valor en el `Rotation` propiedad de la `Label`. El enlace de datos automatiza ese trabajo; el controlador de eventos y el código dentro de ella ya no son necesarios.

Puede establecer un enlace en una instancia de cualquier clase que derive de [ `BindableObject` ](xref:Xamarin.Forms.BindableObject), que incluye `Element`, `VisualElement`, `View`, y `View` derivados.  El enlace siempre se establece en el objeto de destino. El enlace hace referencia al objeto de origen. Para establecer el enlace de datos, use los siguientes dos miembros de la clase de destino:

- El [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propiedad especifica el objeto de origen.
- El [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método especifica la propiedad de destino y la propiedad de origen.

En este ejemplo, el `Label` es el destino de enlace y el `Slider` es el origen de enlace. Los cambios en el `Slider` origen afectan a la rotación de la `Label` destino. Los datos fluyen desde el origen al destino.

El `SetBinding` definido por el método `BindableObject` tiene un argumento de tipo [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) desde el que el [ `Binding` ](xref:Xamarin.Forms.Binding) se deriva de la clase, pero existen otras `SetBinding` métodos definido por el [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions) clase. El archivo de código subyacente en el **enlace básico de código** sea más sencillo ejemplo utiliza [ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) método de extensión de esta clase.

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

La `Label` objeto es el destino de enlace, por lo que es el objeto en que se establece esta propiedad y en la que se llama al método. El `BindingContext` propiedad indica el origen de enlace, que es el `Slider`.

El `SetBinding` método se llama en el destino de enlace, pero especifica la propiedad de destino y la propiedad de origen. La propiedad de destino se especifica como un `BindableProperty` objeto: `Label.RotationProperty`. La propiedad de origen se especifica como una cadena e indica la `Value` propiedad de `Slider`.

El `SetBinding` método revela una de las reglas de enlaces de datos más importantes:

*La propiedad de destino debe estar respaldada por una propiedad enlazable.*

Esta regla implica que el objeto de destino debe ser una instancia de una clase que deriva de `BindableObject`. Consulte la [ **propiedades enlazables** ](~/xamarin-forms/xaml/bindable-properties.md) artículo para obtener información general de objetos enlazables y propiedades enlazables.

No hay ninguna regla de este tipo para la propiedad de origen, que se especifica como una cadena. Internamente, se usa la reflexión para tener acceso a la propiedad real. En este caso concreto, sin embargo, el `Value` propiedad también está respaldada por una propiedad enlazable.

El código puede ser un poco simplificado: el `RotationProperty` propiedad enlazable se define mediante `VisualElement`y heredadas por `Label` y `ContentPage` así, por lo que no es necesario el nombre de clase en el `SetBinding` llamar a:

```csharp
label.SetBinding(RotationProperty, "Value");
```

Sin embargo, incluido el nombre de clase es un buen recordatorio del objeto de destino.

Al manipular el `Slider`, el `Label` gira según corresponda:

[![Código Basice enlace](basic-bindings-images/basiccodebinding-small.png "enlace código básico")](basic-bindings-images/basiccodebinding-large.png#lightbox "enlace código básico")

El **enlace Xaml básico** es idéntica a la página **enlace código básico** excepto que define el enlace de datos completo en XAML:

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

Al igual que en código, el enlace de datos se establece en el objeto de destino, que es el `Label`. Intervienen dos extensiones de marcado XAML. Estos son reconocibles al instante por los delimitadores de la llave de cierre:

- El `x:Reference` extensión de marcado es necesaria para hacer referencia al objeto de origen, que es el `Slider` denominado `slider`.
- El `Binding` vínculos de la extensión de marcado el `Rotation` propiedad de la `Label` a la `Value` propiedad de la `Slider`.

Consulte el artículo [las extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md) para obtener más información acerca de las extensiones de marcado XAML. El `x:Reference` extensión de marcado es compatible con la [ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension) clase; `Binding` es compatible con la [ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension) clase. Como el código XML se indican los prefijos de espacio de nombres, `x:Reference` forma parte de la especificación de XAML 2009, mientras que `Binding` forma parte de Xamarin.Forms. Observe que no aparecen comillas encerrado entre llaves.

Es fácil olvidar el `x:Reference` extensión de marcado cuando se establece la `BindingContext`. Es habitual erróneamente establezca la propiedad directamente en el nombre del origen de enlace similar al siguiente:

```xaml
BindingContext="slider"
```

Pero eso no es correcto. Ese marcado establece el `BindingContext` propiedad a un `string` objeto cuyos caracteres deletreen "slider"!

Tenga en cuenta que la propiedad de origen se especifica con el [ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path) propiedad de `BindingExtension`, que se corresponde con el [ `Path` ](xref:Xamarin.Forms.Binding.Path) propiedad de la [ `Binding` ](xref:Xamarin.Forms.Binding) clase.

El marcado que se muestra en el **enlace de XAML básica** se puede simplificar la página: las extensiones de marcado XAML como `x:Reference` y `Binding` puede tener *propiedad de contenido* definido de atributos, lo que para XAML las extensiones de marcado significa que el nombre de propiedad no es necesario que aparezcan. El `Name` es la propiedad del contenido `x:Reference`y el `Path` propiedad es la propiedad content de `Binding`, lo que significa que se pueden eliminar de las expresiones:

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Enlaces sin un contexto de enlace

El `BindingContext` propiedad es un componente importante de los enlaces de datos, pero no siempre es necesario. El objeto de origen en su lugar, se puede especificar en el `SetBinding` llamar o el `Binding` extensión de marcado.

Esto se muestra en el **enlace de código alternativa** ejemplo. El archivo XAML es similar a la **enlace básico de código** de ejemplo, salvo que el `Slider` se define al control de la `Scale` propiedad de la `Label`. Por ese motivo, la `Slider` está establecido para un intervalo de &ndash;2 a 2:

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

El archivo de código subyacente establece el enlace con el [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) definido por el método `BindableObject`. El argumento es un [constructor](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) para el [ `Binding` ](xref:Xamarin.Forms.Binding) clase:

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

Ejecutar este programa podría ser un poco sorprendente:

[![Enlaces de código alternativa](basic-bindings-images/alternativecodebinding-small.png "enlace código alternativa")](basic-bindings-images/alternativecodebinding-large.png#lightbox "enlace código alternativa")

La pantalla de iOS de la izquierda muestra el aspecto de la pantalla cuando la página aparece por primera vez. ¿Donde es el `Label`?

El problema es que el `Slider` tiene un valor inicial de 0. Esto hace que el `Scale` propiedad de la `Label` también se establezca en 0, reemplazar su valor predeterminado de 1. Esto da como resultado la `Label` siendo invisible inicialmente. Como demuestran las capturas de pantalla de Android y plataforma Universal de Windows (UWP), se puede manipular el `Slider` para realizar la `Label` aparezca de nuevo, pero su desaparición inicial es desconcertante.

Descubrirá en el [siguiente artículo](binding-mode.md) cómo evitar este problema, inicialice la `Slider` desde el valor predeterminado de la `Scale` propiedad.

El **alternativa XAML enlace** página muestra el enlace equivalente completamente en XAML:

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

El `Source` propiedad está establecida a incrustado `x:Reference` extensión de marcado que en caso contrario, tiene la misma sintaxis que la configuración de la `BindingContext`. Observe que no aparecen comillas dentro de las llaves de cierre, y que las dos propiedades deben estar separadas por punto y coma.

La propiedad de contenido de la `Binding` extensión de marcado es `Path`, pero la `Path=` solo se puede eliminar parte de la extensión de marcado si es la primera propiedad en la expresión. Para eliminar el `Path=` parte, es necesario intercambiar las dos propiedades:

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Aunque las extensiones de marcado XAML normalmente están delimitadas por llaves, también puede expresar como elementos de objeto:

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

Ahora el `Source` y `Path` propiedades son atributos normales de XAML: los valores que aparecen entre comillas y los atributos no están separados por punto y coma. El `x:Reference` extensión de marcado también puede convertirse en un elemento de objeto:

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

Establecen los ejemplos mostrados hasta ahora la `BindingContext` propiedad y el `Source` propiedad de `Binding` a un `x:Reference` markup extension para hacer referencia a otra vista en la página. Estas dos propiedades son de tipo `Object`, y se pueden establecer en cualquier objeto que incluye las propiedades que son adecuadas para orígenes de enlace.

En los artículos con antelación, descubrirá que puede establecer el `BindingContext` o `Source` propiedad a un `x:Static` markup extension para hacer referencia al valor de una propiedad estática o un campo, o un `StaticResource` markup extension para hacer referencia a un objeto almacenado en un diccionario de recursos, o directamente a un objeto, que suele ser (pero no siempre) una instancia de una clase ViewModel.

El `BindingContext` propiedad puede establecerse en un `Binding` objeto para que el `Source` y `Path` propiedades de `Binding` definir el contexto de enlace.

## <a name="binding-context-inheritance"></a>Herencia de contexto de enlace

En este artículo, ha visto que puede especificar el objeto de origen mediante el `BindingContext` propiedad o el `Source` propiedad de la `Binding` objeto. Si ambas están establecidas, el `Source` propiedad de la `Binding` tiene prioridad sobre la `BindingContext`.

El `BindingContext` propiedad tiene una característica muy importante:

*La configuración de la `BindingContext` propiedad se hereda a través del árbol visual.*

Como verá, esto puede ser muy útil para simplificar las expresiones de enlace y, en algunos casos &mdash; especialmente en escenarios de Model-View-ViewModel (MVVM) &mdash; es esencial.

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

El `BindingContext` propiedad de la `StackLayout` está establecido en el `slider` objeto. Ambos heredan este contexto de enlace el `Label` y `BoxView`, tanto de que tiene sus `Rotation` propiedades establecidas en el `Value` propiedad de la `Slider`:

[![Herencia de contexto de enlace](basic-bindings-images/bindingcontextinheritance-small.png "herencia de contexto de enlace")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "herencia de contexto de enlace")

En el [siguiente artículo](binding-mode.md), verá cómo la *modo de enlace* puede cambiar el flujo de datos entre los objetos de origen y destino.


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
