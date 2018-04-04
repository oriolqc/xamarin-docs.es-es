---
title: Trabajar con controladores de navegación
description: Este artículo tratan diseñar y trabajar con barras de navegación dentro de una aplicación Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 8a9a1c852137a2bcc0d46615e69eef0a245a9768
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-navigation-controllers"></a>Trabajar con controladores de navegación

_Este artículo tratan diseñar y trabajar con barras de navegación dentro de una aplicación Xamarin.tvOS._

Barras de navegación pueden agregarse a la parte superior de las vistas para mostrar un título y botones de barra de desplazamiento opcional. Normalmente se utilizan cuando el usuario haya navegado en una página principal, como una vista de tabla, colección o un menú a una vista secundaria que muestra los detalles del elemento seleccionado.

[![](navigation-bars-images/navbar01.png "Barra de navegación de ejemplo")](navigation-bars-images/navbar01.png#lightbox)

Además de título (que se muestra en el centro), barras de navegación puede contener uno o varios botones de barra de navegación (`UIBarButtonItem`) en los lados izquierdo y derecho de la barra.

> [!IMPORTANT]
> Barras de navegación son completamente transparentes de forma predeterminada. Debe tener cuidado para asegurarse de que el contenido de la barra de navegación sigue siendo legible sobre el contenido que están bajo él. Por ejemplo, cuando en una vista de tabla o la colección se desplaza el contenido en él.




<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>Barras de navegación y los guiones gráficos

Es la manera más fácil trabajar con barras de navegación en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)


1. En el **solución Pad**, haga doble clic en `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **barra de navegación** desde el **cuadro de herramientas** y colóquela en la vista en la parte superior de la pantalla: 

    [![](navigation-bars-images/navbar02.png "Una barra de navegación")](navigation-bars-images/navbar02.png#lightbox)
1. Haga doble clic en el **barra de navegación** para seleccionar a **elemento de navegación**. En el **Widget** pestaña de la **panel de propiedades**, puede establecer la **título**: 

    [![](navigation-bars-images/navbar03.png "Establecer el título")](navigation-bars-images/navbar03.png#lightbox)
1. A continuación, puede agregar uno o varios **elementos de botón de barra de** a cualquiera de los extremos de la barra de: 

    [![](navigation-bars-images/navbar04.png "Un elemento de botón de barra")](navigation-bars-images/navbar04.png#lightbox)
1. Por último, el cable telefónico el **elementos de botón de barra de** a acciones en el **eventos** pestaña de la **el Explorador de propiedades**: 

    [![](navigation-bars-images/navbar05.png "Una barra de acción del elemento de botón")](navigation-bars-images/navbar05.png#lightbox)
1. Guarde los cambios.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. En el **el Explorador de soluciones**, haga doble clic en `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **barra de navegación** desde el **cuadro de herramientas** y colóquela en la vista en la parte superior de la pantalla: 

    [![](navigation-bars-images/navbar02-vs.png "Una barra de navegación")](navigation-bars-images/navbar02-vs.png#lightbox)
1. Haga doble clic en el **barra de navegación** para seleccionar a **elemento de navegación**. En el **Widget** pestaña de la **el Explorador de propiedades**, puede establecer la **título**: 

    [![](navigation-bars-images/navbar03-vs.png "Establecer el título")](navigation-bars-images/navbar03-vs.png#lightbox)
1. A continuación, puede agregar uno o varios **elementos de botón de barra de** a cualquiera de los extremos de la barra de: 

    [![](navigation-bars-images/navbar04-vs.png "Un elementos de botón de barras")](navigation-bars-images/navbar04-vs.png#lightbox)
1. Por último, el cable telefónico el **elementos de botón de barra de** a acciones en el **eventos** pestaña de la **el Explorador de propiedades**: 

    [![](navigation-bars-images/navbar05-vs.png "Una barra de acciones del elemento de botón")](navigation-bars-images/navbar05-vs.png#lightbox)
1. Guarde los cambios.


-----

> [!IMPORTANT]
> Aunque es posible asignar eventos como `TouchUpInside` a un elemento de interfaz de usuario (por ejemplo, un UIButton) en el Diseñador de iOS, nunca se llamará Apple TV no tiene un toque la pantalla o admitir eventos de toque. Debe utilizar siempre el `Primary Action` eventos al crear controladores de eventos para tvOS elementos de la interfaz de usuario.




El código siguiente proporciona un ejemplo de controladores de eventos en tres BarButtonItems diferentes: `ShowFirstHotel`, `ShowSecondHotel`, y `ShowThirdHotel`. Cuando cada elemento se hace clic, la imagen de fondo `HotelImage` se cambia. Esto se haya modificado en el controlador de vista (ejemplo `ViewController.cs`) archivo:

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

Siempre que un botón `Enabled` propiedad es `true` y no está cubierto por otro control o vista, se puede establecer el elemento enfocado mediante el control remoto Siri.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con barras de navegación dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
