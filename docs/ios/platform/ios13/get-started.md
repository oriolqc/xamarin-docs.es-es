---
title: Empezar a trabajar con iOS 13, iPadOS 13, 13 de tvOS y watchOS 6
description: Este documento describe cómo configurar hasta las compilación iOS 13, iPadOS 13, 13 de tvOS y watchOS 6 aplicaciones con Xamarin. Describe cómo descargar Xcode 11 y actualizar Visual Studio para Mac y Visual Studio de 2019.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: d2bb69394d8e9bfeb949a734291179a3e6f5a495
ms.sourcegitcommit: a6ba6ed086bcde4f52fb05f83c59c68e8aa5e436
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540441"
---
# <a name="get-started-with-ios-13"></a>Introducción a iOS 13

![Características en versión preliminar](~/media/shared/preview.png)

Este documento describe cómo empezar a crear aplicaciones de Xamarin que requieren API publicadas con Xcode 11, 13 de iOS. El uso de la vista previa requiere macOS 10.14.4 (Mojave) o una versión posterior.

## <a name="download-and-install"></a>Descarga e instalación

1. **Instalar Xcode 11 beta** : los desarrolladores de Apple registrado pueden descargar e instalar la versión más reciente de Xcode 11 beta desde la [Portal Apple Developer](https://developer.apple.com/download/) o **App Store**.

2. **Ejecutar Xcode 11 beta** – ejecutar Xcode 11 antes de actualizar y ejecutar Visual Studio para Mac, cuando se instale algunas herramientas que Xamarin requiere.

3. En Visual Studio para Mac, seleccione **Visual Studio > Buscar actualizaciones...** , seleccione el **vistas previas de 11 Xcode** de canal e instalar las actualizaciones disponibles.

4. En Visual Studio para Mac, seleccione **Visual Studio > Preferencias > proyectos > ubicaciones de SDK > Apple** y seleccione **Xcode beta.app**.

5. (Opcional) Si la evaluación de esta versión preliminar mediante _Xcode 11 beta 3_, debe habilitar la vinculación. Haga clic en el proyecto, vaya a **Opciones > compilación de iOS > comportamiento del enlazador** y establezca el valor de comportamiento del enlazador en **vincular solo SDK de marco**. Esta solución no será necesaria en una vista previa futura.

6. (Opcional) **Instalar iOS 13 en los dispositivos iOS** : dispositivo de prueba de las aplicaciones que usan las API que se introdujo con el 11 de Xcode, Apple registrado los desarrolladores pueden [descargar](https://developer.apple.com/download) e instalar el sistema operativo en sus dispositivos. Porque iOS está en beta, tenga cuidado de instalarlo en su dispositivo primario.

   > [!TIP]
   > Incluso si la aplicación no utiliza ninguna API nueva, asegúrese de compilarlo con los SDK más reciente de 11 de Xcode y probarlo para asegurarse de que todo funciona según lo previsto. Si una aplicación no llama a ninguna API nueva, puede compilarla con estos nuevos SDK y probarla en los dispositivos que aún no se han actualizado al nuevo sistema operativo.
   >
   > Antes de actualizar los dispositivos al sistema operativo más reciente que se libere de Apple para probar sus aplicaciones de Xamarin, no olvide:
   >
   > - Lectura [notas de la versión de Apple](https://developer.apple.com/download/) para las actualizaciones del sistema operativo.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar Xcode](https://developer.apple.com/download/)
