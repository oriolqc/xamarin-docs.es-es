---
title: En segundo plano
description: Un vistazo a los mecanismos internos de Xamarin.Mac
ms.topic: article
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 2ba3ffb421dc64bba7df1e10a40125f14365f29e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="under-the-hood"></a>En segundo plano

_Un vistazo a los mecanismos internos de Xamarin.Mac_

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Hacia delante de la compilación de tiempo (AOT)](aot.md)

Hacia delante (AOT) de tiempo de compilación es una técnica de optimización eficaces para mejorar el rendimiento de inicio. Sin embargo, también afecta a su tiempo de compilación, el tamaño de la aplicación y la ejecución del programa de maneras significativas, por lo que merece la pena entender cómo funciona.

## <a name="mac-architecturearchitecturemd"></a>[Arquitectura de Mac](architecture.md)

Relación del Xamarin.Mac para Objective-C, incluidos conceptos, como la compilación, los selectores, registradores, iniciar la aplicación y el generador.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrador de Xamarin.Mac](registrar.md)

Xamarin.Mac relaciona la diferencia entre el entorno administrado y en tiempo de ejecución del cacao, lo que permite a las clases administradas llamar a clases de Objective-C no administradas y se vuelve a llamar cuando se producen eventos. El trabajo necesario para realizar esta "mágica" se controla mediante el registrador, pero a veces puede resultar útil comprender lo que ocurre "under the hood".
