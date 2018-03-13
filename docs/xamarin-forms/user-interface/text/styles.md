---
title: Estilos
description: Texto de estilo de Xamarin.Forms
ms.topic: article
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 6da9b4d36c8caca23328318b4f2bff784038bacd
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="styles"></a>Estilos

_Texto de estilo de Xamarin.Forms_


Estilos pueden usarse para ajustar la apariencia de las etiquetas, las entradas y editores. Estilos pueden definirse una vez y utilizados por muchas vistas, pero un estilo sólo puede utilizarse con las vistas de un tipo.
Estilos pueden indicarse un `Key` y aplicar selectivamente mediante un control específico `Style` propiedad.

En este artículo se tratan los siguientes temas:

- **[Estilos integrados](#Built-In_Styles)**  &ndash; utiliza los estilos integrados para vistas basadas en texto de estilo a lo largo de la aplicación.
- **[Estilos personalizados](#Custom_Styles)**  &ndash; definir estilos personalizados cuando las opciones integradas no son suficientes.
- **[Aplicar estilos](#Applying_Styles)**  &ndash; estilos integrados y personalizados se aplican a las vistas.
- **[Accesibilidad](#Accessibility)**  &ndash; Asegúrese de que texto respeta la configuración de accesibilidad.

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
- [Style](http://developer.xamarin.comhttps://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
