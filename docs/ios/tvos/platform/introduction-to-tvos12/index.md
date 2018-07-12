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
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38847566"
---
# <a name="introduction-to-tvos-12"></a>Introducción a tvOS 12

![Vista previa](~/media/shared/preview.png)

> [!WARNING]
> Soporte técnico de 12 de tvOS de Xamarin está actualmente en versión preliminar, no es lo que significa que puede contener errores, con todas las características, y puede cambiar. Usar solo para realizar experimentos.

> [!NOTE]
> - Revise el [Introducción](~/ios/platform/introduction-to-ios12/get-started.md) guía para obtener instrucciones sobre cómo empezar a crear aplicaciones de iOS 12 y 12 de tvOS con Xamarin.
> - Para obtener más información, lea el [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/) para Xamarin de versión preliminar.

Este documento proporciona una descripción general de tvOS nuevos y actualizados 12 características de vista previa de qué Xamarin versión actualmente ofrece enlaces de C#.

## <a name="password-autofill"></a>Relleno automático de contraseña

Con tvOS 12, los usuarios pueden usar sus dispositivos iOS para iniciar sesión en una aplicación de tvOS con un solo toque. Esto se habilita a través de una combinación de `UITextContentType` campos de uso para especificar el nombre de usuario y contraseña, los dominios asociados para establecer una relación entre los entornos de enfoque preferido para seleccionar un elemento que se va a recibir el foco después de que un usuario y una aplicación de tvOS y una aplicación de iOS Proporciona un nombre de usuario y una contraseña.

## <a name="focus-engine-enhancements"></a>Mejoras del motor de foco

tvOS 12 permite todas las aplicaciones, independientemente de cómo se representan, para interactuar con el motor de foco. A través de interacciones del usuario con el control remoto de Siri, el motor de enfoque puede utilizarse con cualquier aplicación para seleccionar un elemento, sugerencia de cambios de foco posibles y actualizar naturalmente foco. Esta opción está habilitada en las aplicaciones personalizadas a través de UIKit `IUIFocusItemContainer` interfaz, el `UIFocusMovementHint` (clase), el `IUIFocusItemScrollableContainer` interfaz y otras clases relacionadas y métodos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS: desarrollador de Apple (Apple)](https://developer.apple.com/tvos/)
- [Novedades de tvOS 12 (Apple) (vídeo)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)
- Versión preliminar de Xamarin [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/)
