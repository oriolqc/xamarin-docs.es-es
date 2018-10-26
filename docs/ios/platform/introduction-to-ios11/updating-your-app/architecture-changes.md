---
title: Cambios de arquitectura en iOS 11
description: Este documento describe el desuso de las aplicaciones de 32 bits en iOS 11. Describe cómo actualizar las aplicaciones a las arquitecturas de 64 bits de destino.
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: b7d1df6ed2a8bd480025681ebcbe48acd7592564
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102275"
---
# <a name="architecture-changes-in-ios-11"></a>Cambios de arquitectura en iOS 11

Uno de los cambios más importantes que debe tener en cuenta con iOS 11 es la interrupción de la compatibilidad de 32 bits para las aplicaciones, como se detalla en [Apple](https://developer.apple.com/news/?id=06282017b) comunicado de prensa. Todas las nuevas aplicaciones y actualizaciones a las aplicaciones existentes deben admitir 64 bits. las aplicaciones de 32 bits **no se iniciarán** en iOS 11.

Para actualizar la aplicación en Visual Studio para Mac, siga estos pasos:

1. Haga doble clic en el nombre del proyecto para abrir **opciones de proyecto**.
2. Vaya a la **compilación de iOS** ficha.
3. Establecer el **arquitecturas compatibles** lista desplegable para **x86_64** para el **depurar | iPhoneSimulator** y **versión | iPhoneSimulator**:

    ![Cambiar la lista desplegable de arquitecturas de simulador](architecture-changes-images/image1.png)

4. Para dispositivos iOS, cambie la configuración a **depurar | iPhone** y establezca el **arquitecturas compatibles** lista desplegable para **ARM64**:

    ![Cambiar la lista desplegable de arquitecturas de dispositivo](architecture-changes-images/image2.png)

5. Cambie la configuración a **versión | iPhone** y establezca el **arquitecturas compatibles** lista desplegable para **ARM64**.

Para obtener más información sobre las arquitecturas de 32 bits y 64 bits, consulte el [consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64/index.md#ios) guía.

## <a name="related-links"></a>Vínculos relacionados

- [Novedades en iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto actualizada Store App (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualización de la aplicación para iOS 11 (sesión WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
