---
title: Mostrar una imagen en Xamarin.iOS
description: En este artículo se trata incluido un recurso de imagen en una aplicación de Xamarin.iOS y mostrar esa imagen mediante el uso de código de C# o mediante la asignación a un control en el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/24/2018
ms.openlocfilehash: 69952b0bac884fe8b14edcbb87aa8fad47594880
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61252196"
---
# <a name="displaying-an-image-in-xamarinios"></a>Mostrar una imagen en Xamarin.iOS

_En este artículo se trata incluido un recurso de imagen en una aplicación de Xamarin.iOS y mostrar esa imagen mediante el uso de código de C# o mediante la asignación a un control en el Diseñador de iOS._

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Cómo agregar y organizar las imágenes en una aplicación Xamarin.iOS

Al agregar una imagen para su uso en una aplicación de Xamarin.iOS, el desarrollador usará un _catálogo de activos_ para admitir cada dispositivo iOS y la resolución necesaria por una aplicación.

Agregado en iOS 7, **conjuntos de imágenes de los catálogos de activos** contienen todas las versiones o representaciones de una imagen que son necesarias para admitir varios dispositivos y factores de una aplicación de escala. En lugar de confiar en el nombre de archivo de activos de imagen, **conjuntos de imágenes** usar un archivo Json para especificar qué imagen pertenece a qué dispositivo o resolución. Esta es la mejor manera de administrar y mantener imágenes en iOS (en iOS 9 o posterior).

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Agregar imágenes a una imagen de catálogo activo conjunto

Como se indicó anteriormente, un **conjuntos de imágenes de los catálogos de activos** contienen todas las versiones o representaciones de una imagen que son necesarias para admitir varios dispositivos y factores de una aplicación de escala. En lugar de confiar en el nombre de archivo de activos de imagen, **conjuntos de imágenes** usar un archivo Json para especificar qué imagen pertenece a qué dispositivo o resolución.

Para crear un nuevo conjunto de imágenes y agregar imágenes a él, realice lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **el Explorador de soluciones**, haga doble clic en el `Assets.xcassets` archivo para abrirlo y editarlo:

    ![](displaying-an-image-images/imageset01.png "El Assets.xcassets en el Explorador de soluciones")
2. Haga doble clic en el **lista Assets** y seleccione **nuevo conjunto de imagen**:

    ![](displaying-an-image-images/imageset02.png "Agregar un nuevo conjunto de imágenes")
3. Seleccione el nuevo conjunto de imágenes y se mostrará el editor:

    ![](displaying-an-image-images/imageset03.png "El editor de conjunto de imágenes")
4. Desde aquí, arrastre imágenes para cada uno de los distintos dispositivos y las resoluciones necesarias. 
5. Haga doble clic en el nuevo conjunto de imágenes **nombre** en el **lista Assets** para editarlo: ![](displaying-an-image-images/imageset04.png "Nombre del conjunto de imágenes nueva edición")

Cuando se usa un **Establecer imagen** en el Diseñador de iOS, simplemente seleccione el nombre del conjunto en la lista desplegable en el Editor de propiedades:

![](displaying-an-image-images/imageset06.png "Seleccione el nombre del conjunto de una imagen en la lista desplegable")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra el catálogo de activos desde el **el Explorador de soluciones**y en la esquina superior izquierda, haga clic en el **Plus** botón:

    ![](displaying-an-image-images/asset5.png "Haga clic en el botón")

2. Seleccione **Agregar conjunto de imagen** y el editor establece la imagen se mostrará para el nuevo conjunto de imágenes. Desde aquí, arrastre imágenes para cada uno de los distintos dispositivos y las resoluciones necesarias. 

    ![](displaying-an-image-images/asset7.png "El editor de conjunto de imágenes")

### <a name="renaming-an-image-set"></a>Cambiar el nombre de un conjunto de imágenes

Para cambiar el nombre de un conjunto de imágenes, realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el **catálogo de activos** archivo para abrirlo y editarlo:

    ![](displaying-an-image-images/rename01.png "El catálogo de activos en el Explorador de soluciones")
2. Seleccione el **Establecer imagen** para cambiar el nombre:

    ![](displaying-an-image-images/rename02.png "Seleccione el conjunto de imágenes para cambiar el nombre")
3. En el **Explorador de propiedades**, desplácese hacia abajo y seleccione **nombre**(bajo la **Misc** sección):

    ![](displaying-an-image-images/rename03.png "Seleccione el nombre en la sección Misc.")
4. Escriba un nuevo **nombre** para el **Establecer imagen** y guarde los cambios.

-----

