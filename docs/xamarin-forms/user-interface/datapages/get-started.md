---
title: Introducción a DataPages
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: d5e73839f889234c816bfff08f3e46dade8dffc9
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846846"
---
# <a name="getting-started-with-datapages"></a>Introducción a DataPages

![](~/media/shared/preview.png "Esta API está actualmente en vista previa")

> [!IMPORTANT]
> DataPages requiere un [Xamarin.Forms tema](~/xamarin-forms/user-interface/themes/index.md) referencia a presentar.


Para empezar a compilar una página de unidad de datos simple con la vista previa DataPages, siga estos pasos. Esta usa demostración un estilo codificado de forma rígida ("eventos") en la vista previa de compilaciones que solo funciona con el formato JSON específico en el código.

[![](get-started-images/demo-sml.png "Aplicación de ejemplo DataPages")](get-started-images/demo.png#lightbox "aplicación de ejemplo DataPages")

## <a name="1-add-nuget-packages"></a>1. Agregar paquetes de NuGet

Agregue estos paquetes de Nuget para los proyectos de biblioteca y aplicación Xamarin.Forms .NET estándar:

* Xamarin.Forms.Pages
* Xamarin.Forms.Theme.Base
* Una implementación de tema Nuget (p. ej. Xamarin.Forms.Themes.Light)

## <a name="2-add-theme-reference"></a>2. Agregar referencia de tema

En el **App.xaml** , agregue un personalizado `xmlns:mytheme` para el tema y asegúrese de que el tema se combinó de diccionario de recursos de la aplicación:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
  xmlns:mytheme="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light"
  x:Class="DataPagesDemo.App">
    <Application.Resources>
        <ResourceDictionary MergedWith="mytheme:LightThemeResources" />
    </Application.Resources>
</Application>
```

**Importante:** también debe seguir los pasos necesarios para [cargar ensamblados de tema (abajo)](#loadtheme) agregando código reutilizable en el iOS `AppDelegate` y Android `MainActivity`. Esto se mejorará en una versión futura de vista previa.


## <a name="3-add-a-xaml-page"></a>3. Agregar una página XAML

Agregue una nueva página XAML para la aplicación de Xamarin.Forms y *cambie la clase base* de `ContentPage` a `Xamarin.Forms.Pages.ListDataPage`. Esto debe hacerse en C# y el código XAML:

**Archivo de C#**

```csharp
public partial class SessionDataPage : Xamarin.Forms.Pages.ListDataPage // was ContentPage
{
  public SessionDataPage ()
  {
    InitializeComponent ();
  }
}
```

**Archivo XAML**

Además de cambiar el elemento raíz para `<p:ListDataPage>` el espacio de nombres personalizado `xmlns:p` también debe haberse agregado:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**Subclase de aplicación**

Cambiar el `App` constructor de clase para que la `MainPage` está establecido en un `NavigationPage` que contiene la nueva `SessionDataPage`. Una página de navegación *debe* utilizarse.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. Agregar el origen de datos

Eliminar el `Content` elemento y reemplazarlo con un `p:ListDataPage.DataSource` para rellenar la página con datos. En el ejemplo siguiente Json remoto se está cargando el archivo de datos desde una dirección URL.

**Nota:** la vista previa *requiere* un `StyleClass` atributo para proporcionar sugerencias de representación para el origen de datos. El `StyleClass="Events"` hace referencia a un diseño que está predefinido en la vista previa y contiene estilos *codificado de forma rígida* para que coincida con el origen de datos JSON que se va a usar.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage"
             Title="Sessions" StyleClass="Events">

    <p:ListDataPage.DataSource>
        <p:JsonDataSource Source="http://demo3143189.mockable.io/sessions" />
    </p:ListDataPage.DataSource>

</p:ListDataPage>
```

**Datos JSON**

Un ejemplo de los datos JSON desde el [origen demostración](http://demo3143189.mockable.io/sessions) se muestra a continuación:

```json
[{
  "end": "2016-04-27T18:00:00Z",
  "start": "2016-04-27T17:15:00Z",
  "abstract": "The new Apple TV has been released, and YOU can be one of the first developers to write apps for it. To make things even better, you can build these apps in C#! This session will introduce the basics of how to create a tvOS app with Xamarin, including: differences between tvOS and iOS APIs, TV user interface best practices, responding to user input, as well as the capabilities and limitations of building apps for a television. Grab some popcorn—this is going to be good!",
  "title": "As Seen On TV … Bringing C# to the Living Room",
  "presenter": "Matthew Soucoup",
  "biography": "Matthew is a Xamarin MVP and Certified Xamarin Developer from Madison, WI. He founded his company Code Mill Technologies and started the Madison Mobile .Net Developers Group.  Matt regularly speaks on .Net and Xamarin development at user groups, code camps and conferences throughout the Midwest. Matt gardens hot peppers, rides bikes, and loves Wisconsin micro-brews and cheese.",
  "image": "http://i.imgur.com/ASj60DP.jpg",
  "avatar": "http://i.imgur.com/ASj60DP.jpg",
  "room": "Crick"
}]
```

## <a name="4-run"></a>4. Ejecute las aplicaciones.

Deben dar como resultado de los pasos anteriores en una página de datos de trabajo:

[![](get-started-images/demo-sml.png "Aplicación de ejemplo DataPages")](get-started-images/demo.png#lightbox "aplicación de ejemplo DataPages")

Esto funciona porque el estilo pregenerado **"Eventos"** existe en el paquete Nuget de tema claro y tiene los estilos definidos que coincida con el origen de datos (p. ej. "title", "image", "presentador").

Los "eventos" `StyleClass` está diseñado para mostrar el `ListDataPage` control con un personalizado `CardView` control que es definido en Xamarin.Forms.Pages. El `CardView` control tiene tres propiedades: `ImageSource`, `Text`, y `Detail`. El tema está codificado para enlazar tres campos del origen de datos (desde el archivo JSON) a estas propiedades para su presentación.

## <a name="5-customize"></a>5. Personalizar

El estilo heredado puede reemplazarse especificando una plantilla y usar enlaces de orígenes de datos. El código XAML siguiente declara una plantilla personalizada para cada fila con el nuevo `ListItemControl` y `{p:DataSourceBinding}` sintaxis que se incluye en el **Xamarin.Forms.Pages** Nuget:

```xaml
<p:ListDataPage.DefaultItemTemplate>
    <DataTemplate>
        <ViewCell>
            <p:ListItemControl
                Title="{p:DataSourceBinding title}"
                Detail="{p:DataSourceBinding room}"
                ImageSource="{p:DataSourceBinding image}"
                DataSource="{Binding Value}"
                HeightRequest="90"
            >
            </p:ListItemControl>
        </ViewCell>
    </DataTemplate>
</p:ListDataPage.DefaultItemTemplate>
```

Proporcionando un `DataTemplate` este código invalida el `StyleClass` y en su lugar, usa el diseño predeterminado para un `ListItemControl`.

[![](get-started-images/custom-sml.png "Aplicación de ejemplo DataPages")](get-started-images/custom.png#lightbox "aplicación de ejemplo DataPages")

Los programadores que prefieren C# para XAML puede crear datos de enlaces de origen demasiado (no olvide incluir un `using Xamarin.Forms.Pages;` instrucción):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```


Es un poco más trabajo para crear temas desde el principio (vea la [guía temas](~/xamarin-forms/user-interface/themes/index.md)) pero las versiones futuras de vista previa hacerlo más fácil hacer.


## <a name="troubleshooting"></a>Solución de problemas

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>No se pudo cargar el archivo o ensamblado 'Xamarin.Forms.Theme.Light' o uno de sus dependencias

En la versión preliminar, es podrán que algunos temas no se pueda cargar en tiempo de ejecución. Agregue el código que se muestra a continuación, en los proyectos pertinentes para corregir este error.

**iOS**

En el **AppDelegate.cs** agregue las siguientes líneas después de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

En el **MainActivity.cs** agregue las siguientes líneas después de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
