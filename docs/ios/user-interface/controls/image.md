---
title: Mostrar imágenes con Xamarin.iOS
description: Este documento describe cómo mostrar imágenes en Xamarin.iOS. Incluye agregar imágenes a una aplicación mediante programación o a través del Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: bb57dbdb87555fb0b3ab8941024e5a84e4723099
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112642"
---
# <a name="displaying-images-with-xamarinios"></a>Mostrar imágenes con Xamarin.iOS

Agregar imágenes a la aplicación requiere dos pasos: en primer lugar, agregue las imágenes al proyecto; a continuación, agregar controles y código para mostrarlos en una pantalla. Hacer referencia a la [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) artículo para obtener más cobertura de Xamarin.iOS de control de imagen.

## <a name="adding-images-to-your-app"></a>Agregar imágenes a la aplicación

Las imágenes se pueden agregar a cualquier carpeta en Visual Studio para la solución de Mac y si el **acción de compilación** está establecido en **contenido** , a continuación, el archivo se incluirá con la aplicación y se pueden mostrar.

Visual Studio para Mac también es compatible con un directorio especial llamado **recursos** que también pueden contener archivos de imagen. Archivos en la carpeta de recursos deben tener el **acción de compilación** establecido en **BundleResource**.

Esta captura de pantalla muestra la **acción de compilación** opciones que aparecen cuando un archivo que se hace:

 [![](image-images/image30a.png "Menú Acción de compilación")](image-images/image30a.png#lightbox)

Normalmente, Visual Studio para Mac elegirá el valor correcto **acción de compilación** automáticamente, pero debe ser consciente de estas opciones, especialmente si mover los archivos en el proyecto.

### <a name="adding-an-image-file"></a>Agregar un archivo de imagen

Para agregar un archivo de imagen al proyecto, haga clic en el proyecto y elija **agregar archivos...**

 [![](image-images/image31a.png "Agregar archivos... menú")](image-images/image31a.png#lightbox)

Seleccione la imagen (o imágenes) que desea incluir en el cuadro de diálogo de archivo estándar. El valor predeterminado la acción de compilación para las imágenes serán **BundleResource** : no se invalidan este valor a menos que tenga un motivo concreto.

 [![](image-images/image32a.png "Agregar cuadro de diálogo de archivos")](image-images/image32a.png#lightbox)

La imagen se agregará al proyecto y disponible para cargarse y mostrarse en el código. Esta captura de pantalla muestra una imagen que se agregan a un proyecto de aplicación de iOS:

 [![](image-images/image33a.png "Imagen en el proyecto")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>¿Qué es el directorio de recursos?

Archivos ubicados en el **recursos** directory reciben un tratamiento diferente de archivos normales: el contenido de la **recursos** carpeta se copian en la raíz de la aplicación y se puede hacer referencia a partir de ahí en el código. Esto puede ser útil por diversos motivos:

-  Almacenar las imágenes configuradas en las propiedades de la aplicación, como los iconos de aplicación y las imágenes de inicio predeterminado.
-  Almacenar otros archivos e imágenes por separado desde el código, por lo que son más fáciles de administrar (subdirectorios se conservan cuando se copien el contenido del directorio de recursos).


El **recursos** directorio es especialmente útil en un proyecto de biblioteca, ya que el código puede suponer que esas imágenes se copiará en la raíz de la aplicación consumidora, lo que facilita a las bibliotecas de código compartido de escritura que requieren imagen, sonido, vídeo, XML u otros archivos.

El **recursos** directorio debe denominarse por lo tanto, y todos los archivos deben tener la acción de compilación establecida en **BundleResource**.

## <a name="displaying-the-image"></a>Mostrar la imagen

En el Diseñador de iOS, utilice un **vista de imagen** para mostrar una imagen o animado serie de imágenes. El **vista de imagen** icono del cuadro de herramientas se muestra a continuación:

 [![](image-images/image35a.png "ImageView en el cuadro de herramientas")](image-images/image35.png#lightbox)

Arrastre el **vista de imagen** desde el **cuadro de herramientas** en el controlador de vista. A continuación, en **imagen de vista > imagen** la lista desplegable proporcionará una lista de todos los archivos de imagen disponible en el proyecto. Seleccione cualquiera de ellos para agregarlo a la vista de imagen.

 [![](image-images/image36a.png "ImageView en el cuadro de herramientas")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>Mostrar la imagen mediante programación

Dado que **SF Monkey.jpg** se encuentra en la raíz de la **recursos** directory estará disponible en tiempo de ejecución en la raíz del paquete de aplicación. Para mostrar esta imagen en un control de vista de imagen, use el código siguiente:

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

Si nos hubiéramos coloca la imagen en **Pics/recursos/SF Monkey.jpg**, a continuación, se incluiría el código la **Pics** carpeta en la ruta de acceso:

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

Archivo de recursos hace referencia a nunca es necesaria su inclusión el **recursos** carpeta.

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
