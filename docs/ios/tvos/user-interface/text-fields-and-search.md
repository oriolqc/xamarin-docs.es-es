---
title: "Trabajar con texto y campos de búsqueda"
description: "Este artículo tratan diseñar y trabajar con texto y los campos de búsqueda dentro de una aplicación Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7d58c30e745e26d1076e75470e527cbe95e85eb6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-text-and-search-fields"></a>Trabajar con texto y campos de búsqueda

_Este artículo tratan diseñar y trabajar con texto y los campos de búsqueda dentro de una aplicación Xamarin.tvOS._



Cuando sea necesario, la aplicación Xamarin.tvOS puede solicitar pequeños fragmentos de texto del usuario (por ejemplo, Id. de usuario y contraseñas) con un campo de texto y el teclado en pantalla:

[![](text-fields-and-search-images/intro01.png "Campo de búsqueda de ejemplo")](text-fields-and-search-images/intro01.png#lightbox)

También puede proporcionar capacidad de búsqueda de palabra clave de contenido de la aplicación mediante un campo de búsqueda:

[![](text-fields-and-search-images/intro02.png "Resultados de la búsqueda de ejemplo")](text-fields-and-search-images/intro02.png#lightbox)

Este documento tratará la información detallada sobre cómo trabajar con texto y los campos de búsqueda en una aplicación Xamarin.tvOS.

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>Acerca de los campos de búsqueda y de texto

Como se mencionó anteriormente, si es necesario, su Xamarin.tvOS puede presentar uno o más campos de texto para recopilar pequeñas cantidades de texto de usuario mediante una pantalla (o el teclado de bluetooth opcional según la versión de tvOS el usuario ha instalado). 

Además, si la aplicación presenta grandes cantidades de contenido para el usuario (por ejemplo, un música, películas o una colección de fotografías), puede incluir un campo de búsqueda que permite al usuario que escriba una pequeña cantidad de texto para filtrar la lista de elementos disponibles.

<a name="Text-Fields" />

## <a name="text-fields"></a>Campos de texto

En tvOS, un campo de texto se presenta como un cuadro de entrada de alto fijo, esquina redondeado que le llevará a un teclado en pantalla cuando el usuario hace clic en él:

[![](text-fields-and-search-images/text01.png "Texto en campos tvOS")](text-fields-and-search-images/text01.png#lightbox)

Cuando el usuario mueve [foco](~/ios/tvos/app-fundamentals/navigation-focus.md) a un campo de texto determinado, se aumenta el y mostrar una sombra profunda. Debe tener esto en cuenta al diseñar la interfaz de usuario, como campos de texto puede superponerse a otros elementos de interfaz de usuario cuando el foco.

Apple tiene las siguientes sugerencias para trabajar con campos de texto:

- **Utilice la entrada de texto con moderación** : debido a la naturaleza del teclado en pantalla, escriba secciones extensas del texto o rellenar varios campos de texto es tedioso al usuario. Una solución mejor consiste en limitar la cantidad de entrada de texto mediante el uso de listas de selección o [botones](~/ios/tvos/user-interface/buttons.md).
- **Usar sugerencias a fin de comunicarse** : campo de texto puede mostrar sugerencias de marcador de posición"" cuando está vacío. Si procede, usar sugerencias para describir el propósito de su campo de texto en lugar de una etiqueta independiente.
- **Seleccione el tipo de teclado predeterminado adecuado** -tvOS proporciona varios tipos de teclado pensadas diferentes, que puede especificar para el campo de texto. Por ejemplo, el teclado de la dirección de correo electrónico puede facilitar la entrada por lo que permite al usuario seleccionar de una lista de direcciones introducidas recientemente.
- **Cuando sea apropiado, utilice campos de texto proteger** -un campo de texto seguros presenta los caracteres escritos como puntos (en lugar de las letras reales). Utilice siempre un campo de texto seguros al recopilar información confidencial como contraseñas.

<a name="Keyboards" />

## <a name="keyboards"></a>Teclados

Cada vez que el usuario hace clic en un campo de texto en la interfaz de usuario, un lineal en pantalla se muestra el teclado. El usuario usa la superficie tocan la [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para seleccionar letras individuales desde el teclado y escriba la información solicitada:

[![](text-fields-and-search-images/keyboard01.png "El teclado remoto Siri")](text-fields-and-search-images/keyboard01.png#lightbox)

Si hay más de un campo de texto en la vista actual, un **siguiente** botón se mostrará automáticamente para seguir el usuario para el siguiente campo de texto. A **realiza** botón se mostrará el último campo de texto que se finalice la entrada de texto y el usuario regresará a la pantalla anterior. 

En cualquier momento, el usuario también puede presionar la **menú** botón del mando a distancia Siri para finalizar la entrada de texto y vuelva a volver a la pantalla anterior.

Apple consta de las siguientes sugerencias para trabajar con las que aparecen en pantalla teclados:

- **Seleccione el tipo de teclado predeterminado adecuado** -tvOS proporciona varios tipos de teclado pensadas diferentes, que puede especificar para el campo de texto. Por ejemplo, el teclado de la dirección de correo electrónico puede facilitar la entrada por lo que permite al usuario seleccionar de una lista de direcciones introducidas recientemente.
- **Cuando corresponda, utilizar las vistas de accesorio de teclado** : además de la norma puede agregar información que siempre se muestran vistas accesorio opcional (como imágenes o etiquetas) en el teclado en pantalla para aclarar el propósito de entrada de texto o a ayudar al usuario especificar la información necesaria.

Para obtener más información sobre cómo trabajar con el teclado en pantalla, consulte de Apple [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType), [administrar el teclado](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [vistas personalizadas para la entrada de datos](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) y [ Guía de programación de texto para iOS](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) documentación.

<a name="Search" />

## <a name="search"></a>Buscar

Un campo de búsqueda presentará una pantalla especializada que proporciona un campo de texto y teclado en pantalla que permite al usuario filtrar una colección de elementos que se muestran debajo del teclado:

[![](text-fields-and-search-images/search01.png "Resultados de la búsqueda de ejemplo")](text-fields-and-search-images/search01.png#lightbox)

Cuando el usuario escribe letras en el campo de búsqueda, los resultados siguientes reflejan automáticamente los resultados de la búsqueda. En cualquier momento, el usuario puede cambiar el foco a los resultados y seleccione uno de los elementos que se presentan.

Apple tiene las siguientes sugerencias para trabajar con campos de búsqueda:

- **Proporcionar búsquedas recientes** : porque escribir texto con el control remoto Siri puede resultar tedioso y los usuarios tienden a repetir las solicitudes de búsqueda, considere la posibilidad de agregar una sección de resultados de búsqueda recientes antes de los resultados actuales en el área de teclado.
- **Cuando sea posible, limite el número de resultados** : porque una amplia lista de elementos puede ser difícil para el usuario analizar y navegar, considere la posibilidad de limitar el número de resultados devueltos.
- **Si es necesario, proporcione resultados de búsqueda filtra** : si el contenido proporcionado por la aplicación se presta, considere la posibilidad de agregar barras de ámbito para permitir al usuario seguir filtrando los resultados de búsqueda devueltos.

Para obtener más información, vea de Apple [UISearchController Class Reference](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html).

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>Trabajar con campos de texto

La manera más fácil trabajar con campos de texto en una aplicación de Xamarin.tvOS es agregarlos en el diseño de interfaz de usuario mediante el Diseñador de iOS.

Haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **solución Pad**, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo.
1. Arrastre una o más **campos de texto** int la superficie de diseño en una vista: 

    [![](text-fields-and-search-images/text02.png "Un campo de texto")](text-fields-and-search-images/text02.png#lightbox)
1. Seleccione el **campos de texto** y asigne a cada uno un único **nombre** en el **Widget** pestaña de la **panel de propiedades**: 

    [![](text-fields-and-search-images/text03.png "La pestaña de Widget de panel de propiedades")](text-fields-and-search-images/text03.png#lightbox)
1. En el **campo de texto** sección, puede definir elementos, como el **marcador de posición** sugerencia y default **valor**: 

    [![](text-fields-and-search-images/text04.png "La sección de campos de texto")](text-fields-and-search-images/text04.png#lightbox)
1. Desplácese hacia abajo para definir las propiedades como **ortográfica**, **mayúsculas y minúsculas** y el valor predeterminado **teclado tipo**: 

    [![](text-fields-and-search-images/text05.png "Corrector ortográfico, mayúsculas y minúsculas y el tipo de teclado predeterminado")](text-fields-and-search-images/text05.png#lightbox) 
1. Guarde los cambios en el guión gráfico.
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. Haga doble clic en el archivo `Main.storyboard` en el **Explorador de soluciones** para abrirlo para su edición.
1. Arrastre una o más **campos de texto** int la superficie de diseño en una vista: 

    [![](text-fields-and-search-images/text02-vs.png "Un campo de texto")](text-fields-and-search-images/text02-vs.png#lightbox)
1. Seleccione el **campos de texto** y asigne a cada uno un único **nombre** en el **Widget** pestaña de la **el Explorador de propiedades**: 

    [![](text-fields-and-search-images/text03-vs.png "La pestaña de Widget")](text-fields-and-search-images/text03-vs.png#lightbox)
1. En el **campo de texto** sección, puede definir elementos, como el **marcador de posición** sugerencia y default **valor**: 

    [![](text-fields-and-search-images/text04-vs.png "La sección de campos de texto")](text-fields-and-search-images/text04-vs.png#lightbox)
1. Desplácese hacia abajo para definir las propiedades como **ortográfica**, **mayúsculas y minúsculas** y el valor predeterminado **teclado tipo**: 

    [![](text-fields-and-search-images/text05-vs.png "Corrector ortográfico, mayúsculas y minúsculas y el tipo de teclado predeterminado")](text-fields-and-search-images/text05-vs.png#lightbox) 
1. Guarde los cambios en el guión gráfico.
    
-----

En el código, puede obtener o establecer el valor de un campo de texto con su `Text` propiedad:

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

También puede usar el `Started` y `Ended` eventos de campo de texto para responder a la entrada de texto inicial y final.

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>Trabajar con campos de búsqueda

La manera más fácil trabajar con campos de búsqueda en una aplicación de Xamarin.tvOS es agregarlos en el diseño de interfaz de usuario mediante el Diseñador de interfaz.

Haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. En el **solución Pad**, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo.
1. Arrastre un nuevo controlador de vista de colección al guión gráfico para presentar los resultados de búsqueda del usuario: 

    [![](text-fields-and-search-images/search02.png "Un controlador de vista de colección")](text-fields-and-search-images/search02.png#lightbox)
1. En el **Widget** pestaña de la **panel de propiedades**, use `SearchResultsViewController` para el **clase** y `SearchResults` para el **Id. de guión gráfico**: 

    [![](text-fields-and-search-images/search03.png "La pestaña de Widget")](text-fields-and-search-images/search03.png#lightbox)
1. Seleccione el **celda prototipo** en la superficie de diseño.
1. En el **Widget** pestaña de la **el Explorador de propiedades**, use `SearchResultCell` para el **clase** y `ImageCell` para el **identificador**: 

    [![](text-fields-and-search-images/search04.png "La pestaña de Widget")](text-fields-and-search-images/search04.png#lightbox)
1. Diseño el diseño de la **celda prototipo** y exponer cada elemento con un único **nombre** en el **Widget** pestaña de la **elExploradordepropiedades**: 

    [![](text-fields-and-search-images/search05.png "El diseño del prototipo de celda de diseño")](text-fields-and-search-images/search05.png#lightbox)
1. Guarde los cambios en el guión gráfico.
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. Haga doble clic en el archivo `Main.storyboard` en el **Explorador de soluciones** para abrirlo para su edición.
1. Arrastre un nuevo controlador de vista de colección al guión gráfico para presentar los resultados de búsqueda del usuario: 

    [![](text-fields-and-search-images/seach02-vs.png "Un controlador de vista de colección")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. En el **Widget** pestaña de la **el Explorador de propiedades**, use `SearchResultsViewController` para el **clase** y `SearchResults` para el **Id. de guión gráfico**: 

    [![](text-fields-and-search-images/search03-vs.png "La pestaña de Widget")](text-fields-and-search-images/search03-vs.png#lightbox)
1. Seleccione el **celda prototipo** en la superficie de diseño.
1. En el **Widget** pestaña de la **el Explorador de propiedades**, use `SearchResultCell` para el **clase** y `ImageCell` para el **identificador**: 

    [![](text-fields-and-search-images/search04-vs.png "La pestaña de Widget")](text-fields-and-search-images/search04-vs.png#lightbox)
1. Diseño el diseño de la **celda prototipo** y exponer cada elemento con un único **nombre** en el **Widget** pestaña de la **elExploradordepropiedades**: 

    [![](text-fields-and-search-images/search05-vs.png "El diseño del prototipo de celda de diseño")](text-fields-and-search-images/search05-vs.png#lightbox)
1. Guarde los cambios en el guión gráfico.
    
-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>Proporcionan un modelo de datos

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A continuación, debe proporcionar una clase para que actúe como el modelo de datos para los resultados que se buscarán el usuario. En el **el Explorador de soluciones**, haga clic en el nombre del proyecto y seleccione **agregar** > **nuevo archivo...**   >  **General** > **clase vacía** y proporcionar un **nombre**: 

[![](text-fields-and-search-images/search06.png "Seleccione la clase vacía y proporcione un nombre")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A continuación, debe proporcionar una clase para que actúe como el modelo de datos para los resultados que se buscarán el usuario. En el **el Explorador de soluciones**, haga clic en el nombre del proyecto y seleccione **agregar** > **nuevo elemento...**   >  **Apple** > **varios** > **clase** y proporcionar un **nombre**: 

[![](text-fields-and-search-images/search06-vs.png "Seleccione la clase y proporcione un nombre")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

Por ejemplo, una aplicación que permite al usuario a una colección de imágenes por título y la palabra clave de búsqueda podría ser similar al siguiente:

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

### <a name="the-collection-view-cell"></a>La celda de la vista de colección

Con el modelo de datos en su lugar, edite el **celda prototipo** (`SearchResultViewCell.cs`) y hacerla apariencia se encuentran los siguientes:

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

El `UpdateUI` método se utilizará para mostrar los campos individuales de la **PictureInformation** elementos (el `PictureInfo` propiedad) en los elementos con nombre de la interfaz de usuario cada vez que se actualiza la propiedad. Por ejemplo, la imagen y el título asociado a la imagen.

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

En primer lugar, el `IUISearchResultsUpdating` interfaz se agrega a la clase para controlar el filtro de búsqueda controlador siendo actualizado por el usuario:

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

También se define una constante para especificar el identificador de la **celda prototipo** (que coincide con el Id. definido en el Diseñador de la interfaz anterior) que se usará más adelante cuando el controlador de la colección solicita una nueva celda:

```csharp
public const string CellID = "ImageCell";
```

Se crea el almacenamiento para una lista completa de elementos que se va a buscar el término de filtro de búsqueda y una lista de elementos que coinciden con ese término:

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

Cuando el `SearchFilter` es cambia, se actualiza la lista de elementos que coincidan y se vuelve a cargar el contenido de la vista de colección. El `FindPictures` rutina es responsable de buscar elementos que coinciden con el término de búsqueda nueva:

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

Para este ejemplo, todos los datos de ejemplo se está creando en la memoria cuando se carga el controlador de vista de colección. En una aplicación real, probablemente se leen estos datos desde un base de datos o un servicio web y solo si es necesario para impedir sobrepasar el televisor de Apple limitada memoria.

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

El `GetCell` devuelve un nuevo método **celda prototipo** (tomando como base el `CellID` definidos anteriormente en el guión gráfico) para cada elemento de la vista de colección:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

El `WillDisplayCell` método se llama antes de la celda que se muestren correctamente por lo que se puede configurar:

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

El `DidUpdateFocus` método proporciona información visual al usuario como resaltar los elementos de la vista de colección de resultados:

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

Por último, el `ItemSelected` método controla el usuario seleccione un elemento (al hacer clic en la superficie de toque con el mando a distancia Siri) en la vista de colección de resultados:

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

Si el campo de búsqueda se presenta como una vista de cuadro de diálogo modal (a través de la parte superior de la vista llamarlo), use el `DismissViewController` método para cerrar la vista de búsqueda cuando el usuario selecciona un elemento. En este ejemplo, el campo de búsqueda se presenta como el contenido de una pestaña de vista de la ficha, por lo que no se está descartando aquí.

Para obtener más información sobre las vistas de colección, visite nuestro [trabajar con vistas de colección](~/ios/tvos/user-interface/collection-views.md) documentación.

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>Presentar el campo de búsqueda

Hay dos formas principales que un campo de búsqueda (y sus asociados teclado en pantalla y resultados de búsqueda) se pueden presentar al usuario en tvOS: 

- **Vista de cuadro de diálogo modal** -el campo de búsqueda se pueden presentar en actual Vista y controlador de vista como una vista de cuadro de diálogo modal de pantalla completa. Esto se hace normalmente en respuesta al usuario hacer clic en un botón u otro elemento de interfaz de usuario. El cuadro de diálogo se cierra cuando el usuario selecciona un elemento en los resultados de búsqueda.
- **Ver el contenido de** -el campo de búsqueda es una parte directa de una vista determinada. Por ejemplo, como el contenido de una pestaña de búsqueda en un controlador de vista de ficha.

En el ejemplo de una lista de búsqueda de imágenes indicadas anteriormente, el campo de búsqueda se presenta como contenido de la vista en la pestaña de búsqueda y el controlador de vista de pestaña de búsqueda es similar a la siguiente:

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

En primer lugar, se define una constante que coincide con el **identificador de guión gráfico** que se le asignó el controlador de vista de colección de resultados de búsqueda en el Diseñador de la interfaz:

```csharp
public const string SearchResultsID = "SearchResults";
```

Después, el `ShowSearchController` método crea un nuevo controlador de recopilación de la vista de búsqueda y muestra que se necesita:

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

En el método anterior, una vez un `SearchResultsViewController` se ha creado una instancia desde el guión gráfico, un nuevo `UISearchController` se crea para que incorpore el campo de búsqueda y teclado en pantalla para el usuario. La colección de resultados de búsqueda (tal como se define por la `SearchResultsViewController`) se mostrarán en este teclado.

Después, el `SearchBar` se configura con información como el **marcador de posición** sugerencia. Proporciona información al usuario sobre el tipo de búsqueda que se lleva a cabo.

A continuación, el campo de búsqueda se presenta al usuario en uno de dos maneras:

- **Vista de cuadro de diálogo modal** : el `PresentViewController` método se llama para presentar la búsqueda en la vista existente, pantalla completa.
- **Ver contenido** - A `UISearchContainerViewController` se crea para contener el controlador de búsqueda. A `UINavigationController` se crea para contener el contenedor de búsqueda, a continuación, se agrega el controlador de navegación para el controlador de vista `AddChildViewController (navController)`y la vista presentadas `View.Add (navController.View)`.

Por último y nuevo basado en el tipo de presentación, ya sea el `ViewDidLoad` o `ViewDidAppear` método llamará el `ShowSearchController` método para presentar la búsqueda para el usuario:

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

Cuando se ejecute la aplicación y la pestaña de búsqueda seleccionada por el usuario, la lista completa sin filtrar de los elementos se presentan al usuario:

[![](text-fields-and-search-images/intro02.png "Resultados de la búsqueda de forma predeterminada")](text-fields-and-search-images/intro02.png#lightbox)

Como el usuario empieza a escribir un término de búsqueda, la lista de resultados se filtran por ese término y se actualizan automáticamente:

[![](text-fields-and-search-images/intro03.png "Resultados de la búsqueda filtrados")](text-fields-and-search-images/intro03.png#lightbox)

En cualquier momento, el usuario puede cambiar el foco a un elemento en los resultados de búsqueda y haga clic en la superficie del control remoto de Siri para seleccionarlo y táctil.

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con texto y los campos de búsqueda dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo. Ha explicado cómo crear contenido de texto y recopilación de búsqueda en el Diseñador de la interfaz y se presentó a dos distintas maneras en que un campo de búsqueda se pueden presentar al usuario en tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
