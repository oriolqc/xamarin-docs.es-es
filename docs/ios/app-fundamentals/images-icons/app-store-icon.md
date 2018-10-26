---
title: Iconos de la App Store de Xamarin.iOS
description: Este documento describe cómo usar los catálogos de recursos para administrar un icono de App Store para una aplicación de Xamarin.iOS. Anteriormente, los iconos de App Store se administraban con iTunes Connect.
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/26/2017
ms.openlocfilehash: 53e25ae9f4650254f2aaaa03dc8727fae674c9f0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105888"
---
# <a name="app-store-icons-in-xamarinios"></a>Iconos de la App Store de Xamarin.iOS

Antes de Xcode 9 se agregaron todos los iconos de App Store a través de iTunes Connect. Sin embargo, esto ya no es el caso. Iconos de App Store ahora deben incluirse como parte de su paquete del proyecto y agrega dentro de un catálogo de recursos. Apple rechazará las aplicaciones que no tienen un icono de App Store.

El icono de App Store es la cara de la aplicación a los usuarios, por lo que debe ser fácil de recordar y mostrarse correctamente en un tamaño pequeño. Los iconos fáciles de recordar se reconocen inmediatamente, y son simples y limpios.

Apple sugiere las siguientes directrices al diseñar el icono de la aplicación:

- Haga que el icono sea adecuado para la aplicación.
- Cree un icono que sea coherente con el diseño de la aplicación.
- Evite usar palabras en su icono.
- Piense de manera global: se usa un único icono de aplicación en todos los territorios de la tienda.

Se precisa una imagen de 1024 x 1024 píxeles para el icono de la aplicación que se mostrará en la App Store.  Apple ha manifestado que el icono de App Store del catálogo de recursos no puede ser transparente ni contener un canal alfa.

Para obtener más información, consulte Apple [directrices de interfaz humana de iOS](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/).

## <a name="adding-an-app-store-icon"></a>Agregar un icono de App Store

Los iconos de Application Store ahora deben entregarse en un catálogo de recursos. 

Para agregar un icono de App Store realice lo siguiente:

1. Busque el **AppIcon** conjunto de imágenes del **Assets.xcassets** archivo del proyecto. 
    - Todos los proyectos nuevos deben llegar con un una **Assets.xcassets** archivo que contiene un conjunto de imágenes AppIcon.
    - Para agregar un nuevo catálogo, haga doble clic en el proyecto y seleccione **Agregar > nuevo archivo > catálogo de activos**.
    - Para agregar un nuevo un conjunto de imágenes de icono de aplicación, haga clic en el área de conjunto de iconos y seleccione **iconos de aplicación e imágenes de Inicio > nuevo icono de aplicación**:
    
    ![Agregar nueva opción de conjunto de imágenes](app-store-icon-images/image1.png)

2. Desplácese hasta la **App Store** icono en la lista:

    ![Icono de App Store](app-store-icon-images/image2.png)

3. Haga clic en el icono y busque la imagen de 1024 x 1024 píxeles. Guarde el catálogo de activos.




## <a name="related-links"></a>Vínculos relacionados

- [Administrar iconos con catálogos de activos](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
