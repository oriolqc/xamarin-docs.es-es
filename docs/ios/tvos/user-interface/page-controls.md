---
title: Trabajar con controles de página en Xamarin de tvOS
description: Este documento describe cómo trabajar con controles de la página de tvOS en una aplicación compilada con Xamarin. Proporciona una descripción detallada de los controles de página, se describe cómo configurar en los guiones gráficos y examina cómo responder a eventos de cambio de la página.
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 173bc7713b5b8c330d4d4c5863bef24be8bdcb52
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61179671"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>Trabajar con controles de página en Xamarin de tvOS

A veces es posible que deba mostrar una serie de páginas o imágenes en la aplicación Xamarin.tvOS. Un Control de página se diseñó para mostrar con claridad qué página de un usuario queda en el número máximo de páginas. Un Control de página muestra una serie de puntos en un oscuro, oval en forma de fondo. La página actual mostrará un punto relleno, todas las demás páginas se muestran como puntos huecos. El control de página ajustará los puntos más externo si hay demasiados como para caber en su área de fondo.

[![](page-controls-images/page01.png "Control de página de ejemplo")](page-controls-images/page01.png#lightbox)

Un Control de página en un elemento no interactivo diseñado para proporcionar comentarios al usuario solo. Deberá agregar otros controles para cambiar el número de página actual (por ejemplo, gestos o botones).

Apple tiene las siguientes sugerencias cuando se usa un Control de página:

- **Use sólo las colecciones completas en** -página controles funcionan mejor en un entorno de pantalla completa para mostrar varias páginas que existen en una sola colección.
- **Limitar el número de páginas** -controles de página funcionan mejor para las páginas de diez (10) o menos y un máximo de páginas de veinte (20). Para más de veinte páginas, considere el uso de un [vista de colección](~/ios/tvos/user-interface/collection-views.md) y mostrar las páginas en una cuadrícula.

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>Controles de página y los guiones gráficos

Es la manera más fácil trabajar con controles de página en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

    
1. En el **panel de solución**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **Control de página** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](page-controls-images/page02.png "Un Control de página")](page-controls-images/page02.png#lightbox)
1. En el **ficha Widget** de la **panel de propiedades**, puede ajustar varias propiedades del Control de página, como su **página actual** y **n.º de páginas**: 

    [![](page-controls-images/page03.png "La pestaña de Widget")](page-controls-images/page03.png#lightbox)
1. A continuación, agregue controles o movimientos a la vista para avanzar y retroceder a través de la colección de páginas.
1. Finalmente, asigne **nombres** a los controles para que pueda responder a ellos en C# código. Por ejemplo: 

    [![](page-controls-images/page04.png "Nombre del control")](page-controls-images/page04.png#lightbox)
1. Guarde los cambios.
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **Control de página** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](page-controls-images/page02-vs.png "Un Control de página")](page-controls-images/page02-vs.png#lightbox)
1. En el **ficha Widget** de la **Explorador de propiedades**, puede ajustar varias propiedades del Control de página, como su **página actual** y **n.º de páginas**: 

    [![](page-controls-images/page03-vs.png "La pestaña de Widget")](page-controls-images/page03-vs.png#lightbox)
1. A continuación, agregue controles o movimientos a la vista para avanzar y retroceder a través de la colección de páginas.
1. Finalmente, asigne **nombres** a los controles para que pueda responder a ellos en C# código. Por ejemplo: 

    [![](page-controls-images/page04-vs.png "Nombre del control")](page-controls-images/page04-vs.png#lightbox)
1. Guarde los cambios.
    

-----

> [!IMPORTANT]
> Aunque es posible asignar como eventos `TouchUpInside` a un elemento de interfaz de usuario (por ejemplo, un UIButton) en el Diseñador de iOS, nunca se llamará porque Apple TV no tiene una entrada táctil de pantalla o admitir eventos de toque. Siempre debe usar el `Primary Action` evento al crear controladores de eventos para tvOS elementos de la interfaz de usuario.

Editar el controlador de vista (ejemplo `ViewController.cs`) y agregue el código para controlar las páginas que se está cambiando. Por ejemplo:

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

Echemos un vistazo a dos propiedades del Control de página. En primer lugar, para especificar el número máximo de páginas, use lo siguiente:

```csharp
PageView.Pages = 6;
```

Para cambiar el número de página actual, use el siguiente código:

```csharp
PageView.CurrentPage = PageNumber;
```

El `CurrentPage` propiedad es cero (0) se basa, por lo que la primera página será cero y el último será uno menos el número máximo de páginas.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con Control de página dentro de una aplicación Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
