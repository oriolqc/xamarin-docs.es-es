---
title: Imágenes e iconos en Xamarin.iOS
description: Esta sección incluye una serie de artículos que tratan trabajar con imágenes en una aplicación Xamarin.iOS, como el uso de ellos como iconos, abrir pantallas o incluyendo ellos en proporcionar iconos para tipos de documento personalizadas y los controles.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 444e3cbd88dfd3ff50153d858367ecd0310d240a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784057"
---
# <a name="images-and-icons-in-xamarinios"></a>Imágenes e iconos en Xamarin.iOS

_Esta sección incluye una serie de artículos que tratan trabajar con imágenes en una aplicación Xamarin.iOS, como el uso de ellos como iconos, abrir pantallas o incluyendo ellos en proporcionar iconos para tipos de documento personalizadas y los controles._

Hay varias maneras de esa imagen activos se utilizan dentro de una aplicación de iOS. Simplemente muestre una imagen como parte de la interfaz de usuario de una aplicación, asignarlo a un control de interfaz de usuario como un `UIButton` o `UIImageView`, para proporcionar iconos y pantallas de inicio, Xamarin.iOS resulta muy sencillo agregar excelente material gráfico a una aplicación de iOS de las maneras siguientes: 

- **Imágenes independientes de resolución** : usar la compatibilidad integrada de iOS para trabajar con imágenes de las soluciones de diferentes dispositivos y tipos (iPhone, iPad, etcetera).
- **Conjuntos de imágenes del catálogo de Asset** -uso **conjuntos de imágenes del catálogo de Asset** para administrar y agrupar todas las versiones de un recurso de imagen determinada requerido por una aplicación.
- **Imágenes en el Diseñador de iOS** -Use el Diseñador de iOS para establecer imágenes para los controles.
- **Imágenes en el código** : Use la `UIImage` métodos de la clase para cargar y trabajar con activos de imagen y asignarlas a los controles de interfaz de usuario en código C#.
- **Icono de la aplicación** -definir el icono de aplicación requerido por cada aplicación de iOS. Éste es el icono que se van a puntear el usuario desde la pantalla principal de iOS para iniciar la aplicación. Además, se usa este icono por centro de juegos, si procede.
- **Noticias destacadas icono** -definir el icono de servicios de la aplicación. Cada vez que el usuario escribe el nombre de una aplicación en una búsqueda de Spotlight, se muestra este icono.
- **Icono de configuración** -definir la aplicación **configuración** icono. Si el usuario escribe la **configuración** aplicación en su dispositivo iOS, este icono se mostrará al final de la lista de valores de la aplicación. 
- **Abrir pantallas** -definir la pantalla de inicio de la aplicación. Después de que el usuario puntea el icono de la aplicación y antes de que aparezca la primera vista, se mostrará una pantalla en blanco. Afortunadamente, iOS incluye compatibilidad para mostrar una imagen en lugar de la pantalla en blanco mediante el uso de un guión gráfico. 
- **Icono de iTunes** -proporcionan un icono de iTune. Si usa el método Ad-Hoc de la entrega de una aplicación (ya sea para los usuarios corporativos o para pruebas beta en dispositivos reales), el desarrollador también debe incluir un 512 x 512 y una imagen de 1024 x 1024 que se usará para representar la aplicación en iTunes.
- **Iconos de documento** -utilizar una imagen como un icono para cualquier tipo de documento específico que una aplicación de Xamarin.iOS admite o se crea.

Hay varias consideraciones que deben tenerse en cuenta al crear activos de imagen de una aplicación de iOS, así como varios lugares donde se usará esos recursos. Cada uno de ellos tiene un efecto no solo en el número de activos de imagen será necesarios, sino cómo se crean esos recursos. Los siguientes temas describen los tipos de recursos de imágenes que será necesarios, cómo esos recursos se incluyen en el paquete de la aplicación y cómo se usan los activos de imagen para proporcionar la funcionalidad necesaria:


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[Visualización de una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

Este artículo se tratan incluido un recurso de imagen en una aplicación de Xamarin.iOS y mostrar esa imagen mediante el uso de código de C# o mediante la asignación a un control en el Diseñador de iOS.

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[Iconos de aplicación](~/ios/app-fundamentals/images-icons/app-icons.md)

Esta artículo cubre incluidos y administrar un recurso de imagen en una aplicación Xamarin.iOS para usarse como un icono de la aplicación.

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[Iconos de aplicación alternativos](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple ha agregado varias mejoras para iOS 10.3 que permiten a una aplicación administrar su icono:

 - `ApplicationIconBadgeNumber` -Obtiene o establece el identificador del icono de aplicación en el Springboard.
 - `SupportsAlternateIcons` -If `true` la aplicación tiene un conjunto alternativo de iconos.
 - `AlternateIconName` -Devuelve el nombre del icono alternativo seleccionado actualmente o `null` si utilizando el icono principal.
 - `SetAlternameIconName` : Utilice este método para cambiar el icono de la aplicación al icono alternativo determinado.


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[Pantallas de inicio](~/ios/app-fundamentals/images-icons/launch-screens.md)

Este artículo incluye el uso de un tipo especial de guión gráfico para proporcionar una pantalla de inicio universal para cada tamaño de dispositivo de iOS y la resolución.

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[Tipos de documentos personalizados](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Esta artículo cubre incluidos y administrar un recurso de imagen en una aplicación Xamarin.iOS para usarse como un icono de tipo de documento personalizado.



## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icono personalizado y directrices para la creación de imágenes](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
