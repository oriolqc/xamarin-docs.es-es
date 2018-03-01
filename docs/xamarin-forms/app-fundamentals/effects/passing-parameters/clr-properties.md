---
title: "Pasar parámetros de efecto como propiedades de tiempo de ejecución de lenguaje común"
description: "Propiedades de Common Language Runtime (CLR) pueden utilizarse para definir los parámetros del efecto que no responden a los cambios de propiedad en tiempo de ejecución. Este artículo muestra cómo utilizar propiedades CLR para pasar parámetros a un efecto."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: afe30ae87aa2e465013eb7fef3089cf701d98da6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Pasar parámetros de efecto como propiedades de tiempo de ejecución de lenguaje común

_Propiedades de Common Language Runtime (CLR) pueden utilizarse para definir los parámetros del efecto que no responden a los cambios de propiedad en tiempo de ejecución. Este artículo muestra cómo utilizar propiedades CLR para pasar parámetros a un efecto._

El proceso para crear parámetros de efecto que no responden a los cambios de propiedad en tiempo de ejecución es el siguiente:

1. Crear un `public` clase que cree subclases el [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) clase. La `RoutingEffect` clase representa un efecto de independiente de la plataforma que contiene un efecto interno que es normalmente específica de la plataforma.
1. Cree un constructor que llama al constructor de clase base, pasando una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase de efecto específico de la plataforma.
1. Agregar propiedades a la clase para cada parámetro que se pasa el resultado.

Parámetros, a continuación, pueden pasarse al efecto especificando valores para cada propiedad al crear una instancia del efecto.

La aplicación de ejemplo muestra un `ShadowEffect` que agrega una sombra al texto que muestra un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) control. El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](clr-properties-images/shadow-effect.png "Responsabilidades del proyecto de efecto de sombra")

A [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) en controlar la `HomePage` para personalizar el `LabelShadowEffect` en cada proyecto específico de la plataforma. Los parámetros se pasan a cada `LabelShadowEffect` a través de propiedades en la `ShadowEffect` clase. Cada `LabelShadowEffect` clase se deriva de la `PlatformEffect` clase para cada plataforma. Esto da como resultado una sombra que se agrega el texto mostrado por el `Label` controlar, como se muestra en las capturas de pantalla siguiente:

![](clr-properties-images/screenshots.png "Efecto de sombra en cada plataforma")

## <a name="creating-effect-parameters"></a>Crear parámetros de efecto

A `public` clase que cree subclases el [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) clase se debe crear para representar parámetros de efecto, como se muestra en el ejemplo de código siguiente:

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

El `ShadowEffect` contiene cuatro propiedades que representan los parámetros que se pasan a cada específica de la plataforma `LabelShadowEffect`. El constructor de clase, llama al constructor de clase base, pasando un parámetro que consta de una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase de efecto específico de la plataforma. Por lo tanto, una nueva instancia de la `MyCompany.LabelShadowEffect` se agregará a un control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección cuando un `ShadowEffect` se crea una instancia.

## <a name="consuming-the-effect"></a>Consumir el efecto

El ejemplo de código XAML siguiente muestra un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) control a la que el `ShadowEffect` está conectado:

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

El equivalente [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) en C# se muestra en el ejemplo de código siguiente:

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

En ambos ejemplos de código, una instancia de la `ShadowEffect` se crea una instancia de clase con valores que se especifican para cada propiedad, antes de que se agrega al control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección. Tenga en cuenta que el `ShadowEffect.Color` propiedad usa valores de color específico de la plataforma. Para obtener más información, consulte [clase de dispositivo](~/xamarin-forms/platform/device.md).

## <a name="creating-the-effect-on-each-platform"></a>Crear el efecto en cada plataforma

Las secciones siguientes tratan la implementación específica de la plataforma de la `LabelShadowEffect` clase.

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

El `OnAttached` método recupera el `ShadowEffect` instancia y conjuntos de `Control.Layer` propiedades a los valores de propiedad especificado para crear la sombra. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque no es necesaria ninguna limpieza.

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

El `OnAttached` método recupera el `ShadowEffect` instancia y las llamadas del [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) método para crear una sombra con los valores de propiedad especificado. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque no es necesaria ninguna limpieza.

### <a name="windows-phone--universal-windows-platform-projects"></a>Windows Phone & proyectos de la plataforma Universal de Windows

El siguiente ejemplo de código muestra la `LabelShadowEffect` implementación para los proyectos de Windows Phone y la plataforma Universal de Windows (UWP):

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.WinPhone81
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

El tiempo de ejecución de Windows y la plataforma Universal de Windows no proporcionan un efecto de sombra, lo cual la `LabelShadowEffect` simula la implementación en ambas plataformas uno si agrega un segundo desplazamiento [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) detrás de la principal `Label`. El `OnAttached` método recupera el `ShadowEffect` de la instancia, crea el nuevo `Label`y establece algunas propiedades de diseño en el `Label`. A continuación, crea la sombra estableciendo la [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/), [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/), y [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propiedades que permiten controlar el color y la ubicación de la `Label`. El `shadowLabel` , a continuación, se inserta desplaza detrás de la réplica principal `Label`. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque no es necesaria ninguna limpieza.

## <a name="summary"></a>Resumen

En este artículo ha demostrado usando las propiedades CLR para pasar parámetros a un efecto. Propiedades CLR pueden utilizarse para definir los parámetros del efecto que no responden a los cambios de propiedad en tiempo de ejecución.


## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [RoutingEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)
- [Efecto de sombra (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
