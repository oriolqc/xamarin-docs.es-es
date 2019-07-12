---
title: Paso 2. Configurar el acceso de servicio para aplicaciones móviles
description: Este documento describe cómo proporcionar una aplicación de Xamarin con acceso a una aplicación de Azure protegida por Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6135740095395d5cd7bde9abc1cbbab1e1072161
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832241"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Paso 2. Configurar el acceso de servicio para aplicaciones móviles

Siempre que cualquier recurso, por ejemplo, la aplicación web, servicio web, etc. debe protegerse con Azure Active Directory, deberá estar registrado. Se pueden ver todos los servicios o aplicaciones seguras en **aplicaciones** ficha. Aquí puede seleccionar la aplicación que necesita para tener acceso desde aplicaciones móviles y proporcionar acceso a él.

1. En el **configurar** pestaña, busque **permisos para otras aplicaciones** sección:

  ![](configure-images/2.1-configure.png "En la pestaña configurar, busque la sección permisos para otras aplicaciones")

2. Haga clic en **Agregar aplicación** botón. En la siguiente pantalla emergente debería ver la lista de todas las aplicaciones que están protegidos por Azure Active Directory. Seleccione las aplicaciones que necesita para tener acceso desde la aplicación móvil.

  ![](configure-images/2.2-add-application.png "Seleccione las aplicaciones que necesita para tener acceso desde la aplicación móvil")

3. Después de seleccionar la aplicación, seleccione una vez más la aplicación recién agregada en **permisos para otras aplicaciones** sección y conceder los derechos adecuados.

  ![](configure-images/2.3-permissions.png "Después de seleccionar la aplicación, una vez más seleccione la aplicación recién agregada en la sección permisos para otras aplicaciones y conceder los derechos adecuados")

4. Por último, **guardar** la configuración. Estos servicios deberían estar ahora disponibles en las aplicaciones móviles.



## <a name="related-links"></a>Vínculos relacionados

- [Microsoft NativeClient sample](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
