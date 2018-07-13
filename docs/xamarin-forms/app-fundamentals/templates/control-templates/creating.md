---
title: Creación de una plantilla de control
description: Plantillas de control se pueden definir en el nivel de aplicación o página. En este artículo se muestra cómo crear y usar plantillas de control.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: b83668f6836b1d5d98f67592bf3e2b01e7319edc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998198"
---
# <a name="creating-a-controltemplate"></a>Creación de una plantilla de control

_Plantillas de control se pueden definir en el nivel de aplicación o página. En este artículo se muestra cómo crear y usar plantillas de control._

## <a name="creating-a-controltemplate-in-xaml"></a>Creación de una clase ControlTemplate en XAML

Para definir un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) en el nivel de aplicación, un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) deben agregarse a la `App` clase. De forma predeterminada, todas las aplicaciones de Xamarin.Forms creadas a partir de una plantilla de usar el **aplicación** clase para implementar el [ `Application` ](xref:Xamarin.Forms.Application) subclase. Para declarar un `ControlTemplate` en el nivel de aplicación, en la aplicación `ResourceDictionary` mediante XAML, el valor predeterminado **aplicación** clase debe reemplazarse por un XAML **aplicación** clase y código subyacente asociado, como se muestra en el ejemplo de código siguiente:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.App">
    <Application.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                <Grid>
                    ...
                    <BoxView ... />
                    <Label Text="Control Template Demo App"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                    <ContentPresenter ... />
                    <BoxView Color="Teal" ... />
                    <Label Text="(c) Xamarin 2016"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                </Grid>
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Cada [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) instancia se crea como un objeto reutilizable en una [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary).  Esto se consigue asignando un único cada declaración `x:Key` atributo, que le proporciona una clave descriptiva en el `ResourceDictionary`.

El ejemplo de código siguiente muestra asociado `App` código subyacente:

```csharp
public partial class App : Application
{
  public App ()
  {
    InitializeComponent ();
    MainPage = new HomePage ();
  }
}
```

Así como la configuración de la [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propiedad, el código subyacente también debe llamar a la `InitializeComponent` método para cargar y analizar el XAML asociado.

El siguiente ejemplo de código muestra un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) aplicando el `TealTemplate` a la [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0"
                 ControlTemplate="{StaticResource TealTemplate}">
        <StackLayout VerticalOptions="CenterAndExpand">
            <Label Text="Welcome to the app!" HorizontalOptions="Center" />
            <Button Text="Change Theme" Clicked="OnButtonClicked" />
        </StackLayout>
    </ContentView>
</ContentPage>
```

El `TealTemplate` se asigna a la [ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propiedad utilizando el `StaticResource` extensión de marcado. El [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) propiedad está establecida en un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) que define el contenido que se mostrará en el [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Este contenido se mostrarán mediante el [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) contenidos en el `TealTemplate`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

![](creating-images/teal-theme.png "Plantilla de Control verde azulado")

### <a name="re-theming-an-application-at-runtime"></a>Una aplicación en tiempo de ejecución de RE-temas

Al hacer clic en el **cambiar tema** botón ejecuta el `OnButtonClicked` método, que se muestra en el ejemplo de código siguiente:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Este método reemplaza activo [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) instancia con la alternativa `ControlTemplate` instancia, lo que resulta en la captura de pantalla siguiente:

![](creating-images/aqua-theme.png "Plantilla de Control aguamarina")

> [!NOTE]
> En un `ContentPage`, `Content` se puede asignar la propiedad y el `ControlTemplate` también se puede establecer la propiedad. Cuando esto ocurre, si la `ControlTemplate` contiene un `ContentPresenter` de instancia, el contenido asignado a la `Content` propiedad estará presentada por el `ContentPresenter` dentro de la `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>Configuración de una clase ControlTemplate con un estilo

Un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) también se pueden aplicar mediante un [ `Style` ](xref:Xamarin.Forms.Style) para expandir la capacidad de tema. Esto puede lograrse mediante la creación de un *implícita* o *explícita* estilo para la vista de destino en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)y estableciendo el `ControlTemplate` propiedad del destino ver en el [ `Style` ](xref:Xamarin.Forms.Style) instancia. El siguiente ejemplo de código muestra un *implícita* estilo que se han agregado para el nivel de aplicación [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Dado que el [ `Style` ](xref:Xamarin.Forms.Style) instancia es *implícita*, se aplicará a todos los `ContentView` las instancias de la aplicación. Por lo tanto, ya no es necesario establecer la [ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propiedad, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Para obtener más información sobre los estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Creación de una clase ControlTemplate en nivel de página

Además de crear [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) en el nivel de aplicación, también se pueden crear instancias en el nivel de página, tal como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                ...
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
        ...
    </ContentView>
</ContentPage>
```

Cuando se agrega un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) en el nivel de página, un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) se agrega a la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)y, a continuación, el `ControlTemplate` se incluyen las instancias en el `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Creación de un clase ControlTemplate en C&#35;

Para definir un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) en el nivel de aplicación, un `class` debe crearse que representa el `ControlTemplate`. La clase debe derivar de la [diseño](~/xamarin-forms/user-interface/layouts/index.md) que se va a usar para la plantilla, tal como se muestra en el ejemplo de código siguiente:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var contentPresenter = new ContentPresenter ();
    Children.Add (contentPresenter, 0, 1);
    Grid.SetColumnSpan (contentPresenter, 2);
    ...
  }
}

class AquaTemplate : Grid
{
  ...
}
```

El `AquaTemplate` es idéntica a la clase el `TealTemplate` clase salvo que se usan colores diferentes para el [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) y [ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor) propiedades.

El siguiente ejemplo de código muestra un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) aplicando el `TealTemplate` a la [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
public class HomePageCS : ContentPage
{
  ...
  ControlTemplate tealTemplate = new ControlTemplate (typeof(TealTemplate));
  ControlTemplate aquaTemplate = new ControlTemplate (typeof(AquaTemplate));

  public HomePageCS ()
  {
    var button = new Button { Text = "Change Theme" };
    var contentView = new ContentView {
      Padding = new Thickness (0, 20, 0, 0),
      Content = new StackLayout {
        VerticalOptions = LayoutOptions.CenterAndExpand,
        Children = {
          new Label { Text = "Welcome to the app!", HorizontalOptions = LayoutOptions.Center },
          button
        }
      },
      ControlTemplate = tealTemplate
    };
    ...
    Content = contentView;
  }
}
```

El [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) las instancias se crean especificando el tipo de las clases que definen las plantillas de control, en el `ControlTemplate` constructor.

El [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) propiedad está establecida en un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) que define el contenido que se mostrará en el [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Este contenido se mostrarán mediante el [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) contenidos en el `TealTemplate`. El mismo mecanismo descrito anteriormente se usa para cambiar el tema en tiempo de ejecución para el `AquaTheme`.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo crear y usar plantillas de control. Plantillas de control se pueden definir en el nivel de aplicación o página.


## <a name="related-links"></a>Vínculos relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Tema sencillo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
