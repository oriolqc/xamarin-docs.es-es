---
title: Mostrar alertas
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: b15afa55f170ce86fd66e17209c6db172b1db740
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="displaying-alerts"></a>Mostrar alertas

A partir de iOS 8, UIAlertController tiene completado UIActionSheet reemplazado y UIAlertView de los cuales están en desuso.

A diferencia de las clases que se reemplace, que son subclases de UIView, UIAlertController es una subclase de UIViewController.

Use `UIAlertControllerStyle` para indicar el tipo de alerta que se va a mostrar. Estos tipos de alertas son:

- **UIAlertControllerStyleActionSheet**
    * Previa iOS 8 esto habría sido un UIActionSheet
- **UIAlertControllerStyleAlert**
    * Previa iOS 8 esto habría sido UIAlertView 

Hay tres pasos necesarios para tomar al crear un controlador de alerta:

- Cree y configure la alerta con r:
    * título
    * message
    * preferredStyle
    
- (Opcional) Agregar un campo de texto
- Agregar las acciones necesarias
- Presentar el controlador de vista

La alerta más simple contiene un solo botón, tal y como se muestra en esta captura de pantalla:

 ![Alerta con uno de los botones](alerts-images/alert1.png)

El código para mostrar una alerta sencilla es como sigue:

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

 ![Alerta de hoja de acción](alerts-images/alert3.png)

Se agregan botones a la alerta con la `AddAction` método:

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
- [Controlador de alerta](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)
