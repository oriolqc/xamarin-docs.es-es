---
title: Enlace de marcos nativos
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 219295ad9299b36a763289c4aef8e52cd859ceea
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="binding-native-frameworks"></a>Enlace de marcos nativos

A veces, una biblioteca nativa se distribuye como un [framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Objetivo Sharpie proporciona una característica de comodidad para enlazar correctamente definida marcos de trabajo a través de la `-framework` opción.

Por ejemplo, el enlace la [Adobe Creative SDK Framework](https://creativesdk.adobe.com/downloads.html) para iOS es sencillo:

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

En algunos casos, un marco de trabajo especificará un **Info.plist** que indica en qué SDK el marco debe compilarse. Si no existe esta información y no explícita `-sdk` se pasó a la opción, objetivo Sharpie deducirá desde el marco de trabajo **Info.plist** (ya sea la `DTSDKName` clave o una combinación de la `DTPlatformName` y `DTPlatformVersion`claves).

El `-framework` opción no admite archivos de encabezado explícita que se pasan. El archivo de encabezado aglutina se elige por convención tomando como base el nombre del marco. Si no se puede encontrar un encabezado aglutina, objetivo Sharpie no intentará enlazar el marco de trabajo y que debe realizar manualmente el enlace al proporcionar los archivos de encabezado aglutina correcto para analizar, junto con cualquier argumento de framework para clang (por ejemplo, el `-F`opción de ruta de acceso de búsqueda de marco).

En segundo plano, especificar `-framework` es simplemente un acceso directo. Los siguientes argumentos de enlace son idénticos a los `-framework` abreviada anterior.
De especial importancia es el `-F .` ruta de acceso de búsqueda de framework proporcionada para clang (tenga en cuenta el espacio y el período, que son necesarios como parte del comando).

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>

