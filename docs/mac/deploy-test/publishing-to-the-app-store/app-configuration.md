---
title: "Configuración de aplicaciones de Mac"
description: "En esta guía se describe el proceso de configuración de una aplicación de Xamarin.Mac para su publicación."
ms.topic: article
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: 8f9294c10f8d3287a2985ede9aadf84ce663c38a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="mac-app-configuration"></a>Configuración de aplicaciones de Mac

_En esta guía se describe el proceso de configuración de una aplicación de Xamarin.Mac para su publicación._


## <a name="mac-app-configuration"></a>Configuración de aplicaciones de Mac

Haga doble clic en el proyecto de la aplicación de Mac en Visual Studio para Mac y elija **Opciones**.


### <a name="application-settings"></a>Configuración de la aplicación

Para cambiar la configuración de la aplicación de una aplicación de Xamarin.Mac, haga doble clic en el archivo **Info.plist** en el **Panel de solución**:

![Seleccionar el archivo info.plist](app-configuration-images/config04.png "Seleccionar el archivo info.plist")

Esto mostrará las opciones disponibles para la aplicación:

 [![Modificar el archivo info.plist](app-configuration-images/config01.png "Modificar el archivo info.plist")](app-configuration-images/config01-large.png)

La ejecución de aplicaciones de Mac creadas con Xamarin.Mac tiene los siguientes requisitos del sistema:

- Un equipo Mac que ejecute Mac OS X 10.7 o una versión posterior.


### <a name="signing-settings"></a>Configuración de firma

La sección **Firma de Mac** del cuadro de diálogo **Opciones de proyecto** permite al desarrollador firmar una aplicación de Xamarin.Mac para las pruebas, para autopublicación o para publicación a través del App Store de Apple:

[![El editor de la firma de Mac](app-configuration-images/config02.png "El editor de la firma de Mac")](app-configuration-images/config02-large.png)

Desde aquí seleccione la identidad, el perfil de aprovisionamiento y cualquier derecho personalizado utilizado para firmar la aplicación cuando se compila. Opcionalmente, el desarrollador puede firmar el instalador usado para instalar la aplicación en otro equipo Mac.


### <a name="build-settings"></a>Configuración de compilación

La sección **Compilación de Mac** del cuadro de diálogo **Opciones de proyecto** permite al desarrollador seleccionar la arquitectura para una aplicación de Xamarin.Mac, para controlar qué versión de macOS será compatible con la aplicación y crear un paquete de instalación si la aplicación se compila correctamente:

 [![Modificar la configuración de compilación](app-configuration-images/config03.png "Modificar la configuración de compilación")](app-configuration-images/config03-large.png)


## <a name="related-links"></a>Vínculos relacionados

- [Instalación](/visualstudio/mac/installation/)
- [Ejemplo de Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/resources/developer-id/)
