---
title: Página Master-Detail de Xamarin.Forms
description: 'El Xamarin.Forms MasterDetailPage es una página que administra dos páginas relacionadas de información: una página maestra que presenta los elementos y una página de detalles que muestra los detalles acerca de los elementos en la página maestra. En este artículo se explica cómo usar un MasterDetailPage y navegar entre las páginas de información.'
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 25f6cf341fcf47d5dc5320f73855bb2a4e29a9e8
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675528"
---
# <a name="xamarinforms-master-detail-page"></a>Página Master-Detail de Xamarin.Forms

_El Xamarin.Forms MasterDetailPage es una página que administra dos páginas relacionadas de información: una página maestra que presenta los elementos y una página de detalles que muestra los detalles acerca de los elementos en la página maestra. En este artículo se explica cómo usar un MasterDetailPage y navegar entre las páginas de información._

## <a name="overview"></a>Información general

Normalmente, una página maestra muestra una lista de elementos, como se muestra en las capturas de pantalla siguiente:

[![](master-detail-page-images/masterpage-components.png "Componentes de la página maestra")](master-detail-page-images/masterpage-components-large.png#lightbox "componentes de la página maestra")

La ubicación de la lista de elementos es idéntica en cada plataforma, y seleccionar uno de los elementos se le remitirá a la página de detalles correspondiente. Además, la página maestra también incluye una barra de navegación que contiene un botón que se puede usar para navegar a la página de detalles de activo:

- En iOS, la barra de navegación está presente en la parte superior de la página y tiene un botón que se desplaza a la página de detalles. Además, se puede navegar a la página de detalles de activo deslizándose hacia la página maestra a la izquierda.
- En Android, la barra de navegación está presente en la parte superior de la página y muestra un título, un icono y un botón que navega a la página de detalles. El icono se define en el `[Activity]` atributo que decora el `MainActivity` clase en el proyecto específico de la plataforma Android. Además, la página de detalles activos se puede navegar deslizándose hacia la página maestra a la izquierda, puntee en la página de detalles en el extremo derecho de la pantalla y pulsando el *volver* situado en la parte inferior de la pantalla.
- En la plataforma Universal de Windows (UWP), la barra de navegación está presente en la parte superior de la página y tiene un botón que se desplaza a la página de detalles.

Una muestra de datos de página de detalles que se corresponde con el elemento había seleccionado en el maestro de página y los componentes principales de la página de detalles se muestran en las capturas de pantalla siguiente:

![](master-detail-page-images/detailpage-components.png "Componentes de la página de detalles")

La página de detalles contiene una barra de navegación, cuyo contenido es dependiente de la plataforma:

- En iOS, la barra de navegación está presente en la parte superior de la página muestra un título y tiene un botón que se devuelve a la página maestra, siempre que la instancia de la página de detalles se ajusta en el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instancia. Además, se puede devolver a la página maestra deslizándose hacia la página de detalles a la derecha.
- En Android, una barra de navegación está presente en la parte superior de la página y muestra un título, un icono y un botón que se devuelve a la página maestra. El icono se define en el `[Activity]` atributo que decora el `MainActivity` clase en el proyecto específico de la plataforma Android.
- En UWP, la barra de navegación está presente en la parte superior de la página y muestra un título y tiene un botón que se devuelve a la página maestra.

### <a name="navigation-behavior"></a>Comportamiento de navegación

El comportamiento de la experiencia de navegación entre páginas maestro y detalles es depende de la plataforma:

- En iOS, la página de detalles *diapositivas* a la derecha, como las diapositivas de la página principal de la izquierda y la parte izquierda de los detalles de la página sigue estando visible.
- En Android, las páginas de detalle y maestro son *superpuesta* entre sí.
- En UWP, las páginas de detalle y maestro son *intercambiar*.

Se observará un comportamiento similar en modo horizontal, salvo que la página maestra en iOS y Android tiene un ancho similar como la página principal en modo vertical, por lo que más de la página de detalles será visible.

Para obtener información acerca de cómo controlar el comportamiento de navegación, consulte [controlar el comportamiento de presentación de la página de detalles](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Creación de un MasterDetailPage

Un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) contiene [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) y [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propiedades que son de tipo [ `Page` ](xref:Xamarin.Forms.Page), que se usan para obtener y establecer las páginas maestro y detalles, respectivamente.

> [!IMPORTANT]
> Un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) está diseñado para ser una página de raíz y usarlo como página secundaria en otros tipos de páginas podría provocar un comportamiento inesperado e incoherente. Además, se recomienda que la página principal de un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) siempre debe ser un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancia y que solo se debe rellenar la página de detalles con [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), y `ContentPage` instancias. Esto le ayudará a garantizar una experiencia de usuario coherente en todas las plataformas.

