---
title: Autenticación del servicio Web de Xamarin.Forms
description: Esta guía explica cómo integrar servicios de autenticación en una aplicación de Xamarin.Forms para permitir a los usuarios compartir un back-end al tiempo que sólo tiene acceso a sus propios datos.
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: a36dfba7aa07de1633ca9620674ddb4887902ba9
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650424"
---
# <a name="xamarinforms-web-service-authentication"></a>Autenticación del servicio Web de Xamarin.Forms

## <a name="authenticate-a-restful-web-servicerestmd"></a>[Autenticar un servicio Web de RESTful](rest.md)

HTTP es compatible con el uso de varios mecanismos de autenticación para controlar el acceso a los recursos. Autenticación básica proporciona acceso a recursos solo a los clientes que tienen las credenciales correctas. En este artículo se explica cómo usar la autenticación básica para proteger el acceso a recursos de servicio web RESTful.

## <a name="authenticate-users-with-an-identity-provideroauthmd"></a>[Autenticar a los usuarios con un proveedor de identidades](oauth.md)

Xamarin.Auth es un SDK multiplataforma para autenticar a los usuarios y sus cuentas de almacenamiento. Incluye los autenticadores de OAuth que proporcionan compatibilidad para consumir los proveedores de identidades como Microsoft, Google, Facebook y Twitter. Este artículo explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.

## <a name="authenticate-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[Autenticar a los usuarios con Azure Active Directory B2C](azure-ad-b2c.md)

Azure B2C de Active Directory es una solución de administración de identidades en la nube para aplicaciones móviles y web orientadas al consumidor. En este artículo se explica cómo usar la biblioteca de autenticación de Microsoft (MSAL) y Azure Active Directory B2C para integrar la administración de identidades de consumidor en una aplicación de Xamarin.Forms.

## <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinformsazure-cosmosdb-authmd"></a>[Autenticar a los usuarios con una base de datos de documentos de Azure Cosmos DB y Xamarin.Forms](azure-cosmosdb-auth.md)

Azure bases de datos de documento de Cosmos DB admiten colecciones con particiones, que pueden abarcar varios servidores y particiones, al tiempo que admite almacenamiento ilimitado y rendimiento. En este artículo se explica cómo combinar el control de acceso con las colecciones con particiones, para que un usuario sólo puede tener acceso a sus propios documentos en una aplicación de Xamarin.Forms.
