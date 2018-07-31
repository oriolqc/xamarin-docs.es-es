---
title: ¿Por qué no se admite Jenkins por Xamarin?
description: Este documento describe, en un nivel alto, interacción de Xamarin con el sistema de integración continua Jenkins. También describe algunos problemas comunes que surgen al trabajar con Jenkins.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: asb3993
ms.author: amburns
ms.date: 06/05/2018
ms.openlocfilehash: 54947d04d6241120df4b3a0f60947ed5cc2b7ffd
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351164"
---
# <a name="why-isnt-jenkins-supported-by-xamarin"></a>¿Por qué no se admite Jenkins por Xamarin?

## <a name="jenkins-support-explanation"></a>Explicación de la compatibilidad con Jenkins

Jenkins es un conjunto de elementos de configuración de código abierto; debido a tantos problemas provocados directamente por el Jenkins *propio* debe presentarse como problemas en donde ha obtenido el código; por ejemplo, el [repositorio principal de Jenkins](https://github.com/jenkinsci/jenkins), o el repositorio para [ Jenkins.app](https://github.com/stisti/jenkins-app).

La excepción a esto es para los problemas que se pueden aislados errores determinado en las herramientas de Xamarin; Si sospecha que este puede ser el caso puede comprobar su [opciones de soporte técnico](~/cross-platform/troubleshooting/support-options.md), aunque de nuevo, el problema podría ser algo fuera de la compatibilidad de Xamarin el equipo puede *directamente* ayudar.

## <a name="setup-jenkins-with-xamarin"></a>El programa de instalación de Jenkins con Xamarin

Si bien como se mencionó anteriormente Jenkins problemas no son compatibles directamente con nuestro equipo; el [usando Jenkins con Xamarin](~/tools/ci/jenkins-walkthrough.md) guía puede usarse para configurar un servidor de integración continua Jenkins que se integra con Xamarin. 

## <a name="fixes-for-common-issues"></a>Correcciones para problemas comunes

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins es no se puede encontrar el SDK de Android

El mensaje de error para este problema es algo parecido a esto:

> Error XA5205: no se pudo encontrar el directorio del SDK de Android. Establezca a través de /p:AndroidSdkDirectory

Las opciones para establecer la ubicación del SDK pueden variar en función del complemento de Jenkins Android exacto que está utilizando; es un buen lugar para ver cómo establecer esto en la Guía de complemento. Por ejemplo: el [complemento de emulador Android](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) busca automáticamente el SDK, pero si no puede encontrarla; también se puede establecer la ubicación a través de la página de configuración del sistema Jenkins para dicho complemento. 


## <a name="deprecated-errors"></a>Errores en desuso

> [!IMPORTANT]
> Se ha resuelto este problema en versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de salida del registro de compilación.



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins informa de una licencia de Xamarin no válida
Los mensajes de error para este problema suelen ser algo parecido a

> Error de XA9008: compilación de línea de comandos requiere una licencia empresarial

o

> Error: Starter Edition de Xamarin.iOS no admite la creación fuera de Xamarin Studio 

La causa más común de este escenario es el uso de Jenkins, inicie sesión en una cuenta de usuario no está asociada con su licencia de Xamarin. La manera más sencilla de resolver esto, es instalar Jenkins como una aplicación directamente a través de la cuenta de usuario. Aquí se describen este proceso y algunas consideraciones adicionales: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
