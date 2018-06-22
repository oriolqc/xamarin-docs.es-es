---
title: ¿Qué paquetes de SDK de Android debo instalar?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: df359fae545079c7ac1d7106ec86e9297f183f90
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732780"
---
# <a name="which-android-sdk-packages-should-i-install"></a>¿Qué paquetes de SDK de Android debo instalar?

Instalar el SDK de Android no incluye automáticamente todos los paquetes mínimos necesarios para el desarrollo. Mientras que necesitan varían para desarrolladores individuales, suele ser necesarios para el desarrollo con Xamarin.Android los siguientes paquetes:

## <a name="tools"></a>Herramientas

Instalar las herramientas más recientes de la carpeta de herramientas en el Administrador de SDK:

- Herramientas de Android SDK
- Herramientas de Platform SDK de Android
- Herramientas de compilación del SDK de Android

## <a name="android-platforms"></a>Plataformas de Android

Instale la "plataforma de SDK" para las versiones de Android que ha establecido como mínimo y de destino. 

Ejemplos:

- API 23 de destino
- 23 de API mínima

Sólo debe instalar Platform SDK para API 23

- API 23 de destino
- API mínimo 15

Necesita instalar el SDK de plataformas de API 15 y 23. Tenga en cuenta que no es necesario instalar los niveles de API entre los valores mínimo y el destino (incluso si está backporting para esos niveles de API).

## <a name="system-images"></a>Imágenes de sistema

Estos son solo es necesario si desea utilizar los emuladores de Android de cuadro de Google. Para obtener más información, consulte [el programa de instalación de emulador de Android](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
Los Extras de SDK de Android normalmente no son necesarios; pero resulta útil para ser consciente de ellos, ya que pueden ser necesarios en función de los casos de uso.

## <a name="further-reading"></a>Información adicional
En la guía siguiente se tratan estas opciones y entra en más detalle sobre los paquetes el SDK manager tiene disponible: [Guía de instalación de administrador de SDK de Android](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

