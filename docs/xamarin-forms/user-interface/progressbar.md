---
title: Xamarin.Forms ProgressBar
description: Xamarin.Forms ProgressBar es un control que representa visualmente el progreso como una barra horizontal que se rellena en función de una propiedad de tipo float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 2e2917077575ebc78dbdda8ae55aa230a39a7ba1
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837015"
---
# <a name="xamarinforms-progressbar"></a>Xamarin.Forms ProgressBar
[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)

Xamarin.Forms [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar) es un control que se representa visualmente el progreso como una barra horizontal que se rellena a un porcentaje representado por un `float` valor. El `ProgressBar` clase hereda de [ `View` ](xref:Xamarin.Forms.View).

La siguiente captura de pantalla muestra un `ProgressBar` en iOS y Android:

![Captura de pantalla de ProgressBar en iOS y Android](progressbar-images/progressbars-default.png "ProgressBar en iOS y Android")

El `ProgressBar` control define dos propiedades:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) es un `float` valor que representa el progreso actual como un valor de 0 a 1. `Progress` los valores menores que 0 se unirá a 0, los valores mayores que 1 se unirá a 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) es un `Color` que afecta el interior que representa el progreso actual de color de la barra.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que el `ProgressBar` puede cambiar el estilo y ser el destino de los enlaces de datos.

El `ProgressBar` control también define un `ProgressTo` método que anima la barra de su valor actual en un valor especificado. Para obtener más información, consulte [animar un ProgressBar](#animate-a-progressbar).

> [!NOTE]
> El `ProgressBar` no acepta manipulación del usuario, por lo que se ha omitido al usar la tecla Tab para seleccionar los controles.

## <a name="create-a-progressbar"></a>Crear una barra de progreso

Un `ProgressBar` se pueden crear instancias en XAML. Su `Progress` se puede establecer la propiedad para determinar el porcentaje de relleno de la barra de color interno. Si el `Progress` no está establecida la propiedad, el valor predeterminado es 0. El ejemplo siguiente muestra cómo crear una instancia de un `ProgressBar` en XAML con el elemento opcional `Progress` conjunto de propiedades:

```xaml
<ProgressBar Progress="0.5" />
```

Un `ProgressBar` también se pueden crear en el código:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> No utilice las opciones de diseño horizontal sin restricciones, como `Center`, `Start`, o `End` con `ProgressBar`. En UWP, el `ProgressBar` se contrae en una barra de ancho cero. Mantenga el valor predeterminado `HorizontalOptions` valor `Fill` y no use un ancho de `Auto` al poner un `ProgressBar` en un `Grid` diseño.

## <a name="progressbar-appearance-properties"></a>Propiedades de apariencia de la barra de progreso

El `ProgressColor` propiedad puede establecerse para definir la barra interna de color cuando el `Progress` propiedad es mayor que cero. El ejemplo siguiente muestra cómo crear una instancia de un `ProgressBar` en XAML con el `ProgressColor` conjunto de propiedades:

```xaml
<ProgressBar OnColor="Orange" />
```

El `ProgressColor` propiedad también se puede establecer al crear un `ProgressBar` en el código:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

La siguiente captura de pantalla muestra la `ProgressBar` con el `ProgressColor` propiedad establecida en `Color.Orange` en iOS y Android:

![Captura de pantalla de la barra de progreso con estilo en iOS y Android](progressbar-images/progressbars-styled.png "con estilo ProgressBar en iOS y Android")

## <a name="animate-a-progressbar"></a>Animar un ProgressBar

El `ProgressTo` método anima la `ProgressBar` desde su actual `Progress` valor a un valor proporcionado con el tiempo. El método acepta un `float` progreso valor, un `uint` duración en milisegundos, un `Easing` valor enum y devuelve un `Task<bool>`. El código siguiente muestra cómo animar una `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Para obtener más información sobre la `Easing` enumeración, consulte [funciones de aceleración en Xamarin.Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de ProgressBar](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)
