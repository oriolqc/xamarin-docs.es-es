---
title: Crear un efecto
description: Efectos simplifican la personalización de un control. Este artículo demuestra cómo crear un efecto que cambia el color de fondo del control de entrada cuando el control recibe el foco.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: d308ea4a9d4055c75ef3a4a1283b5d6a5ab24b0a
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846810"
---
# <a name="creating-an-effect"></a>Crear un efecto

_Efectos simplifican la personalización de un control. Este artículo demuestra cómo crear un efecto que cambia el color de fondo del control de entrada cuando el control recibe el foco._

El proceso para crear un efecto en cada proyecto específico de la plataforma es como sigue:

1. Crear una subclase de la `PlatformEffect` clase.
1. Invalidar el `OnAttached` método y escritura lógica para personalizar el control.
1. Invalidar el `OnDetached` método y escritura lógica para limpiar la personalización de control, si es necesario.
1. Agregar un [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) atributo a la clase del efecto. Este atributo establece un espacio de nombres amplia de empresa para aplicar efectos, evitar conflictos con otros efectos con el mismo nombre. Tenga en cuenta que este atributo solo puede aplicarse una vez por proyecto.
1. Agregar un [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributo a la clase del efecto. Este atributo registra el efecto con un identificador único que se utiliza con Xamarin.Forms, junto con el nombre del grupo, para buscar el efecto antes de aplicarlo a un control. El atributo toma dos parámetros: el nombre de tipo de una cadena única que se utilizará para buscar el efecto antes de aplicarlo a un control y el efecto.

El efecto, a continuación, puede utilizarse mediante una asociación para el control adecuado.

> [!NOTE]
> Es opcional proporcionar un efecto en cada proyecto de la plataforma. Intenta utilizar un efecto cuando uno no está registrado, se devolverá un valor distinto de null que no hace nada.

La aplicación de ejemplo muestra un `FocusEffect` que cambia el color de fondo de un control cuando recibe el foco. El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](creating-images/focus-effect.png "Responsabilidades del proyecto de efecto de foco")

Un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) en controlar la `HomePage` para personalizar el `FocusEffect` clase en cada proyecto específico de la plataforma. Cada `FocusEffect` clase se deriva de la `PlatformEffect` clase para cada plataforma. Esto da como resultado el `Entry` control se represente con un color de fondo de específico de la plataforma, que cambia cuando el control recibe el foco, como se muestra en las capturas de pantalla siguiente:

![](creating-images/screenshots-1.png "Efecto en cada plataforma se centran")
![](creating-images/screenshots-2.png "centrarse efecto en cada plataforma")

## <a name="creating-the-effect-on-each-platform"></a>Crear el efecto en cada plataforma

Las secciones siguientes tratan la implementación específica de la plataforma de la `FocusEffect` clase.

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

El `OnAttached` método establece la `BackgroundColor` propiedad del control en púrpura claro con el `UIColor.FromRGB` método y también almacena este color en un campo. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control está asociado el efecto a no tiene un `BackgroundColor` propiedad. No se proporciona ninguna implementación por el `OnDetached` método porque no es necesaria ninguna limpieza.

El `OnElementPropertyChanged` invalidación responde a los cambios de propiedad enlazable en el control de Xamarin.Forms. Cuando el [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) cambios de propiedades, el `BackgroundColor` propiedad del control se cambia a blanco si el control tiene el foco, en caso contrario, se cambia a morado claro. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control está asociado el efecto a no tiene un `BackgroundColor` propiedad.

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

El `OnAttached` llamadas al método el `SetBackgroundColor` método para establecer el color de fondo del control a la luz verde y también almacena este color en un campo. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control está asociado el efecto a no tiene un `SetBackgroundColor` propiedad. No se proporciona ninguna implementación por el `OnDetached` método porque no es necesaria ninguna limpieza.

El `OnElementPropertyChanged` invalidación responde a los cambios de propiedad enlazable en el control de Xamarin.Forms. Cuando el [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) cambios de propiedades, se cambia el color de fondo del control en blanco si el control tiene el foco, en caso contrario se cambia a verde claro. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control está asociado el efecto a no tiene un `BackgroundColor` propiedad.

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

El `OnAttached` método establece la `Background` propiedad del control a aguamarina y se establece la `BackgroundFocusBrush` propiedad en blanco. Esta funcionalidad se ajusta en un `try` / `catch` bloquear en caso de que el control está asociado el efecto a no tiene estas propiedades. No se proporciona ninguna implementación por el `OnDetached` método porque no es necesaria ninguna limpieza.

## <a name="consuming-the-effect"></a>Consumir el efecto

El proceso para consumir un efecto de una biblioteca Xamarin.Forms .NET estándar o un proyecto de biblioteca compartida es como sigue:

1. Declare un control que se pueden personalizar mediante el efecto.
1. Adjuntar el efecto en el control al agregarlo al control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección.

> [!NOTE]
> Una instancia de efecto sólo puede asociarse a un control único. Por lo tanto, se debe resolver un efecto dos veces para usarlo en dos controles.

## <a name="consuming-the-effect-in-xaml"></a>Consumir el efecto en XAML

El ejemplo de código XAML siguiente muestra un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control a la que el `FocusEffect` está conectado:

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

La `FocusEffect` clase en la biblioteca estándar de .NET permite consumir el efecto en XAML y se muestra en el ejemplo de código siguiente:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

El `FocusEffect` clase subclases el [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) (clase), que representa un efecto de independiente de la plataforma que contiene un efecto interno que es normalmente específica de la plataforma. El `FocusEffect` clase llama al constructor de clase base, pasando un parámetro que consta de una concatenación del nombre del grupo de resolución (especificado mediante el [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) atributo en la clase efecto), y el Id. exclusivo que se especificó mediante la [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributo en la clase de efecto. Por lo tanto, cuando la [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) se inicializa en tiempo de ejecución, una nueva instancia de la `MyCompany.FocusEffect` se agrega al control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección.

Efectos también se pueden conectar a los controles mediante un comportamiento o mediante el uso de propiedades adjuntas. Para obtener más información acerca de cómo adjuntar un efecto a un control mediante un comportamiento, consulte [EffectBehavior reutilizable](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Para obtener más información acerca de cómo adjuntar un efecto a un control mediante el uso de propiedades adjuntas, vea [pasar parámetros a un efecto](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Consumir el efecto en C&num;

El equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) en C# se muestra en el ejemplo de código siguiente:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

El `FocusEffect` está conectado a la `Entry` instancia agregando el efecto en el control [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección, como se muestra en el ejemplo de código siguiente:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

El [ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/) devuelve un [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) para el nombre especificado, que es una concatenación del nombre del grupo de resolución (especificado mediante el [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) atributo en la clase efecto) y el identificador único que se especificó mediante la [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributo en la clase de efecto. Si no le proporciona una plataforma el efecto, el `Effect.Resolve` método devolverá no`null` valor.

## <a name="summary"></a>Resumen

Este artículo muestra cómo crear un efecto que cambia el color de fondo de la [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar cuando el control recibe el foco.


## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efecto](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [Efecto de Color de fondo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [Efecto de enfoque (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
