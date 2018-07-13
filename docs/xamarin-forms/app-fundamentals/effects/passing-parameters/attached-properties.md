---
title: Efecto de pasar parámetros como propiedades adjuntas
description: Las propiedades adjuntas se pueden usar para definir los parámetros del efecto que responden a los cambios de propiedad en tiempo de ejecución. Este artículo se muestra el uso de propiedades para pasar parámetros a un efecto y el cambio de un parámetro en tiempo de ejecución asociadas.
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 9483e424a74a88ce3f0eb49624bb5315551f2062
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996456"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>Efecto de pasar parámetros como propiedades adjuntas

_Las propiedades adjuntas se pueden usar para definir los parámetros del efecto que responden a los cambios de propiedad en tiempo de ejecución. Este artículo se muestra el uso de propiedades para pasar parámetros a un efecto y el cambio de un parámetro en tiempo de ejecución asociadas._

El proceso de creación de los parámetros del efecto que responden a los cambios de propiedad en tiempo de ejecución es como sigue:

1. Crear un `static` clase que contiene una propiedad adjunta para cada parámetro que se pasarán al efecto.
1. Agregue una propiedad adjunta adicional a la clase que se usará para controlar la adición o eliminación del efecto para el control que se conecte a la clase. Asegúrese de que este asociado de propiedad registra un `propertyChanged` delegado que se ejecutará cuando cambia el valor de la propiedad.
1. Crear `static` captadores y establecedores para cada propiedad adjunta.
1. Implementar la lógica en el `propertyChanged` delegado para agregar y quitar el efecto.
1. Implementar una clase anidada dentro de la `static` (clase), con el nombre del efecto, cuyas subclases el `RoutingEffect` clase. Para el constructor, llame al constructor de clase base, pasando una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase efecto específico de la plataforma.

A continuación, se pueden pasar parámetros al efecto mediante la adición de las propiedades adjuntas y los valores de propiedad para el control adecuado. Además, los parámetros pueden cambiarse en tiempo de ejecución mediante la especificación de un nuevo valor de propiedad adjunta.

> [!NOTE]
> Una propiedad adjunta es un tipo especial de propiedad enlazable, definida en una clase, pero Unidos a otros objetos y reconocible en XAML como atributos que contienen una clase y un nombre de propiedad separados por un punto. Para obtener más información, consulte [Attached Properties](~/xamarin-forms/xaml/attached-properties.md).

La aplicación de ejemplo se muestra un `ShadowEffect` que agrega una sombra al texto mostrado por un [ `Label` ](xref:Xamarin.Forms.Label) control. Además, se puede cambiar el color de la sombra en tiempo de ejecución. El siguiente diagrama ilustra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](attached-properties-images/shadow-effect.png "Responsabilidades del proyecto de efecto de sombra")

Un [ `Label` ](xref:Xamarin.Forms.Label) control en el `HomePage` se personaliza la `LabelShadowEffect` en cada proyecto específico de plataforma. Los parámetros se pasan a cada `LabelShadowEffect` a través de las propiedades adjuntas de la `ShadowEffect` clase. Cada `LabelShadowEffect` clase se deriva de la `PlatformEffect` clase para cada plataforma. Esto da como resultado una sombra que se agregan al texto mostrado por el `Label` controlar, como se muestra en las capturas de pantalla siguiente:

![](attached-properties-images/screenshots.png "Efecto de sombra paralela en cada plataforma")

## <a name="creating-effect-parameters"></a>Crear parámetros de efecto

Un `static` debe crearse la clase para representar parámetros efecto, como se muestra en el ejemplo de código siguiente:

