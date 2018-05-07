---
title: Configuración de Visual Studio 2017
description: En este artículo, se describen distintas opciones de configuración de Xamarin.iOS para Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 22D82244-890D-4325-B3CC-C0AC49130BCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/16/2018
ms.openlocfilehash: ee08cf7d68bd9d10026f1c15d4438077349fe367
ms.sourcegitcommit: dc6ccf87223942088ca926c0dadd5b5478c683cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="configuring-visual-studio-2017"></a>Configuración de Visual Studio 2017

_En este artículo, se describen distintas opciones de configuración de Xamarin.iOS para Visual Studio._

## <a name="using-matching-xamarinios-versions"></a>Usar versiones coincidentes de Xamarin.iOS

Visual Studio 2017 debe usar la misma versión de Xamarin.iOS que está instalada en el host de compilación de Mac. Para asegurarse de que esto se cumpla:

 - Si usa Visual Studio 2017, seleccione el canal de actualizaciones **Estable** en Visual Studio para Mac.

 - Si usa la versión preliminar de Visual Studio 2017, seleccione el canal de actualizaciones **Alfa** en Visual Studio para Mac.

> [!NOTE]
> A partir de [Visual Studio 2017 versión 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Visual Studio 2017 detecta automáticamente si el host de compilación de Mac usa la misma versión de Xamarin.iOS que Windows. Si hay una discrepancia entre las versiones, Visual Studio 2017 le ofrece instalar la versión correcta de forma remota en el host de compilación de Mac. Para obtener más información, eche un vistazo a la sección [Aprovisionamiento automático de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning) de la guía [Emparejar con Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="ios-toolbar"></a>Barra de herramientas de iOS

Cuando se abre un proyecto de iOS en Visual Studio 2017, la barra de herramientas de iOS debe estar visible.  De forma predeterminada, contiene cuatro botones que son útiles para el desarrollo de Xamarin.iOS:

![Barra de herramientas de iOS de Visual Studio 2017](config-options-images/ios-toolbar.png "Visual Studio 2017's iOS toolbar")

- **Emparejar con Mac**: abre el cuadro de diálogo Emparejar con Mac. Se habilita cuando se abre un proyecto de iOS en Visual Studio 2017.
- **Mostrar simulador de iOS**: en el host de compilación de Mac, coloca en primer plano la ventana del simulador de iOS. Se habilita cuando se abre un proyecto de iOS en Visual Studio 2017.
- **Registro de dispositivos**: abre una ventana que le permite inspeccionar los registros del dispositivo. Se habilita cuando se abre un proyecto de iOS en Visual Studio 2017.
- **Mostrar archivo IPA en servidor de compilación**: abre una ventana en el host de compilación de Mac que muestra la ubicación del archivo .ipa de la aplicación. Se habilita tras completar una compilación para la que se ha creado un archivo .ipa.

Si no aparece esta barra de herramientas, abra el menú **Ver** en Visual Studio 2017 y elija **Barras de herramientas > iOS**:

![Habilitar la barra de herramientas de iOS](config-options-images/ios-toolbar-enable.png "Enabling the iOS toolbar")

## <a name="solution-platforms-drop-down-menu"></a>Menú desplegable Plataformas de solución

El menú desplegable **Plataformas de solución** le permite elegir si la siguiente compilación se dirigirá a un dispositivo físico o a un simulador.

Para asegurarse de que se ve este menú desplegable en la barra de herramientas estándar:

- En Visual Studio 2017, haga clic en la flecha hacia abajo en el borde derecho de la barra de herramientas estándar.
- Elija **Agregar o quitar botones**. 
- Asegúrese de que esté marcado el elemento **Plataformas de solución**:

![Habilitar el menú desplegable Plataformas de solución](config-options-images/solution-platforms-enable.png "Enabling the Solution Platforms drop-down menu")

Con un proyecto de iOS abierto, las barras de herramientas **estándar** y de **iOS** ahora deben ser similares a la captura de pantalla siguiente:

![Barras de herramientas estándar y de iOS](config-options-images/toolbars.png "Standard and iOS toolbars")


