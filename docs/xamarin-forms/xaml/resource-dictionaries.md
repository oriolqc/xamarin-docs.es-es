---
title: Diccionarios de recursos
description: "Recursos XAML son definiciones de objetos que se pueden usar más de una vez. Un ResourceDictionary permite que los recursos definidos en una sola ubicación y volver a utilizar en una aplicación de Xamarin.Forms. En este artículo se explica cómo crear y consumir un ResourceDictionary y cómo se combinan los diccionarios de recursos."
ms.topic: article
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/17/2017
ms.openlocfilehash: 0c2765551c16be605bc78d9ef32a91fd2c4ead8c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="resource-dictionaries"></a>Diccionarios de recursos

_Recursos XAML son definiciones de objetos que se pueden usar más de una vez. Un ResourceDictionary permite que los recursos definidos en una sola ubicación y volver a utilizar en una aplicación de Xamarin.Forms. En este artículo se explica cómo crear y consumir un ResourceDictionary y cómo se combinan los diccionarios de recursos._

## <a name="overview"></a>Información general

A [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) es un repositorio para los recursos utilizados por una aplicación de Xamarin.Forms. Recursos típicos que se almacenan en un `ResourceDictionary` incluyen [estilos](~/xamarin-forms/user-interface/styles/index.md), [plantillas de control](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [plantillas de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colores y convertidores.

En XAML, los recursos se definen en un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) y, a continuación, recuperar y aplicar a los elementos mediante el uso de la `StaticResource` extensión de marcado. En C#, se definen los recursos en un `ResourceDictionary` y, a continuación, recuperar y aplicar a los elementos mediante el uso de un indizador basada en cadena. Sin embargo, hay muy pocas ventajas en usar un `ResourceDictionary` en C#, como recursos puede fácilmente asignarse directamente a las propiedades de los elementos visuales sin tener que recuperar desde un `ResourceDictionary`.

## <a name="creating-and-consuming-a-resourcedictionary"></a>Crear y consumir un ResourceDictionary

Los recursos se pueden definir en un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) que está conectada a la [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) recopilación de una página o un control, o en la [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) colección de la aplicación. Elegir dónde se defina un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) impactos dónde pueden utilizarse:

- Recursos en un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definido en el control de nivel solo se puede aplicar al control y sus elementos secundarios.
- Recursos en un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definido en la página de nivel solo se puede aplicar a la página y a sus elementos secundarios.
- Recursos en un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definido en la aplicación de nivel se puede aplicar a lo largo de la aplicación.

El ejemplo de código XAML siguiente muestra los recursos definidos en un nivel de aplicación [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Esto [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define tres [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) recursos y un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) recursos. Para obtener más información acerca de cómo crear un XAML `App` de clases, consulte [clase App](~/xamarin-forms/app-fundamentals/application-class.md).

Cada recurso tiene una clave que se especifica utilizando el `x:Key` atributo, que proporciona una clave descriptiva en el `ResourceDictionary`. La clave se usa para recuperar un recurso desde el [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) por el `StaticResource` extensión de marcado, como se muestra en el siguiente ejemplo de código XAML que se muestra recursos adicionales definidos en un control de nivel `ResourceDictionary`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

La primera [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancia recupera y consume el `LabelPageHeadingStyle` recursos definidos en el nivel de aplicación [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), con la segunda `Label` instancia recuperar y consumir la `LabelNormalStyle` recursos definidos en el nivel de control `ResourceDictionary`. De forma similar, el [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancia recupera y consume el `NormalTextColor` recursos definidos en el nivel de aplicación `ResourceDictionary`y el `MediumBoldText` recursos definidos en el nivel de control `ResourceDictionary`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](resource-dictionaries-images/screenshots-sml.png "Consumiendo recursos ResourceDictionary")](resource-dictionaries-images/screenshots.png "consumiendo recursos ResourceDictionary")

> [!NOTE]
> **Tenga en cuenta**: recursos que son específicos de una sola página no deben incluirse en un diccionario de recursos en el nivel de aplicación, como por ejemplo, recursos, a continuación, se analizará al iniciar la aplicación en lugar de cuando se solicite una página. Para obtener más información, consulte [reducir el tamaño de diccionario de recursos de aplicación](~/xamarin-forms/deploy-test/performance.md).

## <a name="overriding-resources"></a>Reemplazar recursos

Cuando [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) compartan recursos `x:Key` valores de atributo, definidos más abajo en la jerarquía de la vista de recursos tienen prioridad sobre los definidos mayor seguridad. Por ejemplo, si se establece la `PageBackgroundColor` recurso `Blue` en la aplicación de nivel serán reemplazado por un nivel de página `PageBackgroundColor` del conjunto de recursos `Yellow`. De forma similar, un nivel de página `PageBackgroundColor` recursos serán reemplazados por un nivel de control `PageBackgroundColor` recursos. Esta prioridad se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

La versión original `PageBackgroundColor` y `NormalTextColor` instancias, definidas en el nivel de aplicación, son reemplazadas por la `PageBackgroundColor` y `NormalTextColor` instancias definidas en el nivel de página. Por lo tanto, el color de fondo de página se convierte en azul, y el texto en la página, se convierte en amarillo, como se muestra en las capturas de pantalla siguiente:

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Reemplazar recursos ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png "reemplazar ResourceDictionary recursos")

