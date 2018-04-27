---
title: Pasar parámetros de efecto como propiedades adjuntas
description: Propiedades adjuntas pueden utilizarse para definir los parámetros del efecto que responden a los cambios de propiedad en tiempo de ejecución. Este artículo se muestra el uso de propiedades para pasar parámetros a un efecto y el cambio de un parámetro en tiempo de ejecución asociadas.
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 5bca36189100942e21d1d750dd156dab0cf45fc4
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="passing-effect-parameters-as-attached-properties"></a>Pasar parámetros de efecto como propiedades adjuntas

_Propiedades adjuntas pueden utilizarse para definir los parámetros del efecto que responden a los cambios de propiedad en tiempo de ejecución. Este artículo se muestra el uso de propiedades para pasar parámetros a un efecto y el cambio de un parámetro en tiempo de ejecución asociadas._

El proceso para crear parámetros de efecto que responden a los cambios de propiedad en tiempo de ejecución es el siguiente:

1. Crear un `static` clase que contiene una propiedad adjunta para cada parámetro que se pasa el resultado.
1. Agregar una propiedad adjunta adicional a la clase que se usará para controlar la adición o eliminación del efecto al control que se conecte a la clase. Asegúrese de que esta conectado propiedad registra un `propertyChanged` delegado que se ejecutará cuando cambia el valor de la propiedad.
1. Crear `static` captadores y establecedores para cada propiedad adjuntan.
1. Implementar la lógica en el `propertyChanged` delegado para agregar y quitar el efecto.
1. Implementar una clase anidada dentro de la `static` (clase), con el nombre del efecto, las subclases de la `RoutingEffect` clase. Para el constructor, llame al constructor de clase base, pasando una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase de efecto específico de la plataforma.

Parámetros, a continuación, pueden pasarse al efecto mediante la adición de las propiedades asociadas y valores de propiedad para el control adecuado. Además, parámetros se pueden cambiar en tiempo de ejecución mediante la especificación de un nuevo valor de propiedad adjunta.

> [!NOTE]
> Una propiedad adjunta es un tipo especial de propiedad enlazable, definido en una clase pero asociadas a otros objetos y reconocible en XAML como atributos que contienen una clase y un nombre de propiedad separados por puntos. Para obtener más información, consulte [adjunta propiedades](~/xamarin-forms/xaml/attached-properties.md).

La aplicación de ejemplo muestra un `ShadowEffect` que agrega una sombra al texto que muestra un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) control. Además, se puede cambiar el color de la sombra en tiempo de ejecución. El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](attached-properties-images/shadow-effect.png "Responsabilidades del proyecto de efecto de sombra")

A [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) en controlar la `HomePage` para personalizar el `LabelShadowEffect` en cada proyecto específico de la plataforma. Los parámetros se pasan a cada `LabelShadowEffect` a través de propiedades en adjuntas la `ShadowEffect` clase. Cada `LabelShadowEffect` clase se deriva de la `PlatformEffect` clase para cada plataforma. Esto da como resultado una sombra que se agrega el texto mostrado por el `Label` controlar, como se muestra en las capturas de pantalla siguiente:

![](attached-properties-images/screenshots.png "Efecto de sombra en cada plataforma")

## <a name="creating-effect-parameters"></a>Crear parámetros de efecto

Un `static` clase se debe crear para representar parámetros de efecto, como se muestra en el ejemplo de código siguiente:

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

El `ShadowEffect` contiene cinco propiedades adjuntas, con `static` captadores y establecedores para cada propiedad adjuntan. Cuatro de estas propiedades representan parámetros que se pasan a cada específica de la plataforma `LabelShadowEffect`. El `ShadowEffect` clase también define un `HasShadow` adjunta la propiedad que se usa para controlar la adición o eliminación del efecto al control que el `ShadowEffect` clase está conectada a. Esto adjunta los registros de propiedad del `OnHasShadowChanged` método que se ejecutará cuando cambia el valor de la propiedad. Este método agrega o quita el efecto en función del valor de la `HasShadow` propiedad adjunta.

Anidado `LabelShadowEffect` de la clase, las subclases de la [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) clase es compatible con efecto adición y eliminación. La `RoutingEffect` clase representa un efecto de independiente de la plataforma que contiene un efecto interno que es normalmente específica de la plataforma. Esto simplifica el proceso de eliminación del efecto, porque no hay ningún acceso en tiempo de compilación a la información de tipo para un efecto específico de la plataforma. El `LabelShadowEffect` constructor llama al constructor de clase base, pasando un parámetro que consta de una concatenación del nombre del grupo de resolución y el identificador único que se ha especificado en cada clase de efecto específico de la plataforma. Esto permite la adición de efecto y eliminación en el `OnHasShadowChanged` método, tal como se indica a continuación:

- **Adición de efectos** : una nueva instancia de la `LabelShadowEffect` se agrega al control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección. Se reemplaza con la [ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/) método para agregar el efecto.
- **Efecto eliminación** : la primera instancia de la `LabelShadowEffect` en el control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección se recupera y se quitan.

## <a name="consuming-the-effect"></a>Consumir el efecto

Cada específica de la plataforma `LabelShadowEffect` pueden utilizarse mediante la adición de las propiedades asociadas a un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controlar, como se muestra en el siguiente ejemplo de código XAML:

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

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

