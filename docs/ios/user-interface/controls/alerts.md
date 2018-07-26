---
title: Mostrar las alertas en Xamarin.iOS
description: Este documento describe cómo mostrar las alertas en Xamarin.iOS mediante el uso de la API que se introdujo en iOS 8 UIAlertController.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 788e62b30dbf533df059b0c3805e04ecf7b857aa
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241345"
---
# <a name="displaying-alerts-in-xamarinios"></a>Mostrar las alertas en Xamarin.iOS

A partir de iOS 8, UIAlertController tiene completado UIActionSheet reemplazado y UIAlertView de los cuales están en desuso.

A diferencia de las clases que la reemplazó, que son subclases de UIView, UIAlertController es una subclase de UIViewController.

Use `UIAlertControllerStyle` para indicar el tipo de alerta para mostrar. Estos tipos de alertas son:

- **UIAlertControllerStyleActionSheet**
    * Preliminar iOS 8 esto habría sido un UIActionSheet
- **UIAlertControllerStyleAlert**
    * Preliminar iOS 8 esto habría sido UIAlertView 

Hay tres pasos necesarios para tomar al crear un controlador de alerta:

- Cree y configure la alerta con a:
    * título
    * message
    * preferredStyle
    
- (Opcional) Agregar un campo de texto
- Agregue las acciones necesarias
- Presentar el controlador de vista

La alerta más simple contiene un solo botón, como se muestra en esta captura de pantalla:

 ![Alertar con un botón](alerts-images/alert1.png)

El código para mostrar una alerta simple es como sigue:

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

Mostrar una alerta con varias opciones, se realiza de forma similar, pero agregar dos acciones. Por ejemplo, la captura de pantalla siguiente muestra una alerta con dos botones:

 ![ Alertar con dos botones](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

Las alertas también pueden mostrar una hoja de acción, similar a la captura de pantalla siguiente:

 ![Alerta de la hoja de acción](alerts-images/alert3.png)

Los botones se agregan a la alerta con el `AddAction` método:

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Three pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
- [Controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
