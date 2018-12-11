---
title: Xamarin.Forms Shell
description: En este artículo se explica cómo usar Xamarin.Forms Shell, que reduce la complejidad de las aplicaciones de Xamarin.Forms mediante las características fundamentales de la interfaz de usuario que requieren la mayoría de las aplicaciones.
ms.prod: xamarin
ms.assetid: 1A674212-72DB-4AA4-B626-A4EC135AD1A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2018
ms.openlocfilehash: 933368f7ca1435ece4f20945b2f8e905f7584217
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899388"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![Vista previa](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/Microsoft/TailwindTraders-Mobile)

Xamarin.Forms Shell es un contenedor para aplicaciones que proporciona características fundamentales de la interfaz de usuario que requieren la mayoría de las aplicaciones, lo que le permite centrarse en la carga de trabajo principal de la aplicación. Las aplicaciones de Shell se ofrecen con la siguiente funcionalidad:

- Un único lugar para describir la estructura visual de una aplicación.
- Una interfaz de usuario de navegación común.
- Un servicio de navegación con vinculación en profundidad.
- Un controlador de búsqueda integrado.

Esta funcionalidad reduce la complejidad de las aplicaciones, al tiempo que aumenta la productividad del desarrollador. Además, Shell está escrito teniendo en cuenta la velocidad de representación y el consumo de memoria.

> [!IMPORTANT]
> Las aplicaciones existentes de iOS y Android pueden adoptar Shell y aprovechar inmediatamente las mejoras de navegación, rendimiento y extensibilidad.

Shell ofrece una interfaz de usuario de navegación bien fundamentada basada en controles flotantes y pestañas. El nivel superior del panel de navegación en una aplicación de Shell es un control flotante:

![Control flotante](shell-images/flyout.png "Flyout")

El siguiente nivel de navegación es la barra de pestañas inferiores:

![Pestañas inferiores](shell-images/bottom-tabs-full.png "Bottom tabs")

Cuando el control flotante no está abierto, la barra de pestañas inferiores se considerará el nivel superior de navegación.

En cada pestaña inferior, el siguiente nivel de navegación es la barra de pestañas superiores, donde cada pestaña corresponde a `ContentPage`:

![Pestañas superiores](shell-images/top-tabs-full.png "Top tabs")

En cada `ContentPage`, se pueden agregar instancias adicionales de `ContentPage` a la pila de navegación, así como quitarlas de ella.

## <a name="bootstrapping-a-shell-application"></a>Arranque de una aplicación de Shell

Una aplicación de Shell se arranca estableciendo la propiedad `MainPage` de la clase `App` en una nueva instancia de `Shell`:

```csharp
namespace TailwindTraders.Mobile
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();

            Forms.SetFlags("Shell_Experimental");
            MainPage = new Shell();
        }
    }
}
```

La clase `Shell` es un archivo XAML que describe la estructura visual de la aplicación.

> [!IMPORTANT]
> Shell es en este momento experimental y solo puede usarse agregando `Forms.SetFlags("Shell_Experimental");` a la clase `App` (antes de crear la instancia `Shell`) o al proyecto de plataforma (antes de invocar el método `Forms.Init`).

## <a name="shell-file-hierarchy"></a>Jerarquía de archivos de Shell

Un archivo de Shell se compone de tres elementos jerárquicos:

- `ShellItem`. Uno o varios elementos en el control flotante. Cada `ShellItem` es un elemento secundario de un elemento `Shell`.
- `ShellSection`. Contenido agrupado, navegable con las pestañas inferiores. Cada `ShellSection` es un elemento secundario de un elemento `ShellItem`.
- `ShellContent`. Las instancias de `ContentPage` de la aplicación, que son navegables con las pestañas superiores. Cada `ShellContent` es un elemento secundario de un elemento `ShellSection`.

Ninguno de estos elementos representan ninguna interfaz de usuario, sino más bien la organización de la estructura visual de la aplicación. Shell tomará estos elementos y generará la interfaz de usuario de navegación para el contenido.

En el XAML siguiente se muestra un ejemplo sencillo de un archivo `Shell`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders"
       x:Class=" TailwindTraders.Shell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    <ShellItem>
