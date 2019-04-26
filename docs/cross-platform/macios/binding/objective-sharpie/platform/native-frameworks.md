---
title: Enlace de plataformas nativas
description: Este documento describe cómo usar objetivo Sharpie - opción de marco de trabajo para crear un enlace a una biblioteca distribuido como un marco de trabajo.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ca103ee027597813be51e126aaa05f9aa969af35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199133"
---
# <a name="binding-native-frameworks"></a>Enlace de plataformas nativas

A veces, una biblioteca nativa se distribuye como un [framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Objetivo Sharpie proporciona sirve principalmente para enlazar correctamente definidas marcos de trabajo a través de la `-framework` opción.

Por ejemplo, enlace el [SDK Framework de Adobe Creative](https://creativesdk.adobe.com/downloads.html) para iOS es sencillo:

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

En algunos casos, un marco especificará un **Info.plist** que indica en qué SDK el marco debe compilarse. Si no existe esta información y no explícita `-sdk` se pasa la opción, objetivo Sharpie deducirá desde el marco de trabajo **Info.plist** (ya sea el `DTSDKName` clave o una combinación de la `DTPlatformName` y `DTPlatformVersion`claves).

El `-framework` opción no admite archivos de encabezado explícitas a pasarse. El archivo de encabezado paraguas se elige por convención según el nombre del marco de trabajo. Si no se puede encontrar un encabezado paraguas, Sharpie objetivo no intentará enlazar el marco de trabajo y debe realizar manualmente el enlace, ya que proporciona los archivos de encabezado paraguas correcto para analizar, junto con cualquier argumento de marco de trabajo de clang (por ejemplo, el `-F`opción de ruta de acceso de búsqueda de marco de trabajo).

Internamente, especificar `-framework` es simplemente un acceso directo. Los siguientes argumentos de enlace son idénticos a los `-framework` abreviada anterior.
De especial importancia es la `-F .` ruta de búsqueda de marco de trabajo proporcionada para clang (tenga en cuenta el espacio y el período, que son necesarios como parte del comando).

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>

## <a name="related-links"></a>Vínculos relacionados

- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Generar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)

