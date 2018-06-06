---
title: Iconos de la tienda de aplicaciones de Xamarin.iOS
description: Este documento describe cómo usar catálogos de activo para administrar un icono de la tienda de aplicaciones para una aplicación de Xamarin.iOS. Anteriormente, los iconos de la tienda de aplicaciones se administraban con iTunes Connect.
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/26/2017
ms.openlocfilehash: 749dbf01af382a54fe24652706f6a605ac7b20b4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783615"
---
# <a name="app-store-icons-in-xamarinios"></a>Iconos de la tienda de aplicaciones de Xamarin.iOS

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
