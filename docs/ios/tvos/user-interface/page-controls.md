---
title: "Trabajar con el Control de página"
description: "Este artículo tratan diseñar y trabajar con el Control de página dentro de una aplicación Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b1b53fefdd72c36bdffd3c5ade0b8d86da225b14
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-page-control"></a>Trabajar con el Control de página

_Este artículo tratan diseñar y trabajar con el Control de página dentro de una aplicación Xamarin.tvOS._

En ocasiones sea necesario mostrar una serie de páginas o imágenes en la aplicación Xamarin.tvOS. Un Control de página se diseñó para mostrar con claridad qué página de un usuario está en el número máximo de páginas. Un Control de la página muestra una serie de puntos en un oscuro, oval en forma de segundo plano. Mostrará la página actual un punto relleno, todas las demás páginas que se muestran como puntos vacíos. El control de página ajustará los puntos de mayoría externos si hay demasiadas para que quepa en su área de fondo.

[![](page-controls-images/page01.png "Control de la página de ejemplo")](page-controls-images/page01.png#lightbox)

Un Control de página en un elemento no interactivo diseñado para proporcionar comentarios al usuario solo. Debe agregar otros controles para cambiar el número de página actual (por ejemplo, gestos o botones).

Apple tiene las siguientes sugerencias cuando se utiliza un Control de página:

- **Use solamente para colecciones completas de** -página controles funcionan mejor en un entorno de pantalla completa para mostrar varias páginas que existen en una sola colección.
- **Limitar el número de páginas** -página controles funcionan mejor para los diez (10) o menos páginas y un máximo de páginas de veinte (20). Para más de veinte páginas, considere el uso de un [vista de colección](~/ios/tvos/user-interface/collection-views.md) y mostrar las páginas en una cuadrícula.

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>Controles de página y los guiones gráficos

Es la manera más fácil trabajar con controles de página en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

    
1. En el **solución Pad**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **Control de la página** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](page-controls-images/page02.png "Un Control de página")](page-controls-images/page02.png#lightbox)
1. En el **ficha Widget** de la **panel de propiedades**, puede ajustar varias propiedades del Control de página como su **página actual** y **# de páginas**: 

    [![](page-controls-images/page03.png "La pestaña de Widget")](page-controls-images/page03.png#lightbox)
1. A continuación, agregar controles o movimientos a la vista para avanzar y retroceder a través de la colección de páginas.
1. Por último, asignar **nombres** a los controles de modo que pueda responder a ellos en código C#. Por ejemplo: 

    [![](page-controls-images/page04.png "Nombre del control")](page-controls-images/page04.png#lightbox)
1. Guarde los cambios.
    

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

    
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **Control de la página** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](page-controls-images/page02-vs.png "Un Control de página")](page-controls-images/page02-vs.png#lightbox)
1. En el **ficha Widget** de la **el Explorador de propiedades**, puede ajustar varias propiedades del Control de página como su **página actual** y **# de páginas**: 

    [![](page-controls-images/page03-vs.png "La pestaña de Widget")](page-controls-images/page03-vs.png#lightbox)
1. A continuación, agregar controles o movimientos a la vista para avanzar y retroceder a través de la colección de páginas.
1. Por último, asignar **nombres** a los controles de modo que pueda responder a ellos en código C#. Por ejemplo: 

    [![](page-controls-images/page04-vs.png "Nombre del control")](page-controls-images/page04-vs.png#lightbox)
1. Guarde los cambios.
    

-----

> [!IMPORTANT]
> Aunque es posible asignar eventos como `TouchUpInside` a un elemento de interfaz de usuario (por ejemplo, un UIButton) en el Diseñador de iOS, nunca se llamará Apple TV no tiene un toque la pantalla o admitir eventos de toque. Debe utilizar siempre el `Primary Action` eventos al crear controladores de eventos para tvOS elementos de la interfaz de usuario.




Editar el controlador de vista (ejemplo `ViewController.cs`) de archivos y agregue el código para controlar las páginas que se va a cambiar. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

Echemos un vistazo más de cerca a dos propiedades del Control de página. En primer lugar, para especificar el número máximo de páginas, use lo siguiente:

```csharp
PageView.Pages = 6;
```

Para cambiar el número de página actual, utilice el código siguiente:

```csharp
PageView.CurrentPage = PageNumber;
```

El `CurrentPage` propiedad es cero (0) en función, por lo que la primera página será cero y el último será uno menos el número máximo de páginas.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con el Control de página dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
