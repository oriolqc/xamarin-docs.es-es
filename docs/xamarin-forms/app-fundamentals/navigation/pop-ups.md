---
title: Visualización de elementos emergentes
description: 'Xamarin.Forms proporciona dos elementos de interfaz de usuario similar a registro emergente: una alerta y una hoja de acción. En este artículo muestra cómo utilizar la hoja de alerta y la acción de las API de pedir a los usuarios preguntas sencillas y guiar a los usuarios a través de tareas.'
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 156c2f9dca47a7755d4f810d7921a05662388ded
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996719"
---
# <a name="displaying-pop-ups"></a>Visualización de elementos emergentes

_Xamarin.Forms proporciona dos elementos de interfaz de usuario similar a registro emergente: una alerta y una hoja de acción. En este artículo muestra cómo utilizar la hoja de alerta y la acción de las API de pedir a los usuarios preguntas sencillas y guiar a los usuarios a través de tareas._

Mostrar una alerta o pedirle que tomar una decisión es una tarea común de interfaz de usuario. Xamarin.Forms tiene dos métodos el [ `Page` ](xref:Xamarin.Forms.Page) clase para interactuar con el usuario a través de una ventana emergente: [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) y [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*). Se representan con los controles nativos adecuados en cada plataforma.

## <a name="displaying-an-alert"></a>Mostrar una alerta

Todas las plataformas compatibles con Xamarin.Forms tienen una ventana emergente modal para avisar al usuario o formular preguntas sencillas de ellos. Para mostrar estas alertas en Xamarin.Forms, use el [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) método en cualquier [ `Page` ](xref:Xamarin.Forms.Page). La siguiente línea de código muestra un mensaje simple para el usuario:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Cuadro de diálogo de alerta con un botón")

En este ejemplo no recopila información del usuario. La alerta se muestra modalmente y una vez descartado el usuario continúa interactuando con la aplicación.

El [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) método también se puede utilizar para capturar la respuesta de un usuario presenta dos botones y devolviendo un `boolean`. Para obtener una respuesta de una alerta, escriba un texto de ambos botones y `await` el método. Después de que el usuario selecciona una de las opciones que se devolverá la respuesta a su código. Tenga en cuenta la `async` y `await` palabras clave en el código de ejemplo siguiente:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "cuadro de diálogo con dos botones de alerta")](pop-ups-images/alert2.png#lightbox "cuadro de diálogo con dos botones de alertas")

## <a name="guiding-users-through-tasks"></a>Fundamentales a los usuarios a través de tareas

El [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) es un elemento de interfaz de usuario comunes en iOS. Xamarin.Forms [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*) método le permite incluir este control en las aplicaciones entre plataformas, representación nativas alternativas en Android y UWP.

Para mostrar una hoja de acción, `await` [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*) en cualquier [ `Page` ](xref:Xamarin.Forms.Page), pasando el mensaje y botón etiquetas como cadenas. El método devuelve la etiqueta de cadena del botón que se hizo clic el usuario. Aquí se muestra un ejemplo sencillo:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Cuadro de diálogo como ActionSheet")

El `destroy` botón se representa de forma diferente a los demás y puede dejarse `null` o se ha especificado como el tercer parámetro de cadena. En el ejemplo siguiente se usa el `destroy` botón:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "cuadro de diálogo de hoja de acción con el botón de destruir")](pop-ups-images/action2.png#lightbox "cuadro de diálogo de hoja de acción con el botón de destrucción")

## <a name="summary"></a>Resumen

En este artículo se muestra mediante la hoja de alerta y la acción de las API de pedir a los usuarios preguntas sencillas y guiar a los usuarios a través de tareas. Xamarin.Forms tiene dos métodos el [ `Page` ](xref:Xamarin.Forms.Page) clase para interactuar con el usuario a través de una ventana emergente: [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) y [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*), y son ambos se representan con los controles nativos adecuados en cada plataforma.



## <a name="related-links"></a>Vínculos relacionados

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
