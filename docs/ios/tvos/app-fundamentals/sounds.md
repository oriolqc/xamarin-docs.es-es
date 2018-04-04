---
title: Reproducir sonido con AVAudioPlayer
description: Este artículo muestra cómo utilizar una clase auxiliar para controlar la reproducción del sonido mediante un AVAudioPlayer.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c50aea9c4c35e91c2baa98c94db2fd7c61136d69
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="playing-sound-with-avaudioplayer"></a>Reproducir sonido con AVAudioPlayer

_Este artículo muestra cómo utilizar una clase auxiliar para controlar la reproducción del sonido mediante un AVAudioPlayer._

## <a name="about-the-avaudioplayer"></a>Acerca de la AVAudioPlayer

El `AVAudioPlayer` se usa para los datos de audio de reproducción de memoria o un archivo. Apple recomienda el uso de esta clase para reproducir audio en la aplicación a menos que se está realizando la transmisión por secuencias de red o requieren E/S de audio de latencia baja.

Puede usar el `AVAudioPlayer` para hacer lo siguiente:

- Reproducir sonidos de cualquier duración con bucle opcional.
- Reproducir sonidos varias al mismo tiempo con la sincronización opcional.
- Controlar el volumen, la velocidad de reproducción y la posición de estéreo para cada reproducción de sonidos.
- Admite características como avance rápido o rebobinado.
- Obtener datos de disponibilidad de nivel de reproducción.

`AVAudioPlayer` admite sonidos en cualquier formato de audio proporcionado por iOS, tvOS y OS X como `.aif`, `.wav` o `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Reproducir sonidos en tvOS

Porque tvOS es compatible con las mismas clases de cuadro de herramientas de Audio como iOS, visite nuestro iOS [sonido de juego con AVAudioPlayer](http://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/) documentación para los detalles completos de reproducir audio en una aplicación Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Referencia de AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
