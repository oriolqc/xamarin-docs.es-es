---
title: Reproducir sonido de tvOS con AVAudioPlayer en Xamarin
description: En este artículo se muestra cómo usar una clase auxiliar para controlar la reproducción del sonido mediante un AVAudioPlayer en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 2ce1d4b8564ef9599581aabd6a72ba3af12ec251
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241356"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>Reproducir sonido de tvOS con AVAudioPlayer en Xamarin

## <a name="about-the-avaudioplayer"></a>Acerca de la AVAudioPlayer

El `AVAudioPlayer` se usa para los datos de audio de reproducción de la memoria o un archivo. Apple recomienda el uso de esta clase para reproducir audio en su aplicación, a menos que se está realizando la red de transmisión por secuencias o requerir una E/S de audio de baja latencia.

Puede usar el `AVAudioPlayer` hacer lo siguiente:

- Reproducir sonidos de cualquier duración con bucle opcional.
- Reproducir sonidos varias al mismo tiempo con la sincronización opcional.
- Controlar el volumen, la velocidad de reproducción y la posición de estéreo para cada reproducción de sonidos.
- Admite características como avance rápido o rebobinado.
- Obtener datos de disponibilidad de nivel de reproducción.

`AVAudioPlayer` es compatible con los sonidos en cualquier formato de audio proporcionado por iOS, tvOS y OS X como `.aif`, `.wav` o `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Reproducir sonidos en tvOS

Porque tvOS es compatible con las mismas clases de cuadro de herramientas de Audio como iOS, consulte nuestra iOS [reproducción de sonidos con AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) documentación para los detalles completos de reproducir audio en una aplicación Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Referencia de AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
