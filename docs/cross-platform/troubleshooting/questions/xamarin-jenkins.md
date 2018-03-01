---
title: "¿Por qué no se admite Jenkins por Xamarin?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 8129229a821edd2ef4f251679ee46bca7b74c8f9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="why-isnt-jenkins-supported-by-xamarin"></a>¿Por qué no se admite Jenkins por Xamarin?

## <a name="jenkins-support-explanation"></a>Explicación de la compatibilidad con Jenkins

Jenkins es un conjunto de elementos de configuración de código abierto; debido a problemas de tantos provocados directamente por el Jenkins *propio* debe presentarse como problemas en donde obtuvo el código; por ejemplo, el [repositorio principal de Jenkins](https://github.com/jenkinsci/jenkins), o en el repositorio para [ Jenkins.app](https://github.com/stisti/jenkins-app).

La excepción a esto es para los problemas que se pueden aislados errores determinado en herramientas de Xamarin; Si sospecha que éste puede ser el caso puede comprobar su [opciones de soporte técnico](~/cross-platform/troubleshooting/support-options.md), aunque una vez más, el problema podría ser algo fuera de la compatibilidad de Xamarin el equipo puede *directamente* ayuda con.

## <a name="setup-jenkins-with-xamarin"></a>El programa de instalación Jenkins con Xamarin

Mientras que como se mencionó anteriormente Jenkins problemas no son compatibles directamente con nuestro equipo; el [utilizando Jenkins con Xamarin](~/tools/ci/jenkins-walkthrough.md) guía se puede usar para configurar un servidor de integración continua Jenkins que se integra con Xamarin. 

## <a name="fixes-for-common-issues"></a>Correcciones para problemas comunes
### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins es no se puede encontrar el SDK de Android

El mensaje de error para este problema es similar al siguiente:

> Error XA5205: no se pudo encontrar el directorio del SDK de Android. Establezca a través de /p:AndroidSdkDirectory

Las opciones para establecer la ubicación del SDK pueden variar en función el complemento Jenkins Android exacto que está utilizando; es un buen lugar para buscar cómo establecer esta opción en la Guía de complemento. Por ejemplo, el [complemento de emulador Android](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) busca automáticamente el SDK, pero si no puede encontrarla; la ubicación también puede establecerse a través de la página de configuración del sistema Jenkins para ese complemento. 


## <a name="deprecated-errors"></a>Errores en desuso

> [!IMPORTANT]
> Este problema se ha resuelto en versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre una [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de los resultados de registro de compilación.



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins notifica una licencia de Xamarin no válida
Los mensajes de error para este problema suelen ser algo parecido a

> Error de XA9008: crear desde la línea de comandos requiere una licencia de negocios

o

> Error: La edición de inicio de Xamarin.iOS no admite la creación fuera de Xamarin Studio 

La causa más común de este escenario es el uso de Jenkins, inicie sesión en una cuenta de usuario no está asociada con su licencia de Xamarin. La manera más sencilla de resolver esto, es instalar Jenkins como una aplicación directamente a través de la cuenta de usuario. Que se describen este proceso y algunas consideraciones adicionales: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)

Otra posibilidad es que la información de licencia de Xamarin está dañada de algún modo, puede usar el [guía Resync de licencia de Xamarin](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md) para solucionar este escenario.


