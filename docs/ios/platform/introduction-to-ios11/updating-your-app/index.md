---
title: Actualización de la aplicación a iOS 11
description: Este documento incluye vínculos a diversas guías que describen las nuevas características disponibles para los desarrolladores de Xamarin.iOS con la versión de iOS 11. Por ejemplo, las actualizaciones de diseño visual, cambia de App Store y actualiza el icono de la aplicación.
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 3193f27c30080a4335abfe969acb3c8b33516469
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386356"
---
# <a name="updating-your-app-to-ios-11"></a>Actualización de la aplicación a iOS 11

En iOS 11, Apple ha introducido las actualizaciones de la arquitectura, nuevos cambios visuales y un proceso de actualizada de iTunes Connect. Esta guía analiza cada uno de estos cambios, lo que ayuda a obtener la aplicación de Xamarin.iOS que se actualizó con iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Cambios de arquitectura](architecture-changes.md)

Uno de los cambios más importantes que debe tener en cuenta con iOS 11 es la interrupción de la compatibilidad de 32 bits para las aplicaciones, como se detalla en [Apple](https://developer.apple.com/news/?id=06282017b) comunicado de prensa.

Esta guía le guiará en el proceso de actualización de la aplicación de 64 bits.

## <a name="visual-design-updatesvisual-designmd"></a>[Actualizaciones de diseño Visual](visual-design.md)

En iOS 11, Apple introdujo nuevos cambios visuales, incluidas las actualizaciones de la barra de navegación, barra de búsqueda y las vistas de tabla. Además se han realizado mejoras para permitir una mayor flexibilidad a través de los márgenes y el contenido de pantalla completa. Estos cambios se tratan en esta guía.

## <a name="app-store-changesapp-store-changesmd"></a>[Cambios en App Store](app-store-changes.md)

El App Store de iOS ha producido un diseño completamente nuevo, lo que no sólo permite a los usuarios navegar de forma eficaz el almacén, pero también le permite, los desarrolladores, promueva su aplicación a los usuarios. Estas promociones incluyen actualizaciones a las compras de la aplicación y a la página de producto. iOS 11 también agrega las actualizaciones con respecto a cómo comunicarse con los usuarios, cómo agregar el icono de la aplicación y cómo publicar la aplicación para el público.

## <a name="app-icon-updates"></a>Actualizaciones de iconos de aplicación

> [!NOTE]
> Iconos de aplicación ahora deben entregarse por un _catálogo de activos_. 

Para obtener información sobre el uso de catálogos de activos que hacen referencia a la [App Store icono](~/ios/app-fundamentals/images-icons/app-store-icon.md) guía. Para obtener ayuda con la migración de los iconos de un archivo Info.plist para un catálogo de recursos, consulte el [migración de Info.plist a catálogos de activos](~/ios/app-fundamentals/images-icons/app-icons.md) guía.

El icono requerido en el catálogo de recursos se denomina **App Store** y debe ser de tamaño de 1024 x 1024. Apple ha manifestado que el icono de App Store del catálogo de recursos no puede ser transparente ni contener un canal alfa.

![Ubicación del icono de la tienda de aplicaciones en el catálogo de activos](images/image1.png)

## <a name="related-links"></a>Vínculos relacionados

- [Novedades en iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto actualizada Store App (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualización de la aplicación para iOS 11 (sesión WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
