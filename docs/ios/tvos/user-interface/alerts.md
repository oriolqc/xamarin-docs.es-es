---
title: Trabajar con alertas de tvOS en Xamarin
description: Este documento describe cómo trabajar con alertas de tvOS en Xamarin. Describe una alerta, agregar campos de texto y una clase auxiliar.
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 13c5ae3fac76ec1ec1a0ade135d5919403066226
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111296"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>Trabajar con alertas de tvOS en Xamarin

_En este artículo se explica cómo trabajar con UIAlertController para mostrar un mensaje de alerta al usuario en Xamarin.tvOS._

Si tiene que obtener la atención del usuario tvOS o pida permiso para realizar una acción (por ejemplo, al eliminar un archivo) destructiva, puede presentar un mensaje de alerta con el `UIAlertViewController`:

[![](alerts-images/alert01.png "Un ejemplo UIAlertViewController")](alerts-images/alert01.png#lightbox)

Si además de mostrar un mensaje, puede agregar botones y los campos de texto a una alerta para permitir al usuario a responder a las acciones y proporcionar comentarios.

<a name="About-Alerts" />

## <a name="about-alerts"></a>Acerca de las alertas

Como se indicó anteriormente, las alertas se utilizan para obtener la atención del usuario e informarles del estado de su aplicación o la solicitud de comentarios. Las alertas deben presentar un título, puede tener opcionalmente un mensaje y uno o varios botones o campos de texto.

[![](alerts-images/alert04.png "Una alerta de ejemplo")](alerts-images/alert04.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con alertas:

- **Usar alertas con moderación** -alertas interrumpir el flujo del usuario con la aplicación e interrumpir la experiencia del usuario y por lo tanto, solo debe usarse en situaciones importantes como las notificaciones de error, las compras de la aplicación y las acciones destructivas. 
- **Proporciona opciones útiles** : si la alerta presenta las opciones para el usuario, la debe asegurarse de que cada opción ofrece información crítica y proporciona acciones útiles para el usuario.

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>Los títulos y los mensajes de alertas

Apple tiene las siguientes sugerencias para presentar el título y el mensaje opcional de una alerta:

- **Usar títulos Multiword** -del título de la alerta An debe obtener el punto de la situación en claramente mientras seguimos siendo simple. Un título de una sola palabra rara vez proporciona información suficiente.
- **Use títulos descriptivos que no requieren un mensaje** : siempre que sea posible, considere la posibilidad de hacer que el título de la alerta descriptivo suficiente que el texto del mensaje opcional no es necesario. 
- **Que el mensaje en un breve, la frase completa** : si el mensaje opcional es necesario para obtener el punto de la alerta a través, mantenerlo tan simple como sea posible y que sea una frase completa con uso correcto de mayúsculas y signos de puntuación.

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>Botones de alerta

Apple tiene la siguiente sugerencia para agregar botones a una alerta:

- **Límite de dos botones** : siempre que sea posible, limite la alerta a un máximo de dos botones. Solo las alertas de botón proporcionan información pero ninguna de las acciones. Dos de las alertas de botón proporcionan un sencillo sí/no choice de acción.
- **Usar Succinct, títulos de botón lógico** -Simple títulos de botón de word de uno o dos que describen claramente la acción del botón funcionan mejor. Para obtener más información, consulte nuestra [trabaja con botones](~/ios/tvos/user-interface/buttons.md) documentación.
- **Claramente botones destructivas marca** : para los botones que realizan una acción (por ejemplo, al eliminar un archivo) destructiva marcan claramente con los `UIAlertActionStyle.Destructive` estilo.

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>Mostrar una alerta

Para mostrar una alerta, se crea una instancia de la `UIAlertViewController` y configurarlo mediante la adición de acciones (botones) y seleccione el estilo de la alerta. Por ejemplo, el código siguiente muestra una alerta de Aceptar/Cancelar:

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...
        
var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

Echemos un vistazo a este código en detalle. En primer lugar, creamos una nueva alerta con el título especificado y el mensaje:

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

A continuación, para cada botón que se va a mostrar en la alerta se cree una acción al definir el título del botón, su estilo y la acción que quiera realizar si se presiona el botón:

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ => 
    // Do something when the button is pressed
    ...
);
```

El `UIAlertActionStyle` enum le permiten establecer el estilo del botón como uno de los siguientes:

- **Valor predeterminado** -el botón será el botón predeterminado seleccionado cuando se muestra la alerta.
- **Cancelar** -el botón es el botón de cancelación de la alerta.
- **Destructivas** -resalta el botón como una acción destructiva, como la eliminación de un archivo. Actualmente, tvOS representa el botón destructivo con un fondo rojo.

El `AddAction` método agrega la acción dada a la `UIAlertViewController` y, finalmente, el `PresentViewController (alertController, true, null)` método muestra la alerta especificada para el usuario.

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>Agregar campos de texto

Además de agregar acciones (botones) a la alerta, puede agregar campos de texto a la alerta y permitir que el usuario rellene la información como los identificadores de usuario y contraseñas:

[![](alerts-images/alert02.png "Campo de texto en una alerta")](alerts-images/alert02.png#lightbox)

Si el usuario selecciona el campo de texto, se mostrará el teclado estándar tvOS lo que les permite especificar un valor para el campo:

[![](alerts-images/alert03.png "Escritura de texto")](alerts-images/alert03.png#lightbox)

El código siguiente muestra una alerta de Aceptar o cancelar con un único campo de texto para escribir un valor:

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

El `AddTextField` método agrega un nuevo campo de texto a la alerta que, a continuación, puede configurar estableciendo las propiedades como el marcador de posición texto (es decir, el texto que aparece cuando el campo está vacío), el valor predeterminado de texto y el tipo de teclado. Por ejemplo:

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

Por lo que podemos actuar en el valor del campo de texto más adelante, también nos estamos guardar una copia del utilizando el código siguiente:

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

Después de que el usuario ha escrito un valor en el campo de texto, podemos usar el `field` variable para tener acceso a ese valor.

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>Clase de aplicación auxiliar del controlador de vista de alertas

Porque mostrar tipos simples comunes de alertas mediante `UIAlertViewController` puede dar lugar a un poco de código duplicado, puede usar una clase auxiliar para reducir la cantidad de código repetitivo. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

Uso de esta clase, mostrar y responder a alertas simple pueden realizarse como sigue:

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```


<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata cómo trabajar con `UIAlertController` para mostrar un mensaje de alerta al usuario en Xamarin.tvOS. En primer lugar, se ha mostrado cómo mostrar una alerta sencilla y agregar botones. A continuación, se ha mostrado cómo agregar campos de texto a una alerta. Por último, se ha mostrado cómo usar una clase auxiliar para reducir la cantidad de código repetitivo requerido para mostrar una alerta.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
