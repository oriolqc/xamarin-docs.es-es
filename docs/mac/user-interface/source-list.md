---
title: Listas de origen
description: "Este artículo explica cómo trabajar con listas de origen en una aplicación Xamarin.Mac. Describe cómo crear y mantener listas de origen en Xcode y el generador de interfaz e interactuar con ellos en el código de C#."
ms.topic: article
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 1cc74fb30e59ecd5f6be3cf3e1c84f60cd5ca0a6
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="source-lists"></a>Listas de origen

_Este artículo explica cómo trabajar con listas de origen en una aplicación Xamarin.Mac. Describe cómo crear y mantener listas de origen en Xcode y el generador de interfaz e interactuar con ellos en el código de C#._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tiene acceso al mismo origen de muestra que un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para crear y mantener las listas de origen (o si lo desea crearlos directamente en código de C#).

Una lista de origen es un tipo especial de la vista de esquema que se usa para mostrar el origen de una acción, como la barra lateral en Finder o iTunes.

[ ![](source-list-images/source05.png "Una lista de código fuente de ejemplo")](source-list-images/source05.png)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con listas de origen en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` comandos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>Introducción a las listas de origen

Como se mencionó anteriormente, una lista de origen es un tipo especial de vista de esquema que se usa para mostrar el origen de una acción, como la barra lateral en Finder o iTunes. Una lista de origen es un tipo de tabla que permite al usuario expandir o contraer las filas de datos jerárquicos. A diferencia de una vista de tabla, los elementos en una lista de origen no están en una lista plana, están organizados en una jerarquía, como archivos y carpetas en un disco duro. Si un elemento de una lista de origen contiene otros elementos, se puede expandir o contraer por el usuario.

La lista de origen es una vista de esquema especialmente con estilo (`NSOutlineView`), que a su vez es una subclase de la vista de tabla (`NSTableView`) y como tal, hereda la mayor parte de su comportamiento de su clase primaria. Como resultado, muchas operaciones admitidas por una vista de esquema, también se admiten mediante una lista de origen. Una aplicación de Xamarin.Mac tiene el control de estas características y puede configurar los parámetros de la lista de origen (ya sea en código o el generador de interfaz) para activar o desactivar determinadas operaciones.

Una lista de origen no almacena sus propios datos, en su lugar se basa en un origen de datos (`NSOutlineViewDataSource`) para proporcionar tanto las filas y las columnas necesarias, según sea necesario.

Se puede personalizar el comportamiento de la lista de origen proporcionando una subclase del delegado de la vista de esquema (`NSOutlineViewDelegate`) para admitir el tipo de esquema para seleccionar la funcionalidad, elemento de selección y edición, el seguimiento personalizado y vistas personalizadas para elementos individuales.

Dado que una lista de orígenes de gran parte de su comportamiento y la funcionalidad comparte con una vista de tabla y una vista de esquema, puede ir a través de nuestro [vistas de tabla](~/mac/user-interface/table-view.md) y [vistas de esquema](~/mac/user-interface/outline-view.md) documentación antes de continuar con este artículo.

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>Trabajar con listas de origen

Una lista de origen es un tipo especial de la vista de esquema que se usa para mostrar el origen de una acción, como la barra lateral en Finder o iTunes. A diferencia de las vistas de esquema, antes de que nuestra lista de origen se define en el generador de interfaz, vamos a crear las clases de respaldo en Xamarin.Mac.

En primer lugar, vamos a crear un nuevo `SourceListItem` clase para contener los datos de nuestra lista de origen. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `SourceListItem` para el **nombre** y haga clic en el **New** botón:

[ ![](source-list-images/source01.png "Agregar una clase vacía")](source-list-images/source01.png)

Realizar el `SourceListItem.cs` archivo aspecto similar al siguiente: 

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListItem: NSObject, IEnumerator, IEnumerable
    {
        #region Private Properties
        private string _title;
        private NSImage _icon;
        private string _tag;
        private List<SourceListItem> _items = new List<SourceListItem> ();
        #endregion

        #region Computed Properties
        public string Title {
            get { return _title; }
            set { _title = value; }
        }

        public NSImage Icon {
            get { return _icon; }
            set { _icon = value; }
        }

        public string Tag {
            get { return _tag; }
            set { _tag=value; }
        }
        #endregion

        #region Indexer
        public SourceListItem this[int index]
        {
            get
            {
                return _items[index];
            }

            set
            {
                _items[index] = value;
            }
        }

        public int Count {
            get { return _items.Count; }
        }

        public bool HasChildren {
            get { return (Count > 0); }
        }
        #endregion

        #region Enumerable Routines
        private int _position = -1;

        public IEnumerator GetEnumerator()
        {
            _position = -1;
            return (IEnumerator)this;
        }

        public bool MoveNext()
        {
            _position++;
            return (_position < _items.Count);
        }

        public void Reset()
        {_position = -1;}

        public object Current
        {
            get 
            { 
                try 
                {
                    return _items[_position];
                }

                catch (IndexOutOfRangeException)
                {
                    throw new InvalidOperationException();
                }
            }
        }
        #endregion

        #region Constructors
        public SourceListItem ()
        {
        }

        public SourceListItem (string title)
        {
            // Initialize
            this._title = title;
        }

        public SourceListItem (string title, string icon)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
        }

        public SourceListItem (string title, string icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
        }

        public SourceListItem (string title, NSImage icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon, string tag)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
        }

        public SourceListItem (string title, NSImage icon, string tag, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
            this.Clicked = clicked;
        }
        #endregion

        #region Public Methods
        public void AddItem(SourceListItem item) {
            _items.Add (item);
        }

        public void AddItem(string title) {
            _items.Add (new SourceListItem (title));
        }

        public void AddItem(string title, string icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, string icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, NSImage icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon, string tag) {
            _items.Add (new SourceListItem (title, icon, tag));
        }

        public void AddItem(string title, NSImage icon, string tag, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, tag, clicked));
        }

        public void Insert(int n, SourceListItem item) {
            _items.Insert (n, item);
        }

        public void RemoveItem(SourceListItem item) {
            _items.Remove (item);
        }

        public void RemoveItem(int n) {
            _items.RemoveAt (n);
        }

        public void Clear() {
            _items.Clear ();
        }
        #endregion

        #region Events
        public delegate void ClickedDelegate();
        public event ClickedDelegate Clicked;

        internal void RaiseClickedEvent() {
            // Inform caller
            if (this.Clicked != null)
                this.Clicked ();
        }
        #endregion
    }
}
```

