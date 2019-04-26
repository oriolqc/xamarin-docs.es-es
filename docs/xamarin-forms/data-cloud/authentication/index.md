---
title: Autenticación del acceso a los servicios web
description: Esta guía explica cómo integrar servicios de autenticación en una aplicación de Xamarin.Forms para permitir a los usuarios compartir un back-end al tiempo que sólo tiene acceso a sus propios datos.
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: d598a9b3de31ea6823530f911c3544bf3cebb37f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331188"
---
# <a name="authenticating-access-to-web-services"></a>Autenticación del acceso a los servicios web

_Esta guía explica cómo integrar servicios de autenticación en una aplicación de Xamarin.Forms para permitir a los usuarios compartir un back-end al tiempo que sólo tiene acceso a sus propios datos. Los temas tratados incluyen con la autenticación básica con un servicio REST, mediante el componente Xamarin.Auth para autenticarse con proveedores de identidades de OAuth, y utilizando los mecanismos de autenticación integrados que ofrecen los proveedores diferentes._

## <a name="authenticating-a-restful-web-servicerestmd"></a>[Autenticar un servicio Web RESTful](rest.md)

HTTP es compatible con el uso de varios mecanismos de autenticación para controlar el acceso a los recursos. Autenticación básica proporciona acceso a recursos solo a los clientes que tienen las credenciales correctas. En este artículo se muestra cómo usar la autenticación básica para proteger el acceso a recursos de servicio web RESTful.

## <a name="authenticating-users-with-an-identity-provideroauthmd"></a>[Autenticar a los usuarios con un proveedor de identidades](oauth.md)

Xamarin.Auth es un SDK multiplataforma para autenticar a los usuarios y sus cuentas de almacenamiento. Incluye los autenticadores de OAuth que proporcionan compatibilidad para consumir los proveedores de identidades como Microsoft, Google, Facebook y Twitter. Este artículo explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.

## <a name="authenticating-users-with-azure-mobile-appsazuremd"></a>[Autenticar a los usuarios con Azure Mobile Apps](azure.md)

Azure Mobile Apps usa una variedad de proveedores de identidades externos para admitir la autenticación y autorización de usuarios de la aplicación. A continuación, se pueden establecer permisos en las tablas para restringir el acceso únicamente a usuarios autenticados. En este artículo se explica cómo usar Azure Mobile Apps para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.

## <a name="authenticating-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[Autenticar a los usuarios con Azure Active Directory B2C](azure-ad-b2c.md)

Azure B2C de Active Directory es una solución de administración de identidades en la nube para aplicaciones móviles y web orientadas al consumidor. En este artículo se muestra cómo usar la biblioteca de autenticación de Microsoft (MSAL) y Azure Active Directory B2C para integrar la administración de identidades de consumidor en una aplicación de Xamarin.Forms.

## <a name="integrating-azure-active-directory-b2c-with-azure-mobile-appsazure-ad-b2c-mobile-appmd"></a>[Integración de Azure Active Directory B2C con Azure Mobile Apps](azure-ad-b2c-mobile-app.md)

Azure B2C de Active Directory puede usarse para administrar el flujo de trabajo de autenticación para Azure Mobile Apps. Con este enfoque, la experiencia de administración de identidad está totalmente definida en la nube y puede modificarse sin cambiar el código de aplicación móvil. En este artículo se muestra cómo usar Azure Active Directory B2C para proporcionar autenticación y autorización a una instancia de Azure Mobile Apps con Xamarin.Forms.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a los servicios web](~/cross-platform/data-cloud/web-services/index.md)
- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