Establecer el `ShadowEffect.HasShadow` propiedad adjunta `true` ejecuta el `ShadowEffect.OnHasShadowChanged` método que agrega o quita el `LabelShadowEffect` a la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) control. En ambos ejemplos de código, el `ShadowEffect.Color` propiedad adjunta proporciona valores de color específico de la plataforma. Para obtener más información, consulte [clase de dispositivo](~/xamarin-forms/platform/device.md).

Además, un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) permite que el color de sombra cambiarse en tiempo de ejecución. Cuando el `Button` se hace clic en, los cambios de código siguiente color de la sombra estableciendo la `ShadowEffect.Color` propiedad asociada:

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>Consumir el efecto con un estilo

También pueden usar efectos que pueden utilizarse mediante la adición de propiedades adjuntas a un control por un estilo. El ejemplo de código XAML siguiente muestra un *explícita* estilo para el efecto de sombra, que puede aplicarse a [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controles:

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

El [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) pueden aplicarse a un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) estableciendo su [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedad a la `Style` instancia mediante el `StaticResource`extensión de marcado, como se muestra en el ejemplo de código siguiente:

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

Para obtener más información sobre los estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

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

El `OnAttached` método llama a métodos que recuperan los valores de propiedad adjunta con el `ShadowEffect` captadores y que establece `Control.Layer` propiedades a los valores de propiedad para crear la sombra. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque no es necesaria ninguna limpieza.

#### <a name="responding-to-property-changes"></a>Responder a los cambios de propiedad

Si cualquiera de los `ShadowEffect` adjunta cambian los valores de propiedad en tiempo de ejecución, las necesidades de efecto responder mostrando los cambios. Una versión reemplazada de la `OnElementPropertyChanged` método en la clase específica de la plataforma efecto, es el lugar para responder a los cambios de propiedad enlazable, como se muestra en el ejemplo de código siguiente:

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

El `OnElementPropertyChanged` método actualiza el radio, el color o el desplazamiento de la sombra, siempre que la correspondiente `ShadowEffect` ha cambiado el valor de propiedad adjunta. Siempre se debe realizar una comprobación de la propiedad que se ha modificado, como esta invalidación puede llamarse muchas veces.

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

El `OnAttached` método llama a métodos que recuperan los valores de propiedad adjunta con el `ShadowEffect` captadores y llama a un método que llama el [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) método para crear una sombra con los valores de propiedad. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque no es necesaria ninguna limpieza.

#### <a name="responding-to-property-changes"></a>Responder a los cambios de propiedad

Si cualquiera de los `ShadowEffect` adjunta cambian los valores de propiedad en tiempo de ejecución, las necesidades de efecto responder mostrando los cambios. Una versión reemplazada de la `OnElementPropertyChanged` método en la clase específica de la plataforma efecto, es el lugar para responder a los cambios de propiedad enlazable, como se muestra en el ejemplo de código siguiente:

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

El `OnElementPropertyChanged` método actualiza el radio, el color o el desplazamiento de la sombra, siempre que la correspondiente `ShadowEffect` ha cambiado el valor de propiedad adjunta. Siempre se debe realizar una comprobación de la propiedad que se ha modificado, como esta invalidación puede llamarse muchas veces.

### <a name="universal-windows-platform-project"></a>Proyecto de plataforma universal de Windows

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

La plataforma Universal de Windows no proporcionan un efecto de sombra, lo cual la `LabelShadowEffect` simula la implementación en ambas plataformas uno si agrega un segundo desplazamiento [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) detrás de la réplica principal `Label`. El `OnAttached` crea el nuevo método `Label` y establece algunas propiedades de diseño en el `Label`. A continuación, llama a los métodos que recuperan los valores de propiedad adjunta con el `ShadowEffect` captadores y crea la sombra estableciendo la [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/), [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)y [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propiedades que permiten controlar el color y la ubicación de la `Label`. El `shadowLabel` , a continuación, se inserta desplaza detrás de la réplica principal `Label`. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control que está asociado el efecto a no tiene el `Control.Layer` propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque no es necesaria ninguna limpieza.

#### <a name="responding-to-property-changes"></a>Responder a los cambios de propiedad

Si cualquiera de los `ShadowEffect` adjunta cambian los valores de propiedad en tiempo de ejecución, las necesidades de efecto responder mostrando los cambios. Una versión reemplazada de la `OnElementPropertyChanged` método en la clase específica de la plataforma efecto, es el lugar para responder a los cambios de propiedad enlazable, como se muestra en el ejemplo de código siguiente:

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

El `OnElementPropertyChanged` método actualiza el color o el desplazamiento de la sombra, siempre que la correspondiente `ShadowEffect` ha cambiado el valor de propiedad adjunta. Siempre se debe realizar una comprobación de la propiedad que se ha modificado, como esta invalidación puede llamarse muchas veces.

## <a name="summary"></a>Resumen

Este artículo se demuestra el uso de propiedades para pasar parámetros a un efecto y el cambio de un parámetro en tiempo de ejecución asociadas. Propiedades adjuntas pueden utilizarse para definir los parámetros del efecto que responden a los cambios de propiedad en tiempo de ejecución.


## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efecto](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [RoutingEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)
- [Efecto de sombra (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffectruntimechange/)
