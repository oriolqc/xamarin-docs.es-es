---
title: "Proporcionar comentarios hápticos"
description: "Este artículo explican los nuevos tipos de comentarios hápticos disponible en iOS 10 y cómo implementarlas en Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 9f4eb989fe0c91471c9473c512c4befd36e4ace2
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="providing-haptic-feedback"></a>Proporcionar comentarios hápticos

_Este artículo explican los nuevos tipos de comentarios hápticos disponible en iOS 10 y cómo implementarlas en Xamarin.iOS._

<a name="Overview" />

## <a name="overview"></a>Información general

En el iPhone 7 y iPhone 7 +, Apple ha incluido nuevas respuestas hápticos que proporcionan otras formas de ponerse en contacto físicamente el usuario. Comentarios hápticos (a menudo denominado simplemente como Haptics) usa el sentido de táctil (mediante force, vibración o movimiento) en el diseño de la interfaz de usuario. Utilice estas nuevas opciones de comentarios al tacto para obtener la atención del usuario y reforzar sus acciones.

Los siguientes temas se tratarán en detalle:

- [Acerca de comentarios hápticos](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Acerca de comentarios hápticos

Varios elementos de interfaz de usuario integrados ya proporcionen comentarios hápticos como selectores, los conmutadores y los controles deslizantes. iOS 10 ahora agrega la capacidad de desencadenar mediante programación haptics con una subclase concreta de la `UIFeedbackGenerator` clase.

El programador puede utilizar uno de los siguientes `UIFeedbackGenerator` las subclases mediante programación los comentarios hápticos desencadenador:

- `UIImpactFeedbackGenerator` -Usar este generador de comentarios para complementar una acción o tarea como presentar un "thud" cuando una vista de diapositivas en su lugar, o si dos objetos en la pantalla entran en conflicto.
- `UINotificationFeedbackGenerator` : Use este generador de comentarios para las notificaciones, como un tipo de acción se completan, errores o cualquier otro de advertencia.
- `UISelectionFeedbackGenerator` : Use este generador de comentarios de una selección activamente cambio como un elemento de una lista de selección.

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Utilizar este generador de comentarios para complementar una acción o tarea como presentar un "thud" cuando una vista de diapositivas en su lugar, o si dos objetos en la pantalla entran en conflicto.

Utilice el código siguiente al desencadenador impacto comentarios:

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Cuando el desarrollador crea una nueva instancia de la `UIImpactFeedbackGenerator` clase proporcionan un `UIImpactFeedbackStyle` especificar la intensidad de los comentarios como:

- `Heavy`
- `Medium`
- `Light`

El `Prepare` método de la `UIImpactFeedbackGenerator` se llama para informar al sistema que comentarios hápticos está a punto de producirse para que puede minimizar la latencia.

El `ImpactOccurred` método, a continuación, desencadena hápticos comentarios.

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Utilizar este generador de comentarios para las notificaciones, como un tipo de acción se completan, errores o cualquier otro de advertencia.

Utilice el siguiente código a los comentarios de notificación de desencadenador:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Una nueva instancia de la `UINotificationFeedbackGenerator` se crea la clase y sus `Prepare` método se llama para informar al sistema que comentarios hápticos está a punto de producirse para que puede minimizar la latencia.

El `NotificationOccurred` se llama para desencadenar hápticos comentarios con un determinado `UINotificationFeedbackType` de:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Utilice este generador de comentarios de una selección activamente cambio como un elemento de una lista de selección.

Utilice el siguiente código a los comentarios de selección de desencadenador:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Una nueva instancia de la `UISelectionFeedbackGenerator` se crea la clase y sus `Prepare` método se llama para informar al sistema que comentarios hápticos está a punto de producirse para que puede minimizar la latencia.

El `SelectionChanged` método, a continuación, desencadena hápticos comentarios.

## <a name="summary"></a>Resumen

En este artículo se trata los nuevos tipos de comentarios hápticos disponible en iOS 10 y cómo implementarlas en Xamarin.iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
