---
title: Mostrar elementos emergentes
description: 'Xamarin.Forms proporciona dos elementos de la interfaz de usuario similares a elementos emergentes: una alerta y una hoja de acción. En este artículo, se muestra cómo usar las API de hoja de acción y alerta para realizar preguntas sencillas a los usuarios y guiarlos en la realización de tareas.'
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 36a253a46b76e4c883639ca683a8eab64700b064
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329317"
---
# <a name="displaying-pop-ups"></a>Mostrar elementos emergentes

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)

_Xamarin.Forms proporciona dos elementos de la interfaz de usuario similares a elementos emergentes: una alerta y una hoja de acción. En este artículo, se muestra cómo usar las API de hoja de acción y alerta para realizar preguntas sencillas a los usuarios y guiarlos en la realización de tareas._

Mostrar una alerta o pedir al usuario que seleccione una opción es una tarea común de la interfaz de usuario. Xamarin.Forms tiene dos métodos en la clase [`Page`](xref:Xamarin.Forms.Page) para interactuar con el usuario mediante un elemento emergente: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) y [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*). Se representan con controles nativos adecuados en cada plataforma.

## <a name="displaying-an-alert"></a>Mostrar una alerta

Todas las plataformas compatibles con Xamarin.Forms tienen una ventana emergente modal para mostrar alertas al usuario o realizar preguntas sencillas. Para mostrar estas alertas en Xamarin.Forms, use el método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) en cualquier elemento [`Page`](xref:Xamarin.Forms.Page). En la siguiente línea de código, se muestra un mensaje sencillo al usuario:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Cuadro de diálogo de alerta con un botón")

En este ejemplo, no se recopila información del usuario. La alerta se muestra modalmente y, después de cerrarla, el usuario sigue interactuando con la aplicación.

El método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) también se puede usar para capturar la respuesta de un usuario mediante la presentación de dos botones y la devolución de un elemento `boolean`. Para obtener una respuesta de una alerta, agregue texto para los dos botones y use `await` para esperar el método. Cuando el usuario seleccione una de las opciones, se devolverá la respuesta al código. Observe las palabras clave `async` y `await` en el código de ejemplo siguiente:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "Cuadro de diálogo de alerta con dos botones")](pop-ups-images/alert2.png#lightbox "Alert Dialog with Two Buttons")

## <a name="guiding-users-through-tasks"></a>Guiar a los usuarios en la realización de tareas

El elemento [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) es un elemento de interfaz de usuario común en iOS. El método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) de Xamarin.Forms le permite incluir este control en aplicaciones multiplataforma, lo que representa alternativas nativas en Android y UWP.

Para mostrar una hoja de acción, use `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) en cualquier elemento [`Page`](xref:Xamarin.Forms.Page) y pase el mensaje y las etiquetas de botón como cadenas. Este método devuelve la etiqueta de cadena del botón en el que hizo clic el usuario. A continuación, se muestra un ejemplo sencillo:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Cuadro de diálogo de ActionSheet")

El botón `destroy` se representa de forma distinta y puede dejarse como `null`, o bien se puede especificar como el tercer parámetro de cadena. En el ejemplo siguiente, se usa el botón `destroy`:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Cuadro de diálogo de hoja de acción con el botón Destruir")](pop-ups-images/action2.png#lightbox "Action Sheet Dialog with Destroy Button")

## <a name="summary"></a>Resumen

En este artículo, se muestra cómo usar las API de hoja de acción y alerta para realizar preguntas sencillas a los usuarios y guiarlos en la realización de tareas. Xamarin.Forms tiene dos métodos en la clase [`Page`](xref:Xamarin.Forms.Page) para interactuar con el usuario mediante una ventana emergente ([`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) y [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)) y ambos se representan con controles nativos correspondientes en cada plataforma.



## <a name="related-links"></a>Vínculos relacionados

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
