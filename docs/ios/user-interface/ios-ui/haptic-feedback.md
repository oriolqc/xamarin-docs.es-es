---
title: Provisión de comentarios hápticos en Xamarin.iOS
description: Este documento describe cómo proporcionar comentarios hápticos en una aplicación de Xamarin.iOS. Describe UIImpactFeedbackGenerator UINotificationFeedbackGenerator y UISelectionFeedbackGenerator.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b2c381c59ba1574e80babc2c7e68535a3deffe35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384624"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>Provisión de comentarios hápticos en Xamarin.iOS

<a name="Overview" />

## <a name="overview"></a>Información general

En el iPhone 7 y iPhone 7 Plus, Apple ha incluido las nuevas respuestas hápticos que proporcionan otras formas de captar físicamente el usuario. Comentarios hápticos (a menudo denominados simplemente Haptics) usa el sentido del tacto (a través de fuerza, vibraciones o movimiento) en el diseño de la interfaz de usuario. Utilice estas nuevas opciones de comentarios al tacto para obtener la atención del usuario y reforzar sus acciones.

Los siguientes temas se tratarán en detalle:

- [Acerca de comentarios hápticos](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Acerca de comentarios hápticos

Varios elementos de interfaz de usuario integrados ya proporcionan comentarios hápticos como controles deslizantes, conmutadores y selectores. iOS 10 ahora agrega la posibilidad de desencadenar mediante programación haptics con una subclase concreta de la `UIFeedbackGenerator` clase.

El programador puede utilizar uno de los siguientes `UIFeedbackGenerator` las subclases mediante programación los comentarios hápticos desencadenador:

- `UIImpactFeedbackGenerator` -Usar este generador de comentarios para complementar una acción o tarea como presentar un "thud" cuando se desliza una vista en su lugar, o si dos objetos en la pantalla entran en conflicto.
- `UINotificationFeedbackGenerator` -Usar este generador de comentarios para las notificaciones, como un tipo de acción completa, no se superan o cualquier otro de advertencia.
- `UISelectionFeedbackGenerator` -Usar este generador de comentarios de una selección de cambiar activamente como un elemento de una lista de selección.

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Use este generador de comentarios para complementar una acción o tarea como presentar un "thud" cuando se desliza una vista en su lugar, o si dos objetos en la pantalla entran en conflicto.

Use el siguiente código a los comentarios de impacto de desencadenador:

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Cuando el desarrollador crea una nueva instancia de la `UIImpactFeedbackGenerator` clase proporcionan un `UIImpactFeedbackStyle` especifica la intensidad de los comentarios como:

- `Heavy`
- `Medium`
- `Light`

El `Prepare` método de la `UIImpactFeedbackGenerator` se llama para informar al sistema que comentarios hápticos está a punto de producirse para que puede minimizar la latencia.

El `ImpactOccurred` método, a continuación, desencadena comentarios hápticos.

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Utilizar este generador de comentarios para las notificaciones, como un tipo de acción completa, no se superan o cualquier otro de advertencia.

Use el siguiente código a los comentarios de la notificación de desencadenador:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Una nueva instancia de la `UINotificationFeedbackGenerator` se crea la clase y su `Prepare` método se llama para informar al sistema que comentarios hápticos está a punto de producirse para que puede minimizar la latencia.

El `NotificationOccurred` se llama para desencadenar comentarios hápticos con un determinado `UINotificationFeedbackType` de:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Utilice este generador de comentarios de una selección de cambiar activamente como un elemento de una lista de selección.

Use el siguiente código a los comentarios de selección del desencadenador:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Una nueva instancia de la `UISelectionFeedbackGenerator` se crea la clase y su `Prepare` método se llama para informar al sistema que comentarios hápticos está a punto de producirse para que puede minimizar la latencia.

El `SelectionChanged` método, a continuación, desencadena comentarios hápticos.

## <a name="summary"></a>Resumen

En este artículo ha cubierto los nuevos tipos de comentarios hápticos disponible en iOS 10 y cómo implementarlos en Xamarin.iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
