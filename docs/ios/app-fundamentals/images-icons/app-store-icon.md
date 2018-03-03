---
title: Icono de la tienda de aplicaciones
description: "Esta artículo cubre incluidos y administrar un recurso de imagen en una aplicación Xamarin.iOS para usarse como un icono de almacén de la aplicación."
ms.topic: article
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/26/2017
ms.openlocfilehash: 6ec4328f08859c5331a6250bf44ee705a7fd0744
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="app-store-icon"></a>Icono de la tienda de aplicaciones

_Esta artículo cubre incluidos y administrar un recurso de imagen en una aplicación Xamarin.iOS para usarse como un icono de almacén de la aplicación._

Antes de Xcode 9 se han agregado todos los iconos de la tienda de aplicaciones a través de iTunes Connect. Sin embargo, esto ya no es el caso. Iconos de la tienda de aplicaciones deben se incluye como parte de la agrupación de proyecto y agrega dentro de un catálogo de activos. Se rechazarán las aplicaciones que no contienen un icono de la tienda de aplicaciones por Apple.

El icono de almacén de aplicación es la cara de la aplicación a los usuarios, por lo que debe ser fácil de recordar y mostrar correctamente en un tamaño pequeño. Los iconos fáciles de recordar se reconocen inmediatamente, y son simples y limpios.

Apple sugiere las siguientes directrices al diseñar el icono de la aplicación:

- Haga que el icono sea adecuado para la aplicación.
- Cree un icono que sea coherente con el diseño de la aplicación.
- Evite usar palabras en su icono.
- Piense de manera global: se usa un único icono de aplicación en todos los territorios de la tienda.

Se precisa una imagen de 1024 x 1024 píxeles para el icono de la aplicación que se mostrará en la App Store.  Apple ha manifestado que el icono de App Store del catálogo de recursos no puede ser transparente ni contener un canal alfa.

Para obtener más información, consulte de Apple [iOS directrices de interfaz humana](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/).

## <a name="adding-an-app-store-icon"></a>Agregar un icono de la tienda de aplicaciones

Los iconos de Application Store ahora deben entregarse en un catálogo de recursos. 

Para agregar un icono de la tienda de aplicaciones hacer lo siguiente:

1. Busque la **AppIcon** imagen configurada el **Assets.xcassets** archivo del proyecto. 
    - Todos los proyectos nuevos deben llegar con un un **Assets.xcassets** archivo que contiene un conjunto de imágenes AppIcon.
    - Para agregar un nuevo catálogo de activos, haga doble clic en el proyecto y seleccione **Agregar > nuevo archivo > catálogo de activos**.
    - Para agregar un nuevo un conjunto de imágenes de icono de aplicación, pulse el botón derecho en el área de conjunto de iconos y seleccione **iconos de aplicación e iniciar imágenes > nuevo icono de la aplicación**:
    
    ![Agregar nueva opción de conjunto de imágenes](app-store-icon-images/image1.png)

2. Desplácese hasta la **App Store** icono de la lista:

    ![Icono de la tienda de aplicaciones](app-store-icon-images/image2.png)

3. Haga clic en el icono y busque la imagen de píxel de 1024 x 1024. Guarde el catálogo de activos.




## <a name="related-links"></a>Vínculos relacionados

- [Administración de iconos con catálogos de activos](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
