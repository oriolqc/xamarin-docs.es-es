---
title: Microsoft Azure y Xamarin
description: Este documento incluye vínculos a documentación sobre servicios conectados en Visual Studio para Mac, Azure Mobile Apps, autenticación de Active Directory y API Web.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: 723970a4ad7e2ced85147dbcc6c22f9a45519121
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864603"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure y Xamarin

[![](images/evolve-mikej-azure-sml.png "Características de Azure App Services son fáciles de agregar a las aplicaciones de Xamarin, incluido el almacenamiento de datos en la nube y las notificaciones de inserción multiplataforma")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016: Desarrollo de aplicaciones conectadas con Azure y Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Servicios conectados en Visual Studio para Mac

El nuevo [Connected Services](connected-services.md) característica de Visual Studio para Mac ayuda a los desarrolladores agregar funcionalidad de Azure rápida y fácilmente aplicaciones para dispositivos móviles desde el IDE. Actualmente disponible para las pruebas en el canal alfa.

## <a name="azure-app-services"></a>Servicios de aplicación de Azure

Hay una colección de [documentación de Azure Mobile Apps](~/cross-platform/data-cloud/mobile-apps.md) que le guiará a través del proceso de implementación de la [Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin ofrece también un NuGet de mensajería de Azure se empaqueta para su [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) y [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) para ayudar a implementar notificaciones de inserción entre plataformas.

Configurar las aplicaciones en la [portal de Azure App Services](https://portal.azure.com/) para tener acceso a Mobile Apps, API Web, almacenamiento y mucho más. Obtenga información sobre [cómo los servicios de aplicación son diferentes](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) y eche un vistazo en [estos vídeos de Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticación de Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) puede usarse para los usuarios de inicio de sesión en aplicaciones de Xamarin a través de la [componente Xamarin.Auth](https://www.nuget.org/packages/Xamarin.Auth/).
Las aplicaciones, a continuación, pueden tener acceso a servicios adicionales, como Office 365.

## <a name="webapi"></a>WebAPI

API de Web de Microsoft expone una interfaz similar a REST que puede utilizarse fácilmente en las aplicaciones de Xamarin.
Puede fácilmente número vertical un [sitio Web de Azure](https://trywebsites.azurewebsites.net/) y compile una aplicación basada en WebAPI para conectarse a las aplicaciones de Xamarin.


### <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Introducción a servicios Web](~/cross-platform/data-cloud/web-services/index.md)

En este tutorial se explica cómo integrar REST, las tecnologías de servicios con aplicaciones móviles de Xamarin de web de WCF y SOAP. Examina las diversas implementaciones de servicio, se evalúa como las herramientas disponibles y las bibliotecas para integrarlos y proporciona modelos de ejemplo para consumir datos de servicio. Por último, proporciona una introducción básica a la creación de un servicio web RESTful para su uso con aplicaciones móviles de Xamarin.

## <a name="samples"></a>Ejemplos

Además el [ejemplos de documentación](https://github.com/xamarin/mobile-samples/tree/master/Azure), las aplicaciones completas siguientes muestran diversas características de Azure incorporadas en las aplicaciones de Xamarin:

- [Sport](https://github.com/xamarin/Sport) : aplicación de seguimiento sports-liga descriptivo que usa las notificaciones de inserción y de almacenamiento de datos.
- [Momentos](https://github.com/pierceboggan/Moments) : instantánea foto compartida que usa Azure Storage para las imágenes.
- [Xamarin CRM](https://github.com/xamarin/app-crm) – usa Web API para el back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -aplicaciones móviles de Azure.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) : ejemplo de la [serie arquitectura](https://www.microsoft.com/net/learn/architecture) de libros electrónicos.
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Azure + IoT de ejemplo de Build 2016.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de PCL de Azure (por @paulbatum) (ejemplo)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure Portal](https://azure.microsoft.com/)
- [Cliente móvil de Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
