---
title: Paso 2. Configurar el acceso de servicio de aplicaciones móviles
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: fd6436a664fde7a610b29bba31d0baf35cf88dad
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Paso 2. Configurar el acceso de servicio de aplicaciones móviles

Siempre que cualquier recurso p. ej. de aplicación web, servicio web, etc. deben estar protegidas por Azure Active Directory, tiene que registrar. Todos los servicios o aplicaciones seguras pueden verse en **aplicaciones** ficha. Aquí puede seleccionar la aplicación que necesita para tener acceso desde aplicaciones móviles y proporcionar acceso a él.

1. En el **configurar** ficha, busque **permisos para otras aplicaciones** sección:

  ![](configure-images/2.1-configure.png "En la pestaña configurar, busque la sección permisos para otra aplicaciones")

2.  Haga clic en **Agregar aplicación** botón. En la siguiente pantalla emergente debería ver la lista de todas las aplicaciones que están protegidos por Azure Active Directory. Seleccione las aplicaciones que necesita para tener acceso desde la aplicación móvil.

  ![](configure-images/2.2-add-application.png "Seleccione las aplicaciones que necesita para tener acceso desde la aplicación móvil")

3. Después de seleccionar la aplicación, seleccione de nuevo la aplicación recién agregados en **permisos para otras aplicaciones** sección y conceder los derechos adecuados.

  ![](configure-images/2.3-permissions.png "Después de seleccionar la aplicación, una vez más en sección permisos para otra aplicaciones, seleccione la aplicación recién agregado y conceder los derechos adecuados")

4. Por último, **guardar** la configuración. Estos servicios deberían estar ahora disponibles en las aplicaciones móviles.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
