---
title: Alertas
description: "Este artículo explica cómo trabajar con alertas en una aplicación Xamarin.Mac. Describe cómo crear y mostrar las alertas desde el código C# y responde a las interacciones del usuario."
ms.topic: article
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 8901bb57ace4f05e8c26fdc43dfe8c476927903a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="alerts"></a>Alertas

_Este artículo explica cómo trabajar con alertas en una aplicación Xamarin.Mac. Describe cómo crear y mostrar las alertas desde el código C# y responde a las interacciones del usuario._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, se tiene acceso a la misma alerta que un desarrollador que trabaja *Objective-C* y *Xcode* does. 

Una alerta es un tipo especial de cuadro de diálogo que aparece cuando se produce un problema grave (por ejemplo, un error) o como una advertencia (por ejemplo, la preparación eliminar un archivo). Dado que una alerta es un cuadro de diálogo, también se necesita una respuesta del usuario antes de poder cerrar.

[![](alert-images/alert06.png "Una alerta de ejemplo")](alert-images/alert06.png#lightbox)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con alertas en una aplicación Xamarin.Mac. 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>Introducción a las alertas

Una alerta es un tipo especial de cuadro de diálogo que aparece cuando se produce un problema grave (por ejemplo, un error) o como una advertencia (por ejemplo, la preparación eliminar un archivo). Dado que las alertas interrumpen el usuario ya que debe eliminarse antes de que el usuario puede continuar con su tarea, evitar que se muestre una alerta a menos que sea absolutamente necesario.

Apple recomienda lo siguiente:

- No use una alerta simplemente para proporcionar información a los usuarios.
- No mostrar una alerta para acciones comunes, que se pueden deshacer. Incluso si esa situación podría provocar la pérdida de datos.
- Si una situación merece la pena de una alerta, evite utilizar cualquier otro método o elemento de interfaz de usuario para que se muestre.
- El icono de precaución debe usarse con moderación.
- Describir la situación de alerta con claridad y sucinta en el mensaje de alerta.
- El nombre del botón predeterminado debe corresponder a la acción que se describen en el mensaje de alerta.

Para obtener más información, consulte el [alertas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>Anatomía de una alerta

Como se mencionó anteriormente, las alertas se deberían mostrar al usuario de la aplicación cuando se produce un problema grave o como una advertencia a la pérdida potencial de datos (por ejemplo, cerrar un archivo no guardado). En Xamarin.Mac, se crea una alerta en código C#, por ejemplo:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

El código anterior muestra una alerta con el icono de aplicaciones superpuesto sobre el icono de advertencia, un título, un mensaje de advertencia y una sola **Aceptar** botón:

[![](alert-images/alert01.png "Una alerta con un botón Aceptar")](alert-images/alert01.png#lightbox)

Apple proporciona varias propiedades que pueden usarse para personalizar una alerta:

- **AlertStyle** define el tipo de una alerta como uno de los siguientes:
    - **Advertencia** : se usan para advertir al usuario un evento actual o inminente que no es crítico. Este es el estilo predeterminado.
    - **Informativo** : se usan para advertir al usuario sobre un evento actual o inminente. Actualmente, no hay ninguna diferencia visible entre un **advertencia** y un **informativo**
    - **Crítico** : se usan para advertir al usuario sobre consecuencias graves de producir un evento (por ejemplo, para eliminar un archivo). Este tipo de alerta debe usarse con moderación.
- **MessageText** -este es el principal del mensaje o el título de la alerta y se debe definir rápidamente la situación al usuario.
- **InformativeText** -éste es el cuerpo de la alerta que debe definir claramente la situación y presentar opciones viables para el usuario.
- **Icono** -permite a un icono personalizado que se mostrará al usuario.
- **HelpAnchor** & **ShowsHelp** -permite la alerta asociar a la aplicación HelpBook y mostrar la Ayuda de la alerta.
- **Botones** : de forma predeterminada, una alerta solo tiene la **Aceptar** botón pero la **botones** colección le permite agregar más opciones según sea necesario.
- **ShowsSuppressionButton** : si `true` muestra una casilla de verificación que el usuario puede utilizar para suprimir la alerta más repeticiones del evento que lo desencadenó.
- **AccessoryView** -permite adjuntar otra vista secundaria a la alerta para proporcionar información adicional, como agregar una **campo de texto** de entrada de datos. Si establece un nuevo **AccessoryView** o modificar uno existente, debe llamar a la `Layout()` método para ajustar el diseño visible de la alerta.

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>Mostrar una alerta

Hay dos maneras diferentes que una alerta puede ser mostrada, flotantes o como una hoja. El código siguiente muestra una alerta como flotantes:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
Si se ejecuta este código, se muestra lo siguiente:

[![](alert-images/alert02.png "Una alerta simple")](alert-images/alert02.png#lightbox)

El código siguiente muestra la misma alerta como una hoja:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

Si se ejecuta este código, aparecerá la siguiente:

[![](alert-images/alert03.png "Una alerta que aparece como una hoja")](alert-images/alert03.png#lightbox)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>Trabajar con botones de alerta

De forma predeterminada, se muestra una alerta sólo el **Aceptar** botón. Sin embargo, no está limitado a, puede crear botones adicionales, adjúntelos a la **botones** colección. El código siguiente crea una alerta flotante con un **Aceptar**, **cancelar** y **quizá** botón:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

El primer botón agregado será la _botón predeterminado_ que se activará si el usuario presiona la tecla ENTRAR. El valor devuelto será un entero que representa qué botón el usuario presionado. En nuestro caso, se devolverán los valores siguientes:

- **OK** - 1000.
- **Cancelar** : 1001.
- **Quizá** - 1002.

Si se ejecuta el código, aparecerá la siguiente:

[![](alert-images/alert04.png "Una alerta con tres opciones de botón")](alert-images/alert04.png#lightbox)

Este es el código de la misma alerta como una hoja:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}", result);
});
```
Si se ejecuta este código, aparecerá la siguiente:

[![](alert-images/alert05.png "Una alerta que tres botón aparece como una hoja")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> No agregues nunca más de tres botones a una alerta.

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>Con el botón suprimir

Si la alerta `ShowSuppressButton` propiedad es `true`, la alerta muestra una casilla de verificación que el usuario puede utilizar para suprimir la alerta más repeticiones del evento que lo desencadenó. El código siguiente muestra una alerta flotante con un botón suprimir:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Si el valor de la `alert.SuppressionButton.State` es `NSCellStateValue.On`, el usuario ha activado la casilla de verificación Suprimir, else no han surtido efecto.

Si se ejecuta el código, aparecerá la siguiente:

[![](alert-images/alert06.png "Una alerta con un botón de suprimir")](alert-images/alert06.png#lightbox)

Este es el código de la misma alerta como una hoja:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Si se ejecuta este código, aparecerá la siguiente:

[![](alert-images/alert07.png "Mostrar una alerta con un botón Suprimir como una hoja")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>Agregar una vista secundaria personalizada

Las alertas tienen un `AccessoryView` propiedad que puede usarse para personalizar aún más la alerta y agregar elementos como una **campo de texto** proporcionados por el usuario. El código siguiente crea una alerta flotante con un campo de entrada de texto agregado:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Las líneas claves aquí son `var input = new NSTextField (new CGRect (0, 0, 300, 20));` que crea un nuevo **campo de texto** que se va a agregar la alerta. `alert.AccessoryView = input;` que adjunta el **campo de texto** a la alerta y la llamada a la `Layout()` método, que es necesario para cambiar el tamaño de la alerta para que quepa en la vista secundaria nueva.

Si se ejecuta el código, aparecerá la siguiente:

[![](alert-images/alert08.png "Si se ejecuta el código, a continuación se muestra")](alert-images/alert08.png#lightbox)

Esta es la misma alerta como una hoja:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Si se ejecuta este código, aparecerá la siguiente:

[![](alert-images/alert09.png "Una alerta con una vista personalizada")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con alertas en una aplicación Xamarin.Mac. Hemos visto los diferentes tipos y usos de las alertas, cómo crear y personalizar las alertas y cómo trabajar con alertas en código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacWindows (ejemplo)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con ventanas](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
