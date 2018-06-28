---
title: 'Hello, Xamarin.Forms Multiscreen: análisis detallado'
description: En este artículo se presenta la navegación de páginas y el enlace de datos en una aplicación Xamarin.Forms y se muestra su uso en una aplicación multiplataforma de varias pantallas.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: e4faa36c-6600-48c0-94c4-b4431103a4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 1c7edff3c71b9d7530b2acf21acaa06149156d43
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2018
ms.locfileid: "35242240"
---
# <a name="xamarinforms-multiscreen-deep-dive"></a>Hello, Xamarin.Forms Multiscreen: análisis detallado

En [Xamarin.Forms Multiscreen: inicio rápido](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/quickstart.md), se amplió la aplicación Phoneword para incluir una segunda pantalla que realiza un seguimiento del historial de llamadas para la aplicación. En este artículo se revisa lo que se ha compilado con el fin de comprender la navegación de páginas y el enlace de datos en una aplicación de Xamarin.Forms.

## <a name="navigation"></a>Navegación

Xamarin.Forms proporciona un modelo de navegación integrado que administra la navegación y la experiencia del usuario de una pila de páginas. Este modelo implementa una pila de objetos `Page` en la que el último en entrar es el primero en salir (LIFO). Para pasar de una página a otra, una aplicación insertará una nueva página en esta pila. Para volver a la página anterior, la aplicación sacará la página actual de la pila.

Xamarin.Forms tiene una clase [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) que administra la pila de objetos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). La clase `NavigationPage` también agregará una barra de navegación en la parte superior de la página que muestra un título y un botón <span class="uiitem">Atrás</span> adecuado para la plataforma para volver a la página anterior. En el ejemplo de código siguiente se muestra cómo ajustar una `NavigationPage` alrededor de la primera página de una aplicación:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new MainPage ());
}
```

Todas las instancias [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) tienen una propiedad [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) que expone métodos para modificar la pila de la página. Solo se deberían invocar estos métodos si la aplicación incluye una [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). Para navegar a la `CallHistoryPage`, es necesario invocar el método [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) como se muestra en el ejemplo de código siguiente:

```csharp
async void OnCallHistory(object sender, EventArgs e)
{
    await Navigation.PushAsync (new CallHistoryPage ());
}
```

Esto hace que el nuevo objeto `CallHistoryPage` se inserte en la pila de navegación. Para volver mediante programación a la página original, el objeto `CallHistoryPage` debe invocar el método [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), como se muestra en el ejemplo de código siguiente:

```csharp
await Navigation.PopAsync();
```

Pero en la aplicación Phoneword este código no es necesario porque la clase [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) agrega una barra de navegación en la parte superior de la página que incluye un botón <span class="uiitem">Atrás</span> adecuado para la plataforma para volver a la página anterior.

## <a name="data-binding"></a>Enlace de datos

El enlace de datos se usa para simplificar la forma en que una aplicación de Xamarin.Forms muestra e interactúa con sus datos. Establece una conexión entre la interfaz de usuario y la aplicación subyacente. La clase [`BindableObject`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) contiene gran parte de la infraestructura para admitir el enlace de datos.

El enlace de datos define la relación entre dos objetos. El objeto de *origen* proporcionará los datos. El objeto de *destino* usa (y, a menudo, muestra) los datos del objeto de origen. En la aplicación Phoneword, el destino de enlace es el control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que muestra los números de teléfono, mientras que la colección `PhoneNumbers` es el origen de enlace.

La colección `PhoneNumbers` se declara e inicializa en la clase `App`, como se muestra en el ejemplo de código siguiente:

```csharp
public partial class App : Application
{
   public static List<string> PhoneNumbers { get; set; }

   public App ()
   {
     PhoneNumbers = new List<string>();
     ...
   }
   ...
}
```

La instancia [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) se declara e inicializa en la clase `CallHistoryPage`, como se muestra en el ejemplo de código siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    ...
    <ContentPage.Content>
       ...
       <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
       ...
    </ContentPage.Content>
</ContentPage>
```

En este ejemplo, el control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) mostrará la colección de datos `IEnumerable` a la que se enlaza la propiedad [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView.ItemsSource/). La colección de datos puede ser objetos de cualquier tipo, pero de forma predeterminada, `ListView` usará el método `ToString` de cada elemento para mostrar ese elemento. La extensión de marcado [`x:Static`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) se usa para indicar que la propiedad `ItemsSource` se enlazará a la propiedad estática `PhoneNumbers` de la clase `App`, que puede estar ubicada en el espacio de nombres. `local`.

Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos). Para más información sobre las extensiones de marcado, vea [Extensiones de marcado para el lenguaje XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="additional-concepts-introduced-in-phoneword"></a>Otros conceptos presentados en Phoneword

El [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) es responsable de mostrar una colección de elementos en la pantalla. Cada elemento en el `ListView` se encuentra en una sola celda. Para más información sobre el uso del control `ListView`, vea [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="summary"></a>Resumen

Este artículo introdujo la navegación de páginas y el enlace de datos en una aplicación de Xamarin.Forms, y demostró su uso en una aplicación multiplataforma de varias pantallas.
