---
title: Creación de un efecto
description: Efectos simplifican la personalización de un control. En este artículo se muestra cómo crear un efecto que cambia el color de fondo del control de entrada cuando el control recibe el foco.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: b29d83999724a35293882f7b9efc0158171c4fd2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998165"
---
# <a name="creating-an-effect"></a>Creación de un efecto

_Efectos simplifican la personalización de un control. En este artículo se muestra cómo crear un efecto que cambia el color de fondo del control de entrada cuando el control recibe el foco._

El proceso para crear un efecto de cada proyecto específico de la plataforma es como sigue:

1. Crear una subclase de la `PlatformEffect` clase.
1. Invalidar el `OnAttached` lógica del método y escritura para personalizar el control.
1. Invalidar el `OnDetached` lógica del método y escritura para limpiar la personalización de controles, si es necesario.
1. Agregar un [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) a la clase del efecto. Este atributo establece un espacio de nombres amplia de empresa para los efectos, evitar conflictos con otros efectos con el mismo nombre. Tenga en cuenta que este atributo puede solo se aplica una vez por proyecto.
1. Agregar un [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) a la clase del efecto. Este atributo registra el efecto con un identificador único que se utiliza con Xamarin.Forms, junto con el nombre del grupo, para buscar el efecto antes de aplicarla a un control. El atributo toma dos parámetros: el nombre de tipo del efecto y una cadena única que se usará para buscar el efecto antes de aplicarla a un control.

El efecto, a continuación, puede utilizarse adjuntando el control adecuado.

> [!NOTE]
> Es opcional proporcionar un efecto en cada proyecto de la plataforma. Al intentar utilizar un efecto cuando uno no está registrado, se devolverá un valor distinto de null que no hace nada.

La aplicación de ejemplo se muestra un `FocusEffect` que cambia el color de fondo de un control cuando recibe el foco. El siguiente diagrama ilustra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](creating-images/focus-effect.png "Responsabilidades de proyecto del efecto de foco")

Un [ `Entry` ](xref:Xamarin.Forms.Entry) control en el `HomePage` se personaliza la `FocusEffect` clase en cada proyecto específico de plataforma. Cada `FocusEffect` clase se deriva de la `PlatformEffect` clase para cada plataforma. Esto da como resultado la `Entry` controlar que se representa con un color de fondo de específicos de la plataforma, que cambia cuando el control recibe el foco, como se muestra en las capturas de pantalla siguiente:

![](creating-images/screenshots-1.png "Efecto en cada plataforma se centran")
![](creating-images/screenshots-2.png "centrarse efecto en cada plataforma")

## <a name="creating-the-effect-on-each-platform"></a>Crear el efecto en cada plataforma

Las secciones siguientes describen la implementación específica de la plataforma de la `FocusEffect` clase.

## <a name="ios-project"></a>Proyecto de iOS

