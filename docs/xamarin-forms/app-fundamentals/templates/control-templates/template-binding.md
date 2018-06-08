---
title: Enlace de un elemento ControlTemplate
description: Enlaces de plantilla permiten enlazan controles en una plantilla de control a los datos a las propiedades públicas, habilitar los valores de propiedad de los controles en la plantilla de control que puede modificarse fácilmente. En este artículo se muestra el uso de enlaces de plantilla para realizar el enlace de datos desde una plantilla de control.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 6b2904d06d0982fb30e9a989f03f22b726b9772e
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847181"
---
# <a name="binding-from-a-controltemplate"></a>Enlace de un elemento ControlTemplate

_Enlaces de plantilla permiten enlazan controles en una plantilla de control a los datos a las propiedades públicas, habilitar los valores de propiedad de los controles en la plantilla de control que puede modificarse fácilmente. En este artículo se muestra el uso de enlaces de plantilla para realizar el enlace de datos desde una plantilla de control._

A [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) se utiliza para enlazar la propiedad de un control en una plantilla de control a una propiedad enlazable en el elemento primario de la *destino* vista propietaria de la plantilla de control. Por ejemplo, en lugar de definir el texto mostrado por [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias dentro de la [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/), podría utilizar un enlace de plantilla para enlazar la [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedad enlazables propiedades que definen el texto que se mostrará.

A [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) es similar a una existente [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/), salvo que la *origen* de un `TemplateBinding` automáticamente siempre se establece en el elemento primario de la *destino* vista propietaria de la plantilla de control. Sin embargo, tenga en cuenta que cuando se utiliza un `TemplateBinding` fuera de un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) no se admite.

## <a name="creating-a-templatebinding-in-xaml"></a>Crear un TemplateBinding en XAML

En XAML, un [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) se crea utilizando el [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/) extensión de marcado, como se muestra en el ejemplo de código siguiente:

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

En lugar de establecer la [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedades de texto estático, las propiedades pueden usar enlaces de plantilla para enlazar a las propiedades enlazables en el elemento primario de la *destino* vista propietaria de la [ `ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). Sin embargo, tenga en cuenta que los enlaces de plantilla se enlazan a `Parent.HeaderText` y `Parent.FooterText`, en lugar de `HeaderText` y `FooterText`. Esto es porque en este ejemplo, las propiedades enlazables se definen en el primario de segundo nivel de la *destino* ver, en lugar del elemento primario, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

El *origen* de la plantilla de enlace se establece siempre automáticamente al elemento primario de la *destino* vista propietaria de la plantilla de control, que mostramos la [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) instancia. La plantilla de enlace utiliza la [ `Parent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Parent/) propiedad para devolver el elemento primario de la `ContentView` instancia, que es el [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancia. Por tanto, usan un [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) en el [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) para enlazar a `Parent.HeaderText` y `Parent.FooterText` busca las propiedades enlazables definidas en el `ContentPage`, como se muestra en el ejemplo de código siguiente:

```csharp
public static readonly BindableProperty HeaderTextProperty =
  BindableProperty.Create ("HeaderText", typeof(string), typeof(HomePage), "Control Template Demo App");
public static readonly BindableProperty FooterTextProperty =
  BindableProperty.Create ("FooterText", typeof(string), typeof(HomePage), "(c) Xamarin 2016");

public string HeaderText {
  get { return (string)GetValue (HeaderTextProperty); }
}

public string FooterText {
  get { return (string)GetValue (FooterTextProperty); }
}
```

El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

![](template-binding-images/teal-theme.png "Verde azulado plantilla de Control mediante enlaces de plantilla")

## <a name="creating-a-templatebinding-in-c35"></a>Crear un TemplateBinding en C&#35;

En C#, un [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) se crea mediante la `TemplateBinding` constructor, como se muestra en el ejemplo de código siguiente:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var topLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    topLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.HeaderText"));
    ...
    var bottomLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    bottomLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.FooterText"));
    ...
  }
}
```

En lugar de establecer la [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedades de texto estático, las propiedades pueden usar enlaces de plantilla para enlazar a las propiedades enlazables en el elemento primario de la *destino* vista propietaria de la [ `ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). El enlace de plantilla se crea mediante la [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) método, especifica un [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) instancia como segundo parámetro. Tenga en cuenta que los enlaces de plantilla se enlazan a `Parent.HeaderText` y `Parent.FooterText`, ya que las propiedades enlazables se definen en el primario de segundo nivel de la *destino* ver, en lugar del elemento primario, como se muestra en el ejemplo de código siguiente:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    Content = new ContentView {
      ControlTemplate = tealTemplate,
      Content = new StackLayout {
        ...
      },
      ...
    };
    ...
  }
}
```

Las propiedades enlazables se definen en el `ContentPage`, tal como se describe anteriormente.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Enlazar un BindableProperty a una propiedad Perspective

Como se mencionó anteriormente, un [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) enlaza la propiedad de un control en una plantilla de control a una propiedad enlazable en el elemento primario de la *destino* vista propietaria de la plantilla de control. A su vez, se pueden enlazar estas propiedades se puede enlazar a las propiedades de ViewModels.

En el ejemplo de código siguiente se define dos propiedades en un modelo de vista:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

El `HeaderText` y `FooterText` ViewModel propiedades pueden enlazarse a, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage xmlns:local="clr-namespace:SimpleTheme;assembly=SimpleTheme"
             HeaderText="{Binding HeaderText}" FooterText="{Binding FooterText}" ...>
    <ContentPage.BindingContext>
        <local:HomePageViewModel />
    </ContentPage.BindingContext>
    <ContentView ControlTemplate="{StaticResource TealTemplate}" ...>
    ...
    </ContentView>
</ContentPage>
```

El `HeaderText` y `FooterText` propiedades enlazables se enlazan a la `HomePageViewModel.HeaderText` y `HomePageViewModel.FooterText` propiedades, debido a la configuración de la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) a una instancia de la `HomePageViewModel` clase. En general, esto da como resultado en Propiedades de control en el [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) que se enlaza a [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instancias en el [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), que a su vez de enlazar a Propiedades del modelo de vista.

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    BindingContext = new HomePageViewModel ();
    this.SetBinding (HeaderTextProperty, "HeaderText");
    this.SetBinding (FooterTextProperty, "FooterText");
    ...
  }
}
```

Para obtener más información sobre el enlace de datos a ViewModels, consulte [de enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumen

En este artículo se muestra la utilización de enlaces de plantilla para realizar el enlace de datos de una plantilla de control. Enlaces de plantilla permiten enlazan controles en una plantilla de control a los datos a las propiedades públicas, habilitar los valores de propiedad de los controles en la plantilla de control que puede modificarse fácilmente.



## <a name="related-links"></a>Vínculos relacionados

- [Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Desde los enlaces de datos para MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Tema sencillo con enlace a plantilla (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [Tema sencillo con enlace a plantilla y ViewModel (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)
- [Elemento ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
