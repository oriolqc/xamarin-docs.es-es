---
title: ¿Cómo se habilita Intellisense en archivos .axml Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 84850CB2-1CE2-4D3F-BD01-6B3B033F5A4C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 8278576355299894eab1c7c6d3ceaadb607fe915
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774888"
---
# <a name="how-do-i-enable-intellisense-in-android-axml-files"></a>¿Cómo se habilita Intellisense en archivos .axml Android?

Android Intellisense en Visual Studio necesita dos archivos de esquema XML para que funcione correctamente. Para encontrar estos archivos, vaya a esta carpeta:

`C:\Program Files (x86)\Xamarin Studio\AddIns\MonoDevelop.MonoDroid\schemas`*

* (Anteriormente: `C:\Program Files (x86)\MSBuild\Xamarin\Android`)

En encontrará dos archivos .xsd:

1. android-layout-xml.xsd
2. schemas.android.com.apk.res.android.xsd

Visual Studio conserva todos los esquemas XML dentro de la carpeta correspondiente:

`C:\Program Files (x86)\Microsoft Visual Studio 12.0\Xml\Schemas`

Puede mover estos dos archivos .xsd en la ubicación anterior, o simplemente basta con agregar estos esquemas en Visual Studio. Puede, a continuación, "Agregar" estos esquemas dentro de Visual Studio a través de la **XML > esquemas > agregar** cuadro de diálogo.