Cuando se usa un **Establecer imagen** en código, se hace referencia por su nombre a la `FromBundle` método de la `UIImage` clase. Por ejemplo:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Si las imágenes que se asigna a un conjunto de imagen no aparecen correctamente, asegúrese de que se está usando el nombre de archivo correcta con el `FromBundle` método (el **Establecer imagen** y no el elemento primario **catálogo de activos** nombre). Para las imágenes PNG, el `.png` se puede omitir la extensión. Para otros formatos de imagen, la extensión es necesaria (p ej. `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>Uso de imágenes vectoriales en catálogos de activos

A partir de iOS 8, especial **Vector** tal como se han agregado a la clase **conjuntos de imágenes** que permite al desarrollador incluir un **PDF** con el formato de imagen de vector en el casete incluidos en su lugar archivos de mapa de bits individuales en las diferentes resoluciones. Con este método, proporcione un archivo de un vector único para el `@1x` resolución (con formato como archivo PDF vector) y la `@2x` y `@3x` versiones del archivo se generarán en tiempo de compilación e incluidas en el paquete de la aplicación.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "Imágenes vectoriales en el editor de catálogos de activos")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "Imágenes vectoriales en el editor de catálogos de activos")

-----

Por ejemplo, si el desarrollador incluye un `MonkeyIcon.pdf` archivo como el vector de un catálogo de recursos con una resolución de 150 px x 150px, el mapa de bits siguiente cuando se compila, se incluiría en el paquete de aplicación final activos:

- `MonkeyIcon@1x.png` -150px x 150px resolución.
- `MonkeyIcon@2x.png` -300px x 300px resolución.
- `MonkeyIcon@3x.png` -450px x 450px resolución.

El siguiente debe tenerse en cuenta al usar imágenes vectoriales PDF en catálogos de activos:

- Esto no es soporte completo de vector como el PDF estará rasterizada a un mapa de bits en tiempo de compilación y los mapas de bits se incluye en la aplicación final.
- No se puede ajustar el tamaño de la imagen una vez que se estableció en el catálogo de activos. Si el desarrollador intenta cambiar el tamaño de la imagen (ya sea en el código o mediante el uso de diseño automático y las clases de tamaño) la imagen se distorsiona al igual que cualquier otro mapa de bits.
- Catálogos de recursos solo son compatibles con iOS 7 y versiones posteriores, si una aplicación necesita admitir iOS 6 o inferior, no puede usar los catálogos de activos.

## <a name="working-with-template-images"></a>Trabajar con imágenes de plantilla

Según el diseño de una aplicación de iOS, puede haber ocasiones cuando el desarrollador necesita personalizar un icono o una imagen dentro de la interfaz de usuario para que coincida con un cambio en la combinación de colores (por ejemplo, según las preferencias del usuario).

Para lograr fácilmente este efecto, cambie el _modo de representar_ del recurso de imagen a **imagen de plantilla**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "Establece el modo de representar en la imagen de plantilla")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "Establecer el modo de representar a plantilla")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

Desde el Diseñador de iOS, asigne el recurso de imagen a un control de interfaz de usuario y, a continuación, establezca el **tinte** para colorear la imagen:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "Establece el tono para colorear la imagen")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "Establece el tono para colorear la imagen")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Opcionalmente, el recurso de imagen y el tinte pueden establecerse directamente en el código:

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Para usar una imagen de plantilla completamente desde el código, realice lo siguiente:

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Dado que el `RenderMode` propiedad de un `UIImage` es de solo lectura, utilice el `ImageWithRenderingMode` método para crear una nueva instancia de la imagen con la configuración del modo de representación deseada.

Hay tres, posiblemente, la configuración de `UIImage.RenderMode` a través de la `UIImageRenderingMode` enum:

* `AlwaysOriginal` -Fuerza la imagen que se representará como el archivo de imagen de origen original sin cambios.
* `AlwaysTemplate` -Fuerza la imagen que se representará como una imagen de plantilla por colorear los píxeles con los valores especificados `Tint` color.
* `Automatic` -Bien presenta la imagen como una plantilla o Original en función del entorno que se utiliza en. Por ejemplo, si se utiliza la imagen en un `UIToolBar`, `UINavigationBar`, `UITabBar` o `UISegmentControl` se tratará como una plantilla.

## <a name="adding-new-assets-collections"></a>Adición de nuevas colecciones de recursos

Al trabajar con imágenes de catálogos de activos puede haber ocasiones cuando una nueva colección será necesaria, en lugar de agregar todas las imágenes de la aplicación a la `Assets.xcassets` colección. Por ejemplo, al diseñar los recursos y a petición.

Para agregar un nuevo catálogo de recursos al proyecto:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el **nombre del proyecto** en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo archivo...**
2. Seleccione **iOS** > **catálogo de activos**, escriba un **nombre** para la recopilación y haga clic en el **New** botón:

    ![](displaying-an-image-images/asset01.png "Crear un nuevo catálogo")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el Explorador de soluciones, haga doble clic en **catálogos de activos** carpeta y seleccione **Agregar > nuevo catálogo**.
2. Asígnele un nombre y haga clic en **agregar**:

    ![](displaying-an-image-images/asset1.png "Crear un nuevo catálogo")

-----

Desde aquí, puede actuar la colección en la misma manera que el valor predeterminado `Assets.xcassets` colección incluida automáticamente en el proyecto.

## <a name="using-images-with-controls"></a>Uso de imágenes con controles

Además de utilizar imágenes para admitir una aplicación, iOS también usa imágenes con tipos de control de aplicaciones, como botones, barras de herramientas, barras de navegación, tablas y barras de pestañas. Una manera sencilla de crear una imagen aparecen en un control es asignar un `UIImage` instancia para el control `Image` propiedad.

### <a name="frombundle"></a>FromBundle

El `FromBundle` llamada al método es una llamada sincrónica (bloqueo) que tiene una serie de características de carga y la administración de imágenes integradas, como almacenamiento en caché de soporte técnico y el control automático de archivos de imágenes para diferentes resoluciones.  

El ejemplo siguiente muestra cómo establecer la imagen de un `UITabBarItem` en un `UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Suponiendo que `MyImage` es el nombre de un recurso de imagen que se agrega a un catálogo de recursos anterior. Cuando se trabaja imágenes del catálogo de recursos, simplemente especifique el nombre del conjunto de imágenes en el `FromBundle` método para **PNG** con el formato de imágenes:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Para cualquier otro formato de imagen, incluya la extensión con el nombre. Por ejemplo:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Para obtener más información acerca de los iconos e imágenes, consulte la documentación de Apple en [icono personalizado y directrices para la creación de imagen](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

## <a name="displaying-an-image-in-a-storyboards"></a>Mostrar una imagen en un guiones gráficos

Una vez agregada una imagen a un proyecto de Xamarin.iOS mediante un catálogos de recursos, puede ser fácilmente aparece en un guión gráfico mediante una `UIImageView` en el Diseñador de iOS. Por ejemplo, si se ha agregado el recurso de la imagen siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](displaying-an-image-images/display01.png "Se ha agregado un activo de imagen de ejemplo")

Siga este procedimiento para mostrarlo en un guión gráfico:

1. Haga doble clic en el `Main.storyboard` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo en el Diseñador de iOS.
2. Seleccione un **la imagen de vista** desde el **cuadro de herramientas**:

     ![](displaying-an-image-images/display02.png "Seleccione una vista de imagen en el cuadro de herramientas")
3. Arrastre la vista de imagen a la superficie de diseño y la posición y cambio de tamaño según sea necesario:

    ![](displaying-an-image-images/display03.png "Una nueva vista de imagen en la superficie de diseño")
4. En el **Widget** sección de la **Property Explorer** seleccione deseado **imagen** activos que se mostrará:

    ![](displaying-an-image-images/display04.png "Seleccione el recurso de imagen deseado que se mostrará")
5. En el **vista** sección, use el **modo** para controlar cómo será la imagen cambia de tamaño cuando el **vista de imagen** se cambia el tamaño.
6. Con el **vista de imagen** seleccionado, haga clic en nuevo para agregar **restricciones**:

    ![](displaying-an-image-images/display05.png "Agregar restricciones")
7. Arrastre el controlador "T" en forma de cada borde de la **vista de imagen** al lado correspondiente de la pantalla de la imagen a los lados "anclar". De este modo, el **vista de imagen** se reduzca y crezca cuando se cambia el tamaño de la pantalla.
8. Guarde los cambios en el guión gráfico.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "Se ha agregado un activo de imagen de ejemplo")

