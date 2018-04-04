---
title: Reproducir sonido con AVAudioPlayer
description: Este artículo muestra cómo utilizar una clase auxiliar para controlar la reproducción del sonido mediante un AVAudioPlayer.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: 25e3285a1da1b6a112629001d5412fdd0788c705
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="playing-sound-with-avaudioplayer"></a>Reproducir sonido con AVAudioPlayer

_Este artículo muestra cómo utilizar una clase auxiliar para controlar la reproducción del sonido mediante un AVAudioPlayer._

## <a name="about-the-avaudioplayer"></a>Acerca de la AVAudioPlayer

La `AVAudioPlayer` clase se utiliza para los datos de audio de reproducción de la memoria o un archivo. Apple recomienda el uso de esta clase para reproducir audio en la aplicación a menos que se está realizando la transmisión por secuencias de red o requieren E/S de audio de latencia baja.

Puede usar el `AVAudioPlayer` clase para hacer lo siguiente:

- Reproducir sonidos de cualquier duración con bucle opcional.
- Reproducir sonidos varias al mismo tiempo con la sincronización opcional.
- Controlar el volumen, la velocidad de reproducción y la posición de estéreo para cada reproducción de sonidos.
- Admite características como avance rápido o rebobinado.
- Obtener datos de disponibilidad de nivel de reproducción.

`AVAudioPlayer` admite sonidos en cualquier formato de audio proporcionada iOS, tvOS y macOS como .aif, .wav o. mp3.

## <a name="playing-sounds-in-macos"></a>Reproducir sonidos en macOS

Porque macOS es compatible con las mismas clases de cuadro de herramientas de Audio como iOS, visite nuestro iOS [sonido de juego con AVAudioPlayer](https://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/) documentación para los detalles completos de reproducir audio en una aplicación Xamarin.Mac.



## <a name="related-links"></a>Vínculos relacionados

- [Referencia de AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