En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `SourceListDataSource` para el **nombre** y haga clic en el **New** botón. Realizar el `SourceListDataSource.cs` archivo aspecto similar al siguiente:

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDataSource : NSOutlineViewDataSource
    {
        #region Private Variables
        private SourceListView _controller;
        #endregion

        #region Public Variables
        public List<SourceListItem> Items = new List<SourceListItem>();
        #endregion

        #region Constructors
        public SourceListDataSource (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Properties
        public override nint GetChildrenCount (NSOutlineView outlineView, Foundation.NSObject item)
        {
            if (item == null) {
                return Items.Count;
            } else {
                return ((SourceListItem)item).Count;
            }
        }

        public override bool ItemExpandable (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, Foundation.NSObject item)
        {
            if (item == null) {
                return Items [(int)childIndex];
            } else {
                return ((SourceListItem)item) [(int)childIndex]; 
            }
        }

        public override NSObject GetObjectValue (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            return new NSString (((SourceListItem)item).Title);
        }
        #endregion

        #region Internal Methods
        internal SourceListItem ItemForRow(int row) {
            int index = 0;

            // Look at each group
            foreach (SourceListItem item in Items) {
                // Is the row inside this group?
                if (row >= index && row <= (index + item.Count)) {
                    return item [row - index - 1];
                }

                // Move index
                index += item.Count + 1;
            }

            // Not found 
            return null;
        }
        #endregion
    }
}
```

Esto proporcionará los datos de nuestra lista de origen.

En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `SourceListDelegate` para el **nombre** y haga clic en el **New** botón. Realizar el `SourceListDelegate.cs` archivo aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDelegate : NSOutlineViewDelegate
    {
        #region Private variables
        private SourceListView _controller;
        #endregion

        #region Constructors
        public SourceListDelegate (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Methods
        public override bool ShouldEditTableColumn (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            return false;
        }

        public override NSCell GetCell (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            nint row = outlineView.RowForItem (item);
            return tableColumn.DataCellForRow (row);
        }

        public override bool IsGroupItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            NSTableCellView view = null;

            // Is this a group item?
            if (((SourceListItem)item).HasChildren) {
                view = (NSTableCellView)outlineView.MakeView ("HeaderCell", this);
            } else {
                view = (NSTableCellView)outlineView.MakeView ("DataCell", this);
                view.ImageView.Image = ((SourceListItem)item).Icon;
            }

            // Initialize view
            view.TextField.StringValue = ((SourceListItem)item).Title;

            // Return new view
            return view;
        }

        public override bool ShouldSelectItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return (outlineView.GetParent (item) != null);
        }

        public override void SelectionDidChange (NSNotification notification)
        {
            NSIndexSet selectedIndexes = _controller.SelectedRows;

            // More than one item selected?
            if (selectedIndexes.Count > 1) {
                // Not handling this case
            } else {
                // Grab the item
                var item = _controller.Data.ItemForRow ((int)selectedIndexes.FirstIndex);

                // Was an item found?
                if (item != null) {
                    // Fire the clicked event for the item
                    item.RaiseClickedEvent ();

                    // Inform caller of selection
                    _controller.RaiseItemSelected (item);
                }
            }
        }
        #endregion
    }
}
```

