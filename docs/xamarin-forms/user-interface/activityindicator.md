---
title: Indicador de actividad en Xamarin.Forms
description: El control ActivityIndicator indica a los usuarios que la aplicación esté implicada en una actividad larga, sin dar ninguna indicación del progreso. En este artículo se explica cómo usar un ActivityIndicator en XAML y código.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: abd8150e3aa4ec347c8d956004993340630208bf
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837065"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin.Forms ActivityIndicator
[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)

Xamarin.Forms[ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator) es un control que muestra una animación para mostrar que la aplicación esté implicada en una actividad larga. A diferencia de la [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar), el `ActivityIndicator` no proporciona ninguna indicación de progreso. El `ActivityIndicator` hereda [ `View` ](xref:Xamarin.Forms.View).

La siguiente captura de pantalla muestra un `ActivityIndicator` control en iOS y Android:

![Captura de pantalla de ActivityIndicator en iOS y Android](activityindicator-images/activityindicators-default.png "captura de pantalla de ActivityIndicator en iOS y Android")

El `ActivityIndicator` control define las siguientes propiedades:

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) es un `bool` valor que indica si el `ActivityIndicator` debe ser visible y animación u oculto. Cuando el valor es `false` el `ActivityIndicator` no será visible.
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) es un `Color` valor que define el color de pantalla de la `ActivityIndicator`.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que el `ActivityIndicator` puede cambiar el estilo y ser el destino de los enlaces de datos.

## <a name="create-an-activityindicator"></a>Crear un ActivityIndicator

Un `ActivityIndicator` se pueden crear instancias en XAML. Su `IsRunning` se puede establecer la propiedad para determinar si el control está visible y animación. Si el `IsRunning` propiedad no está establecida, el valor predeterminado es `false` y `ActivityIndicator` no será visible. El ejemplo siguiente muestra cómo crear una instancia de un `ActivityIndicator` en XAML con el elemento opcional `IsRunning` conjunto de propiedades:

```xaml
<ActivityIndicator IsRunning="true" />
```

Un `ActivityIndicator` también se pueden crear en el código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propiedades de apariencia ActivityIndicator

El `Color` propiedad puede establecerse para definir el `ActivityIndicator` color. El ejemplo siguiente muestra cómo crear una instancia de un `ActivityIndicator` en XAML con el `Color` conjunto de propiedades:

```xaml
<ActivityIndicator Color="Orange" />
```

El `Color` propiedad también se puede establecer al crear un `ActivityIndicator` en el código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

La siguiente captura de pantalla muestra la `ActivityIndicator` con el `Color` propiedad establecida en `Color.Orange` en iOS y Android:

![Captura de pantalla de ActivityIndicator con estilo en iOS y Android](activityindicator-images/activityindicators-styled.png "captura de pantalla de ActivityIndicator con estilo en iOS y Android")

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ActivityIndicator](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)
* [Barra de progreso](~/xamarin-forms/user-interface/progressbar.md)
