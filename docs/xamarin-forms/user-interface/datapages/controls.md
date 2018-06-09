---
title: Referencia de controles DataPages
description: Este artículo detallan los controles que están disponibles en el paquete DataPages NuGet Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: c907d55f09d334e167c831a19f9d0edc4c97732f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243134"
---
# <a name="datapages-controls-reference"></a>Referencia de controles DataPages

![](~/media/shared/preview.png "Esta API está actualmente en vista previa")

> [!IMPORTANT]
> DataPages requiere un [Xamarin.Forms tema](~/xamarin-forms/user-interface/themes/index.md) referencia a presentar.


Xamarin.Forms DataPages Nuget incluye una serie de controles que puede aprovechar las ventajas de enlace de origen de datos.

Para utilizar estos controles en XAML, asegúrese de que se ha incluido el espacio de nombres, consulte por ejemplo el `xmlns:pages` declaración siguiente:

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

Los ejemplos siguientes incluyen `DynamicResource` referencias que se tienen que existir en el diccionario de recursos del proyecto para que funcione. También hay un ejemplo de cómo crear un [control personalizado](#custom)

## <a name="built-in-controls"></a>Controles integrados

* [HeroImage](#heroimage)
* [ListItem](#listitem)

<a name="heroimage" />

### <a name="heroimage"></a>HeroImage

El `HeroImage` control tiene cuatro propiedades:

* Texto
* Detalles
* ImageSource.
* Aspecto

```xaml
<pages:HeroImage
    ImageSource="{ DynamicResource HeroImageImage }"
    Text="Keith Ballinger"
    Detail="Xamarin"
/>
```

**Android**

![](controls-images/heroimage-light-android.png "Control de HeroImage en Android") ![ ] (controls-images/heroimage-dark-android.png "Control HeroImage en Android")

**iOS**

![](controls-images/heroimage-light-ios.png "HeroImage Control en iOS") ![ ] (controls-images/heroimage-dark-ios.png "HeroImage Control en iOS")


<a name="listitem" />

### <a name="listitem"></a>ListItem

El `ListItem` diseño del control es similar a nativas para iOS y Android lista o tabla filas, sin embargo también se puede utilizar como una vista normal. En el ejemplo de código por debajo de él se muestra hospedado en un `StackLayout`, pero también se puede utilizar en los controles de lista enlazados a datos scolling.

Hay cinco propiedades:

* Title
* Detalles
* ImageSource.
* PlaceholdImageSource
* Aspecto

```xaml
<StackLayout Spacing="0">
    <pages:ListItemControl
        Detail="Xamarin"
        ImageSource="{ DynamicResource UserImage }"
        Title="Miguel de Icaza"
        PlaceholdImageSource="{ DynamicResource IconImage }"
    />
```

Estas capturas de pantalla muestran la `ListItem` en plataformas iOS y Android usando tanto el claro y oscuro temas:

**Android**

![](controls-images/listitem-light-android.png "Control ListItem en Android") ![ ] (controls-images/listitem-dark-android.png "Control ListItem en Android")

**iOS**

![](controls-images/listitem-light-ios.png "ListItem Control en iOS") ![ ] (controls-images/listitem-dark-ios.png "ListItem Control en iOS")


## <a name="custom-control-example"></a>Ejemplo de Control personalizado

El objetivo de esta personalización `CardView` control es similar a la CardView Android nativo.

Contendrá tres propiedades:

* Texto
* Detalles
* ImageSource.

El objetivo es un control personalizado que tendrá un aspecto similar al código siguiente (tenga en cuenta que un personalizado `xmlns:local` es necesario que haga referencia al ensamblado actual):

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

Debería ser similar a las capturas de pantalla siguientes a través de colores correspondiente a los temas claro y oscuro integrados:

**Android**

![](controls-images/cardview-light-android.png "Control personalizado de CardView en Android") ![ ] (controls-images/cardview-dark-android.png "Control CardView personalizado en Android")

**iOS**

![](controls-images/cardview-light-ios.png "Control personalizado de CardView en iOS") ![ ] (controls-images/cardview-dark-ios.png "CardView Custom Control en iOS")

<a name="custom" />

### <a name="building-the-custom-cardview"></a>Compilar el CardView personalizado

1. [Subclase de DataView](#1)
2. [Definir la fuente, el diseño y los márgenes](#2)
3. [Cree los estilos para los elementos secundarios del Control](#3)
4. [Crear la plantilla de diseño del Control](#4)
5. [Agregar los recursos específicos de tema](#5)
6. [Establezca el elemento ControlTemplate para la clase CardView](#6)
7. [Agregar el Control a una página](#7)

<a name="1" />

#### <a name="1-dataview-subclass"></a>1. Subclase de DataView

La subclase de C# de `DataView` define las propiedades enlazables para el control.

```csharp
public class CardView : DataView
{
    public static readonly BindableProperty TextProperty =
        BindableProperty.Create ("Text", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Text
    {
        get { return (string)GetValue (TextProperty); }
        set { SetValue (TextProperty, value); }
    }

    public static readonly BindableProperty DetailProperty =
        BindableProperty.Create ("Detail", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Detail
    {
        get { return (string)GetValue (DetailProperty); }
        set { SetValue (DetailProperty, value); }
    }

    public static readonly BindableProperty ImageSourceProperty =
        BindableProperty.Create ("ImageSource", typeof (ImageSource), typeof (CardView), null, BindingMode.TwoWay);

    public ImageSource ImageSource
    {
        get { return (ImageSource)GetValue (ImageSourceProperty); }
        set { SetValue (ImageSourceProperty, value); }
    }

    public CardView()
    {
    }
}
```

<a name="2" />

#### <a name="2-define-font-layout-and-margins"></a>2. Definir la fuente, el diseño y los márgenes

El Diseñador de control podría averiguar estos valores como parte del diseño de interfaz de usuario para el control personalizado. Cuando las especificaciones de específico de la plataforma son necesarias, el `OnPlatform` se utiliza el elemento.

Tenga en cuenta que algunos valores hacen referencia a `StaticResource`s: estos se definirán en [paso 5](#5).

```xml
<!-- CARDVIEW FONT SIZES -->
<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewTextFontSize">
        <On Platform="iOS, Android" Value="15" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewDetailFontSize">
        <On Platform="iOS, Android" Value="13" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewTextTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewTextTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewTextTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewTextlMargin">
        <On Platform="iOS" Value="12,10,12,4" />
        <On Platform="Android" Value="20,0,20,5" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewDetailTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewDetailTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewDetailTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewDetailMargin">
        <On Platform="iOS" Value="12,0,10,12" />
        <On Platform="Android" Value="20,0,20,20" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewBackgroundColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewBackgroundColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewBackgroundColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewShadowSize">
        <On Platform="iOS" Value="2" />
        <On Platform="Android" Value="5" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewCornerRadius">
        <On Platform="iOS" Value="0" />
        <On Platform="Android" Value="4" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewShadowColor">
        <On Platform="iOS, Android" Value="#CDCDD1" />
</OnPlatform>
```

<a name="3" />

#### <a name="3-create-styles-for-the-controls-children"></a>3. Cree los estilos para los elementos secundarios del Control

Hacer referencia a todos los elementos definidos a crear a los elementos secundarios que se usará en el control personalizado:

```xml
<!-- EXPLICIT STYLES (will be Classes) -->
<Style TargetType="Label" x:Key="CardViewTextStyle">
    <Setter Property="FontSize" Value="{ StaticResource CardViewTextFontSize }" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewTextTextColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewTextlMargin }" />
    <Setter Property="HorizontalTextAlignment" Value="Start" />
</Style>

<Style TargetType="Label" x:Key="CardViewDetailStyle">
    <Setter Property="HorizontalTextAlignment" Value="Start" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewDetailTextColor }" />
    <Setter Property="FontSize" Value="{ StaticResource CardViewDetailFontSize }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewDetailMargin }" />
</Style>

<Style TargetType="Image" x:Key="CardViewImageImageStyle">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="Center" />
    <Setter Property="WidthRequest" Value="220"/>
    <Setter Property="HeightRequest" Value="165"/>
</Style>
```

<a name="4" />

#### <a name="4-create-the-control-layout-template"></a>4. Crear la plantilla de diseño del Control

El diseño visual del control personalizado se declara explícitamente en la plantilla de control, con los recursos definidos anteriormente:

```xml
<!--- CARDVIEW -->
<ControlTemplate x:Key="CardViewControlControlTemplate">
  <StackLayout
    Spacing="0"
    BackgroundColor="{ TemplateBinding BackgroundColor }"
  >

    <!-- CARDVIEW IMAGE -->
    <Image
      Source="{ TemplateBinding ImageSource }"
      HorizontalOptions="FillAndExpand"
      VerticalOptions="StartAndExpand"
      Aspect="AspectFill"
      Style="{ StaticResource CardViewImageImageStyle }"
    />

    <!-- CARDVIEW TEXT -->
    <Label
      Text="{ TemplateBinding Text }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewTextStyle }"
    />


    <!-- CARDVIEW DETAIL -->
    <Label
      Text="{ TemplateBinding Detail }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewDetailStyle }" />

  </StackLayout>

</ControlTemplate>
```

<a name="5" />

#### <a name="5-add-the-theme-specific-resources"></a>5. Agregar los recursos específicos de tema

Dado que se trata de un control personalizado, agregue los recursos que coinciden con el tema que se está utilizando el diccionario de recursos:

##### <a name="light-theme-colors"></a>Colores del tema claro

```xaml
<Color x:Key="iOSCardViewBackgroundColor">#FFFFFF</Color>
<Color x:Key="AndroidCardViewBackgroundColor">#FFFFFF</Color>

<Color x:Key="AndroidCardViewTextTextColor">#030303</Color>
<Color x:Key="iOSCardViewTextTextColor">#030303</Color>

<Color x:Key="AndroidCardViewDetailTextColor">#8F8E94</Color>
<Color x:Key="iOSCardViewDetailTextColor">#8F8E94</Color>
```

##### <a name="dark-theme-colors"></a>Colores de tema oscuro

```xaml
<!-- CARD VIEW COLORS -->
            <Color x:Key="iOSCardViewBackgroundColor">#404040</Color>
            <Color x:Key="AndroidCardViewBackgroundColor">#404040</Color>

            <Color x:Key="AndroidCardViewTextTextColor">#FFFFFF</Color>
            <Color x:Key="iOSCardViewTextTextColor">#FFFFFF</Color>

            <Color x:Key="AndroidCardViewDetailTextColor">#B5B4B9</Color>
            <Color x:Key="iOSCardViewDetailTextColor">#B5B4B9</Color>
```

<a name="6" />

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6. Establezca el elemento ControlTemplate para la clase CardView

Por último, asegúrese de la clase de C# creada en [paso 1](#1) usa la plantilla de control definida en [paso 4](#4) con un `Style` `Setter` elemento

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

<a name="7" />

#### <a name="7-add-the-control-to-a-page"></a>7. Agregar el Control a una página

El `CardView` control ahora pueden agregarse a una página. En el ejemplo siguiente se muestra que se hospeda en un `StackLayout`:

```xaml
<StackLayout Spacing="0">
  <local:CardView
    Margin="12,6"
    ImageSource="{ DynamicResource CardViewImage }"
    Text="CardView Text"
    Detail="CardView Detail"
  />
</StackLayout>

```
