---
title: Trabajar con los botones de tvOS en Xamarin
description: Este documento describe cómo trabajar con los botones en una aplicación de tvOS con Xamarin. Describe cómo trabajar con los botones en el código y en los guiones gráficos y examina cómo aplicar estilo a un botón.
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/07/2017
ms.openlocfilehash: 6d8fc1daaced24dccead78c4f9d0e5d0959b3755
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199069"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>Trabajar con los botones de tvOS en Xamarin

Usar una instancia de la `UIButton` clase para crear un botón puede recibir el foco, puede seleccionar en una ventana de tvOS. Cuando el usuario selecciona un botón, envía un mensaje de acción para el objeto de destino permitir de entrada de la respuesta de aplicación Xamarin.tvOS al usuario.

[![](buttons-images/buttons01.png "Botones de ejemplo")](buttons-images/buttons01.png#lightbox)

Para obtener más información para trabajar con el foco y navegar con el control remoto de Siri, consulte nuestra [trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y [Siri Remote y Bluetooth controladores](~/ios/tvos/platform/remote-bluetooth.md) documentación.

<a name="About-Buttons" />

## <a name="about-buttons"></a>Acerca de los botones

En tvOS, los botones se utilizan para las acciones específicas de la aplicación y pueden contener un título, un icono o ambos. Cuando el usuario explora la interfaz de usuario de la aplicación mediante el [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), éste se desplaza a lo que cambiar los colores de texto y fondo de botón especificado. También se aplica una sombra en el botón Agregar un efecto 3D parezca subiendo por encima del resto de la interfaz de usuario.

[![](buttons-images/buttons01.png "Botones de ejemplo")](buttons-images/buttons01.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con los botones:

- **Use un título o un icono** : mientras el tanto un icono y se puede incluir un título en un botón, el aforo es limitado por lo que no intente combinar ambos.
- **Claramente botones destructivas de marca** : si realiza un destructivas el botón de acción (por ejemplo, al eliminar un archivo), marque claramente como tal mediante texto o icono. Las acciones destructivas siempre deben presentar un [alerta](~/ios/tvos/user-interface/alerts.md) que pide al usuario para restringir la acción.
- **No usar botones Atrás** -botón del menú del control remoto Siri se usa para volver a la pantalla anterior. La única excepción a esta regla es para compras de la aplicación o acciones destructivas donde un **cancelar** botón debe mostrarse.

Para obtener más información sobre cómo trabajar con el foco y la navegación, consulte nuestra [trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) documentación.

<a name="Button-Icons" />

### <a name="button-icons"></a>Iconos de botón

Apple sugiere que utilice sencillas y muy fácil de reconocer imágenes para los iconos de botón. Son difíciles de reconocer en una pantalla de TV a través de la sala en un sofá, así que intente usar la representación más simple posible transmitir la idea de iconos demasiado complejos. Siempre que sea posible, utilice estándar, conocido imágenes para iconos (por ejemplo, un icono de lupa para la búsqueda).

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botón

Apple tiene las siguientes sugerencias al crear los títulos para los botones:

- **Mostrar texto descriptivo debajo de botones de iconos** - cuando sea posible, colocar texto claros y descriptivos por debajo del icono de botones solo para obtener más el propósito del botón en.
- **Use verbos o frases de verbo para el título** -colocar claramente el estado de la acción que va a realizar cuando el usuario hace clic en el botón.
- **Utilice mayúsculas y minúsculas de estilo del título** - con excepción de los artículos, conjunciones o preposiciones (cuatro letras o menos), debe escribirse en mayúsculas cada palabra del título del botón.
- **Usar un breve, el título a punto** -usar las palabras más corta posible para describir la acción del botón.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>Los botones y los guiones gráficos

Es la manera más fácil trabajar con los botones en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de Xamarin para iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **botón** desde el **biblioteca** y colóquela en la vista: 

    [![](buttons-images/storyboard01.png "Un botón")](buttons-images/storyboard01.png#lightbox)
1. En el **Explorador de propiedades**, puede ajustar varias propiedades del botón, como su **título** y **Color del texto**: 

    [![](buttons-images/storyboard02.png "Propiedades del botón")](buttons-images/storyboard02.png#lightbox)
1. A continuación, cambie a la **pestaña eventos** y transmisión una **eventos** desde el **botón** y llámelo `ButtonPressed`: 

    [![](buttons-images/storyboard03.png "La ficha de eventos")](buttons-images/storyboard03.png#lightbox)
1. Pasarán automáticamente a la `ViewController.cs` vista donde puede colocar el nuevo objeto Action en el código utilizando la **seguridad** y **abajo** teclas de dirección: 

    [![](buttons-images/storyboard04.png "Colocación de una nueva acción en el código")](buttons-images/storyboard04.png#lightbox)
1. Presione el **ENTRAR** para seleccionar la ubicación: 

    [![](buttons-images/storyboard05.png "El editor de código")](buttons-images/storyboard05.png#lightbox)
1. Guarde los cambios a todos los archivos.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **botón** desde el **biblioteca** y colóquela en la vista: 

    [![](buttons-images/storyboard01vs.png "Un botón")](buttons-images/storyboard01vs.png#lightbox)
1. En el **Explorador de propiedades**, puede ajustar varias propiedades del botón, como su **título** y **Color del texto**: 

    [![](buttons-images/storyboard02vs.png "El Explorador de propiedades")](buttons-images/storyboard02vs.png#lightbox)
1. A continuación, cambie a la **pestaña eventos** y transmisión una **eventos** desde el **botón** y llámelo `ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "La ficha de eventos")](buttons-images/storyboard03vs.png#lightbox)
1. Guarde los cambios a todos los archivos.



Editar el controlador de vista (ejemplo `ViewController.cs`) y agregue el código siguiente para controlar el botón está seleccionado:


```

using System;
using UIKit;

namespace tvRemote
{
    public partial class ViewController : UIViewController
    {
        ...

        partial void ButtonPressed (UIButton sender) {
            // Handle click here
            ...
        }
    }
}

```

-----

Siempre que un botón `Enabled` propiedad es `true` y no está cubierto por otro control o vista, se puede convertir el elemento enfocado mediante el control remoto de Siri. Si el usuario selecciona el botón y hace clic en la superficie de interacción, el `ButtonPressed` se ejecutaría la acción definida anteriormente.

> [!IMPORTANT]
> Aunque es posible asignar acciones como `TouchUpInside` a un `UIButton` en iOS Designer al crear un **controlador de eventos**, nunca se llamará porque Apple TV no tiene una entrada táctil de pantalla o admitir eventos de toque. Siempre debe usar el valor predeterminado **tipo de acción** al crear **acciones** para elementos de interfaz de usuario de tvOS.




Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>Los botones y el código

Opcionalmente, un `UIButton` pueden crearse en C# de código y la agrega a la vista de la aplicación de tvOS. Por ejemplo:

```csharp
var button = new UIButton(UIButtonType.System);
button.Frame = new CGRect (25, 25, 300, 150);
button.SetTitle ("Hello", UIControlState.Normal);
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
View.AddSubview (button);
```

Cuando se crea un nuevo `UIButton` en el código, especifica su `UIButtonType` como uno de los siguientes:

- **Sistema** : este es el tipo de botón presentada por tvOS estándar y es el tipo que usan con más frecuencia.
- **DetailDisclosure** -presenta un tipo "Bajar" del botón que se usa para ocultar o mostrar información detallada.
- **InfoDark** -un oscuro detallada información botón muestra una "i" en un círculo.
- **InfoLight** -una luz detallada información botón muestra una "i" en un círculo.
- **AddContact** -mostrar el botón como un botón Agregar contacto.
- **Custom** -le permite personalizar varias características del botón.

A continuación, defina el tamaño en pantalla y la ubicación del botón. Ejemplo:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

A continuación, establezca el título del botón. `UIButtons` son diferentes que la mayoría `UIKit` controles en cuanto que tienen un estado por lo que no se puede simplemente cambian el título, tendrá que cambiarla para un determinado `UIControlState`. Por ejemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

A continuación, use el `AllEvents` evento para ver cuando el usuario ha hecho clic el botón. Ejemplo:

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

Por último, agregue el botón a la vista para que aparezca:

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> Aunque es posible asignar acciones como `TouchUpInside` a un `UIButton`, nunca se llamará porque Apple TV no tiene una entrada táctil de pantalla o admitir eventos de toque. Siempre debe utilizar los eventos como **AllEvents** o **PrimaryActionTriggered**.




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Aplicar un estilo a un botón

tvOS proporciona varias propiedades de un `UIButton` que puede utilizarse para proporcionar su título y el estilo con cosas como el color de fondo e imágenes.

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botón

Como vimos anteriormente, `UIButtons` son diferentes que la mayoría `UIKit` controles en cuanto que tienen un estado por lo que no se puede simplemente cambian el título, tendrá que cambiarla para un determinado `UIControlState`. Por ejemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Puede establecer el color del título para el botón mediante el `SetTitleColor` método. Por ejemplo:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

Y no se puede ajustar el título de la sombra utilizando el `SetTitleShadowColor`. Por ejemplo:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Puede establecer la sombra del título para cambiar de *grabado* a *relieve* cuando se resalta el botón con el código siguiente:

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

Además, puede usar con atributos de texto como el título del botón. Por ejemplo:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>Imágenes de botón

Un `UIButton` puede tener una imagen asociada a él y puede utilizar una imagen como fondo.

Para establecer la imagen de fondo de un botón para un determinado `UIControlState`, use el código siguiente:

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Establecer el `AdjustsImageWhenHiglighted` propiedad `true` para dibujar la imagen más clara cuando se resalta el botón (es decir, el valor predeterminado). Establecer el `AdjustsImageWhenDisabled` propiedad `true` para dibujar la imagen más oscura cuando el botón está deshabilitado (nuevamente, esto es el valor predeterminado).

Para establecer la imagen mostrada en el botón, use el siguiente código:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Use el `TintColor` propiedad para establecer un tono de color que se aplica en el título y la imagen del botón. Para los botones de la `Custom` tipo, esta propiedad no tiene ningún efecto, debe implementar la `TintColor` comportamiento usted mismo.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con los botones dentro de una aplicación Xamarin.tvOS. Se ha mostrado cómo trabajar con los botones en el Diseñador de iOS y cómo crear botones en C# código. Por último, se ha mostrado cómo modificar el título de un botón y cambiar su estilo y su apariencia.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
