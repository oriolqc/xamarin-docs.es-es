---
title: Imágenes de Xamarin.Mac
description: En este artículo se explica cómo trabajar con imágenes e iconos en una aplicación de Xamarin.Mac. Describe cómo crear y mantener las imágenes necesarias para crear el icono de la aplicación y usar imágenes en el código de C# y en Interface Builder de Xcode.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 719efc87b8843d0d2fcd2643aab23aa6849d940a
ms.sourcegitcommit: 190808013249005ceffbc798f9f4570e8cdc943a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2019
ms.locfileid: "54841385"
---
# <a name="images-in-xamarinmac"></a>Imágenes de Xamarin.Mac

_En este artículo se explica cómo trabajar con imágenes e iconos en una aplicación de Xamarin.Mac. Describe cómo crear y mantener las imágenes necesarias para crear el icono de la aplicación y usar imágenes en el código de C# y en Interface Builder de Xcode._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, tendrá acceso a la misma imagen herramientas e icono que un desarrollador que trabaja *Objective-C* y *Xcode* does.

Hay varias maneras de esa imagen se usan recursos dentro de una aplicación de macOS (antes conocido como Mac OS X). Simplemente se muestre una imagen como parte de la interfaz de usuario de la aplicación, asignarlo a un control de interfaz de usuario como una barra de herramientas o un elemento de lista de origen, para proporcionar iconos, Xamarin.Mac facilita agregar material gráfico excelente a las aplicaciones de macOS de las maneras siguientes : 

- **Elementos de interfaz de usuario** -se pueden mostrar imágenes como fondos o como parte de la aplicación en una vista de imagen (`NSImageView`).
- **Botón** -se pueden mostrar imágenes en los botones (`NSButton`).
- **Imagen de celda** : como parte de un control en función de tabla (`NSTableView` o `NSOutlineView`), las imágenes pueden usarse en una celda de la imagen (`NSImageCell`).
- **Elemento de barra de herramientas** -se pueden agregar imágenes a una barra de herramientas (`NSToolbar`) como un elemento de barra de herramientas de imagen (`NSToolbarItem`).
- **Icono de la lista de origen** : como parte de una lista de origen (un formato especial `NSOutlineView`).
- **Icono de la aplicación** -una serie de imágenes se puede agrupar en un `.icns` establecer y usar como icono de la aplicación. Consulte nuestra [icono de la aplicación](~/mac/deploy-test/app-icon.md) documentación para obtener más información.

Además, macOS proporciona un conjunto de imágenes predefinidas que puede usarse en toda la aplicación.

