---
title: Enlace de una clase Xamarin.Forms ControlTemplate
description: Los enlaces de plantilla permiten enlazan controles en una plantilla de control a los datos a propiedades públicas, habilitar los valores de propiedad en los controles de la plantilla de control que se puede cambiar fácilmente. En este artículo muestra cómo utilizar los enlaces de plantilla para realizar el enlace de datos desde una plantilla de control.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 86de2ad6009365b3debbe1a2310651002b023219
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995572"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Enlace de una clase Xamarin.Forms ControlTemplate

_Los enlaces de plantilla permiten enlazan controles en una plantilla de control a los datos a propiedades públicas, habilitar los valores de propiedad en los controles de la plantilla de control que se puede cambiar fácilmente. En este artículo muestra cómo utilizar los enlaces de plantilla para realizar el enlace de datos desde una plantilla de control._

Un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) se usa para enlazar la propiedad de un control en una plantilla de control a una propiedad enlazable en el elemento primario de la *destino* vista propietaria de la plantilla de control. Por ejemplo, en lugar de definir el texto mostrado por [ `Label` ](xref:Xamarin.Forms.Label) instancias dentro de la [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate), podría usar un enlace a plantilla para enlazar la [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propiedad para propiedades enlazables que definen el texto que se mostrará.

Un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) es similar a una existente [ `Binding` ](xref:Xamarin.Forms.Binding), salvo que el *origen* de un `TemplateBinding` automáticamente siempre se establece en el elemento primario de la *destino* vista propietaria de la plantilla de control. Sin embargo, tenga en cuenta que el uso un `TemplateBinding` fuera de un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) no se admite.

## <a name="creating-a-templatebinding-in-xaml"></a>Creación de TemplateBinding en XAML

En XAML, un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) se crea utilizando el [ `TemplateBinding` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) extensión de marcado, como se muestra en el ejemplo de código siguiente:

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

En lugar de establecer el [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propiedades de texto estático, las propiedades pueden utilizar los enlaces de plantilla para enlazar a propiedades enlazables del elemento primario de la *destino* vista que posee el [ `ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Sin embargo, tenga en cuenta que los enlaces de plantilla se enlazan a `Parent.HeaderText` y `Parent.FooterText`, en lugar de `HeaderText` y `FooterText`. Esto es porque en este ejemplo, las propiedades enlazables se definen en el elemento primario principal de la *destino* ver, en lugar del elemento primario, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

El *origen* de la plantilla de enlace siempre automáticamente se establece en el elemento primario de la *destino* vista propietaria de la plantilla de control, este es el [ `ContentView` ](xref:Xamarin.Forms.ContentView) instancia de. La plantilla de enlace utiliza la [ `Parent` ](xref:Xamarin.Forms.Element.Parent) propiedad para devolver el elemento primario de la `ContentView` instancia, que es el [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancia. Por lo tanto, uso un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) en el [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) para enlazar a `Parent.HeaderText` y `Parent.FooterText` busca las propiedades enlazables definidas en el `ContentPage`, como se muestra en el ejemplo de código siguiente:

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

![](template-binding-images/teal-theme.png "Verde azulado plantilla de Control mediante los enlaces de plantilla")

## <a name="creating-a-templatebinding-in-c35"></a>Creación de TemplateBinding en C&#35;

En C#, un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) se crea mediante el `TemplateBinding` constructor, tal y como se muestra en el ejemplo de código siguiente:

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

En lugar de establecer el [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propiedades de texto estático, las propiedades pueden utilizar los enlaces de plantilla para enlazar a propiedades enlazables del elemento primario de la *destino* vista que posee el [ `ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). El enlace a plantilla se crea mediante la [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método, especifica un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) instancia como segundo parámetro. Tenga en cuenta que los enlaces de plantilla enlazar a `Parent.HeaderText` y `Parent.FooterText`, ya que las propiedades enlazables se definen en el elemento primario principal de la *destino* ver, en lugar del elemento primario, como se muestra en el ejemplo de código siguiente:

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

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Enlazar un BindableProperty a una propiedad ViewModel

Como se mencionó anteriormente, un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) enlaza la propiedad de un control en una plantilla de control a una propiedad enlazable en el elemento primario de la *destino* vista propietaria de la plantilla de control. A su vez, se pueden enlazar estas propiedades se puede enlazar a propiedades en ViewModel.

El ejemplo de código siguiente define dos propiedades en un modelo de vista:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

El `HeaderText` y `FooterText` propiedades ViewModel pueden enlazarse a, tal como se muestra en el ejemplo de código XAML siguiente:

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

El `HeaderText` y `FooterText` propiedades enlazables se enlazan a la `HomePageViewModel.HeaderText` y `HomePageViewModel.FooterText` propiedades, debido a la configuración la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) a una instancia de la `HomePageViewModel` clase. En general, esto da como resultado las propiedades de control en el [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) que se enlaza a [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instancias en el [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), que a su vez de enlazar a Propiedades de ViewModel.

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

Para obtener más información sobre el enlace de datos a ViewModel, consulte [desde los enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumen

En este artículo se muestra la utilización de enlaces de plantilla para realizar el enlace de datos desde una plantilla de control. Los enlaces de plantilla permiten enlazan controles en una plantilla de control a los datos a propiedades públicas, habilitar los valores de propiedad en los controles de la plantilla de control que se puede cambiar fácilmente.



## <a name="related-links"></a>Vínculos relacionados

- [Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Enlaces de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Tema sencillo con el enlace de plantilla (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [Tema sencillo con enlace de plantillas y ViewModel (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