Siga este procedimiento para mostrarlo en un guión gráfico:

1. Haga doble clic en el `Main.storyboard` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo en el Diseñador de iOS.
2. Seleccione un **la imagen de vista** desde el **cuadro de herramientas**:

     ![](displaying-an-image-images/display02vs.png "Seleccione una vista de imagen en el cuadro de herramientas")
3. Arrastre la vista de imagen a la superficie de diseño y la posición y cambio de tamaño según sea necesario:

    ![](displaying-an-image-images/display03vs.png "Una nueva vista de imagen en la superficie de diseño")
4. En el **Widget** sección de la **Property Explorer** seleccione deseado **imagen** activos que se mostrará:

    ![](displaying-an-image-images/display04vs.png "Seleccione el recurso de imagen deseado que se mostrará")
5. En el **vista** sección, use el **modo** para controlar cómo será la imagen cambia de tamaño cuando el **vista de imagen** se cambia el tamaño.
6. Con el **vista de imagen** seleccionado, haga clic en nuevo para agregar **restricciones**:

    ![](displaying-an-image-images/display05vs.png "Agregar restricciones")
7. Arrastre el controlador "T" en forma de cada borde de la **vista de imagen** al lado correspondiente de la pantalla de la imagen a los lados "anclar". De este modo, el **vista de imagen** se reduzca y crezca cuando se cambia el tamaño de la pantalla.
8. Guarde los cambios en el guión gráfico.

-----

## <a name="displaying-an-image-in-code"></a>Mostrar una imagen en el código

Al igual que para mostrar una imagen en un guión gráfico, una vez que se ha agregado una imagen a un proyecto de Xamarin.iOS mediante un catálogos de activos, se puede fácilmente mostrar mediante código C#.

Considere el ejemplo siguiente:

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

Este código crea un nuevo `UIImageView` y le asigna una posición y tamaño inicial. A continuación, carga la imagen desde un recurso de imagen que se agrega al proyecto y agrega el `UIImageView` con el elemento primario `UIView` para mostrarla.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Tamaño de la imagen y la resolución (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
