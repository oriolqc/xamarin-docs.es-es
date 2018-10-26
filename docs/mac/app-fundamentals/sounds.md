---
title: Reproducción de sonidos con AVAudioPlayer en Xamarin.Mac
description: Este documento describe cómo reproducir sonidos con AVAudioPlayer en una aplicación de Xamarin.Mac. Describe AVAudioPlayer en un alto nivel y vínculos a otra documentación que explora lo más completamente.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 10/19/2016
ms.openlocfilehash: 9aeb7bbfc2fddef1f690b5299ec060c475ea1ce7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120221"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>Reproducción de sonidos con AVAudioPlayer en Xamarin.Mac

## <a name="about-the-avaudioplayer"></a>Acerca de la AVAudioPlayer

La `AVAudioPlayer` clase se utiliza para los datos de audio de reproducción de la memoria o un archivo. Apple recomienda el uso de esta clase para reproducir audio en su aplicación, a menos que se está realizando la red de transmisión por secuencias o requerir una E/S de audio de baja latencia.

Puede usar el `AVAudioPlayer` clase para hacer lo siguiente:

- Reproducir sonidos de cualquier duración con bucle opcional.
- Reproducir sonidos varias al mismo tiempo con la sincronización opcional.
- Controlar el volumen, la velocidad de reproducción y la posición de estéreo para cada reproducción de sonidos.
- Admite características como avance rápido o rebobinado.
- Obtener datos de disponibilidad de nivel de reproducción.

`AVAudioPlayer` es compatible con sonidos en cualquier formato de audio proporcionado por iOS, tvOS y macOS como .aif, .wav o. mp3.

## <a name="playing-sounds-in-macos"></a>Reproducir sonidos en macOS

Dado que macOS es compatible con las mismas clases de cuadro de herramientas de Audio como iOS, consulte nuestra iOS [reproducción de sonidos con AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) documentación para los detalles completos de reproducir audio en una aplicación de Xamarin.Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia de AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
