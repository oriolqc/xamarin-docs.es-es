---
title: Introducción a iOS 12
description: Este documento proporciona una descripción detallada de algunas API de iOS 12 para la versión preliminar de Xamarin en qué versión proporciona enlaces de C#.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/08/2018
ms.openlocfilehash: 81375e8c66e5504604d0d4cb3be34afd58f4269d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615153"
---
# <a name="introduction-to-ios-12"></a>Introducción a iOS 12

Este documento proporciona una descripción detallada de algunas API de iOS 12 para la versión preliminar de Xamarin en qué versión proporciona enlaces de C#.

Para empezar a compilar aplicaciones de iOS 12 con Xamarin, consulte el [Guía de introducción](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit es el marco de realidad aumentada incluido con iOS. ARKit 2 permite que varios usuarios interactúen entre sí en una escena de realidad aumentada, hace posible conservar los objetos en el espacio y volver a ellas en un momento posterior y proporciona reconocimiento de imágenes 2D y 3D y seguimiento de reconocimiento de objetos. 12 de iOS también proporciona buscar AR rápida, una manera de representar usdz modelos de cuentas por cobrar en sus aplicaciones.

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Métodos abreviados de Siri](siri-shortcuts.md)

Métodos abreviados de Siri permiten a los desarrolladores más profundamente integrar sus aplicaciones con Siri. Con los métodos abreviados de Siri, los usuarios pueden usar los comandos de voz para abrir el contenido o iniciar tareas en segundo plano o inician estas mismas tareas a través de los métodos abreviados que Siri se sugiere en la pantalla de bloqueo.

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

Core ML 2 reduce el tamaño de la aplicación a través de cuantificación del modelo y modelos flexibles, mejora el rendimiento de la aplicación con una nueva API de predicción por lotes y utiliza modelos personalizados para admitir los avances en machine learning.

## <a name="notification-improvementsnotificationsindexmd"></a>[Mejoras en la notificación](notifications/index.md)

En iOS 12, agrupadas notificaciones permiten a las notificaciones de usuario presentes en la aplicación o agrupaciones de subprocesos. Texto de resumen proporciona más información acerca de un grupo de notificación.

Las extensiones de contenido de notificación en iOS 12 permiten interfaces de usuario personalizada y botones de acción dinámica.

## <a name="natural-language-frameworknatural-languagemd"></a>[Marco de lenguaje natural](natural-language.md)

El marco de lenguaje Natural permite que las aplicaciones realizar varios tipos de análisis de lenguaje. Por ejemplo, puede identificar partes de la oración y determinar el idioma que se representa mediante un bloque de texto.

## <a name="vision-framework"></a>Marco de trabajo de visión

El marco de trabajo de visión incluye un detector de caras mejorada que puede detectar caras en las distintas orientaciones. Además, las revisiones de la solicitud pueden seleccionar revisión concreta de algoritmo de framework de visión.

## <a name="photo-and-video-apis"></a>API de vídeo y fotografías

En iOS 12, la segmentación de vertical API devuelve un mate de efectos vertical: una máscara lineal que describe el primer plano del fondo de una imagen vertical y es útil para crear diversos efectos de imagen. iOS 12 hace también es posible utilizar datos de profundidad de la cámara TrueDepth de efectos de vídeo en tiempo real.

## <a name="passwords"></a>Contraseñas

iOS 12 facilita que los usuarios y a los desarrolladores trabajar con las contraseñas:

- Relleno automático de contraseña y contraseñas seguras automática permiten generar, almacenar y utilizar contraseñas seguras en aplicaciones de iOS al registrarse para obtener e inicie sesión en una aplicación automáticamente.
- Relleno automático de código de seguridad hace posible usar códigos de autenticación basada en SMS sin manual cortar y pegar o la memorización.
- La `ASWebAuthenticationSession` clase simplifica el proceso de trabajo con los servicios de autenticación federada.
- Las extensiones de proveedor de credenciales de autorrelleno hacen posible para las aplicaciones de la contraseña de terceros proporcionar el nombre de usuario y contraseñas para los campos de inicio de sesión.

## <a name="healthkit-updates"></a>Actualizaciones de HealthKit

iOS 11.3 introducidas [historias clínicas](https://www.apple.com/healthcare/health-records/), lo que permite a los usuarios descargar su salud registrar información de distintas instituciones sanitarias y verlo en sus dispositivos iOS. iOS 12 agrega las API que permiten a las aplicaciones de terceros para que accedan a estos datos.

## <a name="imessage-app-presentation-contexts"></a>iMessage contextos de presentación de aplicación

En iOS 12, las aplicaciones de iMessage admiten contextos de presentación, que permiten a las aplicaciones se ejecuten como una aplicación iMessage normal o en el contexto de una foto o un efecto de vídeo.

## <a name="network-framework"></a>Marco de redes

Marco de redes, la red de la pila subyacente el `URLSession` API suele utilizarse en aplicaciones de iOS, ahora está disponible como un marco de trabajo independiente, lo que sea más fácil trabajar con TCP, UDP, TLS, IPv4 e IPv6 y mucho más.

## <a name="carplay"></a>CarPlay

En iOS 12, las aplicaciones de terceros pueden entregar mapas e instrucciones de navegación de giro por en CarPlay mediante el nuevo marco CarPlay.

## <a name="deprecations"></a>Elementos obsoletos

Con 12 iOS, Apple ha dejado de:

- OpenGL ES, [alientan a los desarrolladores](https://developer.apple.com/ios/whats-new/) adoptar el sistema operativo.
- [`UIWebView`](https://developer.xamarin.com/api/type/UIKit.UIWebView/), [en favor de `WKWebView` ](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

## <a name="related-links"></a>Vínculos relacionados

- [Prepárese para iOS 12 (Apple)](https://developer.apple.com/ios/)
