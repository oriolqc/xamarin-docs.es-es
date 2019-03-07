---
title: Material de Xamarin.Forms Visual
description: Xamarin.Forms Material Visual puede utilizarse para crear aplicaciones de Xamarin.Forms que tienen un aspecto idénticos o idéntica en gran medida en iOS y Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 46ffe29f898e2f7bd8fcbe759f5a2f54e3dd60e1
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557507"
---
# <a name="xamarinforms-material-visual"></a>Material de Xamarin.Forms Visual

Xamarin.Forms Material Visual puede utilizarse para crear aplicaciones de Xamarin.Forms que tienen un aspecto idénticos o idéntica en gran medida en iOS y Android. Esto se logra mediante el uso de los representadores adicionales que implementan una apariencia material, con las aplicaciones que participan en la apariencia a través de la `Visual` propiedad:

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>
```

El código de C# equivalente es:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

> [!IMPORTANT]
> El `Visual` propiedad está definida en el `VisualElement` (clase), con vistas al heredar el `Visual` valor de propiedad de sus elementos primarios. Por consiguiente, establecer el `Visual` propiedad en un `ContentPage` garantiza que todas las vistas admitidas en la página usará ese aspecto visual. Además, el `Visual` propiedad se puede invalidar en una vista.

Los representadores de material actualmente se incluyen para las siguientes vistas en iOS y Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)

Funcionalmente, los representadores de material no son distintos a los representadores de forma predeterminada.

En iOS y Android, el proyecto de plataforma debe tener la [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/ https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) instalado el paquete de NuGet. Después de instalar el paquete de NuGet, se requiere código de inicialización en cada proyecto de la plataforma, *después* el `Xamarin.Forms.Forms.Init` llamada al método. Para iOS, use el código siguiente:

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

En Android, debe pasar los mismos parámetros que se pasan a `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init(this, bundle);
FormsMaterial.Init(this, bundle);
```

> [!IMPORTANT]
> En Android, material solo funciona con TargetFramework 9.0 (28 de API). Además, el proyecto de plataforma debe usar v28 de las bibliotecas de soporte técnico y su tema debe heredar de un tema de los componentes de Material o continuar heredar de un tema de AppCompat. Para obtener más información, consulte [Introducción a los componentes de Material para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Las capturas de pantalla siguientes muestran una interfaz de usuario que incluye las cuatro vistas de para qué material representadores existen, pero va a representar utilizando a los representadores de forma predeterminada:

[![Captura de pantalla de los representadores de forma predeterminada, en iOS y Android](material-visual-images/default-renderers.png "las vistas con los representadores de forma predeterminada")](material-visual-images/default-renderers-large.png#lightbox)

Las capturas de pantalla siguientes muestran la misma interfaz de usuario que se va a representar utilizando a los representadores de materiales:

[![Captura de pantalla de los representadores de material, en iOS y Android](material-visual-images/material-renderers.png "las vistas con los representadores de material")](material-visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> Con los representadores de material los controles representados permanecen controles nativos y, por lo tanto, seguirá habiendo diferencias de la interfaz de usuario entre plataformas para áreas como fuentes, colores, sombras y elevación.

## <a name="material-renderers"></a>Representadores de material

Los representadores de material pueden personalizarse, al igual que los representadores de forma predeterminada, mediante el uso de las clases base siguientes:

- `MaterialButtonRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

El código siguiente muestra un ejemplo de personalización de la `MaterialProgressBarRenderer` clase:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        ...
    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
