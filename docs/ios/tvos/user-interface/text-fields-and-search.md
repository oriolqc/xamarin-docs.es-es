---
title: Trabajar con texto y los campos de búsqueda de Xamarin de tvOS
description: Este documento describe cómo trabajar con campos de texto y la búsqueda en una aplicación de tvOS con Xamarin. Proporciona información general de los campos de texto y la búsqueda y se describen los teclados, integración de guión gráfico, modelos de datos de búsqueda y mucho más.
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f1085044f2147bec0910a87f8a6174c4648ef9b8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61162746"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>Trabajar con texto y los campos de búsqueda de Xamarin de tvOS

Cuando sea necesario, la aplicación Xamarin.tvOS puede solicitar pequeños fragmentos de texto al usuario (por ejemplo, los identificadores de usuario y contraseñas) mediante un campo de texto y el teclado en pantalla:

[![](text-fields-and-search-images/intro01.png "Campo de búsqueda de ejemplo")](text-fields-and-search-images/intro01.png#lightbox)

También puede proporcionar capacidad de búsqueda de palabra clave de contenido de la aplicación mediante un campo de búsqueda:

[![](text-fields-and-search-images/intro02.png "Resultados de búsqueda de ejemplo")](text-fields-and-search-images/intro02.png#lightbox)

Este documento trata los detalles de trabajar con texto y campos de búsqueda en una aplicación Xamarin.tvOS.

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>Acerca de texto y los campos de búsqueda

Como se indicó anteriormente, si es necesario, su Xamarin.tvOS pueden presentar uno o varios campos de texto para recopilar pequeñas cantidades de texto de usuario mediante una pantalla (o teclado bluetooth opcional según la versión de tvOS el usuario ha instalado). 

Además, si la aplicación presenta grandes cantidades de contenido para el usuario (por ejemplo, un música, películas o una colección de fotografías), es posible que desee incluir un campo de búsqueda que permite al usuario escribir una pequeña cantidad de texto para filtrar la lista de elementos disponibles.

<a name="Text-Fields" />

## <a name="text-fields"></a>Campos de texto

En tvOS, un campo de texto se presenta como un cuadro de entrada alto fijo, esquina redondeado, aparecerá un teclado en pantalla cuando el usuario hace clic en él:

[![](text-fields-and-search-images/text01.png "Texto en campos tvOS")](text-fields-and-search-images/text01.png#lightbox)

Cuando el usuario mueve [foco](~/ios/tvos/app-fundamentals/navigation-focus.md) a un campo de texto determinado, aumenten de tamaño y mostrar una sombra profunda. Debe tener esto en cuenta al diseñar la interfaz de usuario, como los campos de texto pueden superponerse a otros elementos de interfaz de usuario cuando el foco.

Apple tiene las siguientes sugerencias para trabajar con campos de texto:

- **Utilice la entrada de texto con moderación** : debido a la naturaleza del teclado en pantalla, es tedioso al usuario especificar secciones extensas del texto o rellenar varios campos de texto. Una mejor solución consiste en limitar la cantidad de entrada de texto mediante el uso de las listas de selección o [botones](~/ios/tvos/user-interface/buttons.md).
- **Usar sugerencias a propósito comunicarse** : campo de texto puede mostrar "sugerencias" de marcador de posición cuando están vacías. En su caso, utilice las sugerencias para describir el propósito del campo de texto en lugar de una etiqueta independiente.
- **Seleccione el tipo de teclado predeterminado adecuado** -tvOS proporciona varios tipos de teclado pensadas diferentes, que se pueden especificar para el campo de texto. Por ejemplo, el teclado de la dirección de correo electrónico puede facilitar la entrada, ya que permite al usuario seleccionar de una lista de direcciones incluidas recientemente.
- **Cuando es necesario, utilice campos de texto seguro** -un campo de texto seguro presenta los caracteres escritos como puntos (en lugar de las letras real). Utilice siempre un campo de texto seguro al recopilar la información confidencial como contraseñas.

<a name="Keyboards" />

## <a name="keyboards"></a>Teclados

Cada vez que el usuario hace clic en un campo de texto en la interfaz de usuario, un lineal en pantalla se muestra el teclado. El usuario utiliza la superficie de tocar la [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para seleccionar las letras individuales desde el teclado y escriba la información solicitada:

[![](text-fields-and-search-images/keyboard01.png "El teclado de Siri Remote")](text-fields-and-search-images/keyboard01.png#lightbox)

Si hay más de un campo de texto en la vista actual, un **siguiente** botón se mostrará automáticamente para llevar al usuario al siguiente campo de texto. Un **realiza** botón se mostrará para el último campo de texto que finalizará la entrada de texto y el usuario regresará a la pantalla anterior. 

En cualquier momento, el usuario también puede presionar el **menú** botón del mando a distancia Siri para finalizar la entrada de texto y vuelva a volver a la pantalla anterior.

Apple tiene las siguientes sugerencias para trabajar con pantalla teclados:

- **Seleccione el tipo de teclado predeterminado adecuado** -tvOS proporciona varios tipos de teclado pensadas diferentes, que se pueden especificar para el campo de texto. Por ejemplo, el teclado de la dirección de correo electrónico puede facilitar la entrada, ya que permite al usuario seleccionar de una lista de direcciones incluidas recientemente.
- **Cuando sea adecuado, utilice las vistas de accesorio de teclado** : además de la norma se puede agregar información que siempre se muestran vistas accesorio opcional (por ejemplo, imágenes o etiquetas) para el teclado en pantalla para aclarar el propósito de entrada de texto o a ayudar al usuario escribir la información necesaria.

Para obtener más información sobre cómo trabajar con el teclado en pantalla, consulte Apple [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType), [administrar el teclado](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [vistas personalizadas para la entrada de datos](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) y [ Guía de programación de texto a iOS](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) documentación.

<a name="Search" />

## <a name="search"></a>Buscar

Un campo de búsqueda presentará una pantalla especializada que proporciona un campo de texto y el teclado en pantalla que permite al usuario filtrar una colección de elementos que se muestran debajo del teclado:

[![](text-fields-and-search-images/search01.png "Resultados de búsqueda de ejemplo")](text-fields-and-search-images/search01.png#lightbox)

Cuando el usuario escriba las letras en el campo de búsqueda, los resultados siguientes reflejará automáticamente los resultados de la búsqueda. En cualquier momento, el usuario puede cambiar el foco a los resultados y seleccione uno de los elementos presentados.

Apple tiene las siguientes sugerencias para trabajar con campos de búsqueda:

- **Proporcione búsquedas recientes** : porque la escritura de texto con el control remoto de Siri puede resultar tedioso y los usuarios tienden a repetir las solicitudes de búsqueda, considere la posibilidad de agregar una sección de resultados de búsqueda recientes antes de los resultados actuales en el área de teclado.
- **Cuando sea posible, limite el número de resultados** : porque una amplia lista de elementos puede ser difícil para el usuario analizar y navegar, considere la posibilidad de limitar el número de resultados devueltos.
- **Si es necesario, proporcione resultados de búsqueda filtra** : si el contenido proporcionado por la aplicación se presta, considere la posibilidad de agregar barras de ámbito para permitir al usuario seguir filtrando los resultados de búsqueda devueltos.

Para obtener más información, consulte Apple [referencia de clase UISearchController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html).

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>Trabajar con campos de texto

Es la manera más fácil trabajar con campos de texto en una aplicación Xamarin.tvOS agregarlos al diseño de la interfaz de usuario mediante el Diseñador de iOS.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **panel de solución**, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo.
1. Arrastre una o más **campos de texto** int la superficie de diseño en una vista: 

    [![](text-fields-and-search-images/text02.png "Un campo de texto")](text-fields-and-search-images/text02.png#lightbox)
1. Seleccione el **campos de texto** y asignar a cada una un único **nombre** en el **Widget** pestaña de la **panel de propiedades**: 

    [![](text-fields-and-search-images/text03.png "La pestaña de Widget del panel de propiedades")](text-fields-and-search-images/text03.png#lightbox)
1. En el **campo de texto** sección, puede definir elementos, como el **marcador de posición** predeterminada y sugerencia **valor**: 

    [![](text-fields-and-search-images/text04.png "La sección de campos de texto")](text-fields-and-search-images/text04.png#lightbox)
1. Desplácese hacia abajo para definir las propiedades como **ortográfica**, **capitalización** y el valor predeterminado **tipo de teclado**: 

    [![](text-fields-and-search-images/text05.png "Corrector ortográfico, mayúsculas y minúsculas y el tipo de teclado predeterminada")](text-fields-and-search-images/text05.png#lightbox) 
1. Guarde los cambios en el guión gráfico.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. Haga doble clic en el archivo `Main.storyboard` en el **Explorador de soluciones** para abrirlo para su edición.
1. Arrastre una o más **campos de texto** int la superficie de diseño en una vista: 

    [![](text-fields-and-search-images/text02-vs.png "Un campo de texto")](text-fields-and-search-images/text02-vs.png#lightbox)
1. Seleccione el **campos de texto** y asignar a cada una un único **nombre** en el **Widget** pestaña de la **Explorador de propiedades**: 

    [![](text-fields-and-search-images/text03-vs.png "La pestaña de Widget")](text-fields-and-search-images/text03-vs.png#lightbox)
1. En el **campo de texto** sección, puede definir elementos, como el **marcador de posición** predeterminada y sugerencia **valor**: 

    [![](text-fields-and-search-images/text04-vs.png "La sección de campos de texto")](text-fields-and-search-images/text04-vs.png#lightbox)
1. Desplácese hacia abajo para definir las propiedades como **ortográfica**, **capitalización** y el valor predeterminado **tipo de teclado**: 

    [![](text-fields-and-search-images/text05-vs.png "Corrector ortográfico, mayúsculas y minúsculas y el tipo de teclado predeterminada")](text-fields-and-search-images/text05-vs.png#lightbox) 
1. Guarde los cambios en el guión gráfico.
    
-----

En el código, puede obtener o establecer el valor de un campo de texto con su `Text` propiedad:

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

También puede usar el `Started` y `Ended` eventos de campo de texto para responder a la entrada de texto inicial y final.

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>Trabajar con campos de búsqueda

Es la manera más fácil trabajar con campos de búsqueda en una aplicación Xamarin.tvOS agregarlos al diseño de la interfaz de usuario mediante el Diseñador de interfaz.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
1. En el **panel de solución**, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo.
1. Arrastre un nuevo controlador de vista de colección al guión gráfico para presentar los resultados de búsqueda del usuario: 

    [![](text-fields-and-search-images/search02.png "Un controlador de vista de colección")](text-fields-and-search-images/search02.png#lightbox)
1. En el **Widget** pestaña de la **panel de propiedades**, utilice `SearchResultsViewController` para el **clase** y `SearchResults` para el **identificador de guión gráfico**: 

    [![](text-fields-and-search-images/search03.png "La pestaña de Widget")](text-fields-and-search-images/search03.png#lightbox)
1. Seleccione el **celda prototipo** en la superficie de diseño.
1. En el **Widget** pestaña de la **Explorador de propiedades**, utilice `SearchResultCell` para el **clase** y `ImageCell` para el **identificador**: 

    [![](text-fields-and-search-images/search04.png "La pestaña de Widget")](text-fields-and-search-images/search04.png#lightbox)
1. El diseño de diseño de la **prototipo de la celda** y exponer cada elemento con un único **nombre** en el **Widget** pestaña de la **elExploradordepropiedades**: 

    [![](text-fields-and-search-images/search05.png "El diseño del prototipo de celda del diseño")](text-fields-and-search-images/search05.png#lightbox)
1. Guarde los cambios en el guión gráfico.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. Haga doble clic en el archivo `Main.storyboard` en el **Explorador de soluciones** para abrirlo para su edición.
1. Arrastre un nuevo controlador de vista de colección al guión gráfico para presentar los resultados de búsqueda del usuario: 

    [![](text-fields-and-search-images/seach02-vs.png "Un controlador de vista de colección")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. En el **Widget** pestaña de la **Explorador de propiedades**, utilice `SearchResultsViewController` para el **clase** y `SearchResults` para el **identificador de guión gráfico**: 

    [![](text-fields-and-search-images/search03-vs.png "La pestaña de Widget")](text-fields-and-search-images/search03-vs.png#lightbox)
1. Seleccione el **celda prototipo** en la superficie de diseño.
1. En el **Widget** pestaña de la **Explorador de propiedades**, utilice `SearchResultCell` para el **clase** y `ImageCell` para el **identificador**: 

    [![](text-fields-and-search-images/search04-vs.png "La pestaña de Widget")](text-fields-and-search-images/search04-vs.png#lightbox)
1. El diseño de diseño de la **prototipo de la celda** y exponer cada elemento con un único **nombre** en el **Widget** pestaña de la **elExploradordepropiedades**: 

    [![](text-fields-and-search-images/search05-vs.png "El diseño del prototipo de celda del diseño")](text-fields-and-search-images/search05-vs.png#lightbox)
1. Guarde los cambios en el guión gráfico.
    
-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>Proporcionar un modelo de datos

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A continuación, deberá proporcionar una clase para que actúe como el modelo de datos para los resultados que se buscarán el usuario. En el **el Explorador de soluciones**, haga clic en el nombre del proyecto y seleccione **agregar** > **nuevo archivo...**   >  **General** > **clase vacía** y proporcionar un **nombre**: 

[![](text-fields-and-search-images/search06.png "Seleccione clase vacía y proporcione un nombre")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A continuación, deberá proporcionar una clase para que actúe como el modelo de datos para los resultados que se buscarán el usuario. En el **el Explorador de soluciones**, haga clic en el nombre del proyecto y seleccione **agregar** > **nuevo elemento...**   >  **Apple** > **Misc** > **clase** y proporcionar un **nombre**: 

[![](text-fields-and-search-images/search06-vs.png "Seleccione la clase y proporcione un nombre")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

Por ejemplo, una aplicación que permite al usuario a una colección de imágenes por título y la palabra clave de búsqueda podría parecerse a lo siguiente:

```csharp
using System;
using Foundation;

namespace tvText
{
    public class PictureInformation : NSObject
    {
        #region Computed Properties
        public string Title { get; set;}
        public string ImageName { get; set;}
        public string Keywords { get; set;}
        #endregion

        #region Constructors
        public PictureInformation (string title, string imageName, string keywords)
        {
            // Initialize
            this.Title = title;
            this.ImageName = imageName;
            this.Keywords = keywords;
        }
        #endregion
    }
}
```

<a name="The-Collection-View-Cell" />

### <a name="the-collection-view-cell"></a>La celda de vista de colección

Con el modelo de datos en su lugar, edite el **prototipo celda** (`SearchResultViewCell.cs`) y conviértalo en apariencia se encuentran los siguientes:

```csharp
using Foundation;
using System;
using UIKit;

namespace tvText
{
    public partial class SearchResultViewCell : UICollectionViewCell
    {
        #region Private Variables
        private PictureInformation _pictureInfo = null;
        #endregion

        #region Computed Properties
        public PictureInformation PictureInfo {
            get { return _pictureInfo; }
            set {
                _pictureInfo = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            UpdateUI ();
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Anything to process?
            if (PictureInfo == null) return;

            try {
                Picture.Image = UIImage.FromBundle (PictureInfo.ImageName);
                Picture.AdjustsImageWhenAncestorFocused = true;
                Title.Text = PictureInfo.Title;
                TextColor = UIColor.LightGray;
            } catch {
                // Ignore errors if view isn't fully loaded
            }
        }
        #endregion
    }

}
```

El `UpdateUI` método se usará para mostrar los campos individuales de la **PictureInformation** elementos (el `PictureInfo` propiedad) en los elementos con nombre de la interfaz de usuario cada vez que se actualiza la propiedad. Por ejemplo, la imagen y el título asociado a la imagen.

<a name="The-Collection-View-Controller" />

### <a name="the-collection-view-controller"></a>El controlador de vista de colección

A continuación, modifique el controlador de vista de colección de resultados de búsqueda (`SearchResultsViewController.cs`) y darle un aspecto similar al siguiente:

```csharp
using Foundation;
using System;
using UIKit;
using System.Collections.Generic;

namespace tvText
{
    public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
    {
        #region Constants
        public const string CellID = "ImageCell";
        #endregion

        #region Private Variables
        private string _searchFilter = "";
        #endregion

        #region Computed Properties
        public List<PictureInformation> AllPictures { get; set;}
        public List<PictureInformation> FoundPictures { get; set; }
        public string SearchFilter {
            get { return _searchFilter; }
            set {
                _searchFilter = value.ToLower();
                FindPictures ();
                CollectionView?.ReloadData ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultsViewController (IntPtr handle) : base (handle)
        {
            // Initialize
            this.AllPictures = new List<PictureInformation> ();
            this.FoundPictures = new List<PictureInformation> ();
            PopulatePictures ();
            FindPictures ();

        }
        #endregion

        #region Private Methods
        private void PopulatePictures ()
        {
            // Clear list
            AllPictures.Clear ();

            // Add images
            AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
            AllPictures.Add (new PictureInformation ("Cheese Plate", "CheesePlate", "cheese,plate,bread"));
            AllPictures.Add (new PictureInformation ("Coffee House", "CoffeeHouse", "coffee,people,menu,restaurant,cafe"));
            AllPictures.Add (new PictureInformation ("Computer and Expresso", "ComputerExpresso", "computer,coffee,expresso,phone,notebook"));
            AllPictures.Add (new PictureInformation ("Hamburger", "Hamburger", "meat,bread,cheese,tomato,pickle,lettus"));
            AllPictures.Add (new PictureInformation ("Lasagna Dinner", "Lasagna", "salad,bread,plate,lasagna,pasta"));
            AllPictures.Add (new PictureInformation ("Expresso Meeting", "PeopleExpresso", "people,bag,phone,expresso,coffee,table,tablet,notebook"));
            AllPictures.Add (new PictureInformation ("Soup and Sandwich", "SoupAndSandwich", "soup,sandwich,bread,meat,plate,tomato,lettus,egg"));
            AllPictures.Add (new PictureInformation ("Morning Coffee", "TabletCoffee", "tablet,person,man,coffee,magazine,table"));
            AllPictures.Add (new PictureInformation ("Evening Coffee", "TabletMagCoffee", "tablet,magazine,coffee,table"));
        }

        private void FindPictures ()
        {
            // Clear list
            FoundPictures.Clear ();

            // Scan each picture for a match
            foreach (PictureInformation picture in AllPictures) {
                if (SearchFilter == "") {
                    // If no search term, everything matches
                    FoundPictures.Add (picture);
                } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
                    // If the search term is in the title or keywords, we've found a match
                    FoundPictures.Add (picture);
                }
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            // Only one section in this collection
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            // Return the number of matching pictures
            return FoundPictures.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a new cell and return it
            var cell = collectionView.DequeueReusableCell (CellID, indexPath);
            return (UICollectionViewCell)cell;
        }

        public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
        {
            // Grab the cell
            var currentCell = cell as SearchResultViewCell;
            if (currentCell == null)
                throw new Exception ("Expected to display a `SearchResultViewCell`.");

            // Display the current picture info in the cell
            var item = FoundPictures [indexPath.Row];
            currentCell.PictureInfo = item;
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // If this Search Controller was presented as a modal view, close
            // it before continuing
            // DismissViewController (true, null);

            // Grab the picture being selected and report it
            var picture = FoundPictures [indexPath.Row];
            Console.WriteLine ("Selected: {0}", picture.Title);
        }

        public void UpdateSearchResultsForSearchController (UISearchController searchController)
        {
            // Save the search filter and update the Collection View
            SearchFilter = searchController.SearchBar.Text ?? string.Empty;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
            if (previousItem != null) {
                UIView.Animate (0.2, () => {
                    previousItem.TextColor = UIColor.LightGray;
                });
            }

            var nextItem = context.NextFocusedView as SearchResultViewCell;
            if (nextItem != null) {
                UIView.Animate (0.2, () => {
                    nextItem.TextColor = UIColor.Black;
                });
            }
        }
        #endregion
    }
}
```

En primer lugar, el `IUISearchResultsUpdating` interfaz se agrega a la clase para controlar el filtro de controlador de búsqueda que se está actualizando el usuario:

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

También se define una constante para especificar el identificador de la **celda prototipo** (que coincide con el identificador definido en el Diseñador de la interfaz anterior) que se usará más adelante cuando el controlador de la colección solicita una nueva celda:

```csharp
public const string CellID = "ImageCell";
```

Se crea el almacenamiento de la lista completa de los elementos que se busca el término de filtro de búsqueda y una lista de elementos que coinciden con ese término:

```csharp
private string _searchFilter = "";
...

public List<PictureInformation> AllPictures { get; set;}
public List<PictureInformation> FoundPictures { get; set; }
public string SearchFilter {
    get { return _searchFilter; }
    set {
        _searchFilter = value.ToLower();
        FindPictures ();
        CollectionView?.ReloadData ();
    }
}
```

Cuando el `SearchFilter` es cambia, se actualiza la lista de elementos coincidentes y se vuelve a cargar el contenido de la vista de colección. El `FindPictures` rutina es responsable de encontrar los elementos que coinciden con el nuevo término de búsqueda:

```csharp
private void FindPictures ()
{
    // Clear list
    FoundPictures.Clear ();

    // Scan each picture for a match
    foreach (PictureInformation picture in AllPictures) {
        if (SearchFilter == "") {
            // If no search term, everything matches
            FoundPictures.Add (picture);
        } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
            // If the search term is in the title or keywords, we've found a match
            FoundPictures.Add (picture);
        }
    }
}
```

El valor de la `SearchFilter` se actualizará (que actualizará la vista de colección de resultados) cuando el usuario cambia el filtro en el controlador de búsqueda:

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

El `PopulatePictures` método inicialmente rellena la colección de elementos disponibles:

```csharp
private void PopulatePictures ()
{
    // Clear list
    AllPictures.Clear ();

    // Add images
    AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
    ...
}
```

Para este ejemplo, todos los datos de ejemplo se está creando en la memoria cuando se carga el controlador de vista de colección. En una aplicación real, probablemente se leerá estos datos desde una base de datos o servicio web y solo según sea necesario para impedir sobrepasar el televisor Apple memoria limitada.

El `NumberOfSections` y `GetItemsCount` métodos proporcionan el número de elementos coincidentes:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    // Only one section in this collection
    return 1;
}

public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    // Return the number of matching pictures
    return FoundPictures.Count;
}
```

El `GetCell` devuelve un nuevo método **prototipo celda** (según la `CellID` definido anteriormente en el guión gráfico) para cada elemento de la vista de colección:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

El `WillDisplayCell` se llama al método antes de la celda que se muestran por lo que puede configurarse:

```csharp
public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
{
    // Grab the cell
    var currentCell = cell as SearchResultViewCell;
    if (currentCell == null)
        throw new Exception ("Expected to display a `SearchResultViewCell`.");

    // Display the current picture info in the cell
    var item = FoundPictures [indexPath.Row];
    currentCell.PictureInfo = item;
}
```

El `DidUpdateFocus` método proporciona comentarios visuales al usuario como que resaltan los elementos en la vista de colección de resultados:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
    if (previousItem != null) {
        UIView.Animate (0.2, () => {
            previousItem.TextColor = UIColor.LightGray;
        });
    }

    var nextItem = context.NextFocusedView as SearchResultViewCell;
    if (nextItem != null) {
        UIView.Animate (0.2, () => {
            nextItem.TextColor = UIColor.Black;
        });
    }
}
```

Por último, el `ItemSelected` método controla el usuario selecciona un elemento (al hacer clic en la superficie de interacción con el servidor remoto de Siri) de la vista de colección de resultados:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // If this Search Controller was presented as a modal view, close
    // it before continuing
    // DismissViewController (true, null);

    // Grab the picture being selected and report it
    var picture = FoundPictures [indexPath.Row];
    Console.WriteLine ("Selected: {0}", picture.Title);
}
```

Si el campo de búsqueda se presenta como una vista de cuadro de diálogo modal (a través de la parte superior de la vista que lo llamen), use el `DismissViewController` método para cerrar la vista de búsqueda cuando el usuario selecciona un elemento. En este ejemplo, el campo de búsqueda se presenta como el contenido de una pestaña de la pestaña vista, por lo que no está descartando aquí.

Para obtener más información sobre las vistas de colección, consulte nuestra [trabajar con las vistas de colección](~/ios/tvos/user-interface/collection-views.md) documentación.

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>Presenta el campo de búsqueda

Hay dos maneras principales que un campo de búsqueda (y sus asociados, teclado en pantalla y los resultados de búsqueda) se pueden presentar al usuario en tvOS: 

- **Vista de cuadro de diálogo modal** -el campo de búsqueda se pueden presentar a través de la actual, vista y controlador de vista como una vista de cuadro de diálogo modal de pantalla completa. Esto se hace normalmente en respuesta al usuario al hacer clic en un botón u otro elemento de interfaz de usuario. El cuadro de diálogo se cierra cuando el usuario selecciona un elemento en los resultados de búsqueda.
- **Ver contenido** -el campo de búsqueda es una parte directa de una vista determinada. Por ejemplo, como el contenido de una pestaña de búsqueda en un controlador de vista de ficha.

El ejemplo de una lista de búsqueda de imágenes proporcionada más arriba, el campo de búsqueda se presenta como contenido de la vista en la pestaña de búsqueda y el controlador de vista de la pestaña de búsqueda es similar a la siguiente:

```csharp
using System;
using UIKit;

namespace tvText
{
    public partial class SecondViewController : UIViewController
    {
        #region Constants
        public const string SearchResultsID = "SearchResults";
        #endregion

        #region Computed Properties
        public SearchResultsViewController ResultsController { get; set;}
        #endregion

        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        public void ShowSearchController ()
        {
            // Build an instance of the Search Results View Controller from the Storyboard
            ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
            if (ResultsController == null)
                throw new Exception ("Unable to instantiate a SearchResultsViewController.");

            // Create an initialize a new search controller
            var searchController = new UISearchController (ResultsController) {
                SearchResultsUpdater = ResultsController,
                HidesNavigationBarDuringPresentation = false
            };

            // Set any required search parameters
            searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

            // The Search Results View Controller can be presented as a modal view
            // PresentViewController (searchController, true, null);

            // Or in the case of this sample, the Search View Controller is being
            // presented as the contents of the Search Tab directly. Use either one
            // or the other method to display the Search Controller (not both).
            var container = new UISearchContainerViewController (searchController);
            var navController = new UINavigationController (container);
            AddChildViewController (navController);
            View.Add (navController.View);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // If the Search Controller is being displayed as the content
            // of the search tab, include it here.
            ShowSearchController ();
        }

        public override void ViewDidAppear (bool animated)
        {
            base.ViewDidAppear (animated);

            // If the Search Controller is being presented as a modal view,
            // call it here to display it over the contents of the Search
            // tab.
            // ShowSearchController ();
        }
        #endregion
    }
}
```

En primer lugar, se define una constante que coincide con el **identificador de guión gráfico** que asignó en el controlador de vista de colección de resultados de búsqueda en el Diseñador de la interfaz:

```csharp
public const string SearchResultsID = "SearchResults";
```

A continuación, el `ShowSearchController` método crea un nuevo controlador de colección de la vista de búsqueda y muestra se necesitaba:

```csharp
public void ShowSearchController ()
{
    // Build an instance of the Search Results View Controller from the Storyboard
    ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
    if (ResultsController == null)
        throw new Exception ("Unable to instantiate a SearchResultsViewController.");

    // Create an initialize a new search controller
    var searchController = new UISearchController (ResultsController) {
        SearchResultsUpdater = ResultsController,
        HidesNavigationBarDuringPresentation = false
    };

    // Set any required search parameters
    searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

    // The Search Results View Controller can be presented as a modal view
    // PresentViewController (searchController, true, null);

    // Or in the case of this sample, the Search View Controller is being
    // presented as the contents of the Search Tab directly. Use either one
    // or the other method to display the Search Controller (not both).
    var container = new UISearchContainerViewController (searchController);
    var navController = new UINavigationController (container);
    AddChildViewController (navController);
    View.Add (navController.View);
}
```

En el método anterior, una vez un `SearchResultsViewController` se ha creado una instancia desde el guión gráfico, un nuevo `UISearchController` se crea para presentar el campo de búsqueda y el teclado en pantalla para el usuario. La colección de resultados de búsqueda (como se define en el `SearchResultsViewController`) se mostrará en el teclado.

A continuación, el `SearchBar` se configura con la información como el **marcador de posición** sugerencia. Esto proporciona información al usuario sobre el tipo de búsqueda que se realizan.

A continuación, el campo de búsqueda se presenta al usuario en uno de dos maneras:

- **Vista de cuadro de diálogo modal** : la `PresentViewController` método se llama para presentar la búsqueda a través de la vista existente, pantalla completa.
- **Ver contenido** - A `UISearchContainerViewController` se crea para contener el controlador de búsqueda. Un `UINavigationController` se crea para contener el contenedor de búsqueda, a continuación, se agrega el controlador de navegación para el controlador de vista `AddChildViewController (navController)`y la vista presentadas `View.Add (navController.View)`.

Por último y vuelva a según el tipo de presentación, ya sea el `ViewDidLoad` o `ViewDidAppear` método llamará el `ShowSearchController` método para presentar la búsqueda para el usuario:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // If the Search Controller is being displayed as the content
    // of the search tab, include it here.
    ShowSearchController ();
}

public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    // If the Search Controller is being presented as a modal view,
    // call it here to display it over the contents of the Search
    // tab.
    // ShowSearchController ();
}
```

Cuando se ejecuta la aplicación y la pestaña de búsqueda seleccionada por el usuario, la lista completa sin filtrar de los elementos se presentará al usuario:

[![](text-fields-and-search-images/intro02.png "Resultados de búsqueda predeterminado")](text-fields-and-search-images/intro02.png#lightbox)

Como el usuario empieza a escribir un término de búsqueda, la lista de resultados se filtran por ese término y se actualiza automáticamente:

[![](text-fields-and-search-images/intro03.png "Resultados de búsqueda filtrados")](text-fields-and-search-images/intro03.png#lightbox)

En cualquier momento, el usuario puede cambiar el foco a un elemento en los resultados de búsqueda y haga clic en la superficie de interacción de Siri remoto para seleccionarlo.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con texto y campos de búsqueda dentro de una aplicación Xamarin.tvOS. Se ha mostrado cómo crear contenido de texto y la colección de búsqueda en el Diseñador de interfaz y se ha mostrado a dos maneras diferentes de un campo de búsqueda podrían presentarse al usuario en tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
