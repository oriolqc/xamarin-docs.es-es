---
title: ¿Qué paquetes de Android SDK debo instalar?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: 04a07d5b7f37222515136e5592f31a4583b02fe3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157369"
---
# <a name="which-android-sdk-packages-should-i-install"></a>¿Qué paquetes de Android SDK debo instalar?

Instalar el SDK de Android no incluye automáticamente todos los paquetes mínimos necesarios para el desarrollo. Aunque necesitan varían para desarrolladores individuales, suele ser necesarios para el desarrollo con Xamarin.Android los siguientes paquetes:

## <a name="tools"></a>Herramientas

Instale las herramientas más recientes de la carpeta de herramientas en el Administrador de SDK:

- Herramientas de Android SDK
- Herramientas de la plataforma Android SDK
- Herramientas de compilación de Android SDK

## <a name="android-platforms"></a>Plataformas de Android

Instale la "plataforma de SDK" para las versiones de Android que ha establecido como mínimo y de destino. 

Ejemplos:

- API 23 de destino
- Mínimo de API 23

Solo necesita instalar el SDK de plataforma de API 23

- API 23 de destino
- Mínimo de API 15

Debe instalar el SDK de plataformas para API 15 y 23. Tenga en cuenta que no es necesario instalar los niveles de API entre los valores mínimo y de destino (incluso si son backporting para esos niveles de API).

## <a name="system-images"></a>Imágenes del sistema

Estos son solo es necesario si desea utilizar los emuladores de Android de fábrica de Google. Para obtener más información, consulte [configuración de Android Emulator](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Extras
Los Extras de SDK de Android no suelen ser necesarios; pero resulta útil ser consciente de ellos, ya que pueden ser necesarios según el caso de uso.

## <a name="further-reading"></a>Información adicional
La siguiente guía cubre estas opciones y entra en más detalles sobre los paquetes del SDK manager tiene disponible: [Guía de instalación de Android SDK Manager](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

