---
title: Introducción a Xamarin.Forms Shell
description: Xamarin.Forms Shell proporciona las características fundamentales que necesitan la mayoría de las aplicaciones, como una experiencia de usuario común de navegación, un esquema de navegación basado en URI y un controlador de búsqueda integrado.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f6662f20485c6671edcb5a1654569cdd8498607e
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005202"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de aplicaciones móviles, como por ejemplo:

- Un único lugar para describir la jerarquía visual de una aplicación.
- Una interfaz de usuario de navegación común.
- Un esquema de navegación basado en URI que permite la navegación a cualquier página de la aplicación.
- Un controlador de búsqueda integrado.

Además, las aplicaciones de Shell se benefician de una mayor velocidad de representación y un consumo reducido de memoria.

> [!IMPORTANT]
> El shell de Xamarin.Forms solo está disponible en iOS y Android. Las aplicaciones existentes de iOS y Android pueden adoptar Shell y aprovechar inmediatamente las mejoras de navegación, rendimiento y extensibilidad.

## <a name="shell-navigation-experience"></a>Experiencia de navegación de Shell

Shell ofrece una experiencia de navegación bien fundamentada basada en controles flotantes y pestañas. El nivel superior del panel de navegación en una aplicación de Shell es un control flotante:

[![Captura de pantalla de un control flotante de Shell en iOS y Android](introduction-images/flyout.png "Control flotante de Shell")](introduction-images/flyout-large.png#lightbox "Shell flyout")

Al seleccionar un elemento de control flotante, se selecciona y muestra la pestaña inferior que representa el elemento:

[![Captura de pantalla de las pestañas inferiores de Shell en iOS y Android](introduction-images/monkeys.png "Pestañas inferiores de Shell")](introduction-images/monkeys-large.png#lightbox "Shell bottom tabs")

> [!NOTE]
> Cuando el control flotante no está abierto, la barra de pestañas inferior se considera el nivel superior de navegación en la aplicación.

Cada pestaña muestra un objeto [ `ContentPage`](xref:Xamarin.Forms.ContentPage). Sin embargo, si una pestaña inferior contiene más de una página, las páginas son navegables mediante la barra de pestañas superior:

[![Captura de pantalla de pestañas superiores de Shell en iOS y Android](introduction-images/cats.png "Pestañas superiores de Shell")](introduction-images/cats-large.png#lightbox "Shell top tabs")

Dentro de cada pestaña, se puede navegar a objetos adicionales [`ContentPage`](xref:Xamarin.Forms.ContentPage):

[![Captura de pantalla de navegación entre páginas de Shell en iOS y Android](introduction-images/cat-details.png "Navegación por aplicaciones de Shell")](introduction-images/cat-details-large.png#lightbox "Shell app navigation")

## <a name="subclassing-the-shell-class"></a>Creación de subclases de la clase de Shell

Un objeto `Shell` en subclase describe la jerarquía visual de una aplicación de Shell y consta de tres objetos jerárquicos principales:

- `FlyoutItem`, que representa uno o varios elementos en el control flotante. Cada objeto `FlyoutItem` es un elemento secundario del objeto `Shell`.
- `Tab`, que representa contenido agrupado, navegable mediante las pestañas inferiores. Cada objeto `Tab` es un elemento secundario de un objeto `FlyoutItem`.
- `ShellContent`, que representa el objeto `ContentPage` en la aplicación. Cada objeto `ShellContent` es un elemento secundario de un objeto `Tab`. Cuando hay más de un objeto `ShellContent` en un objeto `Tab`, los objetos serán navegables mediante las pestañas superiores.

Ninguno de estos objetos representa ninguna interfaz de usuario, sino más bien la organización de la jerarquía visual de la aplicación. Shell tomará estos elementos y generará la interfaz de usuario de navegación del contenido.

> [!NOTE]
> La clase `FlyoutItem` es un alias de la clase `ShellItem`, y la clase `Tab` es un alias de la clase `ShellSection`. Estos alias están diseñados para facilitar el consumo de la API.

El siguiente XAML muestra un ejemplo de un objeto `Shell` en subclase:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png">
                <views:CatsPage />
            </ShellContent>
            <ShellContent Title="Dogs"
                          Icon="dog.png">
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png">
            <views:MonkeysPage />
        </ShellContent>
        <ShellContent Title="Elephants"
                      Icon="elephant.png">  
            <views:ElephantsPage />
        </ShellContent>
        <ShellContent Title="Bears"
                      Icon="bear.png">
            <views:BearsPage />
        </ShellContent>
    </FlyoutItem>
    ...
</Shell>
```

Cuando se ejecuta, este XAML muestra el objeto `CatsPage`, porque es el primer elemento de contenido declarado en el objeto `Shell` en subclase:

[![Captura de pantalla de una aplicación de Shell en iOS y Android](introduction-images/cats.png "Aplicación Shell")](introduction-images/cats-large.png#lightbox "Shell app")

Al presionar el icono de tres barras, o al deslizar el dedo desde la izquierda, se muestra el control flotante:

[![Captura de pantalla de un control flotante de Shell en iOS y Android](introduction-images/flyout-reduced.png "Control flotante de Shell")](introduction-images/flyout-reduced-large.png#lightbox "Shell flyout")

> [!IMPORTANT]
> En una aplicación de Shell, cada [ `ContentPage`](xref:Xamarin.Forms.ContentPage) que es un elemento secundario de un objeto `ShellContent` se crea durante el inicio de la aplicación. Agregar objetos `ShellContent` adicionales mediante esta estrategia dará lugar a la creación de páginas adicionales durante el inicio de la aplicación, lo que puede conducir a una experiencia de inicio deficiente. Sin embargo, Shell también es capaz de crear páginas a petición, en respuesta a la navegación. Para más información, consulte [Carga eficiente de páginas](tabs.md#efficient-page-loading) en la guía [Pestañas de Xamarin.Forms Shell](tabs.md).

## <a name="bootstrapping-a-shell-application"></a>Arranque de una aplicación de Shell

Para arrancar una aplicación de Shell, se establece la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) de la clase `App` en un objeto `Shell` en subclase:

```csharp
namespace Xaminals
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        ...
    }
}
```

En este ejemplo, la clase `AppShell` es un archivo XAML que se deriva de la clase `Shell`, que describe la jerarquía visual de la aplicación.

## <a name="shell-appearance"></a>Apariencia de Shell

La clase `Shell` define las siguientes propiedades que controlan la apariencia de una aplicación de Shell:

- `BackgroundColor`, de tipo `Color`, una propiedad adjunta que define el color de fondo en el cromo de Shell. El color no se rellena detrás del contenido de Shell.
- `DisabledColor`, de tipo `Color`, una propiedad adjunta que define el color para sombrear el texto y los iconos que están deshabilitados.
- `ForegroundColor`, de tipo `Color`, una propiedad adjunta que define el color para sombrear el texto y los iconos.
- `TitleColor`, de tipo `Color`, una propiedad adjunta que define el color usado para el título de la página actual.
- `UnselectedColor`, de tipo `Color`, una propiedad adjunta que define el color usado para el texto y los iconos no seleccionados en el cromo de Shell.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

Además, estas propiedades se pueden establecer mediante Hojas de estilo CSS. Para más información, consulte [Propiedades específicas de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="shell-content-layout"></a>Diseño de contenido de Shell

La clase `Shell` define las siguientes propiedades que afectan al diseño del contenido de la aplicación de Shell:

- `NavBarIsVisible`, de tipo `boolean`, una propiedad adjunta que define si la barra de navegación debe estar visible cuando se presenta una página. Esta propiedad debe establecerse en una página, y su valor predeterminado es `true`.
- `TabBarIsVisible`, de tipo `bool`, una propiedad adjunta que define si la barra de pestañas debe estar visible cuando se presenta una página. Esta propiedad debe establecerse en una página, y su valor predeterminado es `true`.
- `TitleView`, de tipo `View`, una propiedad adjunta que define el objeto `TitleView` para una página. Esta propiedad debe establecerse en una página.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Propiedades específicas de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
