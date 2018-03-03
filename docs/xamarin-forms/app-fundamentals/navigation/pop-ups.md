---
title: Mostrar elementos emergentes
description: "Xamarin.Forms ofrece dos elementos de interfaz de usuario de emergente up similar: una alerta así como una hoja de acción. Este artículo muestra cómo utilizar la hoja de alerta y la acción de las API que se va a pedir a los usuarios preguntas sencillas como guiar a los usuarios a través de tareas."
ms.topic: article
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 957db750b852b40daf1556e8dc8f7ba18e022dba
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="displaying-pop-ups"></a>Mostrar elementos emergentes

_Xamarin.Forms ofrece dos elementos de interfaz de usuario de emergente up similar: una alerta así como una hoja de acción. Este artículo muestra cómo utilizar la hoja de alerta y la acción de las API que se va a pedir a los usuarios preguntas sencillas como guiar a los usuarios a través de tareas._

Mostrar una alerta o pedir a un usuario para tomar una decisión es una tarea común de la interfaz de usuario. Xamarin.Forms tiene dos métodos el [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) clase para interactuar con el usuario a través de una ventana emergente: [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) y [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/). Se representan con adecuado controles nativos en cada plataforma.

## <a name="displaying-an-alert"></a>Mostrar una alerta

Todas las plataformas compatibles con Xamarin.Forms tienen un elemento emergente modal para alertar al usuario o formular preguntas sencillas de ellos. Para mostrar estas alertas de Xamarin.Forms, use la [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) método en cualquier [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). La siguiente línea de código muestra un mensaje sencillo para el usuario:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Cuadro de diálogo alerta con un botón")

En este ejemplo no recopilar información del usuario. La alerta se muestra de forma modal y una vez que descarta el usuario continúa interactuando con la aplicación.

El [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) método también se puede utilizar para capturar la respuesta del usuario, presenta dos botones y se devuelve un `boolean`. Para obtener una respuesta de una alerta, escriba un texto de ambos botones y `await` el método. Cuando el usuario selecciona una de las opciones se devolverá la respuesta a su código. Tenga en cuenta el `async` y `await` palabras clave en el código de ejemplo siguiente:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[ ![DisplayAlert](pop-ups-images/alert2-sml.png "cuadro de diálogo con dos botones de alerta")](pop-ups-images/alert2.png "cuadro de diálogo con dos botones de alerta")

## <a name="guiding-users-through-tasks"></a>Orientación para los usuarios a través de tareas

El [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) es un elemento de interfaz de usuario comunes en iOS. El Xamarin.Forms [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/) método permite incluir este control en las aplicaciones entre plataformas, representación alternativas nativas en Android y Windows Phone.

Para mostrar una hoja de acción, `await` [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/) en cualquier [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), pasando el mensaje y botón etiquetas como cadenas. El método devuelve la etiqueta de cadena del botón que se ha hecho clic el usuario. Aquí se muestra un ejemplo simple:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Cuadro de diálogo de ActionSheet")

El `destroy` botón se representa de forma diferente a los demás y puede dejarse `null` o se ha especificado como el tercer parámetro de cadena. En el ejemplo siguiente se usa el `destroy` botón:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[ ![DisplayActionSheet](pop-ups-images/action2-sml.png "cuadro de diálogo de hoja de acción con el botón de destrucción")](pop-ups-images/action2.png "cuadro de diálogo de hoja de acción con el botón de destrucción")

## <a name="summary"></a>Resumen

Este artículo se muestra mediante la hoja de alerta y la acción API que se va a pedir a los usuarios preguntas sencillas como guiar a los usuarios a través de tareas. Xamarin.Forms tiene dos métodos el [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) clase para interactuar con el usuario a través de una ventana emergente: [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) y [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/), y son ambos se representan con adecuado controles nativos en cada plataforma.



## <a name="related-links"></a>Vínculos relacionados

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