</Shell>
```

> [!NOTE]
> Cada `ShellItem` puede establecer también una propiedad `FlyoutIcon` en un `ImageSource`, que se mostrará a la izquierda del título.

Este XAML muestra `HomePage`, porque es el primer elemento de contenido declarado en el archivo de Shell:

![Página principal](shell-images/homepage.png "Home page")

Al pulsar el botón de tres barras aparece el control flotante:

![Control flotante abierto](shell-images/flyout-initial.png "Flyout open")

Se puede aumentar el número de elementos del control flotante agregando más instancias de `ShellItem` al archivo de Shell. Pero tenga en cuenta que `HomePage` se crea durante el inicio de la aplicación y la adición de instancias adicionales de `ShellItem` con este enfoque hará que estas páginas también se creen durante el inicio de la aplicación. Esto también puede evitarse estableciendo la propiedad `ShellContent.ContentTemplate` en `DataTemplate`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders"
       x:Class=" TailwindTraders.Shell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    <ShellItem>
    <ShellContent Title="Profile"
                  ContentTemplate="{DataTemplate local:ProfilePage}" />
</Shell>
```

Con este enfoque, `ProfilePage` solo se crea cuando el usuario navega a él, y no al iniciarse la aplicación. Además, tenga en cuenta que en `ProfilePage`, los objetos `ShellItem` y `ShellSection` se omiten y que la propiedad `Title` se define en la instancia `ShellContent`. Este enfoque conciso requiere menos marcado, ya que Shell proporciona los contenedores lógicos necesarios (sin introducir vistas adicionales al árbol visual).

Además, puede personalizarse la apariencia de cada `ShellItem` estableciendo la propiedad `Shell.ItemTemplate` en `DataTemplate`:

```xaml
<Shell.ItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Title}" />
        </ContentView>
    </DataTemplate>
</Shell.ItemTemplate>          
```

Este código simplemente cambia la posición del texto de cada `ShellItem` en el control flotante. Tenga en cuenta que Shell proporciona las propiedades `Title` (y `Icon`) en el elemento `BindingContext` del objeto `DataTemplate`.

## <a name="flyout"></a>Control flotante

El control flotante es el menú raíz de la aplicación y consta de un encabezado de control flotante y elementos de menú:

![Control flotante anotado](shell-images/flyout-annotated.png "Annotated flyout")

### <a name="flyout-behavior"></a>Comportamiento del control flotante

Al control flotante se tiene acceso mediante el botón de tres barras o deslizando el dedo por el lado de la pantalla. Pero este comportamiento se puede cambiar estableciendo la propiedad `Shell.FlyoutBehavior` en uno de los miembros de la enumeración `FlyoutBehavior`:

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

Al establecer la propiedad `FlyoutBehavior` en `Disabled` se oculta el control flotante, lo que resulta útil cuando solo se tiene un `ShellItem`. Otros valores válidos de `FlyoutBehavior` son `Flyout` (predeterminado) y `Locked`.

### <a name="flyout-header"></a>Encabezado de control flotante

El encabezado de control flotante es el contenido que aparece, opcionalmente, en la parte superior del control flotante y su apariencia se define mediante un objeto `View` que se puede establecer a través del valor de la propiedad `Shell.FlyoutHeader`:

```xaml
<Shell.FlyoutHeader>
    <local:FlyoutHeader />
</Shell.FlyoutHeader>
```

Como alternativa, la apariencia del encabezado de control flotante se puede definir estableciendo la propiedad `Shell.FlyoutHeaderTemplate` en `DataTemplate`:

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <StackLayout HorizontalOptions="Fill"
                     VerticalOptions="Fill"
                     BackgroundColor="White"
                     Padding="16">
            <Label FontSize="Medium"
                   Text="Smart Shopping">
                <Label.Margin>
                    <Thickness Left="8" />
                </Label.Margin>
            </Label>
            <Button Image="photo"
                    Text="By taking a photo">
                <Button.Margin>
                    <Thickness Top="16" />
                </Button.Margin>
            </Button>
            <Button Image="ia"
                    Text="By using AR">
                <Button.Margin>
                    <Thickness Top="8" />
                </Button.Margin>
            </Button>
        </StackLayout>
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

Este XAML se traduce en el siguiente encabezado de control flotante:

![Encabezado de control flotante](shell-images/flyout-header.png "Flyout header")

De forma predeterminada, el encabezado de control flotante se corregirá en el control flotante mientras el contenido que va a continuación se desplazará si hay suficientes elementos. Pero este comportamiento se puede cambiar estableciendo la propiedad `Shell.FlyoutHeaderBehavior` en uno de los miembros de la enumeración `FlyoutHeaderBehavior`:

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