```csharp
public static class ShadowEffect
{
  public static readonly BindableProperty HasShadowProperty =
    BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false, propertyChanged: OnHasShadowChanged);
  public static readonly BindableProperty ColorProperty =
    BindableProperty.CreateAttached ("Color", typeof(Color), typeof(ShadowEffect), Color.Default);
  public static readonly BindableProperty RadiusProperty =
    BindableProperty.CreateAttached ("Radius", typeof(double), typeof(ShadowEffect), 1.0);
  public static readonly BindableProperty DistanceXProperty =
    BindableProperty.CreateAttached ("DistanceX", typeof(double), typeof(ShadowEffect), 0.0);
  public static readonly BindableProperty DistanceYProperty =
    BindableProperty.CreateAttached ("DistanceY", typeof(double), typeof(ShadowEffect), 0.0);

  public static bool GetHasShadow (BindableObject view)
  {
    return (bool)view.GetValue (HasShadowProperty);
  }

  public static void SetHasShadow (BindableObject view, bool value)
  {
    view.SetValue (HasShadowProperty, value);
  }
  ...

  static void OnHasShadowChanged (BindableObject bindable, object oldValue, object newValue)
  {
    var view = bindable as View;
    if (view == null) {
      return;
    }

    bool hasShadow = (bool)newValue;
    if (hasShadow) {
      view.Effects.Add (new LabelShadowEffect ());
    } else {
      var toRemove = view.Effects.FirstOrDefault (e => e is LabelShadowEffect);
      if (toRemove != null) {
        view.Effects.Remove (toRemove);
      }
    }
  }

  class LabelShadowEffect : RoutingEffect
  {
    public LabelShadowEffect () : base ("MyCompany.LabelShadowEffect")
    {
    }
  }
}
```

El `ShadowEffect` contiene cinco propiedades adjuntas, con `static` captadores y establecedores para cada propiedad adjunta. Cuatro de estas propiedades representan los parámetros que se pasarán a cada uno específico de plataforma `LabelShadowEffect`. El `ShadowEffect` clase define también un `HasShadow` adjunta la propiedad que se usa para controlar la adición o eliminación del efecto para el control que el `ShadowEffect` clase está conectada a. Esto adjunta la propiedad registra el `OnHasShadowChanged` método que se ejecutará cuando cambia el valor de la propiedad. Este método agrega o quita el efecto en función del valor de la `HasShadow` propiedad adjunta.

Anidado `LabelShadowEffect` clase cuyas subclases el [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) clase admite efecto adición y eliminación. La `RoutingEffect` clase representa un efecto de independiente de la plataforma que ajusta un efecto interno que es específico de plataforma normalmente. Esto simplifica el proceso de eliminación del efecto, ya que no hay ningún acceso de tiempo de compilación a la información de tipo para un efecto específico de la plataforma. El `LabelShadowEffect` constructor llama al constructor de clase base, pasando un parámetro que consta de una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase efecto específico de la plataforma. Esto permite efecto adición y eliminación en el `OnHasShadowChanged` método, como se indica a continuación:

- **Adición de efectos** : una nueva instancia de la `LabelShadowEffect` se agrega al control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección. Esto reemplaza mediante el [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) método para agregar el efecto.
- **Eliminación de efectos** : la primera instancia de la `LabelShadowEffect` en el control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección se recupera y se quitan.

## <a name="consuming-the-effect"></a>Consumir el efecto

Cada uno específico de plataforma `LabelShadowEffect` pueden usarse mediante la adición de las propiedades asociadas a un [ `Label` ](xref:Xamarin.Forms.Label) controlar, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Label Text="Label Shadow Effect" ...
       local:ShadowEffect.HasShadow="true" local:ShadowEffect.Radius="5"
       local:ShadowEffect.DistanceX="5" local:ShadowEffect.DistanceY="5">
  <local:ShadowEffect.Color>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="Black" />
        <On Platform="Android" Value="White" />
        <On Platform="UWP" Value="Red" />
    </OnPlatform>
  </local:ShadowEffect.Color>
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

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

