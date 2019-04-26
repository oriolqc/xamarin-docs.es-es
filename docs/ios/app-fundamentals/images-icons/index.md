---
title: Imágenes e iconos en Xamarin.iOS
description: Esta sección incluye una variedad de artículos que tratan cómo trabajar con imágenes en una aplicación de Xamarin.iOS, como el uso de ellos como iconos, pantallas de inicio o incluido ellos en los controles y proporcionar iconos para tipos de documento personalizadas.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f359da6b8744e03cfcbd77d48f5f77f216e828f8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250656"
---
# <a name="images-and-icons-in-xamarinios"></a>Imágenes e iconos en Xamarin.iOS

_Esta sección incluye una variedad de artículos que tratan cómo trabajar con imágenes en una aplicación de Xamarin.iOS, como el uso de ellos como iconos, pantallas de inicio o incluido ellos en los controles y proporcionar iconos para tipos de documento personalizadas._

Hay varias maneras de esa imagen se usan recursos dentro de una aplicación de iOS. Simplemente se muestre una imagen como parte de la interfaz de usuario de una aplicación, asignarlo a un control de interfaz de usuario, como un `UIButton` o `UIImageView`, para proporcionar iconos y pantallas de inicio, Xamarin.iOS facilita agregar material gráfico excelente a una aplicación iOS de las maneras siguientes: 

- **Imágenes independientes de resolución** : usar la compatibilidad integrada de iOS para trabajar con imágenes en diferentes resoluciones de diferentes dispositivos y tipos (iPhone, iPad, etcetera.).
- **Conjuntos de imágenes del catálogo de activos** -uso **conjuntos de imágenes del catálogo de activos** para administrar y agrupar todas las versiones de un recurso de imagen especificado requerido por una aplicación.
- **Las imágenes en el Diseñador de iOS** -Use el Diseñador de iOS para establecer las imágenes para los controles.
- **Las imágenes en el código** : Use el `UIImage` métodos de la clase para cargar y trabajar con activos de imagen y asignarlas a los controles de interfaz de usuario de C# código.
- **Icono de la aplicación** -definir el icono de aplicación requerido por cada aplicación de iOS. Éste es el icono que aprovechará el usuario desde la pantalla principal de iOS para iniciar la aplicación. Además, este icono se utiliza por Game Center, si procede.
- **Icono de Spotlight** -definir el icono de contenido destacado de la aplicación. Cada vez que el usuario escribe el nombre de una aplicación en una búsqueda de Spotlight, este icono se muestra.
- **Icono de configuración** -definición de la aplicación **configuración** icono. Si el usuario escribe el **configuración** aplicación en su dispositivo iOS, este icono se mostrará al final de la lista de valores de la aplicación. 
- **Pantallas de inicio** -definir la pantalla de inicio de la aplicación. Después de que el usuario pulsa el icono de la aplicación y antes de que aparezca la primera vista, se mostrará una pantalla en blanco. Afortunadamente, iOS incluye compatibilidad para mostrar una imagen en lugar de la pantalla en blanco utilizando un guión gráfico. 
- **Icono de iTunes** -proporcionan un icono iTune. Si usa el método Ad Hoc de la entrega de una aplicación (ya sea para usuarios corporativos o para realizar pruebas beta en dispositivos reales), el desarrollador también debe incluir un 512 x 512 y una imagen de 1024 x 1024 que se usará para representar la aplicación en iTunes.
- **Iconos de documento** -usar una imagen como un icono para cualquier tipo de documento específico que admite o que crea una aplicación de Xamarin.iOS.

Hay varias consideraciones que deben tenerse en cuenta al crear activos de imagen de una aplicación de iOS, así como varios lugares donde se usará esos recursos. Cada uno de ellos tiene un efecto en no solo será necesarios cuántos activos de imagen, sino cómo se crean esos recursos. Los siguientes temas tratan los tipos de recursos de imágenes que será necesarios, cómo esos recursos se incluyen en el paquete de la aplicación y cómo se consumen los activos de imagen para proporcionar la funcionalidad necesaria:


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[Visualización de una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

En este artículo se trata incluido un recurso de imagen en una aplicación de Xamarin.iOS y mostrar esa imagen mediante el uso de código de C# o mediante la asignación a un control en el Diseñador de iOS.

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[Iconos de aplicación](~/ios/app-fundamentals/images-icons/app-icons.md)

Este plano de artículo incluido y administrar un recurso de imagen en una aplicación de Xamarin.iOS que se usará como icono de aplicación.

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[Iconos de aplicación alternativos](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple ha agregado varias mejoras a iOS 10.3 que permitan a una aplicación administrar su icono:

 - `ApplicationIconBadgeNumber` : Obtiene o establece el distintivo de icono de la aplicación en el Springboard.
 - `SupportsAlternateIcons` -If `true` la aplicación tiene un conjunto alternativo de iconos.
 - `AlternateIconName` -Devuelve el nombre del icono alternativo seleccionado actualmente o `null` si mediante el icono principal.
 - `SetAlternameIconName` : Use este método para cambiar el icono de la aplicación en el icono alternativo especificado.


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[Pantallas de inicio](~/ios/app-fundamentals/images-icons/launch-screens.md)

En este artículo se describe el uso de un tipo especial de guion gráfico para proporcionar una pantalla de inicio universal para cada tamaño de dispositivo de iOS y la resolución.

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[Tipos de documentos personalizados](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Este plano de artículo incluido y administrar un recurso de imagen en una aplicación de Xamarin.iOS que se usará como un icono de tipo de documento personalizado.



## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icono personalizado y directrices para la creación de imagen](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
