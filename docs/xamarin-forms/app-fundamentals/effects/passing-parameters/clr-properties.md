---
title: Efecto de pasar parámetros como propiedades de tiempo de ejecución de lenguaje común
description: Propiedades de Common Language Runtime (CLR) pueden usarse para definir los parámetros del efecto que no responden a los cambios de propiedad en tiempo de ejecución. En este artículo muestra cómo utilizar propiedades CLR para pasar parámetros a un efecto.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 1bb357b256a7cc6d52d1d92613f38cbf48400c4c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995773"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Efecto de pasar parámetros como propiedades de tiempo de ejecución de lenguaje común

_Propiedades de Common Language Runtime (CLR) pueden usarse para definir los parámetros del efecto que no responden a los cambios de propiedad en tiempo de ejecución. En este artículo muestra cómo utilizar propiedades CLR para pasar parámetros a un efecto._

El proceso de creación de los parámetros del efecto que no responden a los cambios de propiedad en tiempo de ejecución es como sigue:

1. Crear un `public` que cree subclases de clases la [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) clase. La `RoutingEffect` clase representa un efecto de independiente de la plataforma que ajusta un efecto interno que es específico de plataforma normalmente.
1. Cree un constructor que llame al constructor de clase base, pasando una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase efecto específico de la plataforma.
1. Agregar propiedades a la clase para cada parámetro que se pasarán al efecto.

A continuación, se pueden pasar parámetros al efecto especificando valores para cada propiedad al crear una instancia del efecto.

La aplicación de ejemplo se muestra un `ShadowEffect` que agrega una sombra al texto mostrado por un [ `Label` ](xref:Xamarin.Forms.Label) control. El siguiente diagrama ilustra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](clr-properties-images/shadow-effect.png "Responsabilidades del proyecto de efecto de sombra")

Un [ `Label` ](xref:Xamarin.Forms.Label) control en el `HomePage` se personaliza la `LabelShadowEffect` en cada proyecto específico de plataforma. Los parámetros se pasan a cada `LabelShadowEffect` a través de propiedades en el `ShadowEffect` clase. Cada `LabelShadowEffect` clase se deriva de la `PlatformEffect` clase para cada plataforma. Esto da como resultado una sombra que se agregan al texto mostrado por el `Label` controlar, como se muestra en las capturas de pantalla siguiente:

![](clr-properties-images/screenshots.png "Efecto de sombra paralela en cada plataforma")

## <a name="creating-effect-parameters"></a>Crear parámetros de efecto

Un `public` que cree subclases de clases la [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) debe crearse la clase para representar parámetros efecto, como se muestra en el ejemplo de código siguiente:

```csharp
public class ShadowEffect : RoutingEffect
{
  public float Radius { get; set; }

  public Color Color { get; set; }

  public float DistanceX { get; set; }

  public float DistanceY { get; set; }

  public ShadowEffect () : base ("MyCompany.LabelShadowEffect")
  {            
  }
}
```

El `ShadowEffect` contiene cuatro propiedades que representan los parámetros que se pasarán a cada uno específico de plataforma `LabelShadowEffect`. El constructor de clase llama al constructor de clase base, pasando un parámetro que consta de una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase efecto específico de la plataforma. Por lo tanto, una nueva instancia de la `MyCompany.LabelShadowEffect` se agregarán a un control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección cuando un `ShadowEffect` se crea una instancia.

## <a name="consuming-the-effect"></a>Consumir el efecto

El ejemplo de código XAML siguiente muestra un [ `Label` ](xref:Xamarin.Forms.Label) control al que el `ShadowEffect` está conectado:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Effects>
    <local:ShadowEffect Radius="5" DistanceX="5" DistanceY="5">
      <local:ShadowEffect.Color>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Black" />
            <On Platform="Android" Value="White" />
            <On Platform="UWP" Value="Red" />
        </OnPlatform>
      </local:ShadowEffect.Color>
    </local:ShadowEffect>
  </Label.Effects>
