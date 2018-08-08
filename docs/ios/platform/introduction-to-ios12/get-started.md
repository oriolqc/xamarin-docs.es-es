---
title: Introducción a iOS 12, 12 de tvOS y watchOS 5
description: Este documento describe cómo configurar hasta las compilación iOS 12, 12 de tvOS y watchOS 5 aplicaciones con Xamarin. Describe cómo descargar Xcode 10 y actualizar Visual Studio para Mac y Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/07/2018
ms.openlocfilehash: cb84ddc646933d253ca72fe8f9f581364aab698b
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615179"
---
# <a name="getting-started-with-ios-12-tvos-12-and-watchos-5"></a>Introducción a iOS 12, 12 de tvOS y watchOS 5

![Vista previa](~/media/shared/preview.png)

> [!WARNING]
> Compatibilidad con Xamarin el 12 de iOS, tvOS 12 y SDK de watchOS 5 distribuidos con Xcode 10 está actualmente en versión preliminar, lo que significa que los que puede contener errores, no es característica completa y puede cambiar. Usar solo para realizar experimentos.

Este documento describe cómo configurar la aplicación para crear aplicaciones de Xamarin que llaman a API publicadas con Xcode 10. Describe cómo descargar Xcode 10 y actualizar Visual Studio para Mac y Visual Studio 2017.

## <a name="download-and-install"></a>Descarga e instalación

1. **Instale la última versión beta de Xcode 10** : los desarrolladores de Apple registrado pueden descargar e instalar la versión más reciente de Xcode 10 desde la [Portal Apple Developer](https://developer.apple.com/download/).

2. **Ejecutar Xcode 10** – ejecutar Xcode 10 antes de actualizar y ejecutar Visual Studio para Mac o Visual Studio 2017, cuando se instale algunas de las herramientas que Xamarin requiere.

3. **Actualización de Visual Studio para Mac y Visual Studio 2017** : siga las instrucciones de la [versión blog](https://releases.xamarin.com/preview-release-xcode-10-beta-5/) para instalar la versión preliminar de Xamarin.

4. _(opcional)_  **Instalar la última versión beta de iOS en los dispositivos iOS** : dispositivo de prueba de las aplicaciones que usan las API que se introdujo con el 10 de Xcode, registrado pueden los desarrolladores de Apple [descargar](https://developer.apple.com/download) e instalar la versión más reciente versiones beta de desarrollador en sus dispositivos.

   > [!TIP]
   > Incluso si la aplicación no utiliza ninguna API nueva, asegúrese de compilarlo con los SDK de 10 más reciente de Xcode y probarlo para asegurarse de que todo funciona según lo previsto. Si una aplicación no llama a ninguna API nueva, puede compilarla con estos nuevos SDK y probarla en los dispositivos que aún no se han actualizado al nuevo sistema operativo.
   >
   > Antes de actualizar los dispositivos al sistema operativo más reciente que se libere de Apple para probar sus aplicaciones de Xamarin, no olvide:
   >
   > - Lectura [notas de la versión de Apple](https://developer.apple.com/download/) para las actualizaciones del sistema operativo.
   > - Leer la versión preliminar de Xamarin [liberar la entrada de blog](https://releases.xamarin.com/preview-release-xcode-10-beta-5/).

## <a name="related-links"></a>Vínculos relacionados

- [Descargar Xcode 10](https://developer.apple.com/download/)
- Versión preliminar de Xamarin [liberar la entrada de blog](https://releases.xamarin.com/preview-release-xcode-10-beta-5/)
