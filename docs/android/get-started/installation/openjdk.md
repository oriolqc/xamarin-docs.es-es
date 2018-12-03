---
title: Distribución de Mobile OpenJDK de Microsoft
description: Guía paso a paso para configurar y solucionar problemas de la distribución de OpenJDK de Microsoft para desarrollo móvil.
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: aad88ad883dea1e0430a047a71a2c191195efffe
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459907"
---
# <a name="microsofts-mobile-openjdk-distribution"></a>Distribución de Mobile OpenJDK de Microsoft

_En esta guía se describen los pasos para cambiar a una distribución interna de OpenJDK. Esta distribución está pensada para el desarrollo móvil._

## <a name="overview"></a>Información general

A partir de Visual Studio 15.9 y Visual Studio para Mac 7.7, Visual Studio Tools para Xamarin se ha migrado desde el JDK de Oracle a una **versión ligera de OpenJDK que está diseñada únicamente para el desarrollo de Android**. Esta migración es obligatoria, ya que Oracle dejará de ofrecer soporte técnico para la distribución comercial de JDK 8 en 2019 y JDK 8 es una dependencia necesaria para todo el desarrollo de Android.

Las ventajas de esta migración son las siguientes:

- Siempre tendrá una versión de OpenJDK que funcione para el desarrollo de Android.

- La descarga del JDK 9 de Oracle o versiones superiores no afectará a la experiencia de desarrollo.

- Superficie y tamaño de la descarga menores.

- No más problemas con instaladores y servidores de terceros.

Si quiere migrar antes a la experiencia mejorada, hay disponibles compilaciones de la distribución de OpenJDK Mobile para probarlas tanto en Windows como en Mac. A continuación, se describe el proceso de configuración y puede revertir al JDK de Oracle en cualquier momento.

## <a name="download"></a>Descargar

La distribución de Mobile OpenJDK se instala automáticamente por usted si selecciona los paquetes de Android SDK en el instalador de Visual Studio de Windows. Se incluirá en la actualización a la versión 15.9 para los usuarios que hayan seleccionado Android SDK.

En Mac, Mobile OpenJDK se instalará por usted como parte de la carga de trabajo de Android en instalaciones nuevas. Para los usuarios actuales de Visual Studio para Mac, se les solicitará que lo instalen como parte de la actualización a la versión 7.7. El IDE le solicitará que cambie al nuevo JDK y a partir del próximo reinicio ya se habrá aplicado el cambio.

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas con la configuración en Mac o Windows, puede seguir estos pasos para realizar una configuración manual:

Compruebe si OpenJDK está instalado en la máquina en la ubicación correcta:

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Archivos de programa\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

Si esta ruta de acceso está vacía, puede descargar uno de los siguientes paquetes:

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

Dirija el IDE al nuevo JDK:

- **Mac** &ndash; Haga clic en **Herramientas > SDK Manager > Ubicaciones** y cambie la **Ubicación del SDK de Java (SDK)** a la ruta de acceso completa de la instalación de OpenJDK. En el ejemplo siguiente, esta ruta de acceso está establecida en **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**.

![Establecimiento de la ruta del JDK para la distribución de OpenJDK Mobile de Microsoft en un equipo Mac](openjdk-images/vsm.png)

- **Windows** &ndash; Haga clic en **Herramientas > Opciones > Xamarin > Configuración de Android** y cambie la **Ubicación de Java Development Kit** a la ruta de acceso completa de la instalación de OpenJDK. En el ejemplo siguiente, esta ruta de acceso está establecida en **C:\\Archivos de programa\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**:

![Establecimiento de la ruta del JDK para la distribución de OpenJDK Mobile de Microsoft en Windows](openjdk-images/vs.png)

## <a name="known-issues"></a>Problemas conocidos

### <a name="package-openjdkv1regkeyversion1809chipx64-failed-to-install"></a>Error al instalar el paquete "OpenJDKV1.RegKey,version=1.8.0.9,chip=x64"

Este problema puede darse en algunos entornos corporativos. OpenJDK ya está en la máquina: siga los [pasos de solución de errores anteriores](#troubleshooting) para indicar la ubicación correcta del IDE. Puede comprobar el estado de los problemas [aquí](https://developercommunity.visualstudio.com/content/problem/382549/packageidopenjdkv1regkeypackageactioninstallreturn.html).

### <a name="unknown-update-type-zip-when-upgrading-visual-studio-for-mac-to-77"></a>Mensaje "tipo de actualización desconocida: zip" al actualizar Visual Studio para Mac a la versión 7.7

Este problema puede producirse si intenta actualizar una versión anterior de Visual Studio para Mac a la versión 7.7. Para solucionarlo, desmarque OpenJDK en el actualizador, instale las demás actualizaciones y compruebe de nuevo si hay actualizaciones para conseguir el paquete de OpenJDK. Si sigue teniendo problemas, puede seguir los [pasos de solución de errores anteriores](#troubleshooting) para instalar OpenJDK manualmente. Informe de un error y adjunte los registros si se encuentra con este problema.

## <a name="summary"></a>Resumen

En este artículo, ha aprendido a configurar su IDE para usar la distribución de Mobile OpenJDK de Microsoft y a solucionar problemas que se podría encontrar.
