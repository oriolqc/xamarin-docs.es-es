---
title: Bajo el paraguas de Xamarin.Mac
description: Vínculos de este documento diversas guías que describen el funcionamiento interno de Xamarin.Mac. Documentos vinculados se tratan por delante de la compilación de tiempo, la arquitectura de Xamarin.Mac y el registrador de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: c940252a675c38247d2c5bb374b9c30237222bda
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792494"
---
# <a name="under-the-hood-in-xamarinmac"></a>Bajo el paraguas de Xamarin.Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Hacia delante de la compilación de tiempo (AOT)](aot.md)

Hacia delante (AOT) de tiempo de compilación es una técnica de optimización eficaces para mejorar el rendimiento de inicio. Sin embargo, también afecta a su tiempo de compilación, el tamaño de la aplicación y la ejecución del programa de maneras significativas, por lo que merece la pena entender cómo funciona.

## <a name="mac-architecturearchitecturemd"></a>[Arquitectura de Mac](architecture.md)

Relación del Xamarin.Mac para Objective-C, incluidos conceptos, como la compilación, los selectores, registradores, iniciar la aplicación y el generador.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrador de Xamarin.Mac](registrar.md)

Xamarin.Mac relaciona la diferencia entre el entorno administrado y en tiempo de ejecución del cacao, lo que permite a las clases administradas llamar a clases de Objective-C no administradas y se vuelve a llamar cuando se producen eventos. El trabajo necesario para realizar esta "mágica" se controla mediante el registrador, pero a veces puede resultar útil comprender lo que ocurre "under the hood".
