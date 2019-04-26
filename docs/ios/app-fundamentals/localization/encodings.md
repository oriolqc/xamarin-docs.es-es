---
title: Codificaciones de internacionalización de Xamarin.iOS
description: Este documento describe las codificaciones de internacionalización en Xamarin.iOS, se tratan las codificaciones disponibles y cómo agregarlos a una aplicación.
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/28/2017
ms.openlocfilehash: db24c8677b0a3099193132575e92bc43a4c31dea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61251038"
---
# <a name="internationalization-encodings-in-xamarinios"></a>Codificaciones de internacionalización de Xamarin.iOS

No todas las codificaciones se incluyen en la biblioteca de clases de Xamarin.iOS de forma predeterminada.

Para reducir el tamaño de la aplicación, Xamarin.iOS no incluye ninguna codificación específica, y tendrá que indicar mtouch para incluir los ensamblados que contienen la compatibilidad con la codificación que necesita.

Esto se realiza seleccionando las codificaciones adicionales desde el panel de compilación/avanzada de iOS en Visual Studio para Mac o Visual Studio:

 [![](encodings-images/00.png "Seleccionar las codificaciones adicionales")](encodings-images/00.png#lightbox)

 [![](encodings-images/00a.png "Seleccionar las codificaciones adicionales")](encodings-images/00a.png#lightbox)

Puede seleccionar uno de los siguientes:

-  CJK: para Chineese, japonés y coreano
-  Oriente: Árabe, hebreo, turco y Latin5.
-  Otro: Cirílico, Báltico, vietnamita, tailandés y ucraniano
-  poco frecuentes: Codificaciones de EBCDIC y otras páginas de códigos raros
-  oeste de: Idiomas occidentales, Pascua y Europa occidental
-  todo


 <a name="cjk" />


## <a name="cjk"></a>cjk

-  CP51932
-  CP932
-  CP936
-  CP949
-  CP950
-  CP54936


 <a name="mideast" />


## <a name="mideast"></a>Oriente

-  CP1254
-  CP1255
-  CP1256
-  CP28596
-  CP28598
-  CP28599
-  CP38598


 <a name="other" />


## <a name="other"></a>otras

-  CP1251
-  CP1257
-  CP1258
-  CP20866
-  CP21866
-  CP28594
-  CP28595
-  CP57002
-  CP874


 <a name="rare" />


## <a name="rare"></a>poco frecuente

-  CP1026
-  CP1047
-  CP1140
-  CP1141
-  CP1142
-  CP1143
-  CP1144
-  CP1145
-  CP1146
-  CP1147
-  CP1148
-  CP1149
-  CP20273
-  CP20277
-  CP20278
-  CP20280
-  CP20284
-  CP20285
-  CP20290
-  CP20297
-  CP20420
-  CP20424
-  CP20871
-  CP21025
-  CP37
-  CP500
-  CP708
-  CP852
-  CP855
-  CP857
-  CP858
-  CP862
-  CP864
-  CP866
-  CP869
-  CP870
-  CP875


 <a name="west" />


## <a name="west"></a>Oeste de

-  CP10000
-  CP10079
-  CP1250
-  CP1252
-  CP1253
-  CP28592
-  CP28593
-  CP28597
-  CP28605
-  CP437
-  CP850
-  CP860
-  CP861
-  CP863
-  CP865