Establecer el `ShadowEffect.HasShadow` propiedad adjunta `true` ejecuta el `ShadowEffect.OnHasShadowChanged` método que agrega o quita el `LabelShadowEffect` a la [ `Label` ](xref:Xamarin.Forms.Label) control. En ambos ejemplos de código, el `ShadowEffect.Color` propiedad adjunta proporciona valores de color específicos de la plataforma. Para obtener más información, consulte [clase de dispositivo](~/xamarin-forms/platform/device.md).

Además, un [ `Button` ](xref:Xamarin.Forms.Button) permite que el color de sombra que se puede cambiar en tiempo de ejecución. Cuando el `Button` se hace clic en, los cambios de código siguiente color de la sombra estableciendo el `ShadowEffect.Color` propiedad asociada:

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>Consumir el efecto con un estilo

Los efectos que pueden usarse mediante la adición de las propiedades adjuntas a un control también pueden utilizarse en un estilo. El ejemplo de código XAML siguiente muestra un *explícita* estilo para el efecto de sombra paralela, que se puede aplicar a [ `Label` ](xref:Xamarin.Forms.Label) controles:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="True" />
    <Setter Property="local:ShadowEffect.Radius" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceX" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceY" Value="5" />
  </Style.Setters>
</Style>
```

El [ `Style` ](xref:Xamarin.Forms.Style) puede aplicarse a un [ `Label` ](xref:Xamarin.Forms.Label) estableciendo su [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propiedad a la `Style` instancia mediante el `StaticResource`extensión de marcado, como se muestra en el ejemplo de código siguiente:

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

Para obtener más información sobre los estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

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
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                Control.Layer.ShadowOpacity = 1.0f;
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateRadius ()
        {
            Control.Layer.CornerRadius = (nfloat)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            Control.Layer.ShadowColor = ShadowEffect.GetColor (Element).ToCGColor ();
        }

        void UpdateOffset ()
        {
            Control.Layer.ShadowOffset = new CGSize (
                (double)ShadowEffect.GetDistanceX (Element),
                (double)ShadowEffect.GetDistanceY (Element));
        }
    }
```

El `OnAttached` método llama a los métodos que recuperan los valores de propiedad adjunta mediante el `ShadowEffect` captadores y que establezca `Control.Layer` propiedades a los valores de propiedad para crear la sombra. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque es necesaria ninguna limpieza.

#### <a name="responding-to-property-changes"></a>Responder a los cambios de propiedad

Si alguno de los `ShadowEffect` adjunta cambian los valores de propiedad en tiempo de ejecución, el efecto se debe responder mostrando los cambios. Una versión reemplazada de la `OnElementPropertyChanged` método en la clase específica de la plataforma efecto, es el lugar para responder a los cambios de propiedad enlazable, como se muestra en el ejemplo de código siguiente:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

El `OnElementPropertyChanged` método actualiza el radio, el color o el desplazamiento de la sombra, siempre que el correspondiente `ShadowEffect` ha cambiado el valor de propiedad adjunta. Siempre se debe realizar una comprobación para la propiedad que se ha modificado, como esta invalidación puede llamarse varias veces.

### <a name="android-project"></a>Proyecto de Android

