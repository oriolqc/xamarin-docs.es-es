---
title: Gestos de Xamarin.Forms
description: En esta guía se explica cómo se pueden usar los reconocedores de gestos de Xamarin.Forms para detectar la interacción del usuario con las vistas en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106187"
---
# <a name="xamarinforms-gestures"></a>Gestos de Xamarin.Forms

_Reconocedores de gestos que se usan para detectar la interacción del usuario con las vistas en una aplicación de Xamarin.Forms._

La clase [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) de Xamarin.Forms admite los gestos de pulsar, reducir, desplazar lateralmente y deslizar rápidamente en instancias de [`View`](xref:Xamarin.Forms.View).

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[Agregar un reconocedor de gesto de pulsar](tap.md)

Se usa un gesto de pulsar para la detección de pulsación y se reconoce con la clase [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[Agregar un reconocedor de gesto de reducir](pinch.md)

Se usa un gesto de reducir para realizar un zoom interactivo y se reconoce con la clase [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer).

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[Agregar un reconocedor de gesto de desplazar lateralmente](pan.md)

Se usa un gesto de desplazar lateralmente para detectar el movimiento de los dedos alrededor de la pantalla y aplicar ese movimiento al contenido; se reconoce con la clase [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer).

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[Agregar un reconocedor de gesto de deslizar rápidamente](swipe.md)

Un gesto de deslizar rápidamente se produce cuando un dedo se mueve a través de la pantalla en dirección horizontal o vertical y, a menudo, se usa para iniciar la navegación a través del contenido. Los gestos de deslizar rápidamente se reconocen con la clase [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer).
