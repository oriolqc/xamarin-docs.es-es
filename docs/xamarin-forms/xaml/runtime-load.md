---
title: Cargar XAML en tiempo de ejecución en Xamarin.Forms
description: Puede cargar y analizar en tiempo de ejecución con los métodos de extensión LoadFromXaml XAML.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: ce8ba32a1a6a1f69033615558c7ebf15d41e70fe
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61178051"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Cargar XAML en tiempo de ejecución en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)

El [ `Xamarin.Forms.Xaml` ](xref:Xamarin.Forms.Xaml) espacio de nombres incluye dos [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensión que se pueden utilizar para cargar y analizar XAML en tiempo de ejecución.

## <a name="background"></a>Fondo

Cuando se construye una clase de XAML de Xamarin.Forms, el [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) indirectamente se llama al método. Esto ocurre porque el archivo de código subyacente para un XAML llama a la clase el `InitializeComponent` método desde su constructor:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Cuando Visual Studio compila un proyecto que contiene un archivo XAML, analiza el archivo XAML para generar un C# archivo de código (por ejemplo, **MainPage.xaml.g.cs**) que contiene la definición de la `InitializeComponent` método:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

El `InitializeComponent` llamadas al método el [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método para extraer el archivo XAML (o el binario compilado) de la biblioteca .NET Standard. Después de la extracción, inicializa todos los objetos definidos en el archivo XAML, se conectan todos juntos en las relaciones de elementos primarios y secundarios, adjunta los controladores de eventos definidos en el código para los eventos que se establece en el archivo XAML y establece el árbol resultante de objetos como el contenido de la página.

## <a name="loading-xaml-at-runtime"></a>Cargar XAML en tiempo de ejecución

El [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos son `public`, por lo tanto, pueden llamarse desde las aplicaciones de Xamarin.Forms para cargar y analizar XAML en tiempo de ejecución. Esto permite escenarios como una aplicación XAML de descarga de un servicio web, crear la vista necesaria desde el XAML y mostrarla en la aplicación.

> [!WARNING]
> Cargar XAML en tiempo de ejecución tiene un gran costo de rendimiento y, por lo general debe evitarse.

El ejemplo de código siguiente muestra un uso sencillo:

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

En este ejemplo, un [ `Button` ](xref:Xamarin.Forms.Button) se crea la instancia, con su [ `Text` ](xref:Xamarin.Forms.Button.Text) define un valor de propiedad que se establece desde el XAML en el `string`. El `Button` , a continuación, se agrega a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) que se ha definido en el XAML para la página.

> [!NOTE]
> El [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensión permiten especificar un argumento de tipo genérico. Sin embargo, es no suele ser necesario especificar el argumento de tipo, ya que será deriva del tipo de la instancia de su funcionamiento en.

El [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método puede utilizarse para inflado cualquier XAML, con el siguiente ejemplo se infla un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) y, a continuación, vaya a él:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Tener acceso a elementos

Cargar XAML en tiempo de ejecución con el [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método no permite el acceso fuertemente tipado a los elementos XAML que se ha especificado los nombres de objeto en tiempo de ejecución (mediante `x:Name`). Sin embargo, estos elementos XAML se pueden recuperar mediante el [ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) método y, a continuación, acceder a ellos según sea necesario:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

En este ejemplo, el XAML para un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) se aumenta. Este XAML incluye una [ `Label` ](xref:Xamarin.Forms.Label) denominado `monkeyName`, que se recupera mediante la [ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) método antes de que tenga [ `Text` ](xref:Xamarin.Forms.Label.Text) se establece la propiedad.

## <a name="related-links"></a>Vínculos relacionados

- [LoadRuntimeXAML (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)