</Label>
```

El equivalente [ `Label` ](xref:Xamarin.Forms.Label) en C# se muestra en el ejemplo de código siguiente:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};

Color color = Color.Default;
switch (Device.RuntimePlatform)
{
    case Device.iOS:
        color = Color.Black;
        break;
    case Device.Android:
        color = Color.White;
        break;
    case Device.UWP:
        color = Color.Red;
        break;
}

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

En ambos ejemplos de código, una instancia de la `ShadowEffect` se crea una instancia de clase con valores que se especifica para cada propiedad, antes de agregarse al control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección. Tenga en cuenta que el `ShadowEffect.Color` propiedad usa valores de color específicos de la plataforma. Para obtener más información, consulte [clase de dispositivo](~/xamarin-forms/platform/device.md).

## <a name="creating-the-effect-on-each-platform"></a>Crear el efecto en cada plataforma

Las secciones siguientes describen la implementación específica de la plataforma de la `LabelShadowEffect` clase.

### <a name="ios-project"></a>Proyecto de iOS

El siguiente ejemplo de código muestra la `LabelShadowEffect` implementación para el proyecto de iOS:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.CornerRadius = effect.Radius;
                    Control.Layer.ShadowColor = effect.Color.ToCGColor ();
                    Control.Layer.ShadowOffset = new CGSize (effect.DistanceX, effect.DistanceY);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

El `OnAttached` método recupera el `ShadowEffect` instancia y establece `Control.Layer` propiedades a los valores de propiedad especificado para crear la sombra. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque es necesaria ninguna limpieza.

### <a name="android-project"></a>Proyecto de Android

El siguiente ejemplo de código muestra la `LabelShadowEffect` implementación para el proyecto de Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var control = Control as Android.Widget.TextView;
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    float radius = effect.Radius;
                    float distanceX = effect.DistanceX;
                    float distanceY = effect.DistanceY;
                    Android.Graphics.Color color = effect.Color.ToAndroid ();
                    control.SetShadowLayer (radius, distanceX, distanceY, color);
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

El `OnAttached` método recupera el `ShadowEffect` instancia y llama a la [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) método para crear una sombra con los valores de propiedad especificado. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque es necesaria ninguna limpieza.

### <a name="universal-windows-platform-project"></a>Proyecto de la plataforma universal de Windows

El siguiente ejemplo de código muestra la `LabelShadowEffect` implementación para el proyecto de plataforma Universal de Windows (UWP):

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                    if (effect != null) {
                        var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;
                        var shadowLabel = new Label ();
                        shadowLabel.Text = textBlock.Text;
                        shadowLabel.FontAttributes = FontAttributes.Bold;
                        shadowLabel.HorizontalOptions = LayoutOptions.Center;
                        shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;
                        shadowLabel.TextColor = effect.Color;
                        shadowLabel.TranslationX = effect.DistanceX;
                        shadowLabel.TranslationY = effect.DistanceY;

                        ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                        shadowAdded = true;
                    }
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

La plataforma Universal de Windows no proporciona un efecto de sombra, lo cual la `LabelShadowEffect` simula la implementación en ambas plataformas uno mediante la adición de un segundo desplazamiento [ `Label` ](xref:Xamarin.Forms.Label) detrás de la réplica principal `Label`. El `OnAttached` método recupera el `ShadowEffect` de la instancia, se crea el nuevo `Label`y establece algunas propiedades de diseño en el `Label`. A continuación, crea la sombra estableciendo el [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX), y [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propiedades para controlar el color y la ubicación de la `Label`. El `shadowLabel` , a continuación, se inserta desplazamiento respecto a la principal `Label`. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque es necesaria ninguna limpieza.

## <a name="summary"></a>Resumen

Este artículo haya demostrado mediante las propiedades CLR para pasar parámetros a un efecto. Las propiedades CLR pueden usarse para definir los parámetros del efecto que no responden a los cambios de propiedad en tiempo de ejecución.


## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efecto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Efecto de sombra paralela (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
