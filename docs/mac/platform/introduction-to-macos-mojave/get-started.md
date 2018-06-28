---
title: Introducción a macOS Mojave
description: Este documento describe cómo realizar las hasta compilación macOS Mojave aplicaciones con Xamarin.Mac. Describe cómo descargar el 10 de Xcode y actualizar Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: ee5269bf314401328d0184631e817b37ce091479
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067394"
---
# <a name="getting-started-with-macos-mojave"></a>Introducción a macOS Mojave

![Vista previa](~/media/shared/preview.png)

> [!WARNING]
> MacOS de Xamarin soporte Mojave está actualmente en vista previa, lo que significa que puede contener errores, no es completar característica y, puede cambiar.
> Utilizar para la experimentación solo.

> [!NOTE]
> Para obtener más información, lea la [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/) para el Xamarin versión preliminar.

Este documento describe cómo realizar las hasta compilación macOS Mojave aplicaciones con Xamarin.Mac. Describe cómo descargar el 10 de Xcode y actualizar Visual Studio para Mac.

## <a name="download-and-install"></a>Descargue e instale

1. **Instale la última versión beta de 10 Xcode** : los desarrolladores de Apple registrado pueden descargar e instalar la versión más reciente de 10 Xcode desde el [Portal para desarrolladores de Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > MacOS que Mojave SDK se distribuye con Xcode 10.

2. **Ejecutar Xcode 10** – ejecutar Xcode 10 antes de actualizar y ejecutar Visual Studio para Mac; instala algunas herramientas que requiere de Xamarin.

3. **Actualización de Visual Studio para Mac** : siga las instrucciones de la [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/) para instalar la versión preliminar de Xamarin.

4. _(opcional)_  **Instalar la última versión beta de Mojave de macOS en su Mac** : para probar aplicaciones de Xamarin.Mac que usan acaban de introducir macOS Mojave API, puede registrado de los desarrolladores de Apple [descargar](https://developer.apple.com/download/) e instale la última macOS Mojave developer versión beta.

   > [!TIP]
   > Incluso si la aplicación no usa ninguna nueva macOS Mojave API, asegúrese de crear con el SDK de Mojave (instalado con Xcode 10) macOS y probarlo para asegurarse de que todo funciona según lo previsto. Si una aplicación no llama a las nuevas API, puede volver a compilar con el SDK de Mojave macOS y probarlo sin actualizar el sistema operativo de su Mac.

   > [!IMPORTANT]
   > Antes de actualizar el equipo Mac a macOS Mojave para compilar y probar aplicaciones de Xamarin.Mac que llamen a nueva macOS Mojave API:
   > - Lectura [notas de la versión de Apple](https://developer.apple.com/download/) para la actualización del sistema operativo.
   > - Leer la [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/) para el Xamarin versión preliminar. Tenga en cuenta que esta primera vista previa no incluye enlaces para macOS nueva Mojave AppKit APIs (por ejemplo, el modo oscuro).

## <a name="related-links"></a>Vínculos relacionados

- [Descargar Xcode 10](https://developer.apple.com/download/)
- Vista previa de Xamarin [notas de la versión](https://releases.xamarin.com/preview-release-xcode-10-beta/)
