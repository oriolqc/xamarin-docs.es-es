---
title: Plataformas admitidas en Xamarin.iOS
description: Este documento describe cómo compartir código con las plataformas admitidas en una aplicación de Xamarin.iOS. Esto puede hacerse con la herramienta mtouch o las referencias nativas.
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2018
ms.openlocfilehash: cce5356fd1d3d9a5cf16370a4843c3541b00a7c0
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351439"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Plataformas admitidas en Xamarin.iOS

_Este documento describe cómo los desarrolladores de aplicaciones pueden incrustar los marcos de trabajo de usuario en sus aplicaciones._

Con iOS 8.0 Apple posible crear un marco de trabajo incrustado para compartir código entre las extensiones de aplicación y la aplicación principal en Xcode.

Xamarin.iOS 9.0 agrega compatibilidad para el uso de estos marcos incrustados (creados con Xcode) en aplicaciones de Xamarin.iOS. *Lo hará **no** ser posible crear marcos incrustados desde cualquier tipo de proyectos de Xamarin.iOS, solo se consumen los marcos nativos (Objective-C) existentes.*

Hay dos formas de consumir los marcos de trabajo en Xamarin.iOS:

- Pase el marco de trabajo a la herramienta mtouch, agregando lo siguiente a los argumentos mtouch adicionales en el proyecto **compilación de iOS** opciones:

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  Esto tiene que establecerse para cada configuración de proyecto.

- Agregue las referencias nativas en el menú contextual

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Haga doble clic en el proyecto y vaya a agregar las referencias nativas

![](embedded-frameworks-images/xam-native-refs.png "Seleccionar Agregar referencias nativas en Visual Studio para Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Haga doble clic en el proyecto y vaya a agregar las referencias nativas

![](embedded-frameworks-images/vs-native-refs.png "Seleccionar Agregar referencias nativas en Visual Studio")

-----

  Esto funcionará para todas las configuraciones.

En versiones futuras de Visual Studio para Mac y las herramientas de Xamarin para Visual Studio será posible consumir los marcos de trabajo desde el IDE (sin necesidad de editar manualmente archivos de proyecto).

Algunos proyectos de ejemplo pueden encontrarse en [github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>Limitaciones

- Plataformas admitidas solo se admiten en [unificados](~/cross-platform/macios/unified/index.md) proyectos.
- Plataformas admitidas solo se admiten en los proyectos con un destino de implementación de al menos iOS 8.0.
- Si una extensión requiere un marco de trabajo incrustada, la aplicación de contenedor también debe tener una referencia para el marco de trabajo, en caso contrario, que el marco de trabajo no se incluirán en el lote de aplicaciones.

## <a name="the-mono-runtime"></a>El tiempo de ejecución Mono

Internamente Xamarin.iOS aprovecha las ventajas de esta característica para vincular con el tiempo de ejecución Mono como un marco de trabajo, en lugar de vincular el tiempo de ejecución Mono estáticamente en cada extensión y la aplicación contenedora.

Esto se hace automáticamente si la aplicación de contenedor es una aplicación unificada, contiene las extensiones y la implementación de destino es iOS 8.0 o posterior.

Aplicaciones sin extensiones todavía se vincularán con el tiempo de ejecución Mono estáticamente, porque no hay una reducción del tamaño para el uso de un marco de trabajo si se produce solo una aplicación que haga referencia a él.

Este comportamiento puede reemplazarse por el desarrollador de aplicaciones, debe agregar lo siguiente como argumentos mtouch adicionales en las opciones de compilación de iOS del proyecto:

- `--mono:static`: Vincula estáticamente con el tiempo de ejecución Mono.
- `--mono:framework`: Vínculos con el tiempo de ejecución Mono como un marco de trabajo.

Un escenario para la vinculación con el tiempo de ejecución Mono como un marco incluso para las aplicaciones sin extensiones es reducir el tamaño del archivo ejecutable, para solucionar cualquier restricción de tamaño que Apple exige en el archivo ejecutable. Como referencia, el tiempo de ejecución Mono agrega aproximadamente 1,7 MB por arquitectura (como el de Xamarin.iOS 8.12, pero su varía entre versiones e incluso entre las aplicaciones). El marco de trabajo Mono agrega aproximadamente 2,3 MB por arquitectura, lo que significa que para una aplicación única arquitectura sin extensiones, haciendo que el vínculo de la aplicación con el tiempo de ejecución Mono resultante como un marco de trabajo se reduce el archivo ejecutable por ~1.7MB, pero agregar un marco de trabajo ~2.3MB, en todas de aplicación más grande de ~0.6MB.