Esto le dará el comportamiento de nuestra lista de origen.

Por último, en la **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `SourceListView` para el **nombre** y haga clic en el **New** botón. Realizar el `SourceListView.cs` archivo aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }
        
        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

Esto crea una subclase personalizada y reutilizable de `NSOutlineView` (`SourceListView`) que podemos usar para controlar la lista de origen en cualquier aplicación Xamarin.Mac que se realizan.

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>Crear y mantener listas de origen en Xcode.

Ahora, vamos a nuestra lista de origen en el generador de interfaz de diseño. Haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz y arrastre una vista dividida de la **biblioteca Inspector**, agregarlo al controlador de vista y configurarlo para cambiar el tamaño con la vista en la **Editor de restricciones** :

[ ![](source-list-images/source00.png "Modificar restricciones")](source-list-images/source00.png)

A continuación, arrastre una lista de origen de la **biblioteca Inspector**, agregar a la izquierda de la vista de división y configurarlo para cambiar el tamaño con la vista en la **Editor de restricciones**:

[ ![](source-list-images/source02.png "Modificar restricciones")](source-list-images/source02.png)

A continuación, cambie a la **identidad vista**, seleccione la lista de origen y cambiarlo de **clase** a `SourceListView`:

[ ![](source-list-images/source03.png "Establecer el nombre de clase")](source-list-images/source03.png)

Por último, cree un **toma** para nuestra lista de origen denominado `SourceList` en el `ViewController.h` archivo:

[ ![](source-list-images/source04.png "Configurar una salida")](source-list-images/source04.png)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>Rellenar la lista de origen

Editar la `RotationWindow.cs` de archivos en Visual Studio para Mac y hacerla de `AwakeFromNib` método aspecto similar al siguiente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Populate source list
    SourceList.Initialize ();

    var library = new SourceListItem ("Library");
    library.AddItem ("Venues", "house.png", () => {
        Console.WriteLine("Venue Selected");
    });
    library.AddItem ("Singers", "group.png");
    library.AddItem ("Genre", "cards.png");
    library.AddItem ("Publishers", "box.png");
    library.AddItem ("Artist", "person.png");
    library.AddItem ("Music", "album.png");
    SourceList.AddItem (library);

    // Add Rotation 
    var rotation = new SourceListItem ("Rotation"); 
    rotation.AddItem ("View Rotation", "redo.png");
    SourceList.AddItem (rotation);

    // Add Kiosks
    var kiosks = new SourceListItem ("Kiosks");
    kiosks.AddItem ("Sign-in Station 1", "imac");
    kiosks.AddItem ("Sign-in Station 2", "ipad");
    SourceList.AddItem (kiosks);

    // Display side list
    SourceList.ReloadData ();
    SourceList.ExpandItem (null, true);

}
```

El `Initialize ()` método debe llamarse en nuestra lista de origen **toma** _antes de_ todos los elementos se agregan a él. Para cada grupo de elementos, se crea un elemento primario y, a continuación, agregue los elementos de sub a ese elemento de grupo. Cada grupo, a continuación, se agrega a la colección de la lista de origen `SourceList.AddItem (...)`. Las dos últimas líneas cargar los datos de la lista de origen y expande todos los grupos:

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

Por último, editar la `AppDelegate.cs` de archivos y realizar el `DidFinishLaunching` método aspecto similar al siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

Si ejecutamos nuestra aplicación, aparecerá el siguiente:

[ ![](source-list-images/source05.png "Ejecutar una aplicación de ejemplo")](source-list-images/source05.png)

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con listas de origen en una aplicación Xamarin.Mac. Hemos visto cómo crear y mantener listas de origen en el generador de interfaz de Xcode y cómo trabajar con listas de código fuente en código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacOutlines (ejemplo)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [Directrices de interfaz de sistema operativo X humanos](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introducción a las vistas de esquema](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
