---
title: Reproducir sonido con AVAudioPlayer en Xamarin.Mac
description: Este documento describe cómo reproducir un sonido con AVAudioPlayer en una aplicación Xamarin.Mac. Se trata de AVAudioPlayer en un alto nivel y vínculos a otra documentación que lo explora más completa.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: 9e5b9ec43189999f8a0aee29eb50221b494e2133
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791860"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>Reproducir sonido con AVAudioPlayer en Xamarin.Mac

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
