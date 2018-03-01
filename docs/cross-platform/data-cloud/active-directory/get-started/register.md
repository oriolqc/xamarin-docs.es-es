---
title: "Paso 1. Registrar una aplicación para usar Azure Active Directory"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 52d06dc6125f91f98e8f3ee8b4f91ad7b52347a3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Paso 1. Registrar una aplicación para usar Azure Active Directory

1. Vaya a [windowsazure.com](https://manage.windowsazure.com) e inicie sesión con su cuenta de organización en el Portal de Azure o Account de Microsoft. Si no tiene una suscripción de Azure, puede obtener una versión de prueba de [azure.com](http://www.azure.com)

2. Después de iniciar sesión, vaya a la **Active Directory** sección (1) y elija el directorio donde van a registrar la aplicación (2)

  [ ![](register-images/01.-active-directory-in-azure-portal-sml.jpg "Elija el directorio donde van a registrar la aplicación y la sección")](register-images/01.-active-directory-in-azure-portal.jpg)

3. Haga clic en **agregar** para crear una nueva aplicación, a continuación, seleccione **agregar una aplicación que mi organización está desarrollando**

  [ ![](register-images/02.-add-new-application-sml.jpg "Agregar una aplicación que mi organización está desarrollando")](register-images/02.-add-new-application.jpg)

4. En la siguiente pantalla, que su aplicación tenga un nombre (p. ej. XAM-DEMO).
  Asegúrese de seleccionar **aplicación cliente nativa** como el tipo de aplicación.

  ![](register-images/03.-app-name.jpg "Asegúrese de que seleccionar la aplicación cliente nativa como el tipo de aplicación")

5. En la pantalla final, proporcione un **URI de redireccionamiento* que es único para su aplicación, tal y como se devolverá a este URI cuando se completa la autenticación.

  ![](register-images/04.-app-redirect.jpg "En la pantalla final, proporcione un URI de redireccionamiento que sea único en la aplicación como devolverá a este URI cuando se completa la autenticación")

6. Una vez creada la aplicación, vaya a la **configurar** ficha. Anote el **Id. de cliente** que usaremos en nuestra aplicación más tarde. Además, en esta pantalla puede permitir que la aplicación móvil tenga acceso a Active Directory o agregar otra aplicación, como la API Web o de Office 365, que puede usarse en aplicaciones móviles una vez completada la autenticación.

    ![](register-images/05.-configure.jpg "Además, en esta pantalla puede permitir que la aplicación móvil tenga acceso a Active Directory o agregar otra aplicación, como la API Web o de Office 365")



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
