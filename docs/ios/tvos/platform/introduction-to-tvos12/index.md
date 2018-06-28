---
title: Introducción a tvOS 12
description: Este documento proporciona un alto nivel de información general de las características nuevas y actualizadas en tvOS 12 para la versión de vista previa de qué Xamarin actualmente proporciona enlaces de C#.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 03841306ba54e511dbf2f2b86a7c17e9f4669bcd
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067381"
---
# <a name="introduction-to-tvos-12"></a>Introducción a tvOS 12

![Vista previa](~/media/shared/preview.png)

> [!WARNING]
> Compatibilidad de 12 de tvOS de Xamarin está actualmente en vista previa, lo que significa que puede contener errores, no está completo de características, y puede cambiar. Utilizar para la experimentación solo.

> [!NOTE]
> - Revise el [Introducción](~/ios/platform/introduction-to-ios12/get-started.md) guía para obtener instrucciones sobre cómo empezar a compilar iOS 12 y tvOS 12 aplicaciones con Xamarin.
> - Para obtener más información, lea la [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/) para el Xamarin versión preliminar.

Este documento proporciona una descripción general de tvOS nuevos y actualizados 12 características para la vista previa de qué Xamarin versión actualmente proporciona enlaces de C#.

## <a name="password-autofill"></a>Contraseña autorrelleno

Con tvOS 12, los usuarios pueden utilizar sus dispositivos de iOS para iniciar sesión en una aplicación tvOS con un único punteo. Esto se habilita a través de una combinación de `UITextContentType` campos de uso para especificar el nombre de usuario y contraseña, los dominios asociados para establecer una relación entre una aplicación de iOS y una aplicación de tvOS y entornos de foco preferido para seleccionar un elemento para recibir el foco después de que un usuario Proporciona un nombre de usuario y una contraseña.

## <a name="focus-engine-enhancements"></a>Mejoras del motor de foco

tvOS 12 permite todas las aplicaciones, con independencia de cómo se representan, para interactuar con el motor de foco. A través de interacciones del usuario con el control remoto Siri, se puede utilizar el motor de foco con cualquier aplicación para seleccionar un elemento, revele cambios de enfoque posible y naturalmente actualizar foco. Esta opción está habilitada en aplicaciones personalizadas a través del UIKit `IUIFocusItemContainer` interfaz, el `UIFocusMovementHint` (clase), el `IUIFocusItemScrollableContainer` , interfaz y de otros métodos y clases relacionadas.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – para desarrolladores de Apple (Apple)](https://developer.apple.com/tvos/)
- [Novedades de tvOS 12 (Apple) (vídeo)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)
- Vista previa de Xamarin [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/)
