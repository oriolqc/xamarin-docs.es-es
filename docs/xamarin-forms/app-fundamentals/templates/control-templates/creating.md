---
title: Creación de una plantilla de control
description: Las plantillas de control se pueden definir en el nivel de aplicación o en el de página. En este artículo se explica cómo crear y consumir plantillas de control.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: dc26084b94956ea9bc87384e5fdb79695bc8c2b5
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051787"
---
# <a name="creating-a-controltemplate"></a>Creación de una plantilla de control

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)

_Las plantillas de control se pueden definir en el nivel de aplicación o en el de página. En este artículo se explica cómo crear y consumir plantillas de control._

## <a name="creating-a-controltemplate-in-xaml"></a>Creación de una plantilla de control en XAML

Para definir un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en el nivel de aplicación, se debe agregar un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a la clase `App`. De forma predeterminada, en todas las aplicaciones de Xamarin.Forms creadas a partir de una plantilla se usa la clase **App** para implementar la subclase [`Application`](xref:Xamarin.Forms.Application). Para declarar un elemento `ControlTemplate` en el nivel de aplicación, en el objeto `ResourceDictionary` de la aplicación con XAML, la clase **App** predeterminada se debe reemplazar por una clase **App** de XAML y el código subyacente asociado, como se muestra en el ejemplo de código siguiente:

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

Cada instancia de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) se crea como un objeto reutilizable en un elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).  Esto se consigue mediante la asignación de un atributo `x:Key` único a cada declaración, para proporcionarle una clave descriptiva en el objeto `ResourceDictionary`.

En el ejemplo de código siguiente se muestra el código subyacente de `App` asociado:

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

Además de establecer la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage), el código subyacente también debe llamar al método `InitializeComponent` para cargar y analizar el código XAML asociado.

En el ejemplo de código siguiente se muestra un elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) que aplica `TealTemplate` a [`ContentView`](xref:Xamarin.Forms.ContentView):

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

El elemento `TealTemplate` se asigna a la propiedad [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) mediante la extensión de marcado `StaticResource`. La propiedad [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) se establece en un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define el contenido que se mostrará en [`ContentPage`](xref:Xamarin.Forms.ContentPage). Este contenido se mostrará mediante el elemento [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) incluido en `TealTemplate`. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

![](creating-images/teal-theme.png "Plantilla de control de color verde azulado")

### <a name="re-theming-an-application-at-runtime"></a>Cambio de los temas de una aplicación en tiempo de ejecución

Al hacer clic en el botón **Cambiar tema** se ejecuta el método `OnButtonClicked`, que se muestra en el ejemplo de código siguiente:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Este método reemplaza la instancia activa de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) con la instancia de `ControlTemplate` alternativa, lo que da como resultado la captura de pantalla siguiente:

![](creating-images/aqua-theme.png "Plantilla de control aguamarina")

> [!NOTE]
> En un elemento `ContentPage`, se puede asignar la propiedad `Content` y también se puede establecer la propiedad `ControlTemplate`. En ese caso, si `ControlTemplate` contiene una instancia de `ContentPresenter`, el contenido asignado a la propiedad `Content` se presentará por medio del elemento `ContentPresenter` de `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>Configuración de una clase ControlTemplate con un estilo

Una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) también se puede aplicar mediante un elemento [`Style`](xref:Xamarin.Forms.Style) para expandir la capacidad del tema. Esto se puede lograr mediante la creación de un estilo *implícito* o *explícito* para la vista de destino en un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), y estableciendo la propiedad `ControlTemplate` de la vista de destino en la instancia de [`Style`](xref:Xamarin.Forms.Style). En el ejemplo de código siguiente se muestra un estilo *implícito* que se ha agregado al objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de aplicación:

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Como la instancia de [`Style`](xref:Xamarin.Forms.Style) es *implícita*, se aplicará a todas las instancias de `ContentView` de la aplicación. Por tanto, ya no es necesario establecer la propiedad [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate), como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Para obtener más información sobre los estilos, vea [Estilos](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Creación de una plantilla de control en el nivel de página

Además de crear instancias de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en el nivel de aplicación, también se pueden crear en el nivel de página, como se muestra en el ejemplo de código siguiente:

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

Cuando se agrega un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en el nivel de página, se agrega un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a [`ContentPage`](xref:Xamarin.Forms.ContentPage) y, después, se incluyen las instancias de `ControlTemplate` en el objeto `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Creación de una plantilla de control en C&#35;

Para definir un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en el nivel de aplicación, se debe crear un objeto `class` que represente el elemento `ControlTemplate`. La clase se debe derivar del [diseño](~/xamarin-forms/user-interface/layouts/index.md) que se usa para la plantilla, como se muestra en el ejemplo de código siguiente:

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

La clase `AquaTemplate` es idéntica a la clase `TealTemplate`, salvo que se usan otros colores para las propiedades [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) y [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor).

En el ejemplo de código siguiente se muestra un elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) que aplica `TealTemplate` a [`ContentView`](xref:Xamarin.Forms.ContentView):

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

Las instancias de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) se crean mediante la especificación del tipo de las clases que definen las plantillas de control, en el constructor `ControlTemplate`.

La propiedad [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) se establece en un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define el contenido que se mostrará en [`ContentPage`](xref:Xamarin.Forms.ContentPage). Este contenido se mostrará mediante el elemento [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) incluido en `TealTemplate`. El mismo mecanismo descrito anteriormente se usa para cambiar el tema en tiempo de ejecución para el objeto `AquaTheme`.

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo crear y consumir plantillas de control. Las plantillas de control se pueden definir en el nivel de aplicación o en el de página.


## <a name="related-links"></a>Vínculos relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Tema sencillo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
