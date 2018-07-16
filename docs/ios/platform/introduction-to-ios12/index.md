---
title: Introducción a iOS 12
description: Este documento proporciona una descripción detallada de algunas API de iOS 12 para la versión preliminar de Xamarin en qué versión proporciona enlaces de C#.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/08/2018
ms.openlocfilehash: 865a06e9fa430e195ce4ea3c6088785d9513dbf6
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030709"
---
# <a name="introduction-to-ios-12"></a>Introducción a iOS 12

![Vista previa](~/media/shared/preview.png)

> [!WARNING]
> Soporte técnico de 12 de iOS de Xamarin está actualmente en versión preliminar, no es lo que significa que puede contener errores, con todas las características, y puede cambiar. Usar solo para realizar experimentos.

> [!NOTE]
> - Revise el [Introducción](get-started.md) guía para obtener instrucciones sobre cómo empezar a crear aplicaciones de iOS 12 con Xamarin.
> - Para obtener más información, lea la versión preliminar de Xamarin [liberar la entrada de blog](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

Este documento proporciona una descripción detallada de algunas API de iOS 12 para la versión preliminar de Xamarin en qué versión proporciona enlaces de C#.

## <a name="arkit-2"></a>ARKit 2

ARKit es el marco de realidad aumentada incluido con iOS. ARKit 2 permite que varios usuarios interactúen entre sí en una escena de realidad aumentada, hace posible conservar los objetos en el espacio y volver a ellas en un momento posterior y proporciona reconocimiento de imágenes 2D y 3D y seguimiento de reconocimiento de objetos. 12 de iOS también proporciona buscar AR rápida, una manera de representar usdz modelos de cuentas por cobrar en sus aplicaciones.

## <a name="siri-shortcuts"></a>Métodos abreviados de Siri

Métodos abreviados de Siri permiten a los desarrolladores más profundamente integrar sus aplicaciones con Siri. Con los métodos abreviados de Siri, los usuarios pueden usar los comandos de voz para abrir el contenido o iniciar tareas en sus aplicaciones. Siri obtendrá información sobre cuando determinados métodos abreviados son más probables que se usa y se pueden sugerir al usuario a través de notificaciones.

## <a name="core-ml-2"></a>Core ML 2

Core ML 2 reduce el tamaño de la aplicación a través de cuantificación del modelo y modelos flexibles, mejora el rendimiento de la aplicación con una nueva API de predicción por lotes y utiliza modelos personalizados para admitir los avances en machine learning.

## <a name="notification-improvements"></a>Mejoras en la notificación

En iOS 12, agrupadas notificaciones permiten a las notificaciones de usuario presentes en la aplicación o agrupaciones de subprocesos. Texto de resumen se puede usar para proporcionar más información acerca de un grupo de notificación.

Las extensiones de contenido de notificación en iOS 12 permiten acciones dinámicas e interfaces de usuario personalizada. Estas características permiten experiencias más enriquecidas y más relevantes en las notificaciones de usuario.

## <a name="natural-language-framework"></a>Marco de lenguaje natural

El marco de lenguaje Natural permite que las aplicaciones realizar varios tipos de análisis de lenguaje. Por ejemplo, puede usarse para identificar las partes de la oración y determinar el idioma que se representa mediante un bloque de texto.

## <a name="carplay"></a>CarPlay

En iOS 12, las aplicaciones de terceros pueden entregar mapas e instrucciones de navegación de giro por en CarPlay mediante el nuevo marco CarPlay.

## <a name="automatic-strong-passwords"></a>Contraseñas seguras automática

iOS 12 sugerirá y almacenar nombres de usuario y contraseñas seguras para las aplicaciones que contienen una pantalla de la cuenta de crear. Las contraseñas sugeridas pueden generarse basándose en un valor predeterminado, el formato de 20 caracteres o según las reglas de la contraseña especificada por el desarrollador. Esta característica facilita el uso de dominios asociados y especifica los tipos de contenido en el nuevo nombre de usuario y los nuevos campos de contraseña.

## <a name="autofill-credential-provider-extensions"></a>Extensiones de proveedor de credenciales de autorrelleno

Con iOS 12, las aplicaciones de administrador de contraseñas de otros fabricantes pueden proporcionar una extensión para proporcionar los valores de nombre de usuario y contraseña a los campos de inicio de sesión.

## <a name="healthkit-updates"></a>Actualizaciones de HealthKit

iOS 11.3 introducidas [historias clínicas](https://www.apple.com/healthcare/health-records/), lo que permite a los usuarios descargar su salud registrar información de distintas instituciones sanitarias y verlo en sus dispositivos iOS. iOS 12 agrega las API que permiten a las aplicaciones de terceros para que accedan a estos datos.

## <a name="imessage-app-presentation-contexts"></a>iMessage contextos de presentación de aplicación

En iOS 12, las aplicaciones de iMessage admiten contextos de presentación, que permiten a las aplicaciones se ejecuten como una aplicación iMessage normal o en el contexto de una foto o un efecto de vídeo.

## <a name="related-links"></a>Vínculos relacionados

- [Prepárese para iOS 12 (Apple)](https://developer.apple.com/ios/)
- [12 de iOS versión preliminar (Apple)](https://www.apple.com/ios/ios-12-preview/)
- Versión preliminar de Xamarin [liberar la entrada de blog](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
