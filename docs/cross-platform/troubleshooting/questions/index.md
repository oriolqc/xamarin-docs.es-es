---
title: Preguntas más frecuentes de general
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 06aa6569301d1bfdbf9f6fd1e7397a38a9beb6f6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157198"
---
# <a name="general-frequently-asked-questions"></a>Preguntas más frecuentes de general

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[¿Cómo puedo ver qué bibliotecas se admiten en una PCL?](pcl-support-libraries.md)
Esta guía enumera los recursos y métodos para determinar si la biblioteca existente es compatible con las diferentes plataformas de destino PCL o se puede convertir en un perfil PCL.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[Reflection API de PCL](pcl-reflection.md)
Microsoft ha desarrollado una nueva API de reflexión para su uso en bibliotecas de clases portables. Si tiene algún código de reflexión existente que desea mover a una PCL, es posible que no funcione.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Caso práctico de PCL: ¿Cómo puedo resolver problemas relacionados con System.Diagnostics.Tracing para el paquete NuGet de flujo de datos TPL de Microsoft?](pcl-case-study.md)
Xamarin.iOS y Xamarin.Android no implementan el 100% de todos los perfiles PCL que permiten como referencias. Para mayor comodidad práctico en Visual Studio para Mac, Visual Studio y el Administrador de paquetes de NuGet, proyectos de Xamarin permiten el uso de varios perfiles que solo tienen implementaciones incompletas. Por ejemplo, Xamarin.iOS ni Xamarin.Android actualmente incluye una implementación completa de los tipos en el `System.Diagnostics.Tracing` espacio de nombres PCL. Puede solucionar esto al cambiar el proyecto de aplicación para hacer referencia a la portable-net45 + win8 + wp8 + wpa81 versión de la biblioteca TPL Dataflow.

## <a name="nuget-packages--xamarin-components"></a>Paquetes de NuGet y componentes de Xamarin
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[¿Cómo puedo actualizar NuGet?](nuget-update.md)
Las actualizaciones de NuGet, extensiones y complementos que pueden encontrarse en el **actualizaciones** pestaña en el **Administrador de paquetes de NuGet**. Exploración detallada para buscar las actualizaciones en Visual Studio para Mac y Visual Studio está en esta guía.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[¿Cómo se puede cambiar a una versión anterior un paquete NuGet?](nuget-package-downgrade.md)
Visual Studio para Mac y Visual Studio tiene características para seleccionar las versiones anteriores de los paquetes e instalarlos automáticamente; de forma similar a cómo de la actualización de paquetes.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Error de paquetes que faltan después de actualizar paquetes Nuget](nuget-packages-missing.md)
Este problema se produce principalmente en soluciones de aplicación de ejemplo de Xamarin.Forms, pero el potencial para resolver este problema puede ocurrir en cualquier proyecto que usa paquetes de NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Unificación de componentes de Google Play Services y NuGet](gps-components-nuget.md)
Se utiliza para varios componentes de Google Play Services y paquetes de NuGet, sino para facilitar las cosas para los desarrolladores, nos hemos ahora unificada nuestros componentes y NuGet paquetes en dos. En casi todos los casos, se deben utilizar servicios de Google Play. La única razón para usar el paquete (Froyo) es si activamente destino Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[¿Dónde se almacenan los componentes en mi equipo?](component-storage.md)
Siempre que se instala un componente de Xamarin en un proyecto de aplicación, obtiene lo colocan en las dos ubicaciones enumeradas en esta guía.


## <a name="troubleshooting"></a>Solución de problemas
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[¿Dónde se puede encontrar mi información de versión y los registros?](version-logs.md)
Esta guía detalla dónde encontrar más información de diagnóstico que puede utilizarse para solucionar problemas con Xamarin.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[¿Cuándo y cómo se debería presentar un informe de errores?](howto-file-bug.md)
Esta guía proporciona sugerencias para la presentación de informes de errores de alta calidad, para que nuestros ingenieros puedan determinar la causa (y todas las posibles correcciones) para un problema de forma más eficaz.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[¿Por qué no se admite Jenkins por Xamarin?](xamarin-jenkins.md)
Jenkins es un conjunto de elementos de configuración de código abierto; debido a tantos problemas provocados directamente por el Jenkins *propio* debe presentarse como problemas en donde ha obtenido el código; por ejemplo, el [repositorio principal de Jenkins](https://github.com/jenkinsci/jenkins), o el repositorio para [ Jenkins.app](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[¿Qué configuración del proyecto es necesaria para el depurador?](debugger-settings.md)
En orden para que el depurador funcione según lo esperado (puntos de interrupción de posicionamiento, para mostrar los registros de depuración, etc.), pantalla de información de instrumentación y depuración de desarrollador debe habilitarse. Esta guía detalla cómo encontrar y activar esta configuración.

