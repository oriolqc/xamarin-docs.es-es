---
title: Actualizar la aplicación para iOS 11
description: Explorar las nuevas características de iOS 11
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 2581f729d85787021763f50f005e84d6bbb5db01
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="updating-your-app-to-ios-11"></a>Actualizar la aplicación para iOS 11

_Explorar las nuevas características de iOS 11_

En iOS 11, Apple ha introducido actualizaciones de arquitectura, nuevos cambios visuales y un proceso de conectar iTunes actualizada. Esta guía analiza cada uno de estos cambios, como ayuda para obtener la aplicación de Xamarin.iOS actualizada para iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Cambios de arquitectura](architecture-changes.md)

Uno de los cambios más importantes que debe tener en cuenta con iOS 11 es el desuso de soporte de 32 bits para las aplicaciones, como se detalla en [de Apple](https://developer.apple.com/news/?id=06282017b) comunicado de prensa.

Esta guía le guiará en el proceso de actualización de la aplicación de 64 bits.

## <a name="visual-design-updatesvisual-designmd"></a>[Actualizaciones de diseño Visual](visual-design.md)

En iOS 11, Apple incorporó nuevos cambios visuales, incluidas las actualizaciones en la barra de navegación, barra de búsqueda y vistas de la tabla. Además, se realizaron mejoras para proporcionar mayor flexibilidad a través de los márgenes y el contenido de pantalla completa. Estos cambios se tratan en esta guía.

## <a name="app-store-changesapp-store-changesmd"></a>[Cambios en App Store](app-store-changes.md)

La App Store de iOS ha tenido un cambio de diseño completa, que no sólo permite a los usuarios desplazarse eficazmente por el almacén, sino que también le permite, como desarrollador, para promover la aplicación a los usuarios. Dichas promociones incluyen actualizaciones para las compras de la aplicación y a la página del producto. iOS 11 también agrega las actualizaciones relacionadas con cómo comunicarse con los usuarios, cómo agregar el icono de la aplicación y cómo publicar la aplicación para el público.

## <a name="app-icon-updates"></a>Actualizaciones de icono de la aplicación

> [!NOTE]
> Iconos de aplicación ahora deben entregarse por un _catálogo de activos_. 

Para obtener información sobre el uso de catálogos de activos que hacen referencia a la [icono de almacén de la aplicación](~/ios/app-fundamentals/images-icons/app-store-icon.md) guía. Para obtener ayuda con la migración de iconos desde un Info.plist para un catálogo de activos, consulte el [migración desde Info.plist a catálogos de activos](~/ios/app-fundamentals/images-icons/app-icons.md) guía.

El icono necesario en el catálogo de activos se denomina **App Store** y debe ser 1024 x 1024 de tamaño. Apple ha manifestado que el icono de App Store del catálogo de recursos no puede ser transparente ni contener un canal alfa.

![Ubicación del icono de la tienda de aplicaciones en el catálogo de activos](images/image1.png)

## <a name="related-links"></a>Vínculos relacionados

- [What's New en iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto actualizada tienda de aplicaciones (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualizar la aplicación para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
