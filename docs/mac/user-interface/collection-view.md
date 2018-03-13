---
title: "Vistas de colección"
description: "Este artículo describe cómo trabajar con vistas de colección en una aplicación Xamarin.Mac. Describe cómo crear y mantener las vistas de colección en Xcode y el generador de interfaz y trabajar con ellos mediante programación."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/24/2017
ms.openlocfilehash: 9aa66a531b723f176b940ba35ee4e86eae711f7d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="collection-views"></a>Vistas de colección

_Este artículo describe cómo trabajar con vistas de colección en una aplicación Xamarin.Mac. Describe cómo crear y mantener las vistas de colección en Xcode y el generador de interfaz y trabajar con ellos mediante programación._

Al trabajar con C# y .NET en una aplicación Xamarin.Mac, el programador tiene acceso a la misma vista de colección AppKit que controla un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, el programador utiliza del Xcode _interfaz generador_ para crear y mantener las vistas de colección.

A `NSCollectionView` muestra una cuadrícula de subvistas organizada mediante un `NSCollectionViewLayout`. Cada vista secundaria en la cuadrícula se representa mediante un `NSCollectionViewItem` que administra la carga de contenido de la vista de un `.xib` archivo.

[![Ejecutar una aplicación de ejemplo](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

Este artículo tratan los conceptos básicos sobre cómo trabajar con vistas de colección en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal y como se tratan conceptos clave y las técnicas que se utilizan a lo largo de este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` comandos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>Acerca de las vistas de colección

El objetivo principal de una vista de colección (`NSCollectionView`) es organizar visualmente un grupo de objetos de un modo organizado mediante un diseño de la vista de colección (`NSCollectionViewLayout`), donde cada objeto individual (`NSCollectionViewItem`) obtener su propia vista en la colección más grande. Las vistas de colección funcionan a través de técnicas de enlace de datos y valores de clave de codificación y por lo tanto, debe leer la [enlace de datos y valores de clave de codificación](~/mac/app-fundamentals/databinding.md) documentación antes de continuar con este artículo.

La vista de colección no tiene ningún estándar e integradas vista de elemento de colección (como hace un esquema o la vista de tabla), por lo que el programador es responsable de diseñar e implementar un _prototipo vista_ con otros controles de AppKit como campos de imagen , Los campos de texto, etiquetas, etcetera. Esta vista de prototipo se usará para mostrar y trabajar con cada elemento que se está administrando mediante la vista de colección y se almacena en un `.xib` archivo.

Dado que el programador es responsable de la apariencia de un elemento de vista de colección, la vista de colección no tiene ninguna compatibilidad integrada para resaltar un elemento seleccionado en la cuadrícula. Implementar esta característica se tratará en este artículo.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>Definir el modelo de datos

Antes de una vista de colección de enlace en el generador de interfaz, una codificación de clave y valor (KVC) de datos / clase conforme a observar de clave y valor (KVO) debe definirse en la aplicación Xamarin.Mac para que actúe como el _modelo de datos_ para el enlace. El modelo de datos proporciona todos los datos que se mostrarán en la colección y recibe ninguna modificación en los datos que el usuario realice en la interfaz de usuario mientras se ejecuta la aplicación.

Tome como ejemplo de una aplicación que administra un grupo de empleados, la clase siguiente podría usarse para definir el modelo de datos:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

El `PersonModel` se usará el modelo de datos en el resto de este artículo.

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>Trabajar con una vista de colección

Enlace de datos con una vista de colección es muy similar enlace like con una vista de tabla, como `NSCollectionViewDataSource` se utiliza para proporcionar datos para la colección. Puesto que la vista de colección no tiene un formato de presentación de valores preestablecidos, es necesario proporcionar comentarios de interacción de usuario y realizar un seguimiento de la selección del usuario más trabajo.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Crear el prototipo de celda

Puesto que la vista de colección no incluye un prototipo de la celda de forma predeterminada, el programador deberá agregar uno o más `.xib` archivos a la aplicación Xamarin.Mac para definir el diseño y el contenido de las celdas individuales.

Haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el nombre del proyecto y seleccione **agregar** > **nuevo archivo...**
2. Seleccione **Mac** > **View Controller**, asígnele un nombre (como `EmployeeItem` en este ejemplo) y haga clic en el **New** botón para crear: 

    ![Agregar un nuevo controlador de vista](collection-view-images/proto01.png)

    Esto agregará una `EmployeeItem.cs`, `EmployeeItemController.cs` y `EmployeeItemController.xib` archivo a la solución del proyecto.
3. Haga doble clic en el `EmployeeItemController.xib` archivo para abrirlo y editarlo en el generador de interfaz de Xcode.
4. Agregar un `NSBox`, `NSImageView` y dos `NSLabel` controles a la vista y prepararlos como sigue:

    ![Definir el diseño del prototipo de celda](collection-view-images/proto02.png)
5. Abra la **Ayudante para el Editor** y crear un **toma** para el `NSBox` para que se puede utilizar para indicar el estado de selección de una celda:

    ![Exponer el NSBox en una salida](collection-view-images/proto03.png)
6. Vuelva a la **Editor estándar** y seleccione la vista de la imagen.
7. En el **enlace Inspector**, seleccione **enlazar a** > **propietario del archivo** y escriba un **ruta de la clave de modelo** de `self.Person.Icon`:

    ![El icono de enlace](collection-view-images/proto04.png)
8. Seleccione la primera etiqueta y en la **enlace Inspector**, seleccione **enlazar a** > **propietario del archivo** y escriba un **ruta de acceso de clave del modelo**de `self.Person.Name`:

    ![El nombre de enlace](collection-view-images/proto05.png)
9. Seleccione la segunda etiqueta y, en la **enlace Inspector**, seleccione **enlazar a** > **propietario del archivo** y escriba un **ruta de acceso de clave del modelo**de `self.Person.Occupation`:

    ![La ocupación de enlace](collection-view-images/proto06.png)
10. Guardar los cambios a la `.xib` de archivos y vuelva a Visual Studio para sincronizar los cambios.

Editar la `EmployeeItemController.cs` de archivos y darle un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

Se trata de este código en detalle, la clase hereda de `NSCollectionViewItem` por lo que puede actuar como un prototipo para una celda de la vista de colección. El `Person` propiedad expone la clase que se usó para enlazar los datos en la vista de imagen y las etiquetas en Xcode. Se trata de una instancia de la `PersonModel` creado anteriormente.

El `BackgroundColor` propiedad es un acceso directo a la `NSBox` del control `FillColor` que se usará para mostrar el estado de selección de una celda. Reemplazando la `Selected` propiedad de la `NSCollectionViewItem`, el código siguiente establece o borra este estado de selección:

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>Crear el origen de datos de la vista de colección

Un origen de datos de la vista de colección (`NSCollectionViewDataSource`) proporciona todos los datos para una vista de colección, crea y rellena una celda de la vista de colección (mediante el `.xib` prototipo) según sea necesario para cada elemento de la colección.

Agregue una nueva clase del proyecto, llamarlo `CollectionViewDataSource` y darle un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

Se trata de este código en detalle, la clase hereda de `NSCollectionViewDataSource` y expone una lista de `PersonModel` instancias a través de su `Data` propiedad.

Puesto que esta colección solo tiene una sección, el código invalida el `GetNumberOfSections` método y siempre devuelve `1`. Además, el `GetNumberofItems` método se invalida en devuelve el número de elementos de la `Data` lista de propiedades.

El `GetItem` método se llama cada vez que una nueva celda es necesaria y es similar al siguiente:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

El `MakeItem` se denomina método de la vista de colección para crear o devolver una instancia reutilizable de la `EmployeeItemController` y su `Person` propiedad se establece en el elemento que se muestra en la celda solicitada. 

El `EmployeeItemController` debe estar registrado con el controlador de vista de colección con antelación mediante el código siguiente:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

El **identificador** (`EmployeeCell`) utilizado en el `MakeItem` llamar a _debe_ coincide con el nombre del controlador de vista en el que se registró con la vista de colección. Este paso se explicará en detalle a continuación.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Selección de elementos de control

Para controlar la activación y la desactivación de los elementos de la colección un `NSCollectionViewDelegate` será necesario. Puesto que este ejemplo va a utilizar integrado en `NSCollectionViewFlowLayout` tipo de diseño, un `NSCollectionViewDelegateFlowLayout` se requerirá la versión específica de este delegado.

Agregue una nueva clase al proyecto, llamarlo `CollectionViewDelegate` y darle un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

El `ItemsSelected` y `ItemsDeselected` métodos son reemplazados y se utiliza para establecer o borrar el `PersonSelected` propiedad del controlador de vista que lleva a cabo la vista de colección cuando el usuario selecciona o anula la selección de un elemento. Esto se mostrará en detalle a continuación.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Crear la vista de colección en el generador de interfaz

Con todas las piezas de apoyo necesarias en su lugar, se puede editar el guión gráfico principal y le agrega una vista de colección.

Haga lo siguiente:

1. Haga doble clic en el `Main.Storyboard` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo en Xcode del generador de interfaz.
2. Arrastre una vista de colección en la vista principal y cambiar su tamaño para rellenar la vista:

    ![Agregar una vista de colección para el diseño](collection-view-images/collection01.png)
3. Con la vista de colección seleccionado, utilice el Editor de restricciones para anclar a la vista cuando se cambia el tamaño:

    ![Agregar restricciones](collection-view-images/collection02.png)
4. Asegúrese de que está seleccionada la vista de colección en el **superficie de diseño** (y no la **rodeado de vista de desplazamiento** o **vista de Clip** que lo contiene), cambie a la  **Ayudante para el Editor** y crear un **toma** para la vista de colección:

    ![Agregar restricciones](collection-view-images/collection03.png)
5. Guarde los cambios y vuelva a Visual Studio para sincronizar.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Ponerlo general

Todas las piezas de apoyo se han colocado en su lugar con una clase para que actúe como el modelo de datos (`PersonModel`), un `NSCollectionViewDataSource` se ha agregado al suministrar datos, un `NSCollectionViewDelegateFlowLayout` se creó para controlar la selección de elementos y un `NSCollectionView` se agregó al guión gráfico principal y se expone como una salida (`EmployeeCollection`).

El último paso es editar el controlador de vista que contiene la vista de colección y poner todas las piezas juntos para rellenar la colección y controlar la selección de elementos.

Editar la `ViewController.cs` de archivos y darle un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

Echar un vistazo a este código en detalle, un `Datasource` propiedad se define para contener una instancia de la `CollectionViewDataSource` que proporcionará los datos para la vista de colección. A `PersonSelected` propiedad se define para contener el `PersonModel` que representa el elemento seleccionado actualmente en la vista de colección. Esta propiedad también genera el `SelectionChanged` eventos cuando cambia la selección.

La `ConfigureCollectionView` clase se usa para registrar el controlador de vista que actúa como el prototipo de la celda con la vista de colección mediante la siguiente línea:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Tenga en cuenta que la **identificador** (`EmployeeCell`) usa para registrar las coincidencias de prototipo la llama en el `GetItem` método de la `CollectionViewDataSource` definida anteriormente:

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

Además, el tipo del controlador de vista de **debe** coincide con el nombre de la `.xib` archivo que define el prototipo **exactamente**. En el caso de este ejemplo, `EmployeeItemController` y `EmployeeItemController.xib`.

El diseño real de los elementos de la vista de colección se controla mediante una clase de diseño de la vista de colección y se puede cambiar dinámicamente en tiempo de ejecución mediante la asignación de una nueva instancia en la `CollectionViewLayout` propiedad. Si cambia esta propiedad, actualiza la apariencia de las vistas de colección sin animar el cambio.

Apple incluye dos tipos de diseño integrada con la vista de colección que se va a controlar los usos más habituales: `NSCollectionViewFlowLayout` y `NSCollectionViewGridLayout`. Si el desarrollador requiere un formato personalizado, por ejemplo, para presentar los elementos en un círculo, puede crear una instancia personalizada de `NSCollectionViewLayout` e invalidar los métodos necesarios para lograr el efecto deseado.

En este ejemplo usa el diseño de flujo predeterminado, por lo que crea una instancia de la `NSCollectionViewFlowLayout` clase y lo configura como se indica a continuación:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

El `ItemSize` propiedad define el tamaño de cada celda individual de la colección. El `SectionInset` propiedad define los márgenes desde el borde de la colección de celdas se disponen en. `MinimumInteritemSpacing` define el espaciado mínimo entre los elementos y `MinimumLineSpacing` define el espaciado mínimo entre líneas de la colección.

El diseño se asigna a la vista de colección y una instancia de la `CollectionViewDelegate` se adjunta para controlar la selección de elementos:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

El `PopulateWithData` método crea una nueva instancia de la `CollectionViewDataSource`, rellena con datos, que se conecta a la vista de colección y las llamadas del `ReloadData` método para mostrar los elementos:

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

El `ViewDidLoad` método se invalida y se llama el `ConfigureCollectionView` y `PopulateWithData` métodos para mostrar la vista de colección final para el usuario:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con vistas de colección en una aplicación Xamarin.Mac. En primer lugar, examinando expone una clase de C# a C objetivo mediante el uso de clave y valor de codificación (KVC) y observar de clave y valor (KVO). A continuación, ha mostrado cómo usar una clase conforme a KVO y enlazar datos con vistas de recopilación en el generador de interfaz de Xcode. Por último, ha explicado cómo interactuar con vistas de colección en código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacCollectionNew (ejemplo)](https://developer.xamarin.com/samples/mac/MacCollectionNew/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
