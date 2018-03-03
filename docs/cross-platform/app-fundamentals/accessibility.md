---
title: Accesibilidad
description: "Asegúrese de que las aplicaciones son utilizables por el público más amplio posible"
ms.topic: article
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 88d3bf96fb0a19cf606a3869fa16ce565b352d06
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="accessibility"></a>Accesibilidad

_Asegúrese de que las aplicaciones son utilizables por el público más amplio posible_

Accesibilidad se refiere al concepto de diseñar interfaces de usuario de aplicación que funcionan bien características de asistencia de presentación y datos proporcionados por el sistema operativo como tipo de gran tamaño, contraste alto, acercar, lectura de pantalla (texto a voz), indicaciones de comentarios visuales o hápticos, y métodos de entrada alternativos.

Plataformas de escritorios y móviles como iOS, Android y Windows proporcionan integrada en las API que ayudan a los desarrolladores compilar aplicaciones accesibles, por ejemplo, [Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) y [VoiceOver de Apple](http://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>API específicas de plataforma

Para implementar las instrucciones de este documento, use las API proporcionadas por cada plataforma:

- [**Accesibilidad de Android**](~/android/app-fundamentals/accessibility.md)
- [**Accesibilidad de iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Accesibilidad de OS X**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>Lista de comprobación de accesibilidad

Siga estas sugerencias para asegurarse de que las aplicaciones están accesibles para el público más amplio posible. Extraer del repositorio el [lista de comprobación de pruebas de accesibilidad de Android](http://developer.android.com/training/accessibility/testing.html) y [página de accesibilidad de Apple](http://www.apple.com/accessibility/) para obtener información adicional.

### <a name="support-large-fonts-and-high-contrast"></a>Admite fuentes grandes y contraste alto

Evite las dimensiones de control de codificar y, en su lugar, prefieren diseños que se pueden cambiar el tamaño para dar cabida a los tamaños de fuente más grandes.
Probar combinaciones de colores en modo de alto contraste para asegurarse de que son legibles.

### <a name="make-the-user-interface-self-describing"></a>Asegúrese de que el usuario interfaz autodescriptivos

Etiqueta de todos los elementos de la interfaz de usuario con texto descriptivo y sugerencias que son compatibles con la API de lectura en cada plataforma de la pantalla.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>Asegúrese de que las imágenes e iconos tienen una descripción de texto alternativo

Imágenes e iconos que forman parte de la interfaz de usuario de la aplicación (por ejemplo, botones o indicadores de estado, por ejemplo) se deben etiquetar con una descripción accesible.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>Diseño el árbol visual con navegación accesible en cuenta

Usar controles de diseño adecuado o las API para que navegar entre controles mediante otros métodos de entrada sigue el mismo flujo lógico como con la pantalla táctil.

Excluir elementos innecesarios de los lectores de pantalla (imágenes decorativas o las etiquetas para los campos que están aún accesibles, por ejemplo).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>No confíe en señales de audio o color independientes

Evitar situaciones donde la única indicación de progreso, completado o algún otro estado es un sonido o un cambio de color. Ya sea diseñar la interfaz de usuario para incluir indicaciones visuales desactive (con sonido y color de refuerzo solo), o agregar indicadores de accesibilidad específico.

Cuando se eligen colores, intente evitar una paleta que sea difícil distinguir para los usuarios con ciegos de color.

### <a name="captioning-for-video-text-for-audio"></a>Subtítulos (CC) para el texto de vídeo, audio

Proporcionar leyendas para contenido de vídeo y un script legible para el contenido de audio. También es útil proporcionar controles que ajustar la velocidad del contenido de audio o vídeo y asegúrese de ese volumen y son fáciles de encontrar y utilizar los botones Reproducir/pausa.

### <a name="localize"></a>Localize

Descripciones de accesibilidad puede (y debe) se pueden localizar donde la aplicación es compatible con varios idiomas.



## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de Android](~/android/app-fundamentals/accessibility.md)
- [Accesibilidad de iOS](~/ios/app-fundamentals/accessibility.md)
- [Accesibilidad de OS X](~/mac/app-fundamentals/accessibility.md)
- [Accesibilidad de Xamarin.Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
