---
title: Trabajar con botones de tvOS de Xamarin
description: Este documento describe cómo trabajar con botones en una aplicación de tvOS compilada con Xamarin. Explica cómo trabajar con los botones en el código y en los guiones gráficos y examina cómo aplicar estilo a un botón.
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/07/2017
ms.openlocfilehash: 3de732e9eee696ce21ffc5526afd44f29695a313
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789390"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>Trabajar con botones de tvOS de Xamarin

Usar una instancia de la `UIButton` clase para crear un botón seleccionable, puede recibir el foco en una ventana de tvOS. Cuando el usuario selecciona un botón, envía un mensaje de acción para el objeto de destino permitir su responden de aplicación Xamarin.tvOS al usuario de la entrada.

[![](buttons-images/buttons01.png "Botones de ejemplo")](buttons-images/buttons01.png#lightbox)

Para obtener más información sobre trabajar con el foco y navegar con el mando a distancia Siri, visite nuestro [trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y [Siri remoto y controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentación.

<a name="About-Buttons" />

## <a name="about-buttons"></a>Acerca de los botones

En tvOS, botones se utilizan para acciones específicas de la aplicación y pueden contener un título, un icono o ambos. Cuando el usuario explora la interfaz de usuario de la aplicación mediante el [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), foco del botón especificado, lo que cambiar los colores de fondo y de texto. También se aplica una sombra para el botón Agregar un efecto 3D que se parezca a subir por encima del resto de la interfaz de usuario.

[![](buttons-images/buttons01.png "Botones de ejemplo")](buttons-images/buttons01.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con botones:

- **Use un título o un icono** : mientras el tanto un icono y se pueden incluir un título en un botón, espacio está limitado por lo que no intente combinar ambos.
- **Claramente botones destructiva de marca** : si realiza una destructiva el botón de acción (como la eliminación de un archivo), indicar claramente como tales mediante texto o icono. Las acciones destructivas siempre deben presentar un [alerta](~/ios/tvos/user-interface/alerts.md) que pide al usuario para limitar la acción.
- **No usar botones Atrás** -botón del menú del control remoto Siri se utiliza para volver a la pantalla anterior. La única excepción a esta regla es para las compras de la aplicación o acciones destructivas donde un **cancelar** botón debe mostrarse.

Para obtener más información sobre cómo trabajar con el foco y la navegación, consulte nuestro [trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) documentación.

<a name="Button-Icons" />

### <a name="button-icons"></a>Iconos de botón

Apple sugiere que debe utilizarse sencillas y muy fácil de reconocer imágenes de los iconos de botón. Iconos excesivamente complejas son difíciles de reconocer en una pantalla de TV a través de la sala en un sofá, por lo que intente usar la representación más simple posible comunicar la idea. Siempre que sea posible, use estándar, conocidos imágenes para iconos (como una lupa para la búsqueda).

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botón

Apple tiene las siguientes sugerencias al crear los títulos para los botones:

- **Mostrar texto descriptivo debajo de iconos de botones** - cuando sea posible, lugar claro, el texto descriptivo debajo icono solo botones para obtener más el propósito del botón en.
- **Usar verbos o sintagmas verbales para el título** -colocar claramente el estado de la acción que va a realizar cuando el usuario hace clic en el botón.
- **Utilice mayúsculas y minúsculas de estilo del título** : con la excepción de artículos, conjunciones o preposiciones (cuatro letras o menos), deben estar en mayúsculas todas las palabras del título del botón.
- **Usar un valor bajo, título a punto** -utilice las palabras más corta posible para describir la acción del botón.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>Botones y los guiones gráficos

Es la manera más fácil trabajar con botones en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de Xamarin para iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)


1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **botón** desde el **biblioteca** y colóquela en la vista: 

    [![](buttons-images/storyboard01.png "Un botón")](buttons-images/storyboard01.png#lightbox)
1. En el **el Explorador de propiedades**, puede ajustar varias propiedades del botón como su **título** y **Color del texto**: 

    [![](buttons-images/storyboard02.png "Propiedades de botón")](buttons-images/storyboard02.png#lightbox)
1. A continuación, cambie a la **ficha eventos** y cable telefónico un **eventos** desde el **botón** y llámelo `ButtonPressed`: 

    [![](buttons-images/storyboard03.png "La ficha de eventos")](buttons-images/storyboard03.png#lightbox)
1. Pasará automáticamente a la `ViewController.cs` vista donde puede colocar la nueva acción en el código utilizando la **una** y **hacia abajo** teclas de dirección: 

    [![](buttons-images/storyboard04.png "Colocar una nueva acción en el código")](buttons-images/storyboard04.png#lightbox)
1. Presione el **ENTRAR** para seleccionar la ubicación: 

    [![](buttons-images/storyboard05.png "El editor de código")](buttons-images/storyboard05.png#lightbox)
1. Guarde los cambios a todos los archivos.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **botón** desde el **biblioteca** y colóquela en la vista: 

    [![](buttons-images/storyboard01vs.png "Un botón")](buttons-images/storyboard01vs.png#lightbox)
1. En el **el Explorador de propiedades**, puede ajustar varias propiedades del botón como su **título** y **Color del texto**: 

    [![](buttons-images/storyboard02vs.png "El Explorador de propiedades")](buttons-images/storyboard02vs.png#lightbox)
1. A continuación, cambie a la **ficha eventos** y cable telefónico un **eventos** desde el **botón** y llámelo `ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "La ficha de eventos")](buttons-images/storyboard03vs.png#lightbox)
1. Guarde los cambios a todos los archivos.



Editar el controlador de vista (ejemplo `ViewController.cs`) de archivos y agregue el siguiente código para controlar el botón está seleccionado:


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

Siempre que un botón `Enabled` propiedad es `true` y no está cubierto por otro control o vista, se puede establecer el elemento enfocado mediante el control remoto Siri. Si el usuario selecciona el botón y hace clic en la superficie de toque, el `ButtonPressed` se ejecutaría la acción definida anteriormente.

> [!IMPORTANT]
> Aunque es posible asignar acciones como `TouchUpInside` a una `UIButton` en el archivo iOS diseñador al crear un **controlador de eventos**, nunca se llamará porque Apple TV no tiene un toque la pantalla o admitir eventos de toque. Debe utilizar siempre el valor predeterminado **tipo de acción** al crear **acciones** para tvOS elementos de la interfaz de usuario.




Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>Botones y el código

Si lo desea, un `UIButton` se pueden crear en código C# y se agrega a la vista de la aplicación tvOS. Por ejemplo:

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

Cuando se crea un nuevo `UIButton` mediante código, especifique su `UIButtonType` como uno de los siguientes:

- **Sistema** -este es el tipo de botón presentada por tvOS estándar y es el tipo que va a utilizar con más frecuencia.
- **DetailDisclosure** -presenta un tipo de botón que se utiliza para ocultar o mostrar información detallada "Bajar".
- **InfoDark** -un oscuro detallada información botón muestra una "i" en un círculo.
- **InfoLight** -una luz detallada información botón muestra una "i" en un círculo.
- **AddContact** -mostrar el botón como un botón Agregar contacto.
- **Personalizado** -le permite personalizar varias características del botón.

A continuación, defina el tamaño en pantalla y la ubicación del botón. Ejemplo:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

A continuación, establezca el título del botón. `UIButtons` son diferentes de mayoría `UIKit` controles en cuanto que tienen un estado, por lo que no se puede simplemente cambian el título, tendrá que cambiar para un determinado `UIControlState`. Por ejemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

A continuación, use la `AllEvents` evento para ver si el usuario hace clic en el botón. Ejemplo:

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

Por último, puede agregar el botón a la vista para mostrarla:

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> Aunque es posible asignar acciones como `TouchUpInside` a un `UIButton`, nunca se llamará porque Apple TV no tiene un toque la pantalla o admitir eventos de toque. Siempre debe utilizar eventos como **AllEvents** o **PrimaryActionTriggered**.




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Aplicar un estilo a un botón

tvOS proporciona varias propiedades de un `UIButton` que se puede utilizar para proporcionar su título y estilo cosas como color de fondo e imágenes.

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botón

Tal y como se ha explicado anteriormente, `UIButtons` son diferentes de mayoría `UIKit` controles en cuanto que tienen un estado, por lo que no se puede simplemente cambian el título, tendrá que cambiar para un determinado `UIControlState`. Por ejemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Puede establecer el color del título para el botón mediante la `SetTitleColor` método. Por ejemplo:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

Y no se puede ajustar el título de instantáneas mediante el `SetTitleShadowColor`. Por ejemplo:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Puede establecer la sombra de título para cambiar de *grabado* a *relieve* cuando se resalta el botón con el código siguiente:

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

Además, puede utilizar texto con atributos como título del botón. Por ejemplo:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>Imágenes de los botones

Un `UIButton` puede tener una imagen asociada a él y puede utilizar una imagen como fondo.

Para establecer la imagen de fondo de un botón para un determinado `UIControlState`, utilice el código siguiente:

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Establecer el `AdjustsImageWhenHiglighted` propiedad `true` para dibujar la imagen más clara cuando se resalta el botón (es decir, el valor predeterminado). Establecer el `AdjustsImageWhenDisabled` propiedad `true` para dibujar la imagen del borde más oscura cuando el botón está deshabilitado (de nuevo, esto es el valor predeterminado).

Para establecer la imagen mostrada en el botón, utilice el código siguiente:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Use la `TintColor` propiedad para establecer un tono de color que se aplica en el título y la imagen del botón. Para los botones de la `Custom` tipo, esta propiedad no tiene ningún efecto, debe implementar la `TintColor` comportamiento usted mismo.

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con botones dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo. Ha mostrado cómo trabajar con botones en el Diseñador de iOS y cómo crear botones en código C#. Por último, ha explicado cómo modificar el título del botón y cambiar el aspecto y el estilo.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
