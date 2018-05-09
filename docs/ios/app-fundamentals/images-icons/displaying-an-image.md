---
title: Mostrar una imagen
description: Este artículo se tratan incluido un recurso de imagen en una aplicación de Xamarin.iOS y mostrar esa imagen mediante el uso de código de C# o mediante la asignación a un control en el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/24/2018
ms.openlocfilehash: f1f733fa91be7bf76e19896e78809d18494891d3
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="displaying-an-image"></a>Mostrar una imagen

_Este artículo se tratan incluido un recurso de imagen en una aplicación de Xamarin.iOS y mostrar esa imagen mediante el uso de código de C# o mediante la asignación a un control en el Diseñador de iOS._

En este artículo se tratan detalladamente los siguientes temas:

- [Agregar y organizar las imágenes en un Xamarin.iOS App](#adding-assets) : cubre activos de imagen y cómo pueden incluirse, organiza y administra dentro de un proyecto de Xamarin.iOS.
- [Agregar imágenes a los catálogos de activos](#asset-catalogs) -administrar imágenes con catálogos de activos.
    - [Usar imágenes vectoriales en catálogos de activos](#Using-Vector-Images-in-Asset-Catalogs) -proporcionar a todos los tamaños de imagen de un vector único.
- [Trabajar con imágenes de plantilla](#Working-with-Template-Images) -estableciendo un recurso de imagen como una imagen de plantilla, el desarrollador puede fácilmente colorear que se ajuste a los cambios en el tema de interfaz de usuario de una aplicación mediante el establecimiento de la imagen `Tint` propiedad.
- [Uso de imágenes con controles](#controls) : explica el uso de activos de imagen que se incluyen en un proyecto de Xamarin.iOS con controles de interfaz de usuario como `UIButton` y `UIImageView` y cómo trabajar con imágenes en C# con el `UIImage` del objeto [FromBundle](#frombundle) método.
- [Mostrar una imagen en un guiones gráficos](#Displaying-an-Image-in-a-Storyboards) -proporciona un ejemplo de mostrar una imagen con un guión gráfico.
- [Mostrar una imagen en el código](#Displaying-an-Image-in-Code) -proporciona un ejemplo de mostrar una imagen mediante código C#.

<a name="adding-assets" />

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Agregar y organizar las imágenes en una aplicación Xamarin.iOS

Al agregar una imagen para su uso en una aplicación de Xamarin.iOS, el desarrollador usará un _catálogo de activos_ para admitir cada dispositivo iOS y la resolución requeridos por una aplicación.

Agregado en iOS 7, **conjuntos de imágenes de catálogos de activos** contienen todas las versiones o las representaciones de una imagen que son necesarias para admitir varios dispositivos y escalar factores para una aplicación. En lugar de basarse en el nombre de archivo de activos de imagen (vea [imágenes independientes de resolución y nomenclatura de imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md)), **conjuntos de imágenes** use un archivo Json para especificar qué imagen pertenece a qué dispositivo o resolución . Este es el método preferido para administrar y son compatibles con imágenes en iOS (en iOS 9 o superior).

<a name="asset-catalogs" />

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Agregar imágenes a una imagen de catálogo de Asset configuradas

Como se mencionó anteriormente, un **conjuntos de imágenes de catálogos de activos** contienen todas las versiones o las representaciones de una imagen que son necesarias para admitir varios dispositivos y escalar factores para una aplicación. En lugar de basarse en el nombre de archivo de activos de imagen **conjuntos de imágenes** use un archivo Json para especificar qué imagen pertenece a qué dispositivo o la resolución.

Para crear un nuevo conjunto de imagen y agregar imágenes a él, haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **el Explorador de soluciones**, haga doble clic en el `Assets.xcassets` archivo para abrirlo y editarlo:

    ![](displaying-an-image-images/imageset01.png "El Assets.xcassets en el Explorador de soluciones")
2. Haga doble clic en el **lista de activos** y seleccione **nuevo conjunto de imagen**:

    ![](displaying-an-image-images/imageset02.png "Agregar un nuevo conjunto de imágenes")
3. Seleccione el nuevo conjunto de imagen y se mostrará el editor:

    ![](displaying-an-image-images/imageset03.png "El editor de conjunto de imágenes")
4. Desde aquí, arrastre en imágenes para cada uno de los distintos dispositivos y y las soluciones necesarias. (Nota: que estas soluciones coinciden con las resoluciones especificadas en el [tamaños de las imágenes y los nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documento.)
5. Haga doble clic en el nuevo conjunto de imagen **nombre** en el **lista de activos** para editarlo: ![ ] (displaying-an-image-images/imageset04.png "Editar nombre del conjunto de imagen nueva")

Cuando se usa un **imagen establece** en el Diseñador de iOS, simplemente seleccione el nombre del conjunto en la lista desplegable en el Editor de propiedades:

![](displaying-an-image-images/imageset06.png "Seleccione el nombre del conjunto de una imagen en la lista desplegable")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abrir el catálogo de activos de la **el Explorador de soluciones**y en la esquina superior izquierda, haga clic en el **más** botón:

    ![](displaying-an-image-images/asset5.png "Haga clic en el signo más botón")

2. Seleccione **Agregar conjunto de imagen** y el editor de conjuntos de imagen se mostrará para el nuevo conjunto de imagen. Desde aquí, arrastre en imágenes para cada uno de los distintos dispositivos y y las soluciones necesarias. (Nota: que estas soluciones coinciden con las resoluciones especificadas en el [tamaños de las imágenes y los nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documento):

    ![](displaying-an-image-images/asset7.png "El editor de conjunto de imágenes")

### <a name="renaming-an-image-set"></a>Cambiar el nombre de un conjunto de imágenes

Para cambiar el nombre de un conjunto de imagen, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el **catálogo de activos** archivo para abrirlo y editarlo:

    ![](displaying-an-image-images/rename01.png "El catálogo de activos en el Explorador de soluciones")
2. Seleccione el **Establecer imagen** para cambiar el nombre:

    ![](displaying-an-image-images/rename02.png "Seleccione el conjunto de imagen para cambiar el nombre")
3. En el **el Explorador de propiedades**, desplácese a la parte inferior y seleccione **nombre**(bajo la **varios** sección):

    ![](displaying-an-image-images/rename03.png "Seleccione el nombre de la sección de varios")
4. Escriba un nuevo **nombre** para el **Establecer imagen** y guarde los cambios.

-----

Cuando se usa un **Establecer imagen** en el código, hacer referencia a él por su nombre mediante una llamada a la `FromBundle` método de la `UIImage` clase. Por ejemplo:

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Si las imágenes que se asigna a un conjunto de imágenes no aparecen correctamente, asegúrese de que el nombre de archivo correcto que se usa con la `FromBundle` método (el **Establecer imagen** y no el elemento primario **catálogo de activos** nombre). Para las imágenes PNG, el `.png` se puede omitir la extensión. Para otros formatos de imagen, la extensión es necesaria (p. ej. `PurpleMonkey.jpg`).

<a name="Using-Vector-Images-in-Asset-Catalogs" />

### <a name="using-vector-images-in-asset-catalogs"></a>Usar imágenes vectoriales en catálogos de activos

A partir de iOS 8, especiales **Vector** tal y como se ha agregado a la clase **conjuntos de imágenes** que permite al desarrollador incluir un **PDF** con el formato de imagen de vector en el casete incluidos en su lugar archivos de mapa de bits individuales con distintas resoluciones. Con este método, se proporciona un archivo de vector único para la `@1x` resolución (con formato como archivo PDF vector) y la `@2x` y `@3x` las versiones del archivo se generarán en tiempo de compilación e incluidas en el paquete de la aplicación.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](displaying-an-image-images/imageset05.png "Imágenes vectoriales en el editor de catálogos de activos")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/asset8.png "Imágenes vectoriales en el editor de catálogos de activos")

-----

Por ejemplo, si el desarrollador incluye un `MonkeyIcon.pdf` archivo que el vector de un catálogo de activos con una resolución de 150 px x 150px, el mapa de bits siguiente cuando se compila, se incluiría en el paquete de aplicación final activos:

- `MonkeyIcon@1x.png` -150 px x 150px resolución.
- `MonkeyIcon@2x.png` -300px x 300px resolución.
- `MonkeyIcon@3x.png` -450px x 450px resolución.

A continuación debe tenerse en cuenta al usar imágenes vectoriales PDF en catálogos de activo:

- No es soporte vector completa ya que el archivo PDF estarán rasterizada a un mapa de bits en tiempo de compilación y los mapas de bits que se incluye en la aplicación final.
- No se puede ajustar el tamaño de la imagen de una vez que se ha establecido en el catálogo de activos. Si el desarrollador intenta cambiar el tamaño de la imagen (ya sea en el código o mediante el uso de clases de tamaño y diseño automático) la imagen se distorsiona al igual que cualquier otro mapa de bits.
- Catálogos de Asset sólo son compatibles con iOS 7 y versiones posteriores, si una aplicación necesite admitir iOS 6 o inferior, no se puede usar los catálogos de Asset.

<a name="Working-with-Template-Images" />

## <a name="working-with-template-images"></a>Trabajar con imágenes de plantilla

En función del diseño de una aplicación de iOS, puede haber ocasiones cuando el desarrollador debe personalizar un icono o una imagen dentro de la interfaz de usuario para que coincida con un cambio en la combinación de colores (por ejemplo, en función de las preferencias del usuario).

Para lograr fácilmente este efecto, cambie la _modo de representación de_ de los activos de imagen a **imagen de plantilla**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage01.png "Establecer el modo de representación de imagen de plantilla")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage01vs.png "El modo de representar establecido en plantilla")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

En el Diseñador de iOS, asigne el recurso de imagen a un control de interfaz de usuario y, a continuación, establezca el **tinte** para colorear la imagen:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage03.png "Establecer el tinte para colorear la imagen")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage03vs.png "Establecer el tinte para colorear la imagen")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Si lo desea, pueden establecerse el activo de imagen y el tinte directamente en el código:

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Para utilizar una imagen de plantilla completamente desde el código, realice lo siguiente:

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Dado que la `RenderMode` propiedad de un `UIImage` es de solo lectura, utilice el `ImageWithRenderingMode` método para crear una nueva instancia de la imagen con la configuración de modo de representación deseada.

Hay tres posiblemente configuración para `UIImage.RenderMode` a través de la `UIImageRenderingMode` enum:

* `AlwaysOriginal` -Fuerza la imagen se representan como el archivo de imagen de origen original sin cambios.
* `AlwaysTemplate` -Fuerza la imagen a representar como una imagen de plantilla colorear los píxeles con los valores especificados `Tint` color.
* `Automatic` -Bien presenta la imagen como una plantilla o Original basada en el entorno que se utiliza en. Por ejemplo, si se utiliza la imagen en un `UIToolBar`, `UINavigationBar`, `UITabBar` o `UISegmentControl` se tratará como una plantilla.

<a name="Adding-new-Assets-Collections" />

## <a name="adding-new-assets-collections"></a>Agregar nuevas recopilaciones de activos

Al trabajar con imágenes en catálogos de activos puede haber ocasiones cuando una nueva colección será necesaria, en lugar de agregar todas las imágenes de la aplicación a la `Assets.xcassets` colección. Por ejemplo, al diseñar los recursos a petición.

Para agregar un nuevo catálogo de activos para el proyecto:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en el **nombre del proyecto** en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo archivo...**
2. Seleccione **iOS** > **catálogo de activos**, escriba un **nombre** para la recopilación y haga clic en el **New** botón:

    ![](displaying-an-image-images/asset01.png "Crear un nuevo catálogo de activos")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el Explorador de soluciones, haga doble clic en **Asset catálogos** carpeta y seleccione **Agregar > nuevo catálogo de activos**.
2. Asígnele un nombre y haga clic en **agregar**:

    ![](displaying-an-image-images/asset1.png "Crear un nuevo catálogo de activos")

-----


Desde aquí, puede que trabajará con la colección en la misma manera que el valor predeterminado `Assets.xcassets` colección incluida automáticamente en el proyecto.

<a name="controls" />

## <a name="using-images-with-controls"></a>Uso de imágenes con controles

Además de utilizar imágenes para admitir una aplicación, iOS también utiliza imágenes con tipos de control de aplicación, como barras de ficha, barras de herramientas, barras de navegación, tablas y botones. Una manera sencilla de crear una imagen aparecen en un control consiste en asignar un `UIImage` instancia para el control `Image` propiedad.

<a name="frombundle" />

### <a name="frombundle"></a>FromBundle

El `FromBundle` llamada al método es una llamada sincrónica (con bloqueo) que tiene un número de características de carga y la administración de imágenes integradas, como almacenamiento en caché de soporte técnico y el control automático de archivos de imágenes para diferentes resoluciones.  

En el ejemplo siguiente se muestra cómo establecer la imagen de un `UITabBarItem` en un `UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Suponiendo que `MyImage` es el nombre de un recurso de imagen que se agrega a un catálogo de activos anterior. Cuando se trabaja imágenes del catálogo de activos, simplemente especifique el nombre del conjunto de imagen en el `FromBundle` método para **PNG** con el formato de imágenes:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Para cualquier otro formato de imagen, incluya la extensión con el nombre. Por ejemplo:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Para obtener más información acerca de los iconos y las imágenes, consulte la documentación de Apple en [icono personalizado y directrices para la creación de imagen](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

<a name="Displaying-an-Image-in-a-Storyboards" />

## <a name="displaying-an-image-in-a-storyboards"></a>Mostrar una imagen en un guiones gráficos

Una vez agregada una imagen a un proyecto de Xamarin.iOS mediante un catálogos activo, puede ser fácilmente aparece en un guión gráfico mediante una `UIImageView` en el Diseñador de iOS. Por ejemplo, si se ha agregado el recurso de imagen siguientes:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](displaying-an-image-images/display01.png "Se ha agregado un ejemplo activo de imagen")

Realice lo siguiente para mostrar en un guión gráfico:

1. Haga doble clic en el `Main.storyboard` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo en el Diseñador de iOS.
2. Seleccione un **la imagen de vista** desde el **cuadro de herramientas**:

     ![](displaying-an-image-images/display02.png "Seleccione una vista de la imagen del cuadro de herramientas")
3. Arrastre la vista de la imagen a la superficie de diseño y la posición y ajuste su tamaño según sea necesario:

    ![](displaying-an-image-images/display03.png "Una nueva imagen de vista en la superficie de diseño")
4. En el **Widget** sección de la **Property Explorer** seleccione el elemento **imagen** activos que se mostrará:

    ![](displaying-an-image-images/display04.png "Seleccione el recurso de imagen deseado que se mostrará")
5. En el **vista** sección, utilice el **modo** controlar cómo la imagen se cambia de tamaño cuando el **imagen vista** se cambia el tamaño.
6. Con el **imagen vista** seleccionado, haga clic en nuevo para agregar **restricciones**:

    ![](displaying-an-image-images/display05.png "Agregar restricciones")
7. Arrastre el controlador de la forma "T" en cada borde de la **imagen vista** al lado correspondiente de la pantalla "anclar" la imagen a los lados. De esta manera, el **imagen vista** se reducir y crecer según se cambia el tamaño de la pantalla.
8. Guarde los cambios en el guión gráfico.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/display01vs.png "Se ha agregado un ejemplo activo de imagen")

Realice lo siguiente para mostrar en un guión gráfico:

1. Haga doble clic en el `Main.storyboard` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo en el Diseñador de iOS.
2. Seleccione un **la imagen de vista** desde el **cuadro de herramientas**:

     ![](displaying-an-image-images/display02vs.png "Seleccione una vista de la imagen del cuadro de herramientas")
3. Arrastre la vista de la imagen a la superficie de diseño y la posición y ajuste su tamaño según sea necesario:

    ![](displaying-an-image-images/display03vs.png "Una nueva imagen de vista en la superficie de diseño")
4. En el **Widget** sección de la **Property Explorer** seleccione el elemento **imagen** activos que se mostrará:

    ![](displaying-an-image-images/display04vs.png "Seleccione el recurso de imagen deseado que se mostrará")
5. En el **vista** sección, utilice el **modo** controlar cómo la imagen se cambia de tamaño cuando el **imagen vista** se cambia el tamaño.
6. Con el **imagen vista** seleccionado, haga clic en nuevo para agregar **restricciones**:

    ![](displaying-an-image-images/display05vs.png "Agregar restricciones")
7. Arrastre el controlador de la forma "T" en cada borde de la **imagen vista** al lado correspondiente de la pantalla "anclar" la imagen a los lados. De esta manera, el **imagen vista** se reducir y crecer según se cambia el tamaño de la pantalla.
8. Guarde los cambios en el guión gráfico.

-----


<a name="Displaying-an-Image-in-Code" />

## <a name="displaying-an-image-in-code"></a>Mostrar una imagen en el código

Al igual que mostrar una imagen en un guión gráfico, una vez que se ha agregado una imagen a un proyecto de Xamarin.iOS mediante un catálogos activos, que puedan fácilmente mostrarse mediante código C#.

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

Este código crea un nuevo `UIImageView` y le da un tamaño inicial y posición. A continuación, carga la imagen desde un recurso de imagen que se agrega al proyecto y agrega el `UIImageView` al elemento primario `UIView` para mostrarla.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icono personalizado y directrices para la creación de imágenes](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
