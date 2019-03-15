---
title: Material de Xamarin.Forms Visual
description: Xamarin.Forms Material Visual puede utilizarse para crear aplicaciones de Xamarin.Forms que tienen un aspecto idénticos o idéntica en gran medida en iOS y Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: cf6ab8266b0798ccbf29078313bbc7454125a1af
ms.sourcegitcommit: 97dca3face7c4ad5555dfaca88f5b45a70ca556d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/14/2019
ms.locfileid: "57972629"
---
# <a name="xamarinforms-material-visual"></a>Material de Xamarin.Forms Visual

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

[Diseño de materiales](https://material.io) es un sistema de diseño bien fundamentadas creado por Google, que establece el tamaño, color, espaciado y otros aspectos de cómo las vistas y diseños deben aspecto y comportamiento.

Xamarin.Forms Material Visual puede utilizarse para aplicar las reglas de Material Design a las aplicaciones de Xamarin.Forms, crear aplicaciones que tienen un aspecto idénticos o idéntica en gran medida en iOS y Android. Cuando se habilita Material Visual, vistas admitidas adoptarán el mismo diseño multiplataforma, crear una apariencia unificada. Esto se logra con los representadores de material, que se aplican las reglas de diseño de materiales.

Es el proceso para habilitar Visual del Material de Xamarin.Forms en la aplicación:

1. Agregar el [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) paquete NuGet a los proyectos de plataforma Android y iOS. Este paquete de NuGet entrega optimizada de los representadores de Material Design en iOS y Android. En iOS, el paquete proporciona la dependencia transitiva al [Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), que es un C# enlazar a Google [componentes de Material para iOS](https://material.io/develop/ios/). En Android, el paquete proporciona destinos de compilación para asegurarse de que su TargetFramework está configurado correctamente.
1. Inicializar a los representadores de material en cada proyecto de la plataforma. Para obtener más información, consulte [inicializar representadores materiales](#initialize-material-renderers).
1. Consumir los representadores de material estableciendo el [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propiedad `Material` en todas las páginas que deben adoptar las reglas de diseño de materiales. Para obtener más información, consulte [consumir los representadores de material](#consume-material-renderers).
1. [opcional] Personalizar a los representadores de material. Para obtener más información, consulte [personalizar representadores materiales](#customize-material-renderers).

> [!IMPORTANT]
> En Android, los representadores de material requieren una versión mínima de 5.0 (API 21) o superior y un TargetFramework de la versión 9.0 (28 de API). Además, el proyecto de plataforma requiere compatibilidad con Android bibliotecas 28.0.0 o superior, y su tema debe heredar de un tema de los componentes de Material o continuar heredar de un tema de AppCompat. Para obtener más información, consulte [Introducción a los componentes de Material para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Actualmente se incluyen los representadores de material en el [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) paquete NuGet para las vistas siguientes:

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

## <a name="initialize-material-renderers"></a>Inicializar a los representadores de material

Después de instalar el [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) de paquetes de NuGet, el material de representadores deben inicializarse en cada proyecto de la plataforma.

En iOS, esto debe ocurrir en **AppDelegate.cs** invocando el `FormsMaterial.Init` método *después* el `Xamarin.Forms.Forms.Init` método:

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

En Android, esto debe ocurrir en **MainActivity.cs** invocando el `FormsMaterial.Init` método *después* el `Xamarin.Forms.Forms.Init` método:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>Consumir a los representadores de material

Las aplicaciones pueden participar en usando los representadores de material estableciendo el [ `VisualElement.Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propiedad en una página, diseño o vista, para `Material`:

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

El código de C# equivalente es:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

El [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propiedad puede establecerse en cualquier tipo que implemente `IVisual`, con el [ `VisualMarker` ](xref:Xamarin.Forms.VisualMarker) clase que proporciona la siguiente `IVisual` propiedades:

- `Default` : indica que la vista debe presentarse mediante el representador predeterminado.
- `MatchParent` : indica que la vista debe utilizar el representador mismo como su elemento primario directo.
- `Material` : indica que la vista debe representar con un procesador de material.

> [!IMPORTANT]
> El [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propiedad está definida en el [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) (clase), con vistas al heredar el `Visual` valor de propiedad de sus elementos primarios. Por consiguiente, establecer el `Visual` propiedad en un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) garantiza que todas las vistas admitidas en la página usará ese objeto Visual. Además, el `Visual` propiedad se puede invalidar en una vista.

Las capturas de pantalla siguientes muestran una interfaz de usuario que se va a representar utilizando a los representadores de forma predeterminada:

[![Captura de pantalla de los representadores de forma predeterminada, en iOS y Android](material-visual-images/default-renderers.png "las vistas con los representadores de forma predeterminada")](material-visual-images/default-renderers-large.png#lightbox)

Las capturas de pantalla siguientes muestran la misma interfaz de usuario que se va a representar utilizando a los representadores de materiales:

[![Captura de pantalla de los representadores de material, en iOS y Android](material-visual-images/material-renderers.png "las vistas con los representadores de material")](material-visual-images/material-renderers-large.png#lightbox)

Las principales diferencias visibles entre los representadores de material, se muestra aquí y de los representadores de forma predeterminada son que aproveche los representadores de material [ `Button` ](xref:Xamarin.Forms.Button) texto y redondear las esquinas del [ `Frame` ](xref:Xamarin.Forms.Frame)bordes. Sin embargo, los representadores de material usar controles nativos y, por tanto, puede haber diferencias de la interfaz de usuario entre plataformas para áreas como fuentes, colores, sombras y elevación.

> [!NOTE]
> Componentes de diseño de materiales cumplen rigurosamente las directrices de Google. Como resultado, los representadores de Material Design están orientados a ese cambio de tamaño y el comportamiento. Cuando necesite mayor control del comportamiento o los estilos, puede crear sus propios [efecto](~/xamarin-forms/app-fundamentals/effects/index.md), [comportamiento](~/xamarin-forms/app-fundamentals/behaviors/index.md), o [Custom Renderer](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para lograr el detalle que se requiera.

## <a name="customize-material-renderers"></a>Personalizar a los representadores de material

Los representadores de material, opcionalmente, se pueden personalizar, al igual que los representadores de forma predeterminada, a través de las siguientes clases base:

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

En este ejemplo, el `ExportRendererAttribute` especifica que el `CustomMaterialProgressBarRenderer` clase se usará para representar el [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar) vista, con el `IVisual` tipo registrado como el tercer argumento.

> [!NOTE]
> Un representador que especifica un `IVisual` tipo, como parte de su `ExportRendererAttribute`, se usará para representar participa en las vistas, en lugar de con el representador predeterminado. En tiempo de selección del representador, el `Visual` propiedad de la vista es inspeccionar y se incluye en el proceso de selección del representador.

Para obtener más información acerca de los representadores personalizados, consulte [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [Material Visual (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [Crear a un representador Xamarin.Forms Visual](create.md)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