El ejemplo de código XAML siguiente muestra un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) que establece el [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) y [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propiedades:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

En el ejemplo de código siguiente se muestra el equivalente [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) creado en C#:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

El [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) propiedad está establecida en un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancia. El [ `MasterDetailPage.Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propiedad está establecida en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) que contiene un `ContentPage` instancia.

### <a name="creating-the-master-page"></a>Creación de la página principal

El ejemplo de código XAML siguiente muestra la declaración de la `MasterPage` objeto, que se hace referencia a través de la [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

La página consta de un [ `ListView` ](xref:Xamarin.Forms.ListView) que se rellena con datos en XAML estableciendo su [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propiedad a una matriz de `MasterPageItem` instancias. Cada `MasterPageItem` define `Title`, `IconSource`, y `TargetType` propiedades.

Un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) se asigna a la [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propiedad para mostrar cada `MasterPageItem`. El `DataTemplate` contiene un [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) que consta de un [ `Image` ](xref:Xamarin.Forms.Image) y un [ `Label` ](xref:Xamarin.Forms.Label). El [ `Image` ](xref:Xamarin.Forms.Image) muestra el `IconSource` valor de propiedad y el [ `Label` ](xref:Xamarin.Forms.Label) muestra el `Title` valor de propiedad, para cada `MasterPageItem`.

La página tiene su [ `Title` ](xref:Xamarin.Forms.Page.Title) y [ `Icon` ](xref:Xamarin.Forms.Page.Icon) conjunto de propiedades. El icono aparecerá en la página de detalles, siempre que la página de detalles tiene una barra de título. Esto debe estar habilitado en iOS ajustando la instancia de la página de detalles en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instancia.

> [!NOTE]
> El [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) página debe tener su [ `Title` ](xref:Xamarin.Forms.Page.Title) propiedad establecida, o se producirá una excepción.

El siguiente ejemplo de código muestra la página equivalente creada en C#:

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
        var grid = new Grid { Padding = new Thickness(5, 10) };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

        var image = new Image();
        image.SetBinding(Image.SourceProperty, "IconSource");
        var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
        label.SetBinding(Label.TextProperty, "Title");

        grid.Children.Add(image);
        grid.Children.Add(label, 1, 0);

        return new ViewCell { View = grid };
      }),
      SeparatorVisibility = SeparatorVisibility.None
    };

    Icon = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

Las capturas de pantalla siguientes muestran la página maestra en cada plataforma:

![](master-detail-page-images/masterpage.png "Ejemplo de página maestra")

### <a name="creating-and-displaying-the-detail-page"></a>Crear y mostrar la página de detalles

El `MasterPage` instancia contiene un `ListView` propiedad que expone su [ `ListView` ](xref:Xamarin.Forms.ListView) instancia para que el `MainPage` [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) puede registrar la instancia de un controlador de eventos para controlar la [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) eventos. Esto permite la `MainPage` instancia para establecer el [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propiedad a la página que representa el texto seleccionado `ListView` elemento. El ejemplo de código siguiente muestra el controlador de eventos:

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

El `OnItemSelected` método realiza las acciones siguientes:

- Recupera el [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) desde el [ `ListView` ](xref:Xamarin.Forms.ListView) de instancia y siempre que no sea `null`, Establece la página de detalles en una nueva instancia del tipo de página almacenados en el `TargetType`propiedad de la `MasterPageItem`. El tipo de página se encapsula en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instancia para asegurarse de que el icono que se hace referencia a través de la [ `Icon` ](xref:Xamarin.Forms.Page.Icon) propiedad en el `MasterPage` se muestra en la página de detalles en iOS.
- El elemento seleccionado en el [ `ListView` ](xref:Xamarin.Forms.ListView) está establecido en `null` para asegurarse de que ninguno de los `ListView` elementos seleccionará la próxima vez que el `MasterPage` se presenta.
- La página de detalles se presenta al usuario estableciendo el [ `MasterDetailPage.IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propiedad `false`. Esta propiedad controla si se presenta la página maestra o de detalle. Se debe establecer en `true` para mostrar la página maestra y a `false` para mostrar la página de detalles.

Capturas de pantalla siguientes se muestra la `ContactPage` página de detalles, que se muestra después de que se haya seleccionado en la página maestra:

![](master-detail-page-images/detailpage.png "Ejemplo de la página de detalles")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Controlar el comportamiento de presentación de una página de detalles

El modo [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) administra las páginas maestro y detalles depende de si la aplicación se ejecuta en un teléfono o tableta, la orientación del dispositivo y el valor de la [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propiedad. Esta propiedad determina cómo se mostrará la página de detalles. Sus valores posibles son:

- **Valor predeterminado** : las páginas se mostrarán con el valor predeterminado de la plataforma.
- **Elemento flotante** : la página de detalles se tratan o parcialmente cubre la página maestra.
- **División** : se muestra la página principal de la izquierda y la página de detalles está a la derecha.
- **SplitOnLandscape** : una pantalla dividida se usa cuando el dispositivo está en orientación horizontal.
- **SplitOnPortrait** : una pantalla dividida se usa cuando el dispositivo está en orientación vertical.

El ejemplo de código XAML siguiente muestra cómo establecer el [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propiedad en un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

En el ejemplo de código siguiente se muestra el equivalente [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) creado en C#:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

Sin embargo, el valor de la [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propiedad solo afecta a las aplicaciones que se ejecutan en el escritorio y tabletas. Las aplicaciones que se ejecuta en teléfonos siempre tienen la *elemento flotante* comportamiento.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) y navegar entre las páginas de información. Xamarin.Forms `MasterDetailPage` es una página que administra dos páginas de información relacionada: una página maestra que presenta los elementos y una página de detalles que muestra los detalles acerca de los elementos en la página maestra.


## <a name="related-links"></a>Vínculos relacionados

- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
