---
title: "Mostrar imágenes"
ms.topic: article
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 716189fbf1518e9100a78cc5ae64e9e63a24c949
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="displaying-images"></a>Mostrar imágenes

Agregar imágenes a la aplicación requiere dos pasos: en primer lugar, agregue las imágenes al proyecto; a continuación, agregar controles y código para que se muestren en una pantalla. Hacer referencia a la [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) artículo para cobertura de imagen de control en Xamarin.iOS más detallada.

## <a name="adding-images-to-your-app"></a>Agregar imágenes a la aplicación

Pueden agregar imágenes a cualquier carpeta en la Visual Studio para la solución de Mac y si la **acción de compilación** está establecido en **contenido** , a continuación, el archivo se van a incluir en su aplicación y se pueden mostrar.

Visual Studio para Mac también admite un directorio especial denominado recursos que también pueden contener archivos de imagen. Los archivos en la carpeta de recursos deben tener la **acción de compilación** establecido en **BundleResource**.

Esta captura de pantalla muestra la **acción de compilación** opciones que aparecen cuando un archivo se hace doble clic:

 [![](image-images/image30a.png "Menú Acción de compilación")](image-images/image30a.png#lightbox)

Normalmente se elegirá el valor correcto de Visual Studio para Mac **acción de compilación** automáticamente, pero debe ser consciente de esta configuración, especialmente si mueve archivos de su proyecto.

### <a name="adding-an-image-file"></a>Agregar un archivo de imagen

Para agregar un archivo de imagen al proyecto, haga clic en el proyecto y elija **agregar archivos...**

 [![](image-images/image31a.png "Agregar archivos... menú")](image-images/image31a.png#lightbox)

Seleccione la imagen (o imágenes) que se va a incluir en el cuadro de diálogo de archivo estándar. El valor predeterminado la acción de compilación para imágenes serán **BundleResource** : no se invalidan este valor a menos que tenga un motivo concreto.

 [![](image-images/image32a.png "Agregar cuadro de diálogo de archivos")](image-images/image32a.png#lightbox)

La imagen se agregará al proyecto y disponible para cargarse y mostrarse en el código. Esta captura de pantalla muestra una imagen que se agrega a un proyecto de aplicación de iOS:

 [![](image-images/image33a.png "Imagen de proyecto")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>¿Qué es el directorio de recursos?

Archivos ubicados en el directorio de recursos se tratan de manera diferente de archivos normales, el contenido de la carpeta de recursos se copia en la raíz de la aplicación y puede hacer referencia a partir de ahí, en el código. Esto puede ser útil por diversos motivos:

-  Almacenar las imágenes que se configura en las propiedades de la aplicación, como las imágenes de inicio predeterminado y los iconos de la aplicación.
-  Almacenar otros archivos e imágenes por separado desde el código, por lo que sean más fáciles de administrar (subdirectorios se conservan cuando se copia el contenido del directorio de recursos).


El directorio de recursos es especialmente útil en un proyecto de biblioteca, ya que el código puede asumir que esas imágenes se copiarán en la raíz de la aplicación que consume, lo que resulta más fácil escribir bibliotecas de código compartido que requieren imágenes, audio, vídeo, XML u otros archivos.



El directorio de recursos debe denominarse por lo tanto, y todos los archivos deben tener la acción de compilación establecida en **BundleResource**

## <a name="displaying-the-image"></a>Mostrar la imagen

Para mostrar una imagen mediante el diseñador, una vista de la imagen se debe utilizar como un contenedor y puede mostrar una imagen o una animación de imágenes. El **imagen vista** icono del cuadro de herramientas se muestra a continuación:

 [![](image-images/image35a.png "ImageView en el cuadro de herramientas")](image-images/image35.png#lightbox)

Arrastre el **la imagen de vista** desde el **cuadro de herramientas** en el controlador de vista. A continuación, en ** imagen Vista > imagen ** la lista desplegable le proporcionará una lista de todos los archivos de imágenes disponibles en el proyecto. Seleccione cualquiera de ellos para agregarlo a la vista de la imagen.

 [![](image-images/image36a.png "ImageView en el cuadro de herramientas")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>Mostrar la imagen mediante programación

Dado que blocks.jpg se encuentra en la raíz del directorio de recursos estará disponible en tiempo de ejecución en la raíz de la agrupación de aplicaciones. Para mostrar esta imagen en un ImageView control utilice el código siguiente:

```csharp
imageview1.Image = UIImage.FromBundle ("SF Monkey.png");
```

Si se hubiera colocado la imagen en `/Resources/Pics/blocks.jpg` , a continuación, el código podría incluir la carpeta de selecciones en la ruta de acceso:

```csharp
imageview1.Image = UIImage.FromBundle ("Pics/SF Monkey.png");
```

Archivo de recursos hace referencia nunca necesario incluir el `Resources` carpeta.


## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