El siguiente ejemplo de código muestra la `LabelShadowEffect` implementación para el proyecto de Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        Android.Widget.TextView control;
        Android.Graphics.Color color;
        float radius, distanceX, distanceY;

        protected override void OnAttached ()
        {
            try {
                control = Control as Android.Widget.TextView;
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                UpdateControl ();
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateControl ()
        {
            if (control != null) {
                control.SetShadowLayer (radius, distanceX, distanceY, color);
            }
        }

        void UpdateRadius ()
        {
            radius = (float)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            color = ShadowEffect.GetColor (Element).ToAndroid ();
        }

        void UpdateOffset ()
        {
            distanceX = (float)ShadowEffect.GetDistanceX (Element);
            distanceY = (float)ShadowEffect.GetDistanceY (Element);
        }
    }
```

El `OnAttached` método llama a los métodos que recuperan los valores de propiedad adjunta mediante el `ShadowEffect` captadores y llama a un método que llama a la [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) método para crear una sombra con los valores de propiedad. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque es necesaria ninguna limpieza.

#### <a name="responding-to-property-changes"></a>Responder a los cambios de propiedad

Si alguno de los `ShadowEffect` adjunta cambian los valores de propiedad en tiempo de ejecución, el efecto se debe responder mostrando los cambios. Una versión reemplazada de la `OnElementPropertyChanged` método en la clase específica de la plataforma efecto, es el lugar para responder a los cambios de propiedad enlazable, como se muestra en el ejemplo de código siguiente:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
      UpdateControl ();
    }
  }
  ...
}
```

El `OnElementPropertyChanged` método actualiza el radio, el color o el desplazamiento de la sombra, siempre que el correspondiente `ShadowEffect` ha cambiado el valor de propiedad adjunta. Siempre se debe realizar una comprobación para la propiedad que se ha modificado, como esta invalidación puede llamarse varias veces.

### <a name="universal-windows-platform-project"></a>Proyecto de la plataforma universal de Windows

El siguiente ejemplo de código muestra la `LabelShadowEffect` implementación para el proyecto de plataforma Universal de Windows (UWP):

```csharp
[assembly: ResolutionGroupName ("MyCompany")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        Label shadowLabel;
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;

                    shadowLabel = new Label ();
                    shadowLabel.Text = textBlock.Text;
                    shadowLabel.FontAttributes = FontAttributes.Bold;
                    shadowLabel.HorizontalOptions = LayoutOptions.Center;
                    shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;

                    UpdateColor ();
                    UpdateOffset ();

                    ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                    shadowAdded = true;
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateColor ()
        {
            shadowLabel.TextColor = ShadowEffect.GetColor (Element);
        }

        void UpdateOffset ()
        {
            shadowLabel.TranslationX = ShadowEffect.GetDistanceX (Element);
            shadowLabel.TranslationY = ShadowEffect.GetDistanceY (Element);
        }
    }
}
```

La plataforma Universal de Windows no proporciona un efecto de sombra, lo cual la `LabelShadowEffect` simula la implementación en ambas plataformas uno mediante la adición de un segundo desplazamiento [ `Label` ](xref:Xamarin.Forms.Label) detrás de la réplica principal `Label`. El `OnAttached` crea el nuevo método `Label` y establece algunas propiedades de diseño en el `Label`. A continuación, llama a los métodos que recuperan los valores de propiedad adjunta mediante el `ShadowEffect` captadores y crea la sombra estableciendo el [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)y [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propiedades para controlar el color y la ubicación de la `Label`. El `shadowLabel` , a continuación, se inserta desplazamiento respecto a la principal `Label`. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque es necesaria ninguna limpieza.

#### <a name="responding-to-property-changes"></a>Responder a los cambios de propiedad

Si alguno de los `ShadowEffect` adjunta cambian los valores de propiedad en tiempo de ejecución, el efecto se debe responder mostrando los cambios. Una versión reemplazada de la `OnElementPropertyChanged` método en la clase específica de la plataforma efecto, es el lugar para responder a los cambios de propiedad enlazable, como se muestra en el ejemplo de código siguiente:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
                      args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

El `OnElementPropertyChanged` método actualiza el color o el desplazamiento de la sombra, siempre que el correspondiente `ShadowEffect` ha cambiado el valor de propiedad adjunta. Siempre se debe realizar una comprobación para la propiedad que se ha modificado, como esta invalidación puede llamarse varias veces.

## <a name="summary"></a>Resumen

Este artículo se demuestra el uso de propiedades para pasar parámetros a un efecto y el cambio de un parámetro en tiempo de ejecución asociadas. Las propiedades adjuntas se pueden usar para definir los parámetros del efecto que responden a los cambios de propiedad en tiempo de ejecución.


## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efecto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Efecto de sombra paralela (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffectruntimechange/)