El siguiente ejemplo de código muestra la `FocusEffect` implementación para el proyecto de iOS:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.iOS
{
    public class FocusEffect : PlatformEffect
    {
        UIColor backgroundColor;

        protected override void OnAttached ()
        {
            try {
                Control.BackgroundColor = backgroundColor = UIColor.FromRGB (204, 153, 255);
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);

            try {
                if (args.PropertyName == "IsFocused") {
                    if (Control.BackgroundColor == backgroundColor) {
                        Control.BackgroundColor = UIColor.White;
                    } else {
                        Control.BackgroundColor = backgroundColor;
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

El `OnAttached` método establece el `BackgroundColor` propiedad del control en púrpura claro con el `UIColor.FromRGB` método y también almacena este color en un campo. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control está asociado el efecto a no tiene un `BackgroundColor` propiedad. No se proporciona ninguna implementación por el `OnDetached` método porque es necesaria ninguna limpieza.

El `OnElementPropertyChanged` invalidación responde a los cambios de propiedad enlazable en el control de Xamarin.Forms. Cuando el [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) los cambios de propiedad, el `BackgroundColor` se cambia una propiedad del control en blanco si el control tiene el foco, en caso contrario, se cambia a morado claro. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control está asociado el efecto a no tiene un `BackgroundColor` propiedad.

## <a name="android-project"></a>Proyecto de Android

El siguiente ejemplo de código muestra la `FocusEffect` implementación para el proyecto de Android:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached ()
        {
            try {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor (backgroundColor);

            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);
            try {
                if (args.PropertyName == "IsFocused") {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor) {
                        Control.SetBackgroundColor (Android.Graphics.Color.Black);
                    } else {
                        Control.SetBackgroundColor (backgroundColor);
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

El `OnAttached` llamadas al método el `SetBackgroundColor` método para establecer el color de fondo del control a la luz verde y también almacena este color en un campo. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control está asociado el efecto a no tiene un `SetBackgroundColor` propiedad. No se proporciona ninguna implementación por el `OnDetached` método porque es necesaria ninguna limpieza.

El `OnElementPropertyChanged` invalidación responde a los cambios de propiedad enlazable en el control de Xamarin.Forms. Cuando el [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) los cambios de propiedad, se cambia el color de fondo del control en blanco si el control tiene el foco, de lo contrario se cambia a verde claro. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control está asociado el efecto a no tiene un `BackgroundColor` propiedad.

## <a name="universal-windows-platform-projects"></a>Proyectos de la plataforma universal de Windows

El siguiente ejemplo de código muestra la `FocusEffect` implementación para proyectos de plataforma Universal de Windows (UWP):

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.UWP
{
    public class FocusEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            try
            {
                (Control as Windows.UI.Xaml.Controls.Control).Background = new SolidColorBrush(Colors.Cyan);
                (Control as FormsTextBox).BackgroundFocusBrush = new SolidColorBrush(Colors.White);
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }
    }
}
```

El `OnAttached` método establece el `Background` propiedad del control en cian y establece el `BackgroundFocusBrush` propiedad en blanco. Esta funcionalidad se encapsula en un `try` / `catch` bloquear en caso de que el control está asociado el efecto que carece de estas propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque es necesaria ninguna limpieza.

## <a name="consuming-the-effect"></a>Consumir el efecto

El proceso para consumir un efecto desde un proyecto de biblioteca compartida o la biblioteca Xamarin.Forms .NET Standard es como sigue:

1. Declare un control que se va a personalizar el efecto.
1. Adjuntar el efecto para el control al agregarlo al control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección.

> [!NOTE]
> Una instancia de efecto sólo puede asociarse a un único control. Por lo tanto, se debe resolver un efecto dos veces para usarlo en dos controles.

## <a name="consuming-the-effect-in-xaml"></a>Consumir el efecto en XAML

El ejemplo de código XAML siguiente muestra un [ `Entry` ](xref:Xamarin.Forms.Entry) control al que el `FocusEffect` está conectado:

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

La `FocusEffect` clase en la biblioteca estándar de .NET admite el consumo de efecto en XAML y se muestra en el ejemplo de código siguiente:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

El `FocusEffect` subclases de clases la [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) (clase), que representa un efecto de independiente de la plataforma que encapsula un efecto interno que es específico de plataforma normalmente. El `FocusEffect` clase llama al constructor de clase base, pasando un parámetro que consta de una concatenación del nombre del grupo de resolución (especificado mediante el [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo de la clase efecto), y el Id. exclusivo que se especificó mediante la [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributo de la clase del efecto. Por lo tanto, cuando el [ `Entry` ](xref:Xamarin.Forms.Entry) se inicializa en tiempo de ejecución, una nueva instancia de la `MyCompany.FocusEffect` se agrega al control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección.

Los efectos también pueden asociarse a los controles mediante el uso de un comportamiento, o mediante el uso de propiedades adjuntas. Para obtener más información acerca de cómo adjuntar un efecto a un control mediante el uso de un comportamiento, consulte [EffectBehavior reutilizable](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Para obtener más información acerca de cómo adjuntar un efecto a un control mediante el uso de las propiedades adjuntas, consulte [pasar parámetros a un efecto](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Consumir el efecto en C&num;

El equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) en C# se muestra en el ejemplo de código siguiente:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

El `FocusEffect` está asociado a la `Entry` instancia agregando el efecto para el control [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección, como se muestra en el ejemplo de código siguiente:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

El [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) devuelve un [ `Effect` ](xref:Xamarin.Forms.Effect) para el nombre especificado, que es una concatenación del nombre del grupo de resolución (especificado mediante el [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo de la clase efecto) y el identificador único que se especificó mediante la [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributo de la clase del efecto. Si una plataforma no proporciona el efecto, el `Effect.Resolve` devolverá el método que no es`null` valor.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo crear un efecto que cambia el color de fondo de la [ `Entry` ](xref:Xamarin.Forms.Entry) controlar cuando el control recibe el foco.


## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efecto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Efecto de Color de fondo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [Efecto de enfoque (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
