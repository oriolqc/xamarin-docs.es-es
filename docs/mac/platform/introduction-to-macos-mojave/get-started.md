---
title: Introducción a macOS Mojave
description: Este documento describe cómo configurar hasta las compilación macOS Mojave aplicaciones con Xamarin.Mac. Describe cómo descargar Xcode 10 y actualizar Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: ee5269bf314401328d0184631e817b37ce091479
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831350"
---
# <a name="getting-started-with-macos-mojave"></a>Introducción a macOS Mojave

![Vista previa](~/media/shared/preview.png)

> [!WARNING]
> MacOS de Xamarin soporte técnico de Mojave está actualmente en versión preliminar, lo que significa que puede contener errores, no es con todas las características y, puede cambiar.
> Usar solo para realizar experimentos.

> [!NOTE]
> Para obtener más información, lea el [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/) para Xamarin de versión preliminar.

Este documento describe cómo configurar hasta las compilación macOS Mojave aplicaciones con Xamarin.Mac. Describe cómo descargar Xcode 10 y actualizar Visual Studio para Mac.

## <a name="download-and-install"></a>Descarga e instalación

1. **Instale la última versión beta de Xcode 10** : los desarrolladores de Apple registrado pueden descargar e instalar la versión más reciente de Xcode 10 desde la [Portal Apple Developer](https://developer.apple.com/download/).

   > [!NOTE]
   > El SDK de Mojave macOS se distribuye con Xcode 10.

2. **Ejecutar Xcode 10** – ejecutar Xcode 10 antes de actualizar y ejecutar Visual Studio para Mac; algunas herramientas de Xamarin requiere instala.

3. **Actualización de Visual Studio para Mac** : siga las instrucciones de la [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/) para instalar la versión preliminar de Xamarin.

4. _(opcional)_  **Instalar la última versión beta de Mojave de macOS en su Mac** : para probar las aplicaciones de Xamarin.Mac que usan macOS incorporadas recientemente a las API de Mojave, registrado pueden los desarrolladores de Apple [descargar](https://developer.apple.com/download/) e instale la beta más reciente de macOS Mojave para desarrolladores.

   > [!TIP]
   > Incluso si la aplicación no utiliza cualquier nueva macOS Mojave API, asegúrese de compilarlo con el SDK de Mojave (instalado con Xcode 10) de macOS y probarlo para asegurarse de que todo funciona según lo previsto. Si una aplicación no llama a ninguna API nueva, puede compilarla con el SDK de Mojave macOS y probarlo sin actualizar el sistema operativo de su equipo Mac.

   > [!IMPORTANT]
   > Antes de actualizar el equipo Mac a macOS Mojave para compilar y probar aplicaciones de Xamarin.Mac que llaman a macOS nuevas API de Mojave:
   > - Lectura [notas de la versión de Apple](https://developer.apple.com/download/) para la actualización del sistema operativo.
   > - Leer el [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/) para Xamarin de versión preliminar. Tenga en cuenta que esta primera versión preliminar no incluye enlaces para macOS nuevo Mojave AppKit APIs (por ejemplo, de modo oscuro).

## <a name="related-links"></a>Vínculos relacionados

- [Descargar Xcode 10](https://developer.apple.com/download/)
- Versión preliminar de Xamarin [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/)
