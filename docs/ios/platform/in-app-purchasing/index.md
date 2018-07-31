---
title: En la aplicación de compras en Xamarin.iOS
description: Este documento describe cómo vender productos digitales y servicios mediante las APIs StoreKit. Incluye vínculos a guías que describen la configuración, productos consumibles, productos no consumibles, transacciones, suscripciones y mucho más.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 102ff2f11cc2f3d536e3ce9dd595a881f370f764
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351426"
---
# <a name="in-app-purchasing-in-xamarinios"></a>En la aplicación de compras en Xamarin.iOS

las aplicaciones de iOS pueden vender productos digitales o servicios que usan StoreKit: un conjunto de API proporcionados por iOS que se comunican con servidores de Apple para llevar a cabo transacciones financieras con un usuario a través de su identificador de Apple. Las APIs StoreKit son principalmente los relacionados con la recuperación de información de producto y llevar a cabo las transacciones: no hay ningún componente de interfaz de usuario. Las aplicaciones que implementan la compra de la aplicación deben crear su propia interfaz de usuario y realizar un seguimiento de los productos adquiridos con código personalizado para proporcionar servicios de los productos necesarios para el usuario.

Proporcionar una funcionalidad de compra en la aplicación requiere una serie de pasos:

-  **Configuración de la aplicación** : perfil de aprovisionamiento de la aplicación debe estar configurados correctamente.
-  **Creación de productos** : descripciones de productos y los precios que deben crearse en el portal de iTunes Connect.
-  **Implementar StoreKit** : se debe implementar la API de StoreKit según los tipos de productos vendidos.
-  **Creación de la interfaz de usuario y los productos propios** : se deben implementar los productos, incluidos los mecanismos para realizar un seguimiento de cada compra y copia de seguridad/restauración ellos si procede.
-  **Supervisión de ventas y la recepción de los fondos** : utilice la información proporcionada por iTunes Connect para supervisar las tendencias de ventas y realizar un seguimiento de sus ingresos.

Este documento explica cómo completar todos estos pasos para proporcionar en la aplicación adquiere con Xamarin.iOS.

## <a name="requirements"></a>Requisitos

Para admitir la compra de la aplicación debe usar Xamarin.iOS 5.0 o posterior con Xcode 7 y versiones posteriores.

## <a name="contents"></a>Contenido

 * [Configuración y conceptos básicos de las compras desde la aplicación](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

 * [Información general de StoreKit y al recuperar la información de producto](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

 * [Compra de productos consumibles](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

 * [Compra de productos no consumibles](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

 * [Transacciones y comprobación](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

 * [Suscripciones e informes](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>Resumen

En este artículo tiene introdujo el concepto de la compra de la aplicación, se describe cómo configurar la aplicación para aprovechar sus ventajas y presenta ejemplos de uso de Xamarin.iOS. Ha cubierto:

-  **Portal de aprovisionamiento de iOS** : funcionalidad de la compra de directrices para habilitar en la aplicación.
-  **iTunes Connect** : configurar los productos para vender en la aplicación.
-  **Store Kit** : explicación de las clases utilizadas para crear características de compra en la aplicación.
-  **Codificación de la aplicación para la compra** : ejemplos de cómo compilar la compra en la aplicación en una aplicación de Xamarin.iOS.
-  **Reporting** : información general de las estadísticas disponibles a través de iTunes Connect.


## <a name="related-links"></a>Vínculos relacionados

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [En la Guía de programación de compras desde la aplicación](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Guía para desarrolladores de iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Referencia de Framework Kit Store](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificadores de producto de compra en la aplicación preguntas y respuestas](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Nota técnica de compra en la aplicación](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [El envío del primer App Store](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Centro de recursos de App Store](https://developer.apple.com/appstore/index.html)
- [Sugerencias de envío al App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Administrar las aplicaciones](https://developer.apple.com/appstore/resources/managing/index.html)
