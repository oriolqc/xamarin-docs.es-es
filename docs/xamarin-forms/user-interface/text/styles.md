---
title: Estilos de texto de Xamarin.Forms
description: Este artículo se explica cómo la aplicación de estilos al texto en las aplicaciones de Xamarin.Forms. Estilos pueden definirse una vez y utilizados por muchas vistas, pero un estilo sólo puede utilizarse con las vistas de un tipo.
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: b8cc6493c3574180e7938050075c5a70ecebeebf
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245327"
---
# <a name="xamarinforms-text-styles"></a>Estilos de texto de Xamarin.Forms

_Aplicación de estilos al texto de Xamarin.Forms_

Estilos pueden usarse para ajustar la apariencia de las etiquetas, las entradas y editores. Estilos pueden definirse una vez y utilizados por muchas vistas, pero un estilo sólo puede utilizarse con las vistas de un tipo.
Estilos pueden indicarse un `Key` y aplicar selectivamente mediante un control específico `Style` propiedad.

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Estilos integrados

Xamarin.Forms incluye varias [integrados](http://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) estilos para los escenarios comunes:

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Para aplicar uno de los estilos integrados, utilice la `DynamicResource` extensión de marcado para especificar el estilo:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En C#, se seleccionan los estilos integrados de `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

![](styles-images/builtinstyles.png "Ejemplo de estilos de dispositivo")

<a name="Custom_Styles" />

## <a name="custom-styles"></a>Estilos personalizados

Estilos constan de establecedores y establecedores están compuestos de propiedades y los valores de las propiedades se establecerá en.

En C#, un estilo personalizado para una etiqueta con texto rojo de tamaño 30 se definiría como sigue:

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

En XAML:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

Tenga en cuenta que los recursos (incluidos todos los estilos) se definen en `ContentPage.Resources`, que es un elemento relacionado de cuanto más se familiarice `ContentPage.Content` elemento.

![](styles-images/customstyle.png "Ejemplo de estilos personalizados")

<a name="Applying_Styles" />

## <a name="applying-styles"></a>Aplicar estilos

Una vez que se ha creado un estilo, se puede aplicar a cualquier coincidencia de vista su `TargetType`.

En XAML, los estilos personalizados se aplican a vistas proporcionando sus `Style` propiedad con un `StaticResource` el estilo deseado de referencia de extensión de marcado:

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

En C#, estilos pueden se aplican directamente a una vista o agregados a y recuperar desde una página `ResourceDictionary`. Para agregar directamente:

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Para agregar y recuperar de la página `ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Estilos integrados se aplican de forma diferente, porque necesitan para responder a la configuración de accesibilidad. Para aplicar estilos integrados en XAML, el `DynamicResource` se utiliza la extensión de marcado:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En C#, se seleccionan los estilos integrados de `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Accesibilidad

Los estilos integrados existen para que resulten más fáciles de respetar las preferencias de accesibilidad. Cuando se utiliza cualquiera de los estilos integrados, los tamaños de fuente aumentará automáticamente si un usuario establece las preferencias de su accesibilidad en consecuencia.

Considere el ejemplo siguiente de la misma página de vistas de estilo con los estilos integrados con la configuración de accesibilidad habilitados y deshabilitados:

Deshabilitado:

![](styles-images/pre-access.png "Estilos de dispositivo deshabilitado de accesibilidad")

Habilitado:

![](styles-images/post-access.png "Estilos de dispositivo con accesibilidad habilitado")

Para garantizar el acceso, asegúrese de que los estilos integrados se usan como base para los estilos relacionada con el texto dentro de la aplicación y que está utilizando estilos de forma coherente. Vea [estilos](~/xamarin-forms/user-interface/styles/index.md) para obtener más información sobre la extensión y trabajar con estilos en general.


## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones móviles con Xamarin.Forms, capítulo 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