Si se establece el `FlyoutHeaderBehavior` en `CollapseOnScroll`, el control flotante se contrae a medida que se produce el desplazamiento. Otros valores válidos de `FlyoutHeaderBehavior` son `Default`, `Fixed` y `Scroll` (se desplazan con los elementos de menú).

## <a name="menu-items"></a>Elementos de menú

Se puede aumentar el número de elementos del control flotante agregando más instancias `ShellItem`. Aunque también es posible agregar instancias `MenuItem` al control flotante. Esto permite escenarios tales como la navegación a una página idéntica al pasar datos a través de la propiedad `MenuItem.CommandParameter`.

Las instancias `MenuItem` deben agregarse a la colección `Shell.MenuItems`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Views"
       x:Class="TailwindTraders.Shell"
       FlyoutHeaderBehavior="Fixed"
       Title="Tailwind Traders"
       ...>
    <Shell.MenuItems>
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="1"
                  Text="Holiday decorations" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="2"
                  Text="Appliances" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="3"
                  Text="Bathrooms" />
        ...
    </Shell.MenuItems>
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    </ShellItem>    
</Shell>
```

> [!NOTE]
> Cada `MenuItem` puede establecer también una propiedad `Icon` en `FileImageSource`, que se mostrará a la izquierda del texto.

Este XAML se traduce en el siguiente control flotante:

![Control flotante completo](shell-images/flyout-full.png "Full flyout")

Además, puede personalizarse la apariencia de `MenuItem` estableciendo la propiedad `Shell.MenuItemTemplate` en `DataTemplate`:

```xaml
<Shell.MenuItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Text}"
                   FontAttributes="Bold" />
        </ContentView>
    </DataTemplate>
</Shell.MenuItemTemplate>
```

Esto da como resultado que las instancias `MenuItem` tengan su texto representado en negrita:

![Elementos de menú en negrita](shell-images/menuitems-bold.png "Bold menu items")

## <a name="tabs"></a>Pestañas

Las instancias `ShellSection` se representarán como pestañas inferiores, siempre que haya varias instancias de `ShellSection` en un solo `ShellItem`:

```xaml
<ShellItem Title="Bottom Tab Sample"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="AR" Icon="ia.png">
        <ShellContent ContentTemplate="{DataTemplate local:ARPage}"/>
    </ShellSection>
    <ShellSection Title="Photo" Icon="photo.png">
        <ShellContent ContentTemplate="{DataTemplate local:PhotoPage}"/>
    </ShellSection>
</ShellItem>
```

En este ejemplo, las instancias `ShellSection` se representarán como pestañas inferiores:

![Pestañas inferiores](shell-images/tabs-bottom.png "Bottom tabs")

Los elementos de `ShellContent` se representarán como pestañas superiores, siempre que haya varias instancias de `ShellContent` en un solo elemento `ShellSection`:

```xaml
<ShellItem Title="Store Home"
           Shell.TitleView="Store Home"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="Browse Product">
        <ShellContent Title="Featured"
                      ContentTemplate="{DataTemplate local:FeaturedPage}" />
        <ShellContent Title="On Sale"
                      ContentTemplate="{DataTemplate local:SalePage}" />
    </ShellSection>
</ShellItem>
```

En este ejemplo, las instancias `ShellContent` se representarán como pestañas superiores:

![Pestañas superiores](shell-images/tabs-top.png "Top tabs")

El estilo de las pestañas se puede cambiar con estilos XAML o proporcionando un representador personalizado. Por ejemplo, en el ejemplo siguiente se muestra un estilo XAML que establece el color de la pestaña:

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.ShellTabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.ShellTabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.ShellTabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

## <a name="navigation"></a>Navegación

Shell incluye una experiencia de navegación basada en identificadores URI. Los URI ofrecen una experiencia de navegación mejorada que permite la navegación a cualquier página de la aplicación, sin tener que seguir una jerarquía de navegación establecida. También ofrece la posibilidad de navegar hacia atrás sin tener que visitar todas las páginas de la pila de navegación.

Esta navegación basada en identificadores URI se logra con rutas, que son segmentos de URI que se utilizan para navegar dentro de la aplicación. El archivo `Shell` debe declarar un esquema de ruta, un host de ruta y una ruta:

```xaml
<Shell ...
       Route="tailwindtraders"
       RouteHost="www.microsoft.com"
       RouteScheme="app">
    ...
