---
title: Aplicaciones móviles de Microsoft Azure
description: Vínculos de este documento a las guías que describen cómo compilar una aplicación de Xamarin que está conectada a Azure. Analiza el trabajo con el componente de Azure de Xamarin, los usuarios y las notificaciones de inserción.
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: baa687bfb3b2e8306e70e83b6a6ee54595110860
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780635"
---
# <a name="microsoft-azure-mobile-apps"></a>Aplicaciones móviles de Microsoft Azure

_Ejemplos y código descarga para obtener la documentación de portal de Azure._

<!--
NOTE TO AUTHORS: this page is referenced from
http://azure.microsoft.com/develop/mobile/xamarin/
as https://developer.xamarin.com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started  http://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data   http://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push   http://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication http://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs  http://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data    http://go.microsoft.com/fwlink/p/?LinkId=331330
-->


Estos vínculos son para la documentación de Xamarin disponible en la [aplicaciones móviles de Azure](https://docs.microsoft.com/azure/app-service-mobile/) sitio Web.
Agregar funcionalidad de Azure a una aplicación de Xamarin descargando el [Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Trabajar con el componente de Azure de Xamarin

Documentación general [trabajar con la biblioteca de cliente de Xamarin (componente)](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) para realizar diversas tareas con aplicaciones móviles de Azure. Esta página contiene una gran cantidad de fragmentos de código de ejemplo, sin las explicaciones detalladas y ejemplos disponibles en cada uno de los artículos de tutorial que se enumeran a continuación.

## <a name="getting-started"></a>Introducción

Este artículo proporciona instrucciones paso a paso para poner en funcionamiento la primera aplicación de Xamarin Azure.
Incluye crear una nueva aplicación móvil de Azure en el portal y, a continuación, descargar y ejecutar la aplicación configurada previamente.

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
-  [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>Introducción a los usuarios

Proporciona instrucciones completas para configurar y codificar una pantalla de inicio de sesión mediante servicios móviles de Azure. Proveedores de autenticación admitidos incluyen Microsoft, Google, Facebook y Twitter.

-  [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>Autorizar a los usuarios en las secuencias de comandos

Ejemplos de código para servidores backend de Javascript

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>Empezar a trabajar con inserción

Siga las instrucciones para configurar las notificaciones de inserción en los sitios Web de Apple y Google, a continuación, enviar una notificación de inserción de servicios móviles de Azure a un dispositivo.

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)


## <a name="get-started-with-notification-hubs"></a>Introducción a los centros de notificaciones

Complete las instrucciones para configurar las notificaciones de inserción en los sitios Web de Apple y Google, el centro de notificaciones de Azure y, a continuación, generar notificaciones de inserción a dispositivos.

-  [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
-  [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)



## <a name="related-links"></a>Vínculos relacionados

- [GettingStarted (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [NotificationHubs (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Cliente móvil de Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Ruta de acceso de aprendizaje de aplicaciones móviles de Azure](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
