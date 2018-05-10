---
title: Introducción a la integración continua con Xamarin
description: La integración continua es una práctica de ingeniería de software en el que se compila una compilación automatizada y opcionalmente, prueba una aplicación cuando el código se agrega o cambia los desarrolladores en el repositorio de control de versiones del proyecto. Este artículo describe los conceptos generales de la integración continua y algunas de las opciones disponibles para la integración continua con los proyectos de Xamarin.
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: topgenorth
ms.author: toopge
ms.date: 05/04/2017
ms.openlocfilehash: 34838a1527cb3661e8e5ed51b5950f26026e9433
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introducción a la integración continua con Xamarin

_La integración continua es una práctica de ingeniería de software en el que se compila una compilación automatizada y opcionalmente, prueba una aplicación cuando el código se agrega o cambia los desarrolladores en el repositorio de control de versiones del proyecto. Este artículo describe los conceptos generales de la integración continua y algunas de las opciones disponibles para la integración continua con los proyectos de Xamarin._

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]


##  <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[Introducción a la integración continua](~/tools/ci/intro-to-ci.md)

Esta sección describen los distintos componentes relacionados con la integración continua y sus relaciones. Se describen los entornos de integración continua que se describen en las siguientes secciones específicas.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="working-with-continuous-integration-environments"></a>Trabajar con el entorno de integración continua


### <a name="using-app-center-build-with-xamarinappcenterbuildxamarin"></a>[Uso de la compilación de App Center con Xamarin](/appcenter/build/xamarin/)

Crear soluciones de Xamarin.iOS y Xamarin.Android con el centro de la aplicación, directamente desde GitHub, VSTS o Bitbucket.

### <a name="using-teamcity-with-xamarintoolsciteamcitymd"></a>[Uso de TeamCity con Xamarin](~/tools/ci/teamcity.md)

Esta guía describe los pasos que se presentan al utilizar TeamCity para compilar aplicaciones móviles y, a continuación, enviarlos a la aplicación Centro de pruebas.

### <a name="using-jenkins-with-xamarintoolscijenkins-walkthroughmd"></a>[Uso de Jenkins con Xamarin](~/tools/ci/jenkins-walkthrough.md)

Esta guía muestra cómo configurar Jenkins como un servidor de integración continua y automatizar la compilación de aplicaciones móviles creadas con Xamarin. Se describe cómo instalar Jenkins en OS X, configurarlo y configurar las tareas para compilar aplicaciones Xamarin.iOS y Xamarin.Android cuando los cambios se confirman en el sistema de control de versiones.
