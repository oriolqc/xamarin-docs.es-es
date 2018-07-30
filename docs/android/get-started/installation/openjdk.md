---
title: Versión preliminar de la distribución OpenJDK de Microsoft
description: Guía paso a paso para configurar la distribución de OpenJDK de Microsoft.
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: 6c1346918ca6881e551f6c5b89ab16ad13d3f804
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242533"
---
# <a name="microsofts-openjdk-distribution-preview"></a>Versión preliminar de la distribución OpenJDK de Microsoft

_En esta guía se describen los pasos para cambiar a la versión preliminar de la distribución de OpenJDK de Microsoft._

![Características de la versión preliminar](~/media/shared/preview.png)

## <a name="overview"></a>Información general

A partir de Visual Studio 15.9 y Visual Studio para Mac 7.7, Visual Studio Tools para Xamarin ahora migrará desde el JDK de Oracle a una versión ligera de OpenJDK que está diseñada únicamente para el desarrollo de Android:

![Nuevo flujo de trabajo que ofrece una versión preliminar web de OpenJDK en VS 15.8 versión preliminar 5](openjdk-images/openjdk.png)

Las ventajas de esta migración son las siguientes:

- Siempre tendrá una versión de OpenJDK que funcione para el desarrollo de Android.

- Descargar JDK 9 o 10 no afectará la experiencia de desarrollo.

- Superficie y tamaño de la descarga considerablemente menores.

- No más problemas con instaladores y servidores de terceros.

Si quiere migrar antes a la experiencia mejorada, hay disponibles compilaciones de la distribución de OpenJDK para probarlas tanto en Windows como en Mac. A continuación, se describe el proceso de configuración y puede revertir al JDK de Oracle en cualquier momento.

## <a name="download"></a>Descargar

Para comenzar, descargue la compilación correspondiente a su sistema:

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

## <a name="configure"></a>Configurar

Descomprima en la ubicación correcta:

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Archivos de programa\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

> [!IMPORTANT]
> En este ejemplo se usa la compilación 1.8.0.9, pero es posible que la versión que descargue sea más reciente.

Dirija el IDE al nuevo JDK:

- **Mac** &ndash; Haga clic en **Herramientas > SDK Manager > Ubicaciones** y cambie la **Ubicación del SDK de Java (SDK)** a la ruta de acceso completa de la instalación de OpenJDK. En el ejemplo siguiente, esta ruta de acceso está establecida en **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**.

![Establecimiento de la ruta del JDK para la distribución de OpenJDK de Microsoft en un equipo Mac](openjdk-images/vsm.png)

- **Windows** &ndash; Haga clic en **Herramientas > Opciones > Xamarin > Configuración de Android** y cambie la **Ubicación de Java Development Kit** a la ruta de acceso completa de la instalación de OpenJDK. En el ejemplo siguiente, esta ruta de acceso está establecida en **C:\\Archivos de programa\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**:

![Establecimiento de la ruta del JDK para la distribución de OpenJDK de Microsoft en un equipo Windows](openjdk-images/vs.png)

## <a name="revert"></a>Reversión

Para revertir al JDK de Oracle, cambie la ubicación del SDK de Java a la ruta de acceso del JDK de Oracle usado anteriormente y recompile la solución. En Mac, puede hacer clic en **Restaurar valores predeterminados** para revertir a la ruta de acceso del JDK de Oracle.

Si tiene problemas con la distribución de OpenJDK de Microsoft, use la herramienta de comentarios del IDE para informar los problemas con el fin de que puedan hacerles seguimiento y corregirlos rápidamente.

## <a name="known-issues--planned-fix-dates"></a>Problemas conocidos y fechas de corrección planeadas

Es posible que la variable de entorno `JAVA_HOME` no se esté exportando correctamente al SDK y al Administrador de dispositivos. Como solución alternativa, puede establecer esta variable de entorno en la ubicación del OpenJDK en su equipo. Este problema se corrige en las versiones preliminares 15.9.

## <a name="summary"></a>Resumen

En este artículo aprendió a configurar el IDE para usar la versión preliminar de la distribución de OpenJDK de Microsoft, cuya versión estable está programada para más adelante en 2018.
