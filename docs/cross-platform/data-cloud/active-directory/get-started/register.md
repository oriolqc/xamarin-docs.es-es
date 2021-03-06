---
title: Paso 1. Registrar una aplicación para usar Azure Active Directory
description: Este documento describe cómo registrar una aplicación de Azure con Azure Active Directory para que se puede acceder con seguridad a los clientes móviles.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3e852e3e7ab3ac5db28052a47af1ebec4dbd2416
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864668"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Paso 1. Registrar una aplicación para usar Azure Active Directory

1. Vaya a [windowsazure.com](https://manage.windowsazure.com) e inicie sesión con su cuenta de organización en el Portal de Azure o Account de Microsoft. Si no tiene una suscripción de Azure, puede obtener una versión de prueba de [azure.com](https://www.azure.com)

2. Después de iniciar sesión, vaya a la **Active Directory** sección (1) y elija el directorio donde desea registrar la aplicación (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "sección y elija el directorio donde desea registrar la aplicación")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Haga clic en **agregar** para crear nueva aplicación, a continuación, seleccione **agregar una aplicación que mi organización está desarrollando**

   [![](register-images/02.-add-new-application-sml.jpg "Agregar una aplicación que mi organización está desarrollando")](register-images/02.-add-new-application.jpg#lightbox)

4. En la siguiente pantalla, asigne a la aplicación un nombre (p ej. XAM-DEMO).
   Asegúrese de seleccionar **aplicación cliente nativa** como el tipo de aplicación.

   ![](register-images/03.-app-name.jpg "Asegúrese de que seleccionar la aplicación cliente nativa como el tipo de aplicación")

5. En la pantalla final, proporcione un **URI de redireccionamiento* que es único para su aplicación como devolverá a este URI cuando se completa la autenticación.

   ![](register-images/04.-app-redirect.jpg "En la pantalla final, proporcione un URI de redirección que sea único para su aplicación lo devolverá a este URI cuando se completa la autenticación")

6. Una vez creada la aplicación, navegue hasta la **configurar** ficha. Anote el **Id. de cliente** que utilizaremos en nuestra aplicación más adelante. Además, en esta pantalla puede permitir que la aplicación móvil tenga acceso a Active Directory o agregar otra aplicación, como Office 365, que se puede usar la aplicación móvil una vez completada la autenticación o de API Web.

     ![](register-images/05.-configure.jpg "Además, en esta pantalla puede permitir que la aplicación móvil tenga acceso a Active Directory o agregar otra aplicación, como Office 365 o API Web")



## <a name="related-links"></a>Vínculos relacionados

- [Microsoft NativeClient sample](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
