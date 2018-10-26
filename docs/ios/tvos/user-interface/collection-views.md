---
title: Trabajar con las vistas de colección en Xamarin de tvOS
description: Este documento describe cómo trabajar con las vistas de colección en una aplicación de tvOS con Xamarin. Cubre los diseños de vista de colección, crear celdas y con vistas, responder a eventos de usuario y mucho más.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f815afa6b1abb15348019b0c53333b4acb054008
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108033"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>Trabajar con las vistas de colección en Xamarin de tvOS

Las vistas de colección permiten para un grupo de contenido se muestren con diseños arbitrarios. Con compatibilidad integrada, permiten los diseños de cuadrícula o lineal creación fácil, al tiempo que también admite los diseños personalizados.

[![](collection-views-images/collection01.png "Vista de colección de ejemplo")](collection-views-images/collection01.png#lightbox)

La vista de colección mantiene una colección de elementos mediante un delegado y un origen de datos para proporcionar la interacción del usuario y el contenido de la colección. Puesto que la vista de colección se basa en un subsistema de diseño que es independiente de la propia vista, puede cambiar fácilmente la presentación de datos sobre la marcha de la vista de colección que proporciona un diseño diferente.

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>Acerca de las vistas de colección

Como se indicó anteriormente, una vista de colección (`UICollectionView`) administra una colección ordenada de elementos y presenta los elementos con diseños personalizables. Las vistas de colección funcionan de manera similar a las vistas de tabla (`UITableView`), excepto en que pueden utilizar los diseños de elementos presentes en algo más que una sola columna.

Al utilizar una vista de colección en tvOS, la aplicación es responsable de proporcionar los datos asociados a la colección utilizando un origen de datos (`UICollectionViewDataSource`). Ver datos de colección pueden se organizan y presentan en diferentes grupos (secciones).

La vista de colección presenta los elementos individuales en la pantalla con una celda (`UICollectionViewCell`) que proporciona la presentación de una parte determinada de la información de la colección (como una imagen y su título).

Si lo desea, se pueden agregar vistas adicionales a la presentación de la vista de colección para que actúe como encabezado y pies de página para las secciones y celdas. Diseño de la vista de colección es responsable de definir la colocación de estas vistas, junto con las celdas individuales.

La vista de colección puede responder a la interacción del usuario mediante un delegado (`UICollectionViewDelegate`). Este delegado también es responsable de determinar si una celda determinada puede obtener el foco, si se ha seleccionado una celda o si se ha seleccionado uno. En algunos casos, el delegado determina el tamaño de las celdas individuales.

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>Diseños de vista de colección

Una característica clave de una vista de colección es su separación entre los datos que van a presentar y su diseño. Un diseño de vista de colección (`UICollectionViewLayout`) es responsable de proporcionar a la organización y la ubicación de las celdas (y todas las vistas adicionales) en la presentación en pantalla de la vista de colección.

Las celdas individuales se crean mediante la vista de colección desde su origen de datos asociado y, a continuación, se organizan y muestra el diseño de vista de colección especificado.

El diseño de la vista de colección normalmente se proporciona cuando se crea la vista de colección. Sin embargo, puede cambiar el diseño de la vista de colección en cualquier momento y la presentación en pantalla de datos de la vista de colección se actualizarán automáticamente con el nuevo diseño proporcionado.

El diseño de la vista de colección proporciona varios métodos que pueden usarse para animar la transición entre dos diseños diferentes (de forma predeterminada que no se realiza ninguna animación). Además, los diseños de vista de colección puede trabajar con los reconocedores de gestos para animar aún más la interacción del usuario que dé como resultado un cambio en el diseño.

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>Creación de las celdas y vistas adicionales

Origen de datos de la vista de colección solo no es responsable de proporcionar los datos de seguridad de la colección de elementos, sino también las celdas que se usan para mostrar el contenido.

Dado que las vistas de colección se diseñaron para tratar grandes colecciones de elementos, las celdas individuales pueden quitar de la cola y reutilizar para mantener saturen las limitaciones de memoria. Hay dos métodos diferentes para las vistas de eliminación:

- `DequeueReusableCell` : Crea o devuelve una celda del tipo especificado (según lo especificado en el guión gráfico de la aplicación).
- `DequeueReusableSupplementaryView` : Crea o devuelve una vista adicional del tipo especificado (según lo especificado en el guión gráfico de la aplicación).

Antes de llamar a cualquiera de estos métodos, debe registrar la clase, guión gráfico o `.xib` archivo utilizado para crear la vista de la celda con la vista de colección. Por ejemplo:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

Donde `typeof(CityCollectionViewCell)` proporciona la clase que admite la vista y `CityViewDatasource.CardCellId` proporciona el identificador que se utiliza cuando la celda (o vista) se quita de la cola.

Después de la celda se quita de la cola, se configurará con los datos para el elemento que representa y volver a la vista de colección para su presentación.

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>Acerca de los controladores de vista de colección

Un controlador de vista de colección (`UICollectionViewController`) es un controlador de vistas especializadas (`UIViewController`) que proporciona el comportamiento siguiente:

- Es responsable de cargar la vista de colección de su guión gráfico o `.xib` archivo y crear instancias de la vista. Si se crean en código, crea automáticamente una nueva vista de colección no configurado.
- Una vez que se carga la vista de colección, el controlador intenta cargar su origen de datos y el delegado desde el guión gráfico o `.xib` archivo. Si ninguna está disponible, configura a sí mismo como el origen de ambos.
- Garantiza que los datos se cargan antes de la vista de colección se rellena en la primera muestra y vuelve a cargar y desactivar la selección en cada pantalla posteriores.

Además, el controlador de vista de colección proporciona métodos reemplazables que pueden usarse para administrar el ciclo de vida de la vista de colección como `AwakeFromNib` y `ViewWillDisplay`.

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>Guiones gráficos y vistas de colección

La manera más fácil trabajar con una vista de colección en la aplicación Xamarin.tvOS, es agregar una a su guión gráfico. Un ejemplo rápido, vamos a crear una aplicación de ejemplo que presenta una imagen, título y un botón de selección. Si el usuario haga clic en el botón de selección, se mostrará una vista de colección que permiten al usuario elegir una nueva imagen. Cuando se elige una imagen, se cierra la vista de colección y se mostrará la nueva imagen y el título.

Vamos a hacer lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

    
1. Iniciar una nueva **aplicación tvOS de vista única** en Visual Studio para Mac.
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo y abrirlo en el Diseñador de iOS.
1. Agregar una imagen de vista, una etiqueta y un botón a la vista existente y configurarlos para que el siguiente aspecto: 

    [![](collection-views-images/collection02.png "Diseño de ejemplo")](collection-views-images/collection02.png#lightbox)
1. Asignar un **nombre** a la vista de imagen y la etiqueta en el **ficha Widget** de la **Explorador de propiedades**. Por ejemplo: 

    [![](collection-views-images/collection03.png "Establecer el nombre")](collection-views-images/collection03.png#lightbox)
1. A continuación, arrastre un controlador de vista de colección en el guión gráfico: 

    [![](collection-views-images/collection04.png "Un controlador de vista de colección")](collection-views-images/collection04.png#lightbox)
1. Tecla Control mientras arrastra desde el botón para el controlador de vista de colección y seleccione **Push** desde el menú emergente: 

    [![](collection-views-images/collection05.png "Seleccione inserción en el menú emergente")](collection-views-images/collection05.png#lightbox)
1. Cuando se ejecuta la aplicación, esto hará que la vista de colección se puede mostrar cada vez que el usuario hace clic en el botón.
1. Seleccione la vista de colección y escriba los siguientes valores en el **pestaña diseño** de la **Explorador de propiedades**: 

    [![](collection-views-images/collection06.png "El Explorador de propiedades")](collection-views-images/collection06.png#lightbox)
1. Esto controla el tamaño de las celdas individuales y los bordes entre las celdas y el borde externo de la vista de colección.
1. Seleccione el controlador de vista de colección y establece su clase en `CityCollectionViewController` en el **ficha Widget**: 

    [![](collection-views-images/collection07.png "Defina la clase como CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. Seleccione la vista de colección y establece su clase en `CityCollectionView` en el **ficha Widget**: 

    [![](collection-views-images/collection08.png "Defina la clase como CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. Seleccione la celda de vista de colección y establece su clase en `CityCollectionViewCell` en el **ficha Widget**: 

    [![](collection-views-images/collection09.png "Defina la clase como CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. En el **ficha Widget** Asegúrese de que el **diseño** es `Flow` y **dirección de desplazamiento** es `Vertical` para la vista de colección: 

    [![](collection-views-images/collection10.png "La pestaña de Widget")](collection-views-images/collection10.png#lightbox)
1. Seleccione la celda de vista de colección y establezca su **identidad** a `CityCell` en el **ficha Widget**: 

    [![](collection-views-images/collection11.png "Establecer la identidad a CityCell")](collection-views-images/collection11.png#lightbox)
1. Guarde los cambios.
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. Iniciar una nueva **aplicación tvOS de vista única** en Visual Studio.
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo y abrirlo en el Diseñador de iOS.
1. Agregar una imagen de vista, una etiqueta y un botón a la vista existente y configurarlos para que el siguiente aspecto: 

    [![](collection-views-images/collection02vs.png "Configurar el diseño")](collection-views-images/collection02vs.png#lightbox)
1. Asignar un **nombre** a la vista de imagen y la etiqueta en el **ficha Widget** de la **Explorador de propiedades**. Por ejemplo: 

    [![](collection-views-images/collection03vs.png "El Explorador de propiedades")](collection-views-images/collection03vs.png#lightbox)
1. A continuación, arrastre un controlador de vista de colección en el guión gráfico: 

    [![](collection-views-images/collection04vs.png "Un controlador de vista de colección")](collection-views-images/collection04vs.png#lightbox)
1. Tecla Control mientras arrastra desde el botón para el controlador de vista de colección y seleccione **Push** desde el menú emergente: 

    [![](collection-views-images/collection05vs.png "Seleccione inserción en el menú emergente")](collection-views-images/collection05vs.png#lightbox)
1. Cuando se ejecuta la aplicación, esto hará que la vista de colección se puede mostrar cada vez que el usuario hace clic en el botón.
1. Seleccione la vista de colección y, en el **pestaña diseño** de la **Explorador de propiedades** escriba el **ancho** como _361_ y  **Alto** como _256_ 
1. Esto controla el tamaño de las celdas individuales y los bordes entre las celdas y el borde externo de la vista de colección.
1. Seleccione el controlador de vista de colección y establece su clase en `CityCollectionViewController` en el **ficha Widget**: 

    [![](collection-views-images/collection07vs.png "Defina la clase como CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. Seleccione la vista de colección y establece su clase en `CityCollectionView` en el **ficha Widget**: 

    [![](collection-views-images/collection08vs.png "Defina la clase como CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. Seleccione la celda de vista de colección y establece su clase en `CityCollectionViewCell` en el **ficha Widget**: 

    [![](collection-views-images/collection09vs.png "Defina la clase como CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. En el **ficha Widget** Asegúrese de que el **diseño** es `Flow` y **dirección de desplazamiento** es `Vertical` para la vista de colección: 

    [![](collection-views-images/collection10vs.png "Ficha Widget de sección")](collection-views-images/collection10vs.png#lightbox)
1. Seleccione la celda de vista de colección y establezca su **identidad** a `CityCell` en el **ficha Widget**: 

    [![](collection-views-images/collection11vs.png "Establecer la identidad a CityCell")](collection-views-images/collection11vs.png#lightbox)
1. Guarde los cambios.
    

-----

Si hubiéramos elegido `Custom` para la vista de colección **diseño**, podríamos haber especificado un diseño personalizado. Apple proporciona integrada una `UICollectionViewFlowLayout` y `UICollectionViewDelegateFlowLayout` que puede presentar fácilmente datos en un diseño de cuadrícula (se usan por el `flow` estilo de diseño). 

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>Proporciona datos para la vista de colección

Ahora que tenemos nuestra vista de colección (y el controlador de vista de colección) agregado a nuestro guión gráfico, necesitamos proporcionar los datos de la colección. 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>El modelo de datos

En primer lugar, vamos a crear un modelo para los datos que contiene el nombre de archivo para la imagen mostrar el título y una marca para permitir la ciudad que se seleccionen.

Crear un `CityInfo` clase y dele un aspecto similar al siguiente:

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>La celda de vista de colección

Ahora necesitamos definir cómo se presentará los datos para cada celda. Editar el `CityCollectionViewCell.cs` archivo (creado automáticamente desde el archivo de guión gráfico) y dele un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

Para nuestra aplicación tvOS, se va a mostrar una imagen y un título opcional. Si no se puede seleccionar dicha ciudad, nos estamos atenuación de la vista de imagen con el código siguiente:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Cuando la celda que contiene la imagen se pone en foco por el usuario, queremos usar integrado efecto Parallax en él se puede establecer la propiedad siguiente:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Para obtener más información sobre la navegación y foco, vea nuestra [trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y [Siri Remote y Bluetooth controladores](~/ios/tvos/platform/remote-bluetooth.md) documentación.


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>El proveedor de datos de la vista de colección

Con nuestro modelo de datos que creó y nuestro diseño de celda definido, vamos a crear un origen de datos para la vista de colección. El origen de datos será responsable de proporcionar no solo los datos de respaldo, sino también eliminar de la cola las celdas para mostrar las celdas individuales en la pantalla.

Crear un `CityViewDatasource` clase y dele un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

Permiten examinar esta clase en detalle. En primer lugar, se heredan de `UICollectionViewDataSource` y proporcionar un acceso directo para el Id. de celdas (que se asigna en el Diseñador de iOS):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

A continuación se proporcionan almacenamiento para los datos de colección y proporciona una clase para rellenar los datos:

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

A continuación, se muestra cómo reemplazar el `NumberOfSections` método y devolver el número de secciones (grupos de elementos) que permite ver nuestra colección tiene. En este caso, hay solo uno:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

A continuación, se devuelve el número de elementos de la colección utilizando el código siguiente:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

Por último, una celda reutilizable, dequeue cuando solicitan la vista de colección con el código siguiente:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

Una vez que obtenemos una celda de vista de colección de nuestro `CityCollectionViewCell` tipo, se rellenará con el elemento especificado.

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>Responder a eventos de usuario

Porque queremos que el usuario sea capaz de seleccionar un elemento de la colección, es necesario proporcionar un delegado de la vista de colección para controlar esta interacción. Y necesitamos para proporcionar una forma de hacerle nuestra vista llamada saber qué elemento el usuario ha seleccionado.

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>El delegado de la aplicación

Se necesita una manera para relacionar el elemento actualmente seleccionado de la vista de colección a la vista que realiza la llamada. Vamos a usar una propiedad personalizada en nuestra `AppDelegate`. Editar el `AppDelegate.cs` archivo y agregue el código siguiente:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Esto define la propiedad y establece la ciudad de forma predeterminada que inicialmente se mostrará. Se podrá utilizar más adelante, esta propiedad para mostrar la selección del usuario y permitir que la seleccione cambiarse.

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>El delegado de la vista de colección

A continuación, agregue un nuevo `CityViewDelegate` clase al proyecto y dele un aspecto similar al siguiente:


```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

Echemos un vistazo a esta clase. En primer lugar, se heredan de `UICollectionViewDelegateFlowLayout`. El motivo por el que se hereda de esta clase y no el `UICollectionViewDelegate` es que estamos usando la integrada `UICollectionViewFlowLayout` presentar nuestros elementos y no un tipo de diseño personalizado.

A continuación, devolvemos el tamaño de los elementos individuales mediante este código:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

A continuación, se decide si una celda determinada puede obtener el foco mediante el código siguiente: 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

Comprobamos si tiene un fragmento de datos de copia de seguridad dado su `CanSelect` marca establecida en `true` y devuelve ese valor. Para obtener más información sobre la navegación y foco, vea nuestra [trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y [Siri Remote y Bluetooth controladores](~/ios/tvos/platform/remote-bluetooth.md) documentación.

Por último, se responde al usuario seleccionar un elemento con el código siguiente:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

A continuación establecemos la `SelectedCity` propiedad de nuestro `AppDelegate` al elemento que seleccionó el usuario y se cierra el controlador de vista de colección, volver a la vista que nos llama. No hemos definido la `ParentController` propiedad de la vista de colección aún, haremos todo lo que a continuación.

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>Configuración de la vista de colección

Ahora necesitamos modificar la vista de colección y asignar a nuestro origen de datos y el delegado. Editar el `CityCollectionView.cs` archivo (creado automáticamente desde el guión gráfico) y dele un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

En primer lugar, proporcionamos un acceso directo para tener acceso a nuestro `AppDelegate`: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

A continuación, se proporciona un acceso directo a origen de la vista de colección de datos y una propiedad para obtener acceso al controlador de vista de colección (usa nuestro delegado anterior para cerrar la colección cuando el usuario realiza una selección):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

A continuación, usamos el siguiente código para inicializar la vista de colección y asignar nuestra clase de celda, el origen de datos y el delegado:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

Por último, queremos que el título debajo de la imagen solo esté visible cuando el usuario lo ha resaltado (enfocado). Para hacerlo con el código siguiente:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

Se establece la transparencia del anterior elemento pierde el foco a cero (0) y la transparencia del siguiente elemento obtenga el foco al 100%. Estos transición animarse también.


## <a name="configuring-the-collection-view-controller"></a>Configurar el controlador de vista de colección

Ahora es necesario realizar la configuración final en la vista de colección y permitir que el controlador establecer la propiedad que hemos definido para que la vista de colección se puede cerrar una vez que el usuario realiza una selección.

Editar el `CityCollectionViewController.cs` archivo (creado automáticamente desde el guión gráfico) y dele un aspecto similar al siguiente:

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>Perspectiva general 

Ahora que tenemos todas las partes elaborado para rellenar y controlar la vista de colección, necesitamos realizar las ediciones finales a nuestra vista principal para tenerlo todo junto.

Editar el `ViewController.cs` archivo (creado automáticamente desde el guión gráfico) y dele un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
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
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

Inicialmente, el código siguiente muestra el elemento seleccionado de la `SelectedCity` propiedad de la `AppDelegate` y lo vuelve a mostrar cuando el usuario ha realizado una selección de la vista de colección:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app" />

## <a name="testing-the-app"></a>Probar la aplicación

Con todo lo que en su lugar, si compilar y ejecuta la aplicación, se muestra la vista principal con la ciudad de forma predeterminada:

[![](collection-views-images/run01.png "La pantalla principal")](collection-views-images/run01.png#lightbox)

Si el usuario haga clic en el **seleccionar una vista** se mostrará el botón, la vista de colección:

[![](collection-views-images/run02.png "La vista de colección")](collection-views-images/run02.png#lightbox)

Cualquier ciudad que tiene su `CanSelect` propiedad establecida en `false` aparecerá atenuado y el usuario no podrá establecer el foco. Cuando el usuario resalta un elemento (hacerla enfocado) se muestra el título y pueden usar el efecto de paralaje inclinación sutileza la imagen en 3D.

Cuando el usuario hace clic en seleccionar una imagen, se cierra la vista de colección y se vuelve a mostrar la vista principal con la nueva imagen:

[![](collection-views-images/run03.png "Una nueva imagen en la pantalla principal")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>Crear diseño personalizado y la reordenación de elementos

Una de las características clave del uso de una vista de colección es la capacidad para crear diseños personalizados. Puesto que tvOS hereda de iOS, el proceso para crear un diseño personalizado es el mismo. Consulte nuestra [Introducción a las vistas de colección](~/ios/user-interface/controls/uicollectionview.md) documentación para obtener más información.

Se agregaron recientemente a las vistas de colección para iOS 9 fue la capacidad para poder permitir la reordenación de elementos de la colección. De nuevo, puesto que tvOS 9 es un subconjunto de iOS 9, ello ellos igual. Consulte nuestra [los cambios de la vista de colección](~/ios/user-interface/controls/uicollectionview.md) documento para obtener más detalles.


<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con las vistas de colección dentro de una aplicación Xamarin.tvOS. En primer lugar, describen todos los elementos que componen la vista de colección. A continuación, se ha mostrado cómo diseñar e implementar una vista de colección mediante un guión gráfico. Por último, se proporcionan vínculos a información sobre cómo crear diseños personalizados y reordenación de elementos.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