</Shell>
```

Juntos, los valores de las propiedades `RouteScheme`, `RouteHost` y `Route` forman el identificador URI raíz de `app://www.microsoft.com/tailwindtraders`.

Cada elemento de la clase `Shell` también puede definir una propiedad de ruta que se puede usar en la navegación mediante programación.

En el constructor `Shell`, o en cualquier otra ubicación que se ejecute antes de invocar una ruta, se pueden registrar explícitamente rutas adicionales para cualquier página que no esté representada por un elemento de Shell (como las instancias de `MenuItem`):

```csharp
Routing.RegisterRoute("productcategory", typeof(ProductCategoryPage));
```

### <a name="implementing-navigation"></a>Implementación de la navegación

Los elementos de menú exponen una propiedad `Command` que se puede usar para implementar la navegación:

```csharp
public ICommand ProductTypeCommand { get; } = new Command<string>(NavigateToProductType);

static void NavigateToProductType(string typeId)
{
  (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"app:///tailwindtraders/productcategory?id={typeId}", true);
}
```

Para invocar la navegación, hay que obtener una referencia a la aplicación `Shell` mediante la propiedad `MainPage` de la clase `Application`. Seguidamente, se puede invocar la navegación mediante una llamada al método `GoToAsync` y pasar un URI válido como argumento. El método `GoToAsync` navega mediante un objeto `ShellNavigationState`, que se construirá a partir de un `string` o un `Uri`.

### <a name="passing-data"></a>Pasar datos

Se pueden pasar datos de una página a otra mediante parámetros de cadena de consulta. Shell establecerá los valores de los parámetros de la cadena de consulta en `ContentPage` o ViewModel cuando se agreguen atributos de propiedad de consulta a la clase:

```csharp
[QueryProperty("TypeID", "id")]
public partial class ProductCategoryPage : ContentPage
{
    private string _typeId;

    public ProductCategoryPage()
    {
        InitializeComponent();

        BindingContext = new ProductCategoryViewModel();
    }

    public string TypeID
    {
        get => _typeId;
        set => MyLabel.Text = value;
    }
}
```

El atributo `QueryProperty` especifica que el elemento `TypeID` recibirá los datos pasados en el parámetro de cadena de consulta `id` desde el URI en la llamada al método `GoToAsync`.

### <a name="intercepting-navigation"></a>Intercepción de navegación

Shell ofrece la posibilidad de enlazar la ruta de navegación antes de que se complete y cuando se haya completado. Esto puede llevarse a cabo mediante el registro de controladores de eventos de los eventos `Navigating` y `Navigated`, respectivamente:

```xaml
<Shell ...
       Navigating="OnShellNavigating">
    ...
</Shell>
```

```csharp
void OnShellNavigating(object sender, ShellNavigatingEventArgs e)
{
  if (...)
  {
    e.Cancel(); // Cancel the navigation
  }
}
```

La clase `ShellNavigatingEventArgs` ofrece las siguientes propiedades:

| Propiedad. | Tipo | Descripción |
|---|---|---|
| Current | `ShellNavigationState` | Identificador URI base de la página actual. |
| Origen | `ShellNavigationState` | Identificador URI que representa el origen de la navegación. |
| Destino | `ShellNavigationSource`  | Identificador URI que representa el destino de la navegación. |
| CanCancel  | `bool` | Valor que indica si es posible cancelar la navegación. |
| Cancelado  | `bool` | Valor que indica si la navegación se ha cancelado. |

Además, la clase `ShellNavigatingEventArgs` ofrece un método `Cancel`.

La clase `ShellNavigatedEventArgs` ofrece las siguientes propiedades:

| Propiedad. | Tipo | Descripción |
|---|---|---|
| Current | `ShellNavigationState` | Identificador URI de la página actual. |
| Anterior| `ShellNavigationState` | Identificador URI de la página anterior. |
| Origen  | `ShellNavigationSource` | Identificador URI que representa el origen de la navegación. |

Además, Shell ofrece un método `OnBackButtonPressed` que se puede invalidar y permite interceptar la pulsación de un botón Atrás.

## <a name="related-links"></a>Vínculos relacionados

- [Tailwind Traders (ejemplo)](https://github.com/Microsoft/TailwindTraders-Mobile)
