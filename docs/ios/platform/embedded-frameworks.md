---
title: Marcos incrustados en Xamarin.iOS
description: Este documento describe cómo compartir código con marcos incrustados en una aplicación de Xamarin.iOS. Esto puede hacerse con la herramienta de mtouch o las referencias nativas.
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: e42f0940fe3fc132c9d381907aad5afbe474c4ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787297"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Marcos incrustados en Xamarin.iOS

_Este documento describe cómo los desarrolladores de aplicaciones pueden incrustar los marcos de trabajo de usuario en sus aplicaciones._

Con iOS 8.0 Apple posibilita crear un marco de trabajo incrustado para compartir código entre las extensiones de aplicación y la aplicación principal en Xcode.

Xamarin.iOS 9.0 agrega compatibilidad para utilizar estos marcos incrustados (creados con Xcode) en aplicaciones de Xamarin.iOS. *Hará lo siguiente **no** que pueda crear marcos incrustados desde cualquier tipo de proyectos de Xamarin.iOS, solo consumen existentes marcos (Objective-C) nativo.*

Hay dos maneras de utilizar marcos de trabajo en Xamarin.iOS:

- Pase el marco de trabajo a la herramienta mtouch, debe agregar lo siguiente a los argumentos de mtouch adicionales en el proyecto **iOS compilación** opciones:

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  Esto debe establecerse para cada configuración de proyecto.

- Agregue las referencias nativas en el menú contextual

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Haga doble clic en el proyecto y vaya a agregar las referencias nativas

![](embedded-frameworks-images/xam-native-refs.png "Seleccione las referencias nativas de complemento de Visual Studio para Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Haga doble clic en el proyecto y vaya a agregar las referencias nativas

![](embedded-frameworks-images/vs-native-refs.png "Seleccione las referencias nativas de complemento de Visual Studio")

-----

  Esto funciona para todas las configuraciones.

En versiones futuras de Visual Studio para Mac y las herramientas de Xamarin para Visual Studio será posible utilizar marcos de trabajo desde el IDE (sin necesidad de editar manualmente archivos de proyecto).

Algunos proyectos de ejemplo pueden encontrarse en [github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>Limitaciones

- Marcos incrustados solo se admiten en [unificado](~/cross-platform/macios/unified/index.md) proyectos.
- Marcos incrustados solo se admiten en los proyectos con un destino de implementación de como mínimo iOS 8.0.
- Si una extensión requiere un marco de trabajo incrustado, la aplicación de contenedor también debe tener una referencia para el marco de trabajo, en caso contrario que el marco de trabajo no se incluirá en el paquete de aplicación.

## <a name="the-mono-runtime"></a>El tiempo de ejecución Mono

Internamente Xamarin.iOS aprovecha las ventajas de esta característica para vincular con el tiempo de ejecución Mono como marco de trabajo, en lugar de vincular estáticamente el tiempo de ejecución Mono en cada extensión y la aplicación de contenedor.

Esto se realiza automáticamente si la aplicación de contenedor es una aplicación unificado, contiene las extensiones y la implementación de destino es iOS 8.0 o posterior.

Aplicaciones sin extensiones aún se vinculan con el tiempo de ejecución Mono estáticamente, porque no hay una reducción del tamaño para el uso de un marco de trabajo si hay solo una aplicación hace referencia a él.

Este comportamiento puede reemplazarse por el desarrollador de aplicaciones, debe agregar lo siguiente como argumento mtouch adicionales en las opciones de compilación de iOS del proyecto:

- `--mono:static`: Vincula estáticamente con el tiempo de ejecución Mono.
- `--mono:framework`: Vínculos con el tiempo de ejecución Mono como un marco de trabajo.

Un escenario para vincular con el tiempo de ejecución Mono como marco de trabajo incluso para las aplicaciones sin extensiones es reducir el tamaño del archivo ejecutable, para superar las restricciones de tamaño de que Apple se aplica en el archivo ejecutable. Como referencia, el tiempo de ejecución Mono agrega aproximadamente 1.7MB por arquitectura (como de Xamarin.iOS 8.12, sin embargo su varía entre versiones e incluso entre aplicaciones). El marco de trabajo de Mono agrega aproximadamente 2.3MB por arquitectura, lo que significa que para una aplicación de arquitectura única sin las extensiones, haciendo que el vínculo de la aplicación con el tiempo de ejecución Mono resultante como un marco de trabajo se reduce el archivo ejecutable por ~1.7MB, pero agregar un marco de trabajo ~2.3MB, en todas de aplicación más grande de ~0.6MB.

