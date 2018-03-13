---
title: Servicios en Visual Studio conectados para Mac
description: "Agregar almacenamiento de datos de Azure, la autenticación y las notificaciones de inserción a aplicaciones móviles desde dentro de Visual Studio para Mac"
ms.topic: article
ms.prod: xamarin
ms.assetid: ADDFB3A5-DB6A-417C-8ACE-33D107FB75C2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 5c9ae8d65a2bfdd035cccb6b911448ea047f8c40
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="connected-services-walkthrough"></a>Tutorial de servicios conectados

El flujo de trabajo de servicios conectados pone el flujo de trabajo de portal de Azure en Visual Studio para Mac, por lo que no tiene que dejar su proyecto para agregar servicios.

Este tutorial muestra cómo agregar un servicio de Azure back-end, que ofrece almacenamiento de datos en la nube, la autenticación y las notificaciones de inserción a una aplicación de biblioteca de clases portables (PCL) de Xamarin.Forms y multiplataforma.


1.  Iniciar haciendo doble clic en el **servicios conectados** nodo en la solución, que le llevará a la **servicios galería**.
  Se trata de una lista de todos los servicios disponibles para este tipo de aplicación. Seleccione un servicio (como **móvil back-end con el servicio de aplicación de Azure**) haciendo clic en él.

  [![](connected-services-images/image001-sml.png "Conecta el nodo Servicios de Visual Studio para Mac")](connected-services-images/image001.png#lightbox)

2. La página de detalles de servicio tiene una descripción del servicio y las dependencias para instalarse.
  Haga clic en el **agregar** botón para agregar las dependencias a la aplicación:

  [![](connected-services-images/image002-sml.png "Back-end de dispositivos móvil con Azure")](connected-services-images/image002.png#lightbox)

3. Las dependencias deben agregarse a la PCL y los proyectos específicos de plataforma para que funcione.
  Seleccione las casillas de verificación para agregar el servicio a todos los proyectos que hará referencia a él (directa o indirectamente):

  [![](connected-services-images/image003-sml.png "Compruebe todos los proyectos que se deben hacer referencia al servicio")](connected-services-images/image003.png#lightbox)

4. Elija **Accept** en el **la aceptación de licencia** cuadros de diálogo para los paquetes de NuGet.
  Puede haber dos cuadros de diálogo para aceptar, uno de lo MobileClient y las dependencias y otro para SQLiteStore, lo cual es necesario para la sincronización de datos sin conexión:

  [![](connected-services-images/image004-sml.png "Acepte los contratos de licencia")](connected-services-images/image004.png#lightbox)

  ![](connected-services-images/image005.png "Ventana de aceptación de licencia")

5. Una vez que se han agregado las dependencias, se pedirá que inicie sesión con la cuenta que desea usar para comunicarse con Azure.
  Si ya ha iniciado sesión con un identificador de Microsoft, Visual Studio para Mac intentará capturar las suscripciones de Azure y los servicios de aplicación asociados con ellos. Si no tiene ninguna suscripción, puede agregar uno por registrarse para una evaluación gratuita ni la compra de un plan de suscripción en el portal de Azure.

6. Seleccione un servicio de aplicaciones de la lista. Esto rellenará el código de plantilla para el `MobileServiceClient` objeto con la dirección URL correspondiente del servicio de aplicación en Azure:

  [![](connected-services-images/image006-sml.png "Seleccione el servicio de aplicación de lista")](connected-services-images/image006.png#lightbox)

  Si no hay ningún servicio que aparece, haga clic en el **New** botón (consulte el paso 9).

7. Copie el código de plantilla para el `MobileServiceClient` en la PCL. La aplicación de la ubicación de archivo en no es importante, siempre que hay sólo una instancia del mismo.
  El enfoque recomendado es crear un `AzureService` clase que controla todas las interacciones de Azure y utiliza el `MobileServiceClient`:

  ![](connected-services-images/image007.png "Copie el código de configuración en la aplicación")

8. Seguir la documentación de **pasos** para agregar datos, la sincronización sin conexión, la autenticación y las notificaciones de inserción a la aplicación:

  [![](connected-services-images/image008-sml.png "Revisa la siguiente información de pasos")](connected-services-images/image008.png#lightbox)

10. Si no tiene ningún servicio de aplicación existente, puede crear nuevos servicios de Visual Studio para Mac.
  Haga clic en el **New** botón en la parte inferior izquierda de la lista de servicios para abrir la **nuevo servicio en la aplicación** cuadro de diálogo:

  [![](connected-services-images/image009-sml.png "Crear un nuevo servicio de aplicación en Visual Studio para Mac")](connected-services-images/image009.png#lightbox)

Un nuevo servicio requiere los siguientes parámetros:

-   **Nombre del servicio de aplicación** : nombre o identificador único para el plan
-   **Suscripción** : la suscripción que le gustaría usar para pagar por el servicio
-   **Grupo de recursos** : una forma u organizar todos los recursos de Azure para un proyecto. Opción para usar existente o cree uno nuevo. Si se trata de su primer servicio de Azure, cree uno nuevo.
-   **El Plan de servicio** : determina la ubicación y el costo de todos los recursos que lo usan. Opción para usar existente o cree uno nuevo. Si se trata de su primer servicio de Azure, utilice el valor predeterminado o cree uno nuevo en el nivel gratis (F1).

Visite la [documentación de servicio de aplicaciones de Azure](https://docs.microsoft.com/azure/app-service/) para obtener más información


## <a name="related-links"></a>Vínculos relacionados

- [Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/)
- [Notas de la versión](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Connected_Services)
