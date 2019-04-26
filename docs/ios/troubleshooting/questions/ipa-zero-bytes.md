---
title: El archivo IPA es de 0 bytes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4835c980b6b11c92ec1c81dea69f229aa5652275
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420941"
---
# <a name="ipa-file-is-0-bytes"></a>El archivo IPA es de 0 bytes

> [!IMPORTANT]
> Se ha resuelto este problema en versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de salida del registro de compilación.



Hubo algunos problemas conocidos en las versiones anteriores de Xamarin que podría provocar que el archivo IPA en Windows sea 0 bytes. 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Se ha corregido en Xamarin para Visual Studio 3.11.584 
- [Error 24416 - configuración de creación "Ad-Hoc" desde la línea de comandos hace no copiar IPA archivo para Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Error 24417 - cambiar "las propiedades del proyecto -> iOS IPA Opciones -> nombre del paquete" evita que IPA que se vuelve a copiar en Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Error 29822 - [XVS.iOS 3.11] configuración "Compilación" número diferente de "Version" número causas IPA no va a copiar en Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Se ha corregido en Xamarin para Visual Studio 4.1.0.496
- [Errores 27989: Mostrar archivo ipa en produce un error de servidor de compilación si el nombre del ensamblado no coincide con el nombre del proyecto](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
