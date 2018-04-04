---
title: Cambios en la arquitectura
description: Explorar las nuevas características de iOS 11
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 6dd874fd803d2b3de21b0b604cbc2bafa5bf1aec
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="architecture-changes"></a>Cambios en la arquitectura

_Explorar las nuevas características de iOS 11_

Uno de los cambios más importantes que debe tener en cuenta con iOS 11 es el desuso de soporte de 32 bits para las aplicaciones, como se detalla en [de Apple](https://developer.apple.com/news/?id=06282017b) comunicado de prensa. Todas las nuevas aplicaciones y actualizaciones a las aplicaciones existentes deben admitir 64 bits. aplicaciones de 32 bits **no se iniciarán** en iOS 11.

Para actualizar la aplicación en Visual Studio para Mac, siga estos pasos:

1. Haga doble clic en el nombre del proyecto para abrir **Project Options**.
2. Vaya a la **iOS compilación** ficha.
3. Establecer el **admite arquitecturas** lista desplegable- **x86_64** para el **depurar | iPhoneSimulator** y **versión | iPhoneSimulator**:

    ![Cambiar arquitecturas simulador desplegable](architecture-changes-images/image1.png)

4. Para dispositivos iOS, cambie la configuración a **depurar | iPhone** y establezca el **admite arquitecturas** lista desplegable- **ARM64**:

    ![Cambiar la lista desplegable de arquitecturas de dispositivos](architecture-changes-images/image2.png)

5. Cambie la configuración a **versión | iPhone** y establezca el **admite arquitecturas** lista desplegable- **ARM64**.

Para obtener más información sobre las arquitecturas de 32 bits y 64 bits, consulte el [consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64/index.md#ios) guía.

## <a name="related-links"></a>Vínculos relacionados

- [What's New en iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto actualizada tienda de aplicaciones (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualizar la aplicación para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
