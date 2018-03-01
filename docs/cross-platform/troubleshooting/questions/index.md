---
title: "Preguntas más frecuentes de general"
ms.topic: article
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 0c01bfb08d525fd336b29f405304efaeaf749f35
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="general-frequently-asked-questions"></a>Preguntas más frecuentes de general

## <a name="visual-studio-2017-release-candidate"></a>Candidato de versión comercial Visual Studio de 2017
### <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarinvisualstudio-2017-rcmd"></a>[¿Puedo usar a 2017 Release Candidate de Visual Studio con Xamarin?](visualstudio-2017-rc.md)
Para obtener una descripción de las implicaciones del uso de Xamarin con Visual Studio 2017 Release Candidate (RC), así como para obtener información sobre cómo instalar Xamarin en Visual RC Studio2017 actuales.

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables
### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[¿Cómo puedo ver qué bibliotecas se admiten en una PCL?](pcl-support-libraries.md)
Esta guía enumera los recursos y métodos para determinar si la biblioteca existente es compatible con las diferentes plataformas de destino PCL, o se puede convertir a un perfil PCL.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[API de reflexión de PCL](pcl-reflection.md)
Microsoft ha desarrollado una nueva API de reflexión para su uso en bibliotecas de clases Portable. Si tiene algún código de reflexión existente que desee mover a una PCL, es posible que no funcione.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Caso práctico de PCL: ¿Cómo puedo resolver problemas relacionados a System.Diagnostics.Tracing para el paquete NuGet de flujo de datos TPL de Microsoft?](pcl-case-study.md)
Xamarin.iOS y Xamarin.Android no implementan el 100% de todos los perfiles PCL permiten como referencias. Por comodidad resulta muy práctico en Visual Studio para Mac, Visual Studio y el Administrador de paquetes de NuGet, proyectos de Xamarin permiten el uso de diversos perfiles que solo tienen implementaciones incompletas. Por ejemplo, Xamarin.iOS ni Xamarin.Android actualmente incluye una implementación completa de los tipos en el `System.Diagnostics.Tracing` PCL espacio de nombres. Puede solucionar esto cambiando el proyecto de aplicación para hacer referencia a la función portable net45 win8 wp8 + + wpa81 versión de la biblioteca de TPL Dataflow.

## <a name="nuget-packages--xamarin-components"></a>Paquetes de NuGet y componentes de Xamarin
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[¿Cómo puedo actualizar NuGet?](nuget-update.md)
Las actualizaciones de NuGet, extensiones y complementos pueden encontrarse en el **actualizaciones** pestaña en el **Administrador de paquetes de NuGet**. Navegación detallada para buscar las actualizaciones en Visual Studio para Mac y Visual Studio está en esta guía.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[¿Cómo se puede degradar un paquete de NuGet?](nuget-package-downgrade.md)
Visual Studio para Mac y Visual Studio tienen unas características para seleccionar las versiones anteriores de los paquetes e instalarlos automáticamente; es similar a la de la actualización de paquetes.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Error de paquetes que faltan después de actualizar paquetes de Nuget](nuget-packages-missing.md)
Este problema ha notificado principalmente en soluciones de aplicación de ejemplo de Xamarin.Forms, pero la posibilidad de que este problema puede ocurrir en cualquier proyecto que utilice paquetes de NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Unificación de Google Play de servicios de componentes y NuGet](gps-components-nuget.md)
Se utiliza para varios componentes de servicios de reproducción de Google y paquetes de NuGet, sino para facilitar las cosas para los desarrolladores, nos hemos ahora y unificada de nuestros componentes NuGet paquetes en dos. En casi todos los casos, se debe utilizar servicios de Google Play. La única razón para usar el paquete (Froyo) es si activamente destino Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[¿Dónde se almacenan los componentes en mi equipo?](component-storage.md)
Siempre que se instala un componente de Xamarin en un proyecto de aplicación, se coloca en las dos ubicaciones aparece en esta guía.


## <a name="troubleshooting"></a>Solución de problemas
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[¿Dónde puedo encontrar mi información de versión y los registros?](version-logs.md)
Esta guía describe dónde encontrar información de diagnóstico más que puede utilizarse para solucionar problemas de Xamarin.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[¿Cuándo y cómo debería presentar un informe de errores?](howto-file-bug.md)
Esta guía proporciona sugerencias para la presentación de informes de errores de alta calidad, por lo que nuestros ingenieros son capaces de determinar la causa (y todas las posibles correcciones) para un problema de forma más eficaz.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[¿Por qué no se admite Jenkins por Xamarin?](xamarin-jenkins.md)
Jenkins es un conjunto de elementos de configuración de código abierto; debido a problemas de tantos provocados directamente por el Jenkins *propio* debe presentarse como problemas en donde obtuvo el código; por ejemplo, el [repositorio principal de Jenkins](https://github.com/jenkinsci/jenkins), o en el repositorio para [ Jenkins.app](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[¿Qué configuración del proyecto es necesaria para que el depurador?](debugger-settings.md)
En orden para que el depurador para que funcione como se esperaba (visitas puntos de interrupción, mostrar registros de depuración, etc.), pantalla de información de instrumentación y depuración de desarrollador debe deben habilitarse. Esta guía detalla cómo encontrar y activar estas opciones.

