---
title: Imágenes en Xamarin.Mac
description: Este artículo explica cómo trabajar con imágenes e iconos en una aplicación Xamarin.Mac. Describe cómo crear y mantener las imágenes necesarias para crear el icono de la aplicación y utilizar imágenes en código de C# y el generador de interfaz de Xcode.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: ae0a42051d56eb8bf002c61dfbc60c99924ff301
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792101"
---
# <a name="images-in-xamarinmac"></a>Imágenes en Xamarin.Mac

_Este artículo explica cómo trabajar con imágenes e iconos en una aplicación Xamarin.Mac. Describe cómo crear y mantener las imágenes necesarias para crear el icono de la aplicación y utilizar imágenes en código de C# y el generador de interfaz de Xcode._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tendrá acceso a la misma imagen y el icono de las herramientas que un desarrollador que trabaja *Objective-C* y *Xcode* does.

Hay varias maneras de esa imagen activos se utilizan dentro de una aplicación de macOS (anteriormente conocido como Mac OS X). Simplemente muestre una imagen como parte de la interfaz de usuario de la aplicación, asignarlo a un control de interfaz de usuario como una barra de herramientas o el elemento de lista de origen, para proporcionar iconos, Xamarin.Mac resulta muy sencillo agregar excelente material gráfico a sus aplicaciones macOS de las maneras siguientes : 

- **Elementos de interfaz de usuario** -imágenes pueden mostrarse como fondos o como parte de la aplicación en una vista de imagen (`NSImageView`).
- **Botón** -imágenes se pueden mostrar en los botones (`NSButton`).
- **La imagen de celda** - como parte de un control de tabla según (`NSTableView` o `NSOutlineView`), imágenes pueden utilizarse en una celda de la imagen (`NSImageCell`).
- **Elemento de barra de herramientas** -imágenes se pueden agregar a una barra de herramientas (`NSToolbar`) como un elemento de barra de herramientas de imagen (`NSToolbarItem`).
- **Icono de la lista de origen** - como parte de una lista de origen (un formato `NSOutlineView`).
- **Icono de la aplicación** -una serie de imágenes se puede agrupar en un `.icns` establecer y usar como icono de la aplicación. Consulte nuestro [icono de la aplicación](~/mac/deploy-test/app-icon.md) documentación para obtener más información.

Además, macOS proporciona un conjunto de imágenes predefinidas que puede usarse en toda la aplicación.