[![Un ejemplo de ejecución de la aplicación](image-images/intro01.png "un ejemplo de ejecución de la aplicación")](image-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos de trabajar con imágenes e iconos en una aplicación de Xamarin.Mac. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en este artículo.


## <a name="adding-images-to-a-xamarinmac-project"></a>Agregar imágenes a un proyecto de Xamarin.Mac

Al agregar una imagen para su uso en una aplicación de Xamarin.Mac, hay varios lugares y formas de que el desarrollador puede incluir el archivo de imagen al origen del proyecto:

- **Árbol de proyecto principal [en desuso]** -imágenes se pueden agregar directamente al árbol de proyectos. Al llamar a las imágenes almacenadas en el árbol de proyecto principal desde el código, se especifica ninguna ubicación de carpeta. Por ejemplo: `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Carpeta de recursos [en desuso]** -especial **recursos** carpeta es para cualquier archivo que se pasará a formar parte de la aplicación del lote como icono de pantalla de inicio o general de imágenes (o cualquier otra imagen o archivo el desarrollador desea agregar). Al llamar a las imágenes almacenadas en el **recursos** carpeta desde el código, al igual que las imágenes almacenados en el árbol del proyecto principal, se especifica ninguna ubicación de carpeta. Por ejemplo: `NSImage.ImageNamed("tags.png")`.
- **Carpeta o subcarpeta [en desuso] personalizado** : el desarrollador puede agregar una carpeta personalizada en el árbol de origen de los proyectos y almacenar las imágenes no existe. La ubicación donde se agrega el archivo se puede anidar en una subcarpeta de más ayuda para organizar el proyecto. Por ejemplo, si el desarrollador agrega un `Card` carpeta al proyecto y una subcarpeta de `Hearts` a esa carpeta, a continuación, almacenar una imagen **Jack.png** en el `Hearts` carpeta, `NSImage.ImageNamed("Card/Hearts/Jack.png")` cargaría la imagen en tiempo de ejecución.
- **Conjuntos de imágenes del catálogo de activos [preferido]** : se ha agregado en OS X El capitán, **conjuntos de imágenes de los catálogos de activos** contienen todas las versiones o representaciones de una imagen que son necesarias para admitir varios dispositivos y factores de escala su aplicación. En lugar de confiar en el nombre de archivo de recursos de imagen (**@1x**, **@2x**).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Agregar imágenes a una imagen de catálogo activo conjunto

Como se indicó anteriormente, un **conjuntos de imágenes de los catálogos de activos** contienen todas las versiones o representaciones de una imagen que son necesarias para admitir varios dispositivos y factores de la aplicación de escala. En lugar de confiar en el nombre de archivo de recursos de imagen (consulte las imágenes independientes de resolución y nomenclatura de la imagen anterior), **conjuntos de imágenes** usar el Editor de recursos para especificar qué imagen pertenece a qué dispositivo o resolución.

1. En el **panel de solución**, haga doble clic en el **Assets.xcassets** archivo para abrirlo y editarlo: 

    ![Seleccionar el Assets.xcassets](image-images/imageset01.png "seleccionando el Assets.xcassets")
2. Haga doble clic en el **lista Assets** y seleccione **nuevo conjunto de imagen**: 

    [![Agregar un nuevo conjunto de imágenes](image-images/imageset02.png "agregando un nuevo conjunto de imágenes")](image-images/imageset02-large.png#lightbox)
3. Seleccione el nuevo conjunto de imágenes y se mostrará el editor: 

    [![Seleccione el nuevo conjunto de imágenes](image-images/imageset03.png "seleccionando el nuevo conjunto de imágenes")](image-images/imageset03-large.png#lightbox)
4. Desde aquí podemos arrastrar en imágenes para cada uno de los distintos dispositivos y las resoluciones necesarias. 
5. Haga doble clic en el nuevo conjunto de imágenes **nombre** en el **lista Assets** para editarlo: 

    [![Nombre del conjunto de edición de la imagen](image-images/imageset04.png "nombre del conjunto de edición de la imagen")](image-images/imageset04-large.png#lightbox)
    
Un especial **Vector** tal como se han agregado a la clase **conjuntos de imágenes** que nos permite incluir un _PDF_ con el formato de imagen de vector en el casset en su lugar, incluidos los archivos de mapa de bits individuales en las diferentes resoluciones. Con este método, proporcione un archivo de un vector único para el **@1x** resolución (con formato como archivo PDF vector) y la **@2x** y **@3x** versiones del archivo se generarán en tiempo de compilación e incluidas en el paquete de la aplicación.

[![La imagen de conjunto de interfaz de editor](image-images/imageset05.png "la imagen de conjunto de interfaz de editor")](image-images/imageset05-large.png#lightbox)

Por ejemplo, si incluye un `MonkeyIcon.pdf` archivo como el vector de un catálogo de recursos con una resolución de 150 px x 150px, el mapa de bits siguiente cuando se compila, se incluiría en el paquete de aplicación final activos:

1. **MonkeyIcon@1x.png** -150px x 150px resolución.
2. **MonkeyIcon@2x.png** -300px x 300px resolución.
3. **MonkeyIcon@3x.png** -450px x 450px resolución.

El siguiente debe tenerse en cuenta al usar imágenes vectoriales PDF en catálogos de activos:

- Esto no es soporte completo de vector como el PDF estará rasterizada a un mapa de bits en tiempo de compilación y los mapas de bits se incluye en la aplicación final.
- No se puede ajustar el tamaño de la imagen una vez que se estableció en el catálogo de activos. Si se intenta cambiar el tamaño de la imagen (ya sea en el código o mediante el uso de diseño automático y las clases de tamaño) la imagen se distorsiona al igual que cualquier otro mapa de bits.

Cuando se usa un **Establecer imagen** en Interface Builder de Xcode, puede seleccionar simplemente el nombre del conjunto en la lista desplegable en el **Inspector de atributos**:

![Seleccionar una imagen establecido en Interface Builder de Xcode](image-images/imageset06.png "seleccionando una imagen establecido en Interface Builder de Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Adición de nuevas colecciones de recursos

Al trabajar con imágenes de catálogos de activos puede haber ocasiones cuando desee crear una nueva colección, en lugar de agregar todas las imágenes a la **Assets.xcassets** colección. Por ejemplo, al diseñar los recursos y a petición.

Para agregar un nuevo catálogo de recursos al proyecto:

1. Haga doble clic en el proyecto en el **panel de solución** y seleccione **agregar** > **nuevo archivo...**
2. Seleccione **Mac** > **catálogo de activos**, escriba un **nombre** para la recopilación y haga clic en el **New** botón: 

    ![Agregar un nuevo catálogo](image-images/asset01.png "agregando un nuevo catálogo")

Desde aquí puede trabajar con la colección en la misma manera que el valor predeterminado **Assets.xcassets** colección incluida automáticamente en el proyecto.


### <a name="adding-images-to-resources"></a>Agregar imágenes a los recursos

> [!IMPORTANT]
> Este método para trabajar con imágenes en una aplicación de macOS en desuso por Apple. Debe usar [conjuntos de imágenes del catálogo de activos](#asset-catalogs) al administrador de imágenes en su lugar, la aplicación.

Para poder usar un archivo de imagen en la aplicación de Xamarin.Mac (ya sea en el código de C# o de Interface Builder) debe incluirse en el proyecto **recursos** carpeta como un **agrupación de recursos**. Para agregar un archivo a un proyecto, realice lo siguiente:

1. Haga doble clic en el **recursos** carpeta del proyecto en el **panel de solución** y seleccione **agregar** > **agregar archivos...** : 

    ![Agregar un archivo](image-images/add01.png "agregando un archivo")
2. Desde el **agregar archivos** cuadro de diálogo, seleccione las imágenes de archivos para agregar al proyecto, seleccione `BundleResource` para el **acción de compilación de invalidación** y haga clic en el **abierto** botón:

    [![Seleccionar los archivos para agregar](image-images/add02.png "seleccionando los archivos para agregar")](image-images/add02-large.png#lightbox)
3. Si los archivos ya no están en el **recursos** carpeta, se le preguntará si desea **copia**, **mover** o **vínculo** los archivos. Elegir lo que cada palos sus necesidades, por lo general que serán **copia**:

    ![Si se selecciona la acción Agregar](image-images/add04.png "seleccionando la acción de agregar")
4. Los nuevos archivos se incluirán en el proyecto y leer para su uso: 

    ![Los nuevos archivos de imagen va en el panel de solución](image-images/add03.png "los nuevos archivos de imagen va en el panel de solución")
5. Repita el proceso para los archivos de imagen requeridos.

Puede utilizar cualquier png, jpg o archivo pdf como una imagen de origen en la aplicación de Xamarin.Mac. En la siguiente sección, examinaremos agregar versiones de alta resolución de nuestras imágenes e iconos para admitir la Retina según los equipos Mac.

> [!IMPORTANT]
> Si va a agregar imágenes a la **recursos** carpeta, puede dejar el **acción de compilación de invalidación** establecido en **predeterminado**. El valor predeterminado es una acción de compilación para esta carpeta `BundleResource`.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Proporcione versiones de alta resolución de todos los recursos de gráficos de la aplicación

Cualquier recurso de gráficos que se agrega a una aplicación de Xamarin.Mac (iconos, controles personalizados, cursores personalizados, material gráfico personalizado, etc.) deben tener versiones de alta resolución además de sus versiones de resolución estándar. Esto es necesario para que la aplicación tendrá un aspecto óptimo cuando se ejecutan en una pantalla retina equipado equipo Mac.


### <a name="adopt-the-2x-naming-convention"></a>Adoptar la @2x convención de nomenclatura

> [!IMPORTANT]
> Este método para trabajar con imágenes en una aplicación de macOS en desuso por Apple. Debe usar [conjuntos de imágenes del catálogo de activos](#asset-catalogs) al administrador de imágenes en su lugar, la aplicación.

Al crear las versiones estándares y de alta resolución de una imagen, siga esta convención de nomenclatura para el par de imagen al incluirlos en el proyecto de Xamarin.Mac:

- **Resolución estándar**  - **ImageName.filename extensión** (ejemplo: **tags.png**)
- **Alta resolución**   -  **ImageName@2x.filename-extension** (ejemplo: **tags@2x.png**)

Cuando se agrega a un proyecto, podría aparecer como sigue:

![Los archivos de imagen en el panel de solución](image-images/add03.png "los archivos de imagen en el panel de solución")

Cuando se asigna una imagen a un elemento de interfaz de usuario en el generador de interfaz de simplemente le permite seleccionar el archivo en el _ImageName_**.** _extensión_de_nombre_de_archivo_ formato (ejemplo: **tags.png**). El mismo para el uso de una imagen en el código C#, deberá seleccionar el archivo en el _ImageName_**.** _extensión_de_nombre_de_archivo_ formato.

Cuando la aplicación de Xamarin.Mac se ejecuta en un equipo Mac, la _ImageName_**.** _extensión_de_nombre_de_archivo_ imagen con formato, se utilizarán en las pantallas de resolución estándar, el **ImageName@2x.filename-extension** automáticamente se seleccionará la imagen pantalla retina bases equipos Mac.


## <a name="using-images-in-interface-builder"></a>Uso de imágenes en Interface Builder

Cualquier recurso de imagen se ha agregado a la **recursos** carpeta en su Xamarin.Mac del proyecto y ha establecido la acción de compilación **BundleResource** se mostrará automáticamente en Interface Builder y puede ser se selecciona como parte de un elemento de interfaz de usuario (si se ocupa de las imágenes).

Para utilizar una imagen en el generador de interfaz, realice lo siguiente:

1. Agregar una imagen a la **recursos** carpeta con un **acción de compilación** de `BundleResource`: 

     ![Un recurso de imagen en el panel de solución](image-images/ib00.png "un recurso de imagen en el panel de solución")
2. Haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en Interface Builder: 

     [![Editar el guión gráfico principal](image-images/ib01.png "el guión gráfico principal de edición")](image-images/ib01-large.png#lightbox)
3. Arrastre un elemento de interfaz de usuario que toma las imágenes a la superficie de diseño (por ejemplo, un **elemento de barra de herramientas de imagen**): 

     ![Editar un elemento de barra de herramientas](image-images/ib02.png "edita un elemento de barra de herramientas")
4. Seleccione la imagen que ha agregado a la **recursos** carpeta en el **nombre de la imagen** lista desplegable: 

     [![Seleccionar una imagen para un elemento de barra de herramientas](image-images/ib03.png "seleccionar una imagen para un elemento de barra de herramientas")](image-images/ib03-large.png#lightbox)
5. La imagen seleccionada se mostrará en la superficie de diseño: 

     ![La imagen que se muestra en el editor de la barra de herramientas](image-images/ib04.png "la imagen que se muestra en el editor de la barra de herramientas")
6. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Los pasos anteriores funcionan para cualquier elemento de interfaz de usuario que permite su propiedad de imagen debe establecerse el **Inspector de atributos**. Nuevamente, si ha incluido un **@2x** versión del archivo de imagen, se usará automáticamente en pantalla Retina Display en función de equipos Mac.

> [!IMPORTANT]
> Si la imagen no está disponible en el **nombre de la imagen** de lista desplegable, cierre el proyecto .storyboard en Xcode y vuelva a abrirlo desde Visual Studio para Mac. Si la imagen no está todavía disponible, asegúrese de que su **acción de compilación** es `BundleResource` y que la imagen se ha agregado a la **recursos** carpeta.

## <a name="using-images-in-c-code"></a>Uso de imágenes en el código de C#

Al cargar una imagen en memoria mediante código de C# en la aplicación de Xamarin.Mac, se almacenará la imagen en un `NSImage` objeto. Si el archivo de imagen se ha incluido en el paquete de aplicación de Xamarin.Mac (incluido en los recursos), use el siguiente código para cargar la imagen:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

El código anterior usa estático `ImageNamed("...")` método de la `NSImage` clase para cargar la imagen especificada en la memoria desde la **recursos** carpeta, si no se encuentra la imagen, `null` se devolverá. Como imágenes asignada en el generador de interfaz, si ha incluido un **@2x** versión del archivo de imagen, se usará automáticamente en pantalla Retina Display en función de equipos Mac.

Para cargar las imágenes fuera de la agrupación de la aplicación (desde el sistema de archivos de Mac), use el siguiente código:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Trabajar con imágenes de plantilla

En función del diseño de la aplicación macOS, puede haber ocasiones en que deba personalizar un icono o una imagen dentro de la interfaz de usuario para que coincida con un cambio en la combinación de colores (por ejemplo, según las preferencias del usuario).

Para lograr este efecto, cambie el _modo de representar_ de su recurso de imagen a **imagen de plantilla**:

[![Establecer una imagen de plantilla](image-images/templateimage01.png "establecer una imagen de plantilla")](image-images/templateimage01-large.png#lightbox)

Desde el generador de interfaz de Xcode, asigne al recurso de imagen a un control de interfaz de usuario:

![Seleccionar una imagen en Interface Builder de Xcode](image-images/templateimage02.png "seleccionando una imagen en Interface Builder de Xcode")

O bien, opcionalmente, establecer el origen de imagen en el código:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Agregue la siguiente función pública para el controlador de vista:

```csharp
public NSImage ImageTintedWithColor(NSImage sourceImage, NSColor tintColor)
    => NSImage.ImageWithSize(sourceImage.Size, false, rect => {
        // Draw the original source image
        sourceImage.DrawInRect(rect, CGRect.Empty, NSCompositingOperation.SourceOver, 1f);

        // Apply tint
        tintColor.Set();
        NSGraphics.RectFill(rect, NSCompositingOperation.SourceAtop);

        return true;
    });
```

> [!IMPORTANT]
> Especialmente con la llegada de modo oscuro en macOS Mojave, es importante evitar la `LockFocus` API cuando se representa personalizado de rear `NSImage` objetos. Dichas imágenes se vuelven estáticos y no se actualizará automáticamente para reflejar los cambios de densidad de aspecto o presentación.
>
> Empleando el mecanismo de controlador anterior, volver a representar para condiciones dinámicas se realizará automáticamente cuando el `NSImage` se hospeda, por ejemplo, en un `NSImageView`.

Por último, para una imagen de plantilla de matiz, llame a esta función en la imagen para colorear:

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Uso de imágenes con las vistas de tabla

Para incluir una imagen como parte de la celda de un `NSTableView`, deberá cambiar cómo se devuelven los datos de la vista de tabla `NSTableViewDelegate's` `GetViewForItem` método que se usará un `NSTableCellView` en lugar de la típica `NSTextField`. Por ejemplo:

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

Hay unas pocas líneas de interés aquí. En primer lugar, las columnas que se van a incluir una imagen, creamos un nuevo `NSImageView` del tamaño necesario y la ubicación, también creamos un nuevo `NSTextField` y colocar su posición predeterminada en función de si estamos usando una imagen:

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

Por último, necesitamos saber el campo de texto que pueden reducir y crecer con la celda de vista de tabla:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Salida del ejemplo:

[![Un ejemplo de cómo mostrar una imagen en una aplicación](image-images/tables01.png "un ejemplo de cómo mostrar una imagen en una aplicación")](image-images/tables01-large.png#lightbox)

Para obtener más información sobre cómo trabajar con vistas de tabla, vea nuestra [vistas de tabla](~/mac/user-interface/table-view.md) documentación.

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Uso de imágenes con vistas de esquema

Para incluir una imagen como parte de la celda de un `NSOutlineView`, deberá cambiar cómo se devuelven los datos mediante la vista de esquema `NSTableViewDelegate's` `GetView` método que se usará un `NSTableCellView` en lugar de la típica `NSTextField`. Por ejemplo:

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

Hay unas pocas líneas de interés aquí. En primer lugar, las columnas que se van a incluir una imagen, creamos un nuevo `NSImageView` del tamaño necesario y la ubicación, también creamos un nuevo `NSTextField` y colocar su posición predeterminada en función de si estamos usando una imagen:

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

Por último, necesitamos saber el campo de texto que pueden reducir y crecer con la celda de vista de tabla:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Salida del ejemplo:

[![Un ejemplo de una imagen que se muestran en una vista de esquema](image-images/outline01.png "muestra un ejemplo de una imagen que se muestran en una vista de esquema")](image-images/outline01-large.png#lightbox)

Para obtener más información sobre cómo trabajar con vistas de esquema, vea nuestra [vistas de esquema](~/mac/user-interface/outline-view.md) documentación.


## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con imágenes e iconos en una aplicación de Xamarin.Mac. Se ha visto los distintos tipos y usos de imágenes, cómo usar imágenes e iconos en Interface Builder de Xcode y cómo trabajar con imágenes e iconos en el código C#.



## <a name="related-links"></a>Vínculos relacionados

- [MacImages (ejemplo)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [Mac OS X directrices de interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Acerca de la alta resolución para OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
