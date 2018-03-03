---
title: "Aplicaciones móviles de Microsoft Azure"
description: "Ejemplos y código descarga para obtener la documentación de portal de Azure."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: fb3c26b7d090ca42328c61192c794dec1544d1d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="microsoft-azure-mobile-apps"></a>Aplicaciones móviles de Microsoft Azure

_Ejemplos y código descarga para obtener la documentación de portal de Azure._

<!--
NOTE TO AUTHORS: this page is referenced from
http://azure.microsoft.com/en-us/develop/mobile/xamarin/
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


Estos vínculos son para la documentación de Xamarin disponible en la [aplicaciones móviles de Azure](https://azure.microsoft.com/en-us/documentation/services/app-service/mobile/) sitio Web.
Agregar funcionalidad de Azure a un Xamarin aplicación es tan simple como descargar el [Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Trabajar con el componente de Azure de Xamarin

Documentación general [trabajar con la biblioteca de cliente de Xamarin (componente)](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/) para realizar diversas tareas con aplicaciones móviles de Azure. Esta página contiene una gran cantidad de fragmentos de código de ejemplo, sin las explicaciones detalladas y ejemplos disponibles en cada uno de los artículos de tutorial que se enumeran a continuación.

## <a name="getting-started"></a>Introducción

Este artículo proporciona instrucciones paso a paso para poner en funcionamiento la primera aplicación de Xamarin Azure.
Incluye crear una nueva aplicación móvil de Azure en el portal y, a continuación, descargar y ejecutar la aplicación configurada previamente.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started/)
-  [Xamarin.Forms](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-forms-get-started/)

## <a name="validate-modify-and-augment-data-in-scripts"></a>Validar, modificar y aumentar los datos en secuencias de comandos

Muestra cómo agregar secuencias de comandos de servidor a las tablas de datos de servicios móviles de Azure para implementar la validación del lado servidor y otras funciones.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)


## <a name="add-paging-to-data"></a>Agregar paginación a datos

Un ejemplo rápido de grandes conjuntos de datos mediante Skip() y Take() de paginación.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)


## <a name="get-started-with-users"></a>Introducción a los usuarios

Proporciona instrucciones completas para configurar y codificar una pantalla de inicio de sesión mediante servicios móviles de Azure. Proveedores de autenticación admitidos incluyen Microsoft, Google, Facebook y Twitter.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>Autorizar a los usuarios en las secuencias de comandos

Ejemplos de código para servidores backend de Javascript

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>Empezar a trabajar con inserción

Siga las instrucciones para configurar las notificaciones de inserción en los sitios Web de Apple y Google, a continuación, enviar una notificación de inserción de servicios móviles de Azure a un dispositivo.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started-push/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started-push/)


## <a name="get-started-with-notification-hubs"></a>Introducción a los centros de notificaciones

Complete las instrucciones para configurar las notificaciones de inserción en los sitios Web de Apple y Google, el centro de notificaciones de Azure y, a continuación, generar notificaciones de inserción a dispositivos.

-  [iOS](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/)
-  [Android](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/)



## <a name="related-links"></a>Vínculos relacionados

- [GettingStarted (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
- [NotificationHubs (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Ejemplo de Azure PCL (por @paulbatum) (ejemplo)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Cliente móvil de Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Ruta de acceso de aprendizaje de aplicaciones móviles de Azure](https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/)
