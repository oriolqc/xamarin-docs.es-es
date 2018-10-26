---
title: Gestos de Xamarin.Forms
description: Esta guía explica cómo se pueden usar los reconocedores de gestos de Xamarin.Forms para detectar la interacción del usuario con las vistas en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106187"
---
# <a name="xamarinforms-gestures"></a>Gestos de Xamarin.Forms

_Los reconocedores de gestos pueden usarse para detectar la interacción del usuario con las vistas en una aplicación de Xamarin.Forms._

Xamarin.Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer) clase admite la derivación, pinch, panorámica y gestos de pasar el dedo en [ `View` ](xref:Xamarin.Forms.View) instancias.

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[Agregar un reconocedor de movimiento de tap](tap.md)

Un gesto de pulsar se usa para la detección de tap y se reconoce con el [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) clase.

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[Adición de un módulo de reconocimiento del gesto pinch](pinch.md)

Un gesto de reducir se usa para llevar a cabo zoom interactivo y se reconoce con el [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) clase.

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[Agregar un reconocedor de movimiento panorámico](pan.md)

Se usa para detectar el movimiento de dedos alrededor de la pantalla y aplicar ese movimiento al contenido de un movimiento panorámico y se reconoce con el [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) clase.

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[Agregar un reconocedor de gestos de pasar el dedo](swipe.md)

Un gesto de deslizar rápidamente se produce cuando un dedo se mueve a través de la pantalla en dirección horizontal o vertical y, a menudo se usa para iniciar la navegación a través de contenido. Los gestos de pasar el dedo se reconocen con la [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) clase.
