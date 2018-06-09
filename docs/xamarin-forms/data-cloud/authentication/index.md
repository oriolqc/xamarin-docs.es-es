---
title: Autenticar el acceso a servicios Web
description: Esta guía explica cómo integrar servicios de autenticación en una aplicación de Xamarin.Forms para que los usuarios puedan compartir un back-end al tiempo que sólo tiene acceso a sus propios datos.
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: d598a9b3de31ea6823530f911c3544bf3cebb37f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240692"
---
# <a name="authenticating-access-to-web-services"></a>Autenticar el acceso a servicios Web

_Esta guía explica cómo integrar servicios de autenticación en una aplicación de Xamarin.Forms para que los usuarios puedan compartir un back-end al tiempo que sólo tiene acceso a sus propios datos. Los temas tratados incluyen con la autenticación básica con un servicio REST, mediante el componente Xamarin.Auth para autenticarse con proveedores de identidades de OAuth, y utilizando los mecanismos de autenticación integrados proporcionados por proveedores diferentes._

## <a name="authenticating-a-restful-web-servicerestmd"></a>[Autenticar un servicio Web de REST](rest.md)

HTTP admite el uso de varios mecanismos de autenticación para controlar el acceso a los recursos. La autenticación básica proporciona acceso a los recursos a solo los clientes que tengan las credenciales correctas. En este artículo se muestra cómo utilizar la autenticación básica para proteger el acceso a los recursos del servicio web RESTful.

## <a name="authenticating-users-with-an-identity-provideroauthmd"></a>[Autenticar a los usuarios con un proveedor de identidades](oauth.md)

Xamarin.Auth es un SDK de multiplataforma para autenticar a los usuarios y almacenar sus cuentas. Incluye los autenticadores de OAuth que proporcionan compatibilidad para consumir los proveedores de identidad como Google, Microsoft, Facebook y Twitter. Este artículo explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.

## <a name="authenticating-users-with-azure-mobile-appsazuremd"></a>[Autenticar a los usuarios con aplicaciones móviles de Azure](azure.md)

Aplicaciones móviles de Azure utilizar una variedad de proveedores de identidades externo para admitir autenticar y autorizar usuarios de la aplicación. A continuación, se pueden establecer permisos en las tablas para restringir el acceso a solo los usuarios autenticados. Este artículo explica cómo usar aplicaciones móviles de Azure para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.

## <a name="authenticating-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[Autenticar a los usuarios con Azure Active Directory B2C](azure-ad-b2c.md)

Azure Active B2C de directorio es una solución de administración de identidades de nube para aplicaciones web de consumo y móviles. Este artículo demuestra cómo usar la biblioteca de autenticación de Microsoft (MSAL) y Azure Active Directory B2C para integrar la administración de identidades de consumidor en una aplicación de Xamarin.Forms.

## <a name="integrating-azure-active-directory-b2c-with-azure-mobile-appsazure-ad-b2c-mobile-appmd"></a>[Integración de Azure Active Directory B2C con Azure Mobile Apps](azure-ad-b2c-mobile-app.md)

Azure B2C directorio activo puede utilizarse para administrar el flujo de trabajo de autenticación para aplicaciones móviles de Azure. Con este enfoque, la experiencia de administración de identidad está totalmente definida en la nube y puede modificarse sin cambiar el código de aplicaciones móviles. Este artículo demuestra cómo usar Azure Active Directory B2C para proporcionar autenticación y autorización a una instancia de aplicaciones móviles de Azure con Xamarin.Forms.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a los servicios web](~/cross-platform/data-cloud/web-services/index.md)
- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