Sin embargo, tenga en cuenta que la barra de fondo de la [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) está de color amarillo todavía, porque la [ `BarBackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/) propiedad está establecida en el valor de la `PageBackgroundColor` recursos definidos en la aplicación nivel de [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/).

## <a name="merged-resource-dictionaries"></a>Diccionarios de recursos combinados

Diccionarios de recursos combinados combinan uno o varios [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) instancias a otro. Esto se consigue estableciendo la `ResourceDictionary.MergedDictionaries` propiedad a uno o varios diccionarios de recursos que se combinará en la aplicación, la página o el nivel de control `ResourceDictionary`.

> [!IMPORTANT]
> El [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) tipo también tiene un [ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/) propiedad que puede usarse para combinar una sola `ResourceDictionary` a otro, con el `ResourceDictionary` especificado como el valor de la `MergedWith`propiedad combinar con la actual `ResourceDictionary` instancia. Cuando se mezclan a través de la `MergedWith` propiedad, los recursos de la actual `ResourceDictionary` ese recurso compartido `x:Key` atributo valores con los recursos de la `ResourceDictionary` a combinarse, se reemplazará. Sin embargo, la `MergedWith` propiedad dejará de utilizarse en una versión futura de Xamarin.Forms. Por lo tanto, recomienda para usar la `MergedDictionaries` propiedad que se va a combinar `ResourceDictionary` instancias.

El ejemplo de código XAML siguiente muestra un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) denominado `MyResourceDictionary` que contiene un único recurso:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

`MyResourceDictionary` se pueden combinar en cualquier aplicación, la página o el nivel de control `ResourceDictionary`. El ejemplo de código XAML siguiente muestra lo que se combinan en un nivel de página `ResourceDictionary` mediante el `MergedDictionaries` propiedad:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Cuando se combinan [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) recursos compartan idénticos `x:Key` valores de atributo, Xamarin.Forms utiliza la prioridad de los recursos siguientes:

1. Los recursos locales para el diccionario de recursos.
1. Los recursos incluidos en el diccionario de recursos que se ha combinado a través de la [ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/) propiedad.
1. Los recursos contenidos en los diccionarios de recursos que se combinaron a través de la `MergedDictionaries` colección, en el orden en que aparecen en la `MergedDictionaries` propiedad.

> [!NOTE]
> **Tenga en cuenta**: buscar los diccionarios de recursos puede ser una tarea de cálculo intensiva si una aplicación contiene varios diccionarios de recursos de gran tamaño. Por lo tanto, asegúrese de que cada página de una aplicación solo usa los diccionarios de recursos que son adecuados para la página, para evitar búsquedas innecesarias.

## <a name="summary"></a>Resumen

Este artículo explica cómo crear y consumir un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)y cómo se combinan los diccionarios de recursos. Un `ResourceDictionary` permite a los recursos definidos en una sola ubicación y volver a utilizar en una aplicación de Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [Diccionarios de recursos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
