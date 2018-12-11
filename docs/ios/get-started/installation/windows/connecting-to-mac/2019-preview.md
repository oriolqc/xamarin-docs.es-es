---
title: Conexión de versiones preliminares de Visual Studio 2019 en Windows y macOS
description: En esta guía se ofrecen instrucciones sobre cómo usar la versión preliminar de Visual Studio 2019 en Windows para compilar aplicaciones de iOS, mientras se usa la versión preliminar de Visual Studio 2019 para Mac en macOS para hospedar las compilaciones.
ms.prod: xamarin
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/04/2018
ms.openlocfilehash: 1eeb79737bd712da64ce34a6b4fe83ce2823e3eb
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899368"
---
# <a name="visual-studio-2019-preview-pairing"></a>Emparejamiento de la versión preliminar de Visual Studio 2019

![Vista previa](~/media/shared/preview.png)

La [versión preliminar de Visual Studio para Mac 2019](https://docs.microsoft.com/visualstudio/mac/install-preview) es la primera versión con la que la instalación de Visual Studio en paralelo es totalmente compatible con macOS. Esto crea una nueva situación para desarrolladores de Windows que compilan aplicaciones de iOS: si el host de compilación de Mac tiene Visual Studio 2017 para Mac (versión 7.x) y la versión preliminar de Visual Studio 2019 para Mac (versión 8.0), ¿cuál funciona como host de compilación para su instalación de Windows?

_De forma predeterminada_, Visual Studio en Windows usará la versión estable, Visual Studio 2017 para Mac (versión 7.7), independientemente de que se use Visual Studio 2017 o la versión preliminar de Visual Studio 2019 en Windows.

Para probar correctamente las versiones preliminares de Visual Studio 2019 en Windows y macOS:

1. Instale y use la versión preliminar de Visual Studio 2019 (versión 16.0) en el equipo Windows.
2. Instale la versión preliminar de Visual Studio 2019 para Mac (versión 8.0) en el equipo Mac.
3. En **Visual Studio 2019 para Mac**:

    a. Vaya a **Visual Studio > Buscar actualizaciones**.

    b. En la ventana **Actualización de Visual Studio**, seleccione **Xamarin Preview** en el canal de actualización. Se genera la siguiente advertencia:

    > [!WARNING]
    > Pruebe las compilaciones más recientes de la versión preliminar de Visual Studio 2019 para Mac con el entorno de ejecución Mono y los SDK de Xamarin más recientes. **La instalación de compilaciones desde este canal interrumpirá la versión Visual Studio 2017 para Mac.** Use este canal solo si va a compilar aplicaciones de Xamarin en un sistema operativo Windows con las versiones preliminares de Visual Studio 2019.

    c. Pulse el botón **Switch channel** (Cambiar de canal) para habilitar el host de compilación de versión preliminar.

4. Siga las instrucciones de [Emparejar con Mac para el desarrollo de Xamarin.iOS](index.md) y las [sugerencias para solucionar problemas](troubleshooting.md) (si es necesario).