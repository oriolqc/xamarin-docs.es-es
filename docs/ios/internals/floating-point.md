---
title: Operaciones de punto flotante en Xamarin.iOS
description: Este documento describe cómo Xamarin.iOS controla las operaciones de punto flotante de precisión de 32 bits y 64 bits y asociado impacto en el rendimiento.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: c5ee1b833e309c78c7338298cbe5c8800afb1ba1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116243"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operaciones de punto flotante en Xamarin.iOS

Xamarin.iOS realizará de forma predeterminada 32 bits y 64 bits operaciones de punto flotante con precisión de 64 bits en ARM.  

Aunque esta mayor precisión está más próximo a lo que los desarrolladores esperan de operaciones de punto de flotante C# en el escritorio, en el dispositivo móvil, el impacto de rendimiento puede ser considerable.

Es posible compilar el código de punto flotante de 32 bits para usar operaciones de punto flotante de 32 bits.  Para ello, deberá usar al menos 8.10 Xamarin.iOS y un conjunto en su iOS dependen del panel de las opciones de la "mtouch argumentos adicionales" línea de entrada el siguiente valor:

     --aot-options=-O=float32

Esto le informará de los compiladores estáticos (compilador estático integrada de Mono, o lo con tecnología de LLVM) para realizar operaciones de punto flotante con los valores de punto flotante de 32 bits.
