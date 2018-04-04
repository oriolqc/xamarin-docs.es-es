---
title: Creación de una plantilla de control
description: Plantillas de control pueden definirse en el nivel de aplicación o página. Este artículo demuestra cómo crear y utilizar plantillas de control.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e8a0695969609a4b0bbeb38896adae9a7c16ed07
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-controltemplate"></a>Creación de una plantilla de control

_Plantillas de control pueden definirse en el nivel de aplicación o página. Este artículo demuestra cómo crear y utilizar plantillas de control._

## <a name="creating-a-controltemplate-in-xaml"></a>Crear un elemento ControlTemplate en XAML

Para definir un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) en el nivel de aplicación, un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) deben agregarse a la `App` clase. De forma predeterminada, todas las aplicaciones de Xamarin.Forms creadas a partir de una plantilla utilizan el **aplicación** clase para implementar el [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) subclase. Para declarar un `ControlTemplate` en el nivel de aplicación, en la aplicación `ResourceDictionary` mediante XAML, el valor predeterminado **aplicación** clase debe sustituirse por un XAML **aplicación** clase y código subyacente asociado, como se muestra en el ejemplo de código siguiente:

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

Cada [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instancia se crea como un objeto reutilizable en una [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/).  Esto se logra dando a cada declaración de un único `x:Key` atributo, que le proporciona una clave descriptiva en el `ResourceDictionary`.

En el ejemplo de código siguiente se muestra asociado `App` código subyacente:

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

Así como la configuración de la [ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) propiedad, el código subyacente se debe llamar a la `InitializeComponent` para cargar y analizar el XAML asociado.

El siguiente ejemplo de código muestra un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) aplicar el `TealTemplate` a la [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

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

El `TealTemplate` se asigna a la [ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/) propiedad utilizando el `StaticResource` extensión de marcado. El [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propiedad está establecida en un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) que define el contenido que se mostrará en el [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Este contenido se mostrará de la [ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) contenidos en el `TealTemplate`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

![](creating-images/teal-theme.png "Plantilla de Control verde azulado")

### <a name="re-theming-an-application-at-runtime"></a>Re-temas una aplicación en tiempo de ejecución

Al hacer clic en el **cambiar el tema** botón ejecuta el `OnButtonClicked` método, que se muestra en el ejemplo de código siguiente:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Este método reemplaza el activo [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instancia con la alternativa `ControlTemplate` instancia, lo que produce la captura de pantalla siguiente:

![](creating-images/aqua-theme.png "Plantilla de Control aguamarina")

> [!NOTE]
> En un `ContentPage`, `Content` propiedad se puede asignar y `ControlTemplate` también se puede establecer la propiedad. Cuando esto ocurre, si la `ControlTemplate` contiene un `ContentPresenter` de instancia, el contenido asignado a la `Content` propiedad aparecerá por la `ContentPresenter` dentro de la `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>Establecer un elemento ControlTemplate con un estilo

A [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) también puede aplicarse a través de un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) para expandir aún más la capacidad de tema. Esto puede lograrse mediante la creación de un *implícita* o *explícita* estilo para la vista de destino en un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)y estableciendo el `ControlTemplate` propiedad del destino ver en el [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancia. El siguiente ejemplo de código muestra un *implícita* estilo que se han agregado al nivel de la aplicación [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Dado que la [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancia es *implícita*, se aplicarán a todos los `ContentView` instancias en la aplicación. Por lo tanto, no es necesario establecer el [ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/) propiedad, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Para obtener más información sobre los estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Crear un elemento ControlTemplate en nivel de página

Además de crear [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) en el nivel de aplicación, también se pueden crear instancias en el nivel de página, tal como se muestra en el ejemplo de código siguiente:

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

Al agregar un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) en el nivel de página, un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) se agrega a la [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)y, a continuación, el `ControlTemplate` se incluyen instancias en el `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Crear un elemento ControlTemplate en C&#35;

Para definir un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) en el nivel de aplicación, un `class` debe crearse que representa el `ControlTemplate`. La clase debe derivarse de la [diseño](~/xamarin-forms/user-interface/layouts/index.md) que se va a usar para la plantilla, tal como se muestra en el ejemplo de código siguiente:

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

El `AquaTemplate` clase es idéntica a la `TealTemplate` (clase), salvo que se usan colores diferentes para el [ `BoxView.Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) y [ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) propiedades.

El siguiente ejemplo de código muestra un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) aplicar el `TealTemplate` a la [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

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

El [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instancias se crean especificando el tipo de las clases que definen las plantillas de control, en la `ControlTemplate` constructor.

El [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propiedad está establecida en un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) que define el contenido que se mostrará en el [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Este contenido se mostrará de la [ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) contenidos en el `TealTemplate`. El mismo mecanismo descrito anteriormente se utiliza para cambiar el tema en tiempo de ejecución para el `AquaTheme`.

## <a name="summary"></a>Resumen

Este artículo muestra cómo crear y utilizar plantillas de control. Plantillas de control pueden definirse en el nivel de aplicación o página.


## <a name="related-links"></a>Vínculos relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Tema sencillo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
