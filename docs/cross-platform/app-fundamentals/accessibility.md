---
title: Accesibilidad en aplicaciones de Xamarin
description: Este documento proporciona algunas sugerencias para la creación de aplicaciones accesibles. Por ejemplo, incluye recomendaciones sobre fuentes grandes, contraste alto, interfaces autodescriptivas y mucho más.
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0ec264e0f3d381fdac46c79dd479da2bc768954f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282194"
---
# <a name="accessibility-in-xamarin-apps"></a>Accesibilidad en aplicaciones de Xamarin

_Asegúrese de que sus aplicaciones sean utilizables por el público más amplio posible_

Accesibilidad hace referencia al concepto de diseño de interfaces de usuario de la aplicación que funcionan bien características de visualización y entrada asistencia del sistema operativo, como el tipo grande, contraste alto, acercar, pantalla de lectura (texto a voz), pilas de comentarios hápticos o visual, y métodos de entrada alternativos.

Plataformas de escritorios y móviles como iOS, Android y Windows proporcionan integrada en las API que ayudan a los desarrolladores crear aplicaciones accesibles, como [Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) y [VoiceOver de Apple](http://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>API específicas de plataforma

Para implementar las instrucciones de este documento, use las API proporcionadas por cada plataforma:

- [**Accesibilidad de Android**](~/android/app-fundamentals/accessibility.md)
- [**Accesibilidad de iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Accesibilidad de OS X**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>Lista de comprobación de accesibilidad

Siga estas sugerencias para garantizar que sus aplicaciones sean accesibles para el público más amplio posible. Consulte la [lista de comprobación de pruebas de accesibilidad de Android](https://developer.android.com/training/accessibility/testing.html) y [página de accesibilidad de Apple](http://www.apple.com/accessibility/) para obtener más información.

### <a name="support-large-fonts-and-high-contrast"></a>Compatibilidad con fuentes grandes y contraste alto

Evite las dimensiones del control de codificar y, en su lugar, prefiere los diseños que se pueden cambiar el tamaño para dar cabida a tamaños de fuente mayores.
Probar combinaciones de colores en modo de alto contraste para asegurarse de que son legibles.

### <a name="make-the-user-interface-self-describing"></a>Asegúrese de que el usuario interfaz autodescriptivos

Etiquetar todos los elementos de la interfaz de usuario con texto descriptivo y sugerencias que son compatibles con la API de lectura en cada plataforma de pantalla.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>Asegúrese de que las imágenes e iconos tienen una descripción de texto alternativo

Imágenes e iconos que forman parte de la interfaz de usuario de la aplicación (como botones o indicadores de estado, por ejemplo) se deben etiquetar con una descripción accesible.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>Diseño el árbol visual con navegación accesible en mente

Usar controles de diseño apropiado o las API para que navegar entre los controles mediante métodos de entrada alternativos sigue el mismo flujo lógico como con la pantalla táctil.

Excluir elementos innecesarios de los lectores de pantalla (las imágenes decorativas o etiquetas para los campos que ya son accesibles, por ejemplo).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>No se basan en las pistas de audio o de color por sí solo

Evitar situaciones donde la única indicación de progreso, finalización o algún otro estado es un sonido o cambio de color. O bien, diseñar la interfaz de usuario para incluir indicaciones visuales claro (con sonido y color de refuerzo solo), o agregar indicadores específicos de accesibilidad.

Al elegir los colores, intente evitar una paleta que sea difícil distinguir para los usuarios con color rojo.

### <a name="captioning-for-video-text-for-audio"></a>Subtítulos (CC) para el texto de vídeo, audio

Proporcionar leyendas para el contenido de vídeo y un script legible para el contenido de audio. También es útil proporcionar los controles que ajustar la velocidad del contenido de audio o vídeo y garantizar ese volumen y son fáciles de encontrar y usar los botones Reproducir/pausar.

### <a name="localize"></a>Localize

Descripciones de accesibilidad puede (y debe) pueden localizadas donde la aplicación admite varios idiomas.



## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de Android](~/android/app-fundamentals/accessibility.md)
- [Accesibilidad de iOS](~/ios/app-fundamentals/accessibility.md)
- [Accesibilidad de OS X](~/mac/app-fundamentals/accessibility.md)
- [Accesibilidad de Xamarin.Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
