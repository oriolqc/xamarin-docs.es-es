---
title: Trabajar con las barras de navegación en Xamarin de tvOS
description: Este documento describe cómo trabajar con las barras de navegación en una aplicación de tvOS con Xamarin. Describe cómo configurar las barras de navegación en un guión gráfico y responder a eventos de estos botones.
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 81e6cfe1e532bcfa7616e35adb28b314587bafc8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61161778"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>Trabajar con las barras de navegación en Xamarin de tvOS

Barras de navegación se pueden agregar a la parte superior de las vistas para mostrar un título y botones de barra de desplazamiento opcional. Normalmente se usan cuando el usuario haya navegado desde una página principal, como una vista de tabla, colección o un menú a una vista secundaria que muestra los detalles del elemento seleccionado.

[![](navigation-bars-images/navbar01.png "Ejemplo de barra de navegación")](navigation-bars-images/navbar01.png#lightbox)

Además del título (que se muestra en el centro), barras de navegación puede contener uno o varios botones de barra de navegación (`UIBarButtonItem`) en los lados izquierdo y derecho de la barra.

> [!IMPORTANT]
> Barras de navegación son completamente transparentes de forma predeterminada. Debe tener cuidado para garantizar que el contenido de la barra de navegación permanece legible sobre el contenido debajo de ella. Por ejemplo, cuando en una vista de tabla o colección se desplaza el contenido en él.

<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>Barras de navegación y los guiones gráficos

Es la manera más fácil trabajar con las barras de navegación en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **panel de solución**, haga doble clic en `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **barra de navegación** desde el **cuadro de herramientas** y colóquela en la vista en la parte superior de la pantalla: 

    [![](navigation-bars-images/navbar02.png "Una barra de navegación")](navigation-bars-images/navbar02.png#lightbox)
1. Haga doble clic en el **barra de navegación** para seleccionar a **elemento de navegación**. En el **Widget** pestaña de la **panel de propiedades**, puede establecer el **título**: 

    [![](navigation-bars-images/navbar03.png "Establecer el título")](navigation-bars-images/navbar03.png#lightbox)
1. A continuación, puede agregar uno o varios **los elementos de botón de barra** a cualquiera de los extremos de la barra de: 

    [![](navigation-bars-images/navbar04.png "Un elemento de botón de barra")](navigation-bars-images/navbar04.png#lightbox)
1. Por último, cable telefónico el **los elementos de botón de barra** a acciones en el **eventos** pestaña de la **Explorador de propiedades**: 

    [![](navigation-bars-images/navbar05.png "Una barra en la acción del elemento de botón")](navigation-bars-images/navbar05.png#lightbox)
1. Guarde los cambios.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. En el **el Explorador de soluciones**, haga doble clic en `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **barra de navegación** desde el **cuadro de herramientas** y colóquela en la vista en la parte superior de la pantalla: 

    [![](navigation-bars-images/navbar02-vs.png "Una barra de navegación")](navigation-bars-images/navbar02-vs.png#lightbox)
1. Haga doble clic en el **barra de navegación** para seleccionar a **elemento de navegación**. En el **Widget** pestaña de la **Explorador de propiedades**, puede establecer el **título**: 

    [![](navigation-bars-images/navbar03-vs.png "Establecer el título")](navigation-bars-images/navbar03-vs.png#lightbox)
1. A continuación, puede agregar uno o varios **los elementos de botón de barra** a cualquiera de los extremos de la barra de: 

    [![](navigation-bars-images/navbar04-vs.png "Una barra en elementos de botón")](navigation-bars-images/navbar04-vs.png#lightbox)
1. Por último, cable telefónico el **los elementos de botón de barra** a acciones en el **eventos** pestaña de la **Explorador de propiedades**: 

    [![](navigation-bars-images/navbar05-vs.png "Una barra de acciones de elemento de botón")](navigation-bars-images/navbar05-vs.png#lightbox)
1. Guarde los cambios.


-----

> [!IMPORTANT]
> Aunque es posible asignar como eventos `TouchUpInside` a un elemento de interfaz de usuario (por ejemplo, un UIButton) en el Diseñador de iOS, nunca se llamará porque Apple TV no tiene una entrada táctil de pantalla o admitir eventos de toque. Siempre debe usar el `Primary Action` evento al crear controladores de eventos para tvOS elementos de la interfaz de usuario.

El código siguiente proporciona un ejemplo de controladores de eventos en tres BarButtonItems diferentes: `ShowFirstHotel`, `ShowSecondHotel`, y `ShowThirdHotel`. Cuando cada elemento se hace clic en, la imagen de fondo `HotelImage` se cambia. Se edita en el controlador de vista (ejemplo `ViewController.cs`) archivo:

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

Siempre que un botón `Enabled` propiedad es `true` y no está cubierto por otro control o vista, se puede convertir el elemento enfocado mediante el control remoto de Siri.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con las barras de navegación dentro de una aplicación Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
