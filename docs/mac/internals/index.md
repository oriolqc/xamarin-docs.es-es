---
title: En segundo plano en Xamarin.Mac
description: Este documento incluye vínculos a diversas guías que describen el funcionamiento interno de Xamarin.Mac. Analizar documentos vinculados por delante de la compilación de tiempo, la arquitectura de Xamarin.Mac y el registrador de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 872f26febf3abbe4d659773d2bf2d27348c64513
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118778"
---
# <a name="under-the-hood-in-xamarinmac"></a>En segundo plano en Xamarin.Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Con antelación de compilación time (AOT)](aot.md)

Con antelación de time (AOT), la compilación es una técnica eficaz de optimización para mejorar el rendimiento de inicio. Sin embargo, también afecta a su tiempo de compilación, el tamaño de la aplicación y la ejecución del programa de las maneras más profundas, por lo que merece la pena entender cómo funciona.

## <a name="mac-architecturearchitecturemd"></a>[Arquitectura Mac](architecture.md)

Relación de Xamarin.Mac y Objective-C, incluidos conceptos como la compilación, los selectores, registradores, iniciar la aplicación y el generador.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrador de Xamarin.Mac](registrar.md)

Xamarin.Mac salva la distancia entre el mundo administrado y en tiempo de ejecución de Cocoa, lo que permite a las clases administradas llamar a las clases no administradas de Objective-C y se vuelve a llamar cuando se producen eventos. El trabajo necesario para realizar esta "magia" se controla mediante el registrador, pero a veces puede ser útil entender qué está ocurriendo "under the hood".