[![Un ejemplo de ejecución de la aplicación](image-images/intro01.png "un ejemplo de ejecución de la aplicación")](image-images/intro01-large.png#lightbox)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con imágenes e iconos en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.


## <a name="adding-images-to-a-xamarinmac-project"></a>Agregar imágenes a un proyecto Xamarin.Mac

Al agregar una imagen para su uso en una aplicación Xamarin.Mac, hay varios lugares y formas de que el desarrollador puede incluir el archivo de imagen al origen del proyecto:

- **Árbol de proyecto principal [obsoleto]** -imágenes pueden agregarse directamente al árbol de proyectos. Al llamar a las imágenes almacenadas en el árbol de proyecto principal desde el código, se especifica ninguna ubicación de carpeta. Por ejemplo: `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Carpeta de recursos [obsoleto]** -especial **recursos** carpeta es para cualquier archivo que formará parte de la aplicación de agrupar como general, pantalla de inicio o icono de imágenes (o cualquier otra imagen o archivo el desarrollador desea agregar). Al llamar a las imágenes almacenadas en la **recursos** carpeta desde el código, igual que las imágenes almacenados en el árbol de proyecto principal, se especifica ninguna ubicación de carpeta. Por ejemplo: `NSImage.ImageNamed("tags.png")`.
- **Carpeta o subcarpeta [obsoleto] personalizado** -el programador puede agregar una carpeta personalizada en el árbol de origen de proyectos y almacenar las imágenes no existe. La ubicación donde se agrega el archivo se puede anidar en una subcarpeta adicional como ayuda para organizar el proyecto. Por ejemplo, si el desarrollador agrega un `Card` carpeta del proyecto y una subcarpeta de `Hearts` a esa carpeta, a continuación, almacene una imagen **Jack.png** en el `Hearts` carpeta, `NSImage.ImageNamed("Card/Hearts/Jack.png")` carga la imagen en en tiempo de ejecución.
- **Conjuntos de imágenes de catálogo de Asset [preferido]** , agregados en OS X El capitán, **conjuntos de imágenes de catálogos de activos** contienen todas las versiones o las representaciones de una imagen que son necesarias para admitir varios dispositivos y escalar factores para su aplicación. En lugar de basarse en el nombre de archivo de activos de imagen (**@1x**, **@2x**).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Agregar imágenes a una imagen de catálogo de asset conjunto

Como se mencionó anteriormente, un **conjuntos de imágenes de catálogos de activos** contienen todas las versiones o las representaciones de una imagen que son necesarias para admitir varios dispositivos y escalar factores de la aplicación. En lugar de basarse en el nombre de archivo de activos de imagen (consulte las imágenes independientes de la resolución y la nomenclatura de la imagen anterior), **conjuntos de imágenes** usar el Editor activo para especificar qué imagen pertenece a qué dispositivo o la resolución.

1. En el **solución Pad**, haga doble clic en el **Assets.xcassets** archivo para abrirlo y editarlo: 

    ![Al seleccionar la Assets.xcassets](image-images/imageset01.png "seleccionando el Assets.xcassets")
2. Haga doble clic en el **lista de activos** y seleccione **nuevo conjunto de imagen**: 

    [![Agregar un nuevo conjunto de imagen](image-images/imageset02.png "agregar un nuevo conjunto de imágenes")](image-images/imageset02-large.png#lightbox)
3. Seleccione el nuevo conjunto de imagen y se mostrará el editor: 

    [![Al seleccionar el nuevo conjunto de imagen](image-images/imageset03.png "seleccionando el nuevo conjunto de imagen")](image-images/imageset03-large.png#lightbox)
4. Desde aquí podemos arrastrar en imágenes para cada uno de los distintos dispositivos y resoluciones necesarias. 
5. Haga doble clic en el nuevo conjunto de imagen **nombre** en el **lista de activos** para editarlo: 

    [![Nombre del conjunto de editar la imagen](image-images/imageset04.png "nombre del conjunto de editar la imagen")](image-images/imageset04-large.png#lightbox)
    
Una clase especial **Vector** tal y como se ha agregado a la clase **conjuntos de imágenes** que nos permite incluir un _PDF_ con el formato de imagen de vector en el casset en su lugar, incluidos los archivos de mapa de bits individuales en las resoluciones diferentes. Con este método, proporcionar un archivo de vector único para la **@1x** resolución (con formato como archivo PDF vector) y la **@2x** y **@3x** versiones del archivo se generarán en tiempo de compilación e incluidas en el paquete de la aplicación.

[![Interfaz del editor de conjunto de la imagen](image-images/imageset05.png "imágenes establecer la interfaz de editor")](image-images/imageset05-large.png#lightbox)

Por ejemplo, si incluye un `MonkeyIcon.pdf` archivo que el vector de un catálogo de activos con una resolución de 150 px x 150px, el mapa de bits siguiente cuando se compila, se incluiría en el paquete de aplicación final activos:

1. **MonkeyIcon@1x.png** -150 px x 150px resolución.
2. **MonkeyIcon@2x.png** -300px x 300px resolución.
3. **MonkeyIcon@3x.png** -450px x 450px resolución.

A continuación debe tenerse en cuenta al usar imágenes vectoriales PDF en catálogos de activo:

- No es soporte vector completa ya que el archivo PDF estarán rasterizada a un mapa de bits en tiempo de compilación y los mapas de bits que se incluye en la aplicación final.
- No se puede ajustar el tamaño de la imagen de una vez que se ha establecido en el catálogo de activos. Si intenta cambiar el tamaño de la imagen (ya sea en el código o mediante el uso de clases de tamaño y diseño automático) la imagen se distorsiona al igual que cualquier otro mapa de bits.

Cuando se usa un **Establecer imagen** en el generador de interfaz de Xcode, puede seleccionar simplemente el nombre del conjunto en la lista desplegable en el **atributo Inspector**: **

![Al seleccionar una imagen establecido en el generador de interfaz de Xcode](image-images/imageset06.png "al seleccionar una imagen establecido en el generador de interfaz de Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Agregar nuevas recopilaciones de activos

Al trabajar con imágenes en catálogos de activos puede haber ocasiones cuando desee crear una nueva colección, en lugar de agregar todas las imágenes a la **Assets.xcassets** colección. Por ejemplo, al diseñar los recursos a petición.

Para agregar un nuevo catálogo de activos para el proyecto:

1. Haga doble clic en el proyecto en el **solución Pad** y seleccione **agregar** > **nuevo archivo...**
2. Seleccione **Mac** > **catálogo de activos**, escriba un **nombre** para la recopilación y haga clic en el **New** botón: 

    ![Agregar un nuevo catálogo de Asset](image-images/asset01.png "agregar un nuevo catálogo de activos")

Desde aquí puede trabajar con la colección en la misma manera que el valor predeterminado **Assets.xcassets** colección incluida automáticamente en el proyecto.


### <a name="adding-images-to-resources"></a>Agregar imágenes a los recursos

> [!IMPORTANT]
> Este método para trabajar con imágenes en una aplicación de macOS está en desuso por Apple. Debe usar [conjuntos de imágenes del catálogo de Asset](#asset-catalogs) al administrador de imágenes en su lugar, la aplicación.

Para poder usar un archivo de imagen en la aplicación Xamarin.Mac (ya sea en el código de C# o en el generador de interfaz de) debe incluirse en el proyecto **recursos** carpeta como un **agrupación de recursos**. Para agregar un archivo a un proyecto, haga lo siguiente:

1. Haga doble clic en el **recursos** carpeta en el proyecto en el **solución Pad** y seleccione **agregar** > **agregar archivos...** : 

    ![Agregar un archivo](image-images/add01.png "agregar un archivo")
2. Desde el **agregar archivos** cuadro de diálogo, seleccione las imágenes de archivos para agregar al proyecto, seleccione `BundleResource` para el **acción de compilación de invalidación** y haga clic en el **abiertos** botón:

    [![Seleccionar los archivos que desea agregar](image-images/add02.png "al seleccionar los archivos que desea agregar")](image-images/add02-large.png#lightbox)
3. Si los archivos ya no están en el **recursos** carpeta, se le preguntará si desea **copia**, **mover** o **vínculo** los archivos. Elegir que cada palos sus necesidades, por lo general que serán **copia**:

    ![Seleccione la acción de agregar](image-images/add04.png "seleccionando la acción de agregar")
4. Los nuevos archivos se incluirán en el proyecto y leer para su uso: 

    ![Los nuevos archivos de imagen agregados al relleno de solución](image-images/add03.png "los nuevos archivos de imagen agregados al relleno de solución")
5. Repita el proceso para los archivos de imagen requeridos.

Puede utilizar cualquier png, jpg o archivo pdf como una imagen de origen en la aplicación Xamarin.Mac. En la siguiente sección, veremos agregar versiones de alta resolución de nuestras imágenes e iconos para admitir la Retina según los equipos Mac.

> [!IMPORTANT]
> Si va a agregar imágenes a la **recursos** carpeta, puede dejar el **acción de compilación de invalidación** establecido en **predeterminado**. El valor predeterminado es una acción de compilación para esta carpeta `BundleResource`.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Proporcionar versiones de alta resolución de todos los recursos de gráficos de aplicación

Los activos gráficos que se agregan a una aplicación de Xamarin.Mac (iconos, controles personalizados, cursores personalizados, ilustraciones personalizados, etc.) deben tener versiones de alta resolución además de sus versiones de resolución estándar. Esto es necesario para que la aplicación tendrá un aspecto lo mejor posible cuando se ejecute en una pantalla Retina Display equipado equipo Mac.


### <a name="adopt-the-2x-naming-convention"></a>Adoptar la @2x convención de nomenclatura

> [!IMPORTANT]
> Este método para trabajar con imágenes en una aplicación de macOS está en desuso por Apple. Debe usar [conjuntos de imágenes del catálogo de Asset](#asset-catalogs) al administrador de imágenes en su lugar, la aplicación.

Al crear las versiones estándares y de alta resolución de una imagen, siga esta convención de nomenclatura para el par de imagen al incluirlos en el proyecto Xamarin.Mac:

- **Resolución estándar**  - **ImageName.filename extensión** (ejemplo: **tags.png**)
- **Alta resolución**   -  **ImageName@2x.filename-extension** (ejemplo: **tags@2x.png**)

Cuando se agrega a un proyecto, podría aparecer como sigue:

![Los archivos de imagen en el panel de la solución](image-images/add03.png "los archivos de imagen en el panel de la solución")

Cuando se asigna una imagen a un elemento de interfaz de usuario en el generador de interfaz simplemente obtendrá el archivo en el _ImageName_**.** _extensión de nombre de archivo_ formato (ejemplo: **tags.png**). El mismo para usar una imagen en el código de C#, podrá seleccionar el archivo en el _ImageName_**.** _extensión de nombre de archivo_ formato.

Cuando se Xamarin.Mac aplicación se ejecuta en un equipo Mac, la _ImageName_**.** _extensión de nombre de archivo_ imagen de formato que se usará en las pantallas de resolución estándar, el **ImageName@2x.filename-extension** imagen se seleccionará automáticamente pantalla Retina envergadura equipos Mac.


## <a name="using-images-in-interface-builder"></a>Uso de imágenes en el generador de interfaz

Cualquier recurso de imagen la ha agregado a la **recursos** carpeta en su Xamarin.Mac del proyecto y ha establecido la acción de compilación **BundleResource** aparecerá automáticamente en el generador de interfaz y puede ser selecciona como parte de un elemento de interfaz de usuario (si trata las imágenes).

Para utilizar una imagen en el generador de interfaz, haga lo siguiente:

1. Agregar una imagen a la **recursos** carpeta con una **acción de compilación** de `BundleResource`: 

     ![Un recurso de imagen en el panel de la solución](image-images/ib00.png "un recurso de imagen en el panel de la solución")
2. Haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en el generador de interfaz: 

     [![Editar el guión gráfico principal](image-images/ib01.png "el guión gráfico principal de edición")](image-images/ib01-large.png#lightbox)
3. Arrastre un elemento de interfaz de usuario que tiene imágenes en la superficie de diseño (por ejemplo, un **elemento de la barra de herramientas de imagen**): 

     ![Editar un elemento de la barra de herramientas](image-images/ib02.png "editar un elemento de la barra de herramientas")
4. Seleccione la imagen que ha agregado a la **recursos** carpeta en el **nombre de la imagen** lista desplegable: 

     [![Al seleccionar una imagen para un elemento de barra de herramientas](image-images/ib03.png "al seleccionar una imagen para un elemento de barra de herramientas")](image-images/ib03-large.png#lightbox)
5. La imagen seleccionada se mostrará en la superficie de diseño: 

     ![La imagen que se muestra en el editor de la barra de herramientas](image-images/ib04.png "la imagen que se muestra en el editor de la barra de herramientas")
6. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Los pasos anteriores funcionan para cualquier elemento de interfaz de usuario que permite a su propiedad de imagen que se establecerán en el **Inspector de atributo**. Una vez más, si ha incluido un **@2x** versión de su archivo de imagen, se usará automáticamente en pantalla Retina según los equipos Mac.

> [!IMPORTANT]
> Si la imagen no está disponible en la **nombre de la imagen** de lista desplegable, cierre el proyecto .storyboard en Xcode y vuelva a abrirlo desde Visual Studio para Mac. Si la imagen todavía no está disponible, asegúrese de que su **acción de compilación** es `BundleResource` y que la imagen se ha agregado a la **recursos** carpeta.

## <a name="using-images-in-c-code"></a>Uso de imágenes en código C#

Al cargar una imagen en memoria mediante código C# en la aplicación Xamarin.Mac, se almacenará la imagen en un `NSImage` objeto. Si el archivo de imagen se ha incluido en el paquete de aplicación Xamarin.Mac (incluido en los recursos), utilice el código siguiente para cargar la imagen:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

El código anterior utiliza el método estático `ImageNamed("...")` método de la `NSImage` clase para cargar la imagen especificada en la memoria desde el **recursos** carpeta, si no se encuentra la imagen, `null` se devolverá. Como imágenes asignada en el generador de interfaz, si ha incluido un **@2x** versión de su archivo de imagen, se usará automáticamente en pantalla Retina según los equipos Mac.

Para cargar imágenes fuera de la agrupación de la aplicación (desde el sistema de archivos Mac), utilice el código siguiente:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Trabajar con imágenes de plantilla

En función del diseño de la aplicación de Mac OS, puede haber ocasiones en que necesite personalizar un icono o una imagen dentro de la interfaz de usuario para que coincida con un cambio en la combinación de colores (por ejemplo, en función de las preferencias del usuario).

Para lograr este efecto, cambie la _modo de representación de_ de su recurso de imagen a **imagen de plantilla**:

[![Configuración de una imagen de plantilla](image-images/templateimage01.png "establecer una imagen de plantilla")](image-images/templateimage01-large.png#lightbox)

Desde el generador de interfaz de Xcode, asigne al recurso de imagen a un control de interfaz de usuario:

![Al seleccionar una imagen en el generador de interfaz de Xcode](image-images/templateimage02.png "al seleccionar una imagen en el generador de interfaz de Xcode")

O bien, opcionalmente, establecer el origen de la imagen en el código:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Agregue la siguiente función pública en el controlador de vista:

```csharp
public NSImage ImageTintedWithColor (NSImage image, NSColor tint)
{
    var tintedImage = image.Copy () as NSImage;
    var frame = new CGRect (0, 0, image.Size.Width, image.Size.Height);

    // Apply tint
    tintedImage.LockFocus ();
    tint.Set ();
    NSGraphics.RectFill (frame, NSCompositingOperation.SourceAtop);
    tintedImage.UnlockFocus ();
    tintedImage.Template = false;

    // Return tinted image
    return tintedImage;
}
```

Por último, para agregar tinte a una imagen de plantilla, llame a esta función en la imagen para colorear:

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Uso de imágenes con vistas de tabla

Para incluir una imagen como parte de la celda en una `NSTableView`, deberá cambiar cómo se devuelven los datos de la vista de tabla `NSTableViewDelegate's` `GetViewForItem` método que se usa un `NSTableCellView` en lugar de la típica `NSTextField`. Por ejemplo:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Hay unas pocas líneas de interés aquí. En primer lugar, para las columnas que se van a incluir una imagen, creamos un nuevo `NSImageView` tengan el tamaño necesario y la ubicación, también creamos un nuevo `NSTextField` y establecer su posición predeterminada en función de si estamos usando una imagen:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

En segundo lugar, es necesario incluir la nueva vista de imagen y campo de texto en el elemento primario `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por último, se debe indicar el campo de texto que puede reducir y crecer al ritmo de la celda de la vista de tabla:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Salida del ejemplo:

[![Un ejemplo de mostrar una imagen en una aplicación](image-images/tables01.png "muestra un ejemplo de mostrar una imagen en una aplicación")](image-images/tables01-large.png#lightbox)

Para obtener más información sobre cómo trabajar con vistas de tabla, vea nuestra [vistas de la tabla](~/mac/user-interface/table-view.md) documentación.

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Uso de imágenes con vistas de esquema

Para incluir una imagen como parte de la celda en una `NSOutlineView`, deberá cambiar cómo se devuelven los datos de la vista de esquema `NSTableViewDelegate's` `GetView` método que se usa un `NSTableCellView` en lugar de la típica `NSTextField`. Por ejemplo:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Hay unas pocas líneas de interés aquí. En primer lugar, para las columnas que se van a incluir una imagen, creamos un nuevo `NSImageView` tengan el tamaño necesario y la ubicación, también creamos un nuevo `NSTextField` y establecer su posición predeterminada en función de si estamos usando una imagen:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

En segundo lugar, es necesario incluir la nueva vista de imagen y campo de texto en el elemento primario `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Por último, se debe indicar el campo de texto que puede reducir y crecer al ritmo de la celda de la vista de tabla:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Salida del ejemplo:

[![Un ejemplo de una imagen que se muestra en una vista de esquema](image-images/outline01.png "muestra un ejemplo de una imagen que se muestra en una vista de esquema")](image-images/outline01-large.png#lightbox)

Para obtener más información sobre cómo trabajar con vistas de esquema, vea nuestra [vistas de esquema](~/mac/user-interface/outline-view.md) documentación.


## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con imágenes e iconos en una aplicación Xamarin.Mac. Hemos visto los distintos tipos y los usos de imágenes, cómo usar imágenes e iconos en el generador de interfaz de Xcode y cómo trabajar con imágenes e iconos en código C#.



## <a name="related-links"></a>Vínculos relacionados

- [MacImages (ejemplo)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [macOS X directrices de interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Acerca de la alta resolución para OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
