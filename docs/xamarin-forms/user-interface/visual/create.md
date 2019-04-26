---
title: Crear a un representador Xamarin.Forms Visual
description: Crear objetos visuales de Xamarin.Forms para aplicarse de manera selectiva a los objetos VisualElement, sin tener que las vistas de Xamarin.Forms subclase.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: a11c2045fa6119d0689834c35794bc8913c80bd6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023777"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>Crear a un representador Xamarin.Forms Visual

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

Xamarin.Forms Visual permite crear y aplicar de forma selectiva a los representadores de [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) objetos, sin tener que las vistas de Xamarin.Forms subclase. Un representador que especifica un `IVisual` tipo, como parte de su `ExportRendererAttribute`, se usará para representar participa en las vistas, en lugar de con el representador predeterminado. En tiempo de selección del representador, el `Visual` propiedad de la vista es inspeccionar y se incluye en el proceso de selección del representador.

> [!IMPORTANT]
> Actualmente la [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propiedad no se puede cambiar después de que se ha representado la vista, pero esta operación cambiará en futuras versiones.

El proceso para crear y consumir a un representador Xamarin.Forms Visual es:

1. Cree a los representadores de plataforma para la vista necesaria. Para obtener más información, consulte [crear representadores](#create-platform-renderers).
1. Crear un tipo que se deriva de `IVisual`. Para obtener más información, consulte [crear un tipo IVisual](#create-an-ivisual-type).
1. Registrar el `IVisual` tipo como parte de la `ExportRendererAttribute` que decora los representadores. Para obtener más información, consulte [registrar el tipo IVisual](#register-the-ivisual-type).
1. Usar el representador Visual estableciendo el [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propiedad en la vista para el `IVisual` nombre. Para obtener más información, consulte [consumir el representador Visual](#consume-the-visual-renderer).
1. [opcional] Registrar un nombre para el `IVisual` tipo. Para obtener más información, consulte [registrar un nombre para el tipo de IVisual](#register-a-name-for-the-ivisual-type).

## <a name="create-platform-renderers"></a>Crear a los representadores de plataforma

Para obtener información acerca de cómo crear una clase de procesador, vea [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Sin embargo, tenga en cuenta que un representador Xamarin.Forms Visual se aplica a una vista sin necesidad de subclase de la vista.

Las clases de representador que se describen aquí implementan personalizada [ `Button` ](xref:Xamarin.Forms.Button) que muestra su texto con sombreado.

### <a name="ios"></a>iOS

El ejemplo de código siguiente muestra a la representación de botón para iOS:

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

El ejemplo de código siguiente muestra a la representación de botón para Android:

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>Crear un tipo IVisual

En la biblioteca multiplataforma, creación de un tipo que derive de `IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

El `CustomVisual` tipo, a continuación, se puede registrar en las clases de representador que permita [ `Button` ](xref:Xamarin.Forms.Button) objetos que opten por usar los representadores.

## <a name="register-the-ivisual-type"></a>Registrar el tipo IVisual

En los proyectos de plataforma, decore las clases del representador con la `ExportRendererAttribute`:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        ...
    }
}
```

En este ejemplo, el `ExportRendererAttribute` especifica que el `CustomButtonRenderer` clase se usará para representar consumiendo [ `Button` ](xref:Xamarin.Forms.Button) objetos, con el `IVisual` tipo registrado como el tercer argumento. Un representador que especifica un `IVisual` tipo, como parte de su `ExportRendererAttribute`, se usará para representar participa en las vistas, en lugar de con el representador predeterminado.

## <a name="consume-the-visual-renderer"></a>Usar al representador Visual

Un [ `Button` ](xref:Xamarin.Forms.Button) objeto puede optar por usar las clases del representador estableciendo su [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propiedad `Custom`:

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> En XAML, un convertidor de tipos elimina la necesidad de incluir el sufijo "Visual" en el [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) valor de propiedad. Sin embargo, también se puede especificar el nombre de tipo completo.

El código de C# equivalente es:

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

En tiempo de selección del representador, el [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propiedad de la [ `Button` ](xref:Xamarin.Forms.Button) es inspeccionar y puede incluir en el proceso de selección del representador. Si no encuentra un representador, se usará el representador predeterminado de Xamarin.Forms.

Las capturas de pantalla siguientes muestran el representado [ `Button` ](xref:Xamarin.Forms.Button), que muestra su texto con sombreado:

[![Captura de pantalla del botón personalizado con sombra del texto, en iOS y Android](material-visual-images/custom-button.png "botón con sombra del texto")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>Registrar un nombre para el tipo de IVisual

El [ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute) se puede usar para registrar, opcionalmente, un nombre diferente para el `IVisual` tipo. Este enfoque puede utilizarse para resolver conflictos de nombres entre diferentes bibliotecas Visual, o en situaciones donde desea hacer referencia a un objeto Visual mediante un nombre distinto al nombre de su tipo.

El [ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute) debe definirse en el nivel de ensamblado en la biblioteca de multiplataforma, o en el proyecto de plataforma:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

El `IVisual` , a continuación, se puede utilizar el tipo en su nombre registrado:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> Al consumir un objeto Visual a través de su nombre registrado, se debe incluir cualquier sufijo "Visual".

## <a name="related-links"></a>Vínculos relacionados

- [Material Visual (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [Material de Xamarin.Forms Visual](material-visual.md)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
