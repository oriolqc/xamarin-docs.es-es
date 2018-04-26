---
title: Microsoft Azure
description: Descargas de documentación y código de ejemplo para Azure.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: ab449a58cc87699b97a1ade7721a08f771c4f55d
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="microsoft-azure"></a>Microsoft Azure

_Descargas de documentación y código de ejemplo para Azure._

[ ![](images/evolve-mikej-azure-sml.png "Características de servicios de aplicaciones de Azure son fáciles de agregar a aplicaciones de Xamarin, incluido el almacenamiento de datos en la nube y las notificaciones de inserción entre plataformas")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolucionar 2016: Desarrollo de aplicaciones conectadas con Azure y Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Servicios en Visual Studio conectados para Mac

El nuevo [servicios conectados](connected-services.md) característica de Visual Studio para Mac ayuda a los desarrolladores para agregar funcionalidad de Azure rápida y fácilmente a las aplicaciones móviles desde el IDE. Actualmente disponible para realizar pruebas en el canal alfa.


## <a name="azure-app-services"></a>Servicios de aplicaciones de Azure

Hay una colección de [documentación de aplicaciones móviles de Azure](~/cross-platform/data-cloud/mobile-apps.md) que le guía a través del proceso de implementación de la [Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin también ofrece un NuGet de mensajería de Azure se empaqueta para su [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) y [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) para ayudar a implementar las notificaciones de inserción entre plataformas.

Configurar las aplicaciones en el [portal de servicios de aplicación de Azure](https://portal.azure.com/) para tener acceso a aplicaciones móviles, las API Web, almacenamiento y mucho más. Obtenga información acerca de [cómo los servicios de aplicación son diferentes](http://azure.microsoft.com/updates/whats-new-with-azure-app-service/) y ver en [en estos vídeos de Microsoft](http://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticación de Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) puede usarse para los usuarios de inicio de sesión en aplicaciones de Xamarin a través de la [Xamarin.Auth componente](https://www.nuget.org/packages/Xamarin.Auth/).
Las aplicaciones, a continuación, pueden tener acceso a servicios adicionales, como Office 365.

## <a name="webapi"></a>WebAPI

API de Web de Microsoft expone una interfaz REST que puede consumir fácilmente las aplicaciones de Xamarin.
Puede fácilmente número telefónico un [sitio Web de Azure](https://trywebsites.azurewebsites.net/) y compilar una aplicación basada en WebAPI para conectarse a aplicaciones de Xamarin.


###  <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Introducción a servicios Web](~/cross-platform/data-cloud/web-services/index.md)

Este tutorial presenta cómo integrar REST, SOAP y WCF web tecnologías de servicios con aplicaciones móviles de Xamarin. Examina varias implementaciones de servicio, se evalúa como herramientas disponibles y las bibliotecas para integrarlos y proporciona patrones de ejemplo para consumir datos de servicio. Por último, proporciona una introducción básica a la creación de un servicio web de REST para su uso con una aplicación móvil de Xamarin.

## <a name="samples"></a>Muestras

Además el [ejemplos de documentación](https://github.com/xamarin/mobile-samples/tree/master/Azure), las aplicaciones completas siguientes muestran diversas características de Azure incorporadas a aplicaciones de Xamarin:

- [Sport](https://github.com/xamarin/Sport) : aplicación de seguimiento de descriptivo de liga deportiva que usa las notificaciones de inserción y de almacenamiento de datos.
- [Momentos](https://github.com/pierceboggan/Moments) : fotografía instantánea de uso compartido que utiliza el almacenamiento de Azure para las imágenes.
- [Xamarin CRM](https://github.com/xamarin/app-crm) – usa Web API para el back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -aplicaciones móviles de Azure.

- [Compras](https://github.com/dotnet-architecture/eShopOnContainers) : muestras para la [serie de arquitectura](https://www.microsoft.com/net/learn/architecture) de libros electrónicos.
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Azure + IoT muestra de 2016 de compilación.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Azure PCL (por @paulbatum) (ejemplo)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Portal de Azure](http://azure.microsoft.com/)
- [Cliente móvil para Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
