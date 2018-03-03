---
title: "Compra desde la aplicación"
description: "aplicaciones de iOS pueden vender productos digitales y servicios mediante las API de Kit de almacén. Productos crean y administran en el portal de iTunes Connect. Apple administra el procesamiento de transacciones y aprueba todos los productos antes de que se pueden vender y una tarifa por cada transacción (actualmente 30%). Apple requiere que use la compra en la aplicación de las ventas digitales en la aplicación, pero no puede usar para la venta de mercancías físicas o servicios digitales no. Aplicaciones que ofrecen opciones de pago alternativo de servicios y productos digitales están probables que se rechacen. Este documento explica cómo configurar la aplicación para usar el Kit de almacén y proporciona ejemplos de Xamarin.iOS de los escenarios de compras en la aplicación más comunes."
ms.topic: article
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: af8eb556215679bab2da8f54e8231f7d7d3ed418
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="in-app-purchasing"></a>Compra desde la aplicación

_aplicaciones de iOS pueden vender productos digitales y servicios mediante las API de Kit de almacén. Productos crean y administran en el portal de iTunes Connect. Apple administra el procesamiento de transacciones y aprueba todos los productos antes de que se pueden vender y una tarifa por cada transacción (actualmente 30%). Apple requiere que use la compra en la aplicación de las ventas digitales en la aplicación, pero no puede usar para la venta de mercancías físicas o servicios digitales no. Aplicaciones que ofrecen opciones de pago alternativo de servicios y productos digitales están probables que se rechacen. Este documento explica cómo configurar la aplicación para usar el Kit de almacén y proporciona ejemplos de Xamarin.iOS de los escenarios de compras en la aplicación más comunes._


pueden vender aplicaciones de iOS digitales productos o servicios mediante StoreKit: un conjunto de las API proporcionadas por iOS que se comunican con servidores de Apple para realizar transacciones financieras con el usuario mediante su identificador de Apple. Las APIs StoreKit se ocupa principalmente de recuperar información de productos y llevar a cabo las transacciones: no hay ningún componente de interfaz de usuario. Las aplicaciones que implementan la compra de la aplicación deben crear su propia interfaz de usuario y realizar un seguimiento de los productos adquiridos con código personalizado para proporcionar los servicios o productos necesarios para el usuario.

Proporcionar una funcionalidad de compra en la aplicación requiere una serie de pasos:

-  **Configuración de la aplicación** : perfil de aprovisionamiento de la aplicación debe configurarse correctamente.
-  **Crear productos** : descripciones de productos y los precios se deben crear en el portal de iTunes Connect.
-  **Implementar StoreKit** : se debe implementar la API de StoreKit según los tipos de productos vendidos.
-  **Creación de la interfaz de usuario y los productos por sí mismos** : se deben implementar los productos, incluidos los mecanismos para realizar un seguimiento de cada compra y copia de seguridad/restauración ellos si es necesario.
-  **Supervisión de ventas y la recepción de los fondos** : utilice la información proporcionada por iTunes Connect para supervisar las tendencias de ventas y realizar un seguimiento de los ingresos.


Este documento explica cómo completar todos estos pasos para proporcionar en la aplicación de compras con Xamarin.iOS.


## <a name="requirements"></a>Requisitos

Para admitir la compra de la aplicación debe utilizar Xamarin.iOS 5.0 o posterior con Xcode 7 y versiones posteriores.

## <a name="contents"></a>Contenido

 * [Conceptos básicos de compra en la aplicación y configuración](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

 * [Almacenar la información de producto de recuperar e Introducción al Kit de](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

 * [Compra de productos consumibles](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

 * [Compra de productos no consumible](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

 * [Las transacciones y comprobación](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

 * [Las suscripciones e informes](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)


## <a name="summary"></a>Resumen

Este artículo tiene introdujo el concepto de compra de la aplicación, se describe cómo configurar la aplicación para aprovechar las ventajas del mismo y presenta ejemplos que utilizan Xamarin.iOS. Ha cubierto:

-  **Portal de aprovisionamiento de iOS** : instrucciones para habilitar la aplicación de compra funcionalidad.
-  **iTunes Connect** : configuración de productos para vender en la aplicación.
-  **Almacenar Kit** : explicación de las clases utilizadas para crear características de compra en la aplicación.
-  **La aplicación para las compras de codificación** : ejemplos de cómo crear en la aplicación de compra en una aplicación de Xamarin.iOS.
-  **Reporting** : información general sobre las estadísticas disponibles a través de iTunes Connect.


## <a name="related-links"></a>Vínculos relacionados

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [En la Guía de programación de compras desde la aplicación](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Guía del desarrollador de Connect de iTunes](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Referencia de marco de trabajo Kit de almacén](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificadores de producto de compra en la aplicación de preguntas y respuestas](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Nota técnica de compra en la aplicación](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [El envío del primer tienda de aplicaciones](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Centro de recursos de almacén de aplicación](https://developer.apple.com/appstore/index.html)
- [Sugerencias sobre cómo presentar tienda de aplicaciones](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Directrices de revisión de la tienda de aplicaciones](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Administrar las aplicaciones](https://developer.apple.com/appstore/resources/managing/index.html)
