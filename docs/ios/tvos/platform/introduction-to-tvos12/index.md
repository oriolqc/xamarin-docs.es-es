---
title: Introducción a tvOS 12
description: Este documento proporciona un alto nivel de información general de las características nuevas y actualizadas en tvOS 12 para la versión de vista previa de qué Xamarin actualmente proporciona enlaces de C#.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: f7fb8cc379a070b848c5154c9c1d4fbfc8186266
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615208"
---
# <a name="introduction-to-tvos-12"></a>Introducción a tvOS 12

Este documento proporciona una descripción general de nuevos y actualizados tvOS 12.

Para empezar a crear aplicaciones de 12 de tvOS con Xamarin, eche un vistazo a la [Guía de introducción](~/ios/platform/introduction-to-ios12/get-started.md).

## <a name="tvuikit"></a>TVUIKit

tvOS 12 incluye TVUIKit, un conjunto de API que facilitan a los desarrolladores de tvOS usar controles de tvOS comunes como vistas de póster, los botones de título, las vistas de la tarjeta y Monograma vistas. tvOS 12 también presenta una propiedad que permite que las etiquetas desplazar el texto que es demasiado largo para que sean completamente visibles.

## <a name="password-autofill"></a>Relleno automático de contraseña

Con tvOS 12, los usuarios pueden usar sus dispositivos iOS para iniciar sesión en una aplicación de tvOS con un solo toque. Esto se habilita a través de una combinación de `UITextContentType` campos de uso para especificar el nombre de usuario y contraseña, los dominios asociados para establecer una relación entre los entornos de enfoque preferido para seleccionar un elemento que se va a recibir el foco después de que un usuario y una aplicación de tvOS y una aplicación de iOS Proporciona un nombre de usuario y una contraseña.

## <a name="focus-engine-enhancements"></a>Mejoras del motor de foco

tvOS 12 permite todas las aplicaciones, independientemente de cómo se representan, para interactuar con el motor de foco. A través de interacciones del usuario con el control remoto de Siri, el motor de enfoque puede utilizarse con cualquier aplicación para seleccionar un elemento, sugerencia de cambios de foco posibles y actualizar naturalmente foco. Esta opción está habilitada en las aplicaciones personalizadas a través de UIKit `IUIFocusItemContainer` interfaz, el `UIFocusMovementHint` (clase), el `IUIFocusItemScrollableContainer` interfaz y otras clases relacionadas y métodos.

## <a name="vision-framework"></a>Marco de trabajo de visión

El marco de trabajo de visión incluye un detector de caras mejorada que puede detectar caras en las distintas orientaciones. Además, las revisiones de la solicitud ahora pueden utilizarse para seleccionar una revisión concreta de algoritmo de marco de trabajo de visión.

## <a name="natural-language-framework"></a>Marco de lenguaje natural

El marco de lenguaje Natural permite que las aplicaciones realizar varios tipos de análisis de lenguaje. Por ejemplo, puede usarse para identificar las partes de la oración y determinar el idioma que se representa mediante un bloque de texto.

## <a name="deprecations"></a>Elementos obsoletos

Con 12 tvOS, Apple ha dejado de OpenGL ES, [alientan a los desarrolladores](https://developer.apple.com/tvos/whats-new/) adoptar el sistema operativo.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS: desarrollador de Apple (Apple)](https://developer.apple.com/tvos/)
- [Novedades de tvOS 12 (Apple) (vídeo)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)