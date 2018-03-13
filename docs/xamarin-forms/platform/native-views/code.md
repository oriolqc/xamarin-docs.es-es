---
title: Vistas nativo en C#
description: "Pueden hacer referencia directamente a vistas nativo de iOS y Android, UWP de páginas de Xamarin.Forms creadas con C#. Este artículo muestra cómo agregar vistas nativo a un diseño de Xamarin.Forms creado con C# y cómo reemplazar el diseño de vistas personalizadas para corregir su uso de la API de medida."
ms.topic: article
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 0c4014ecda0501e9309a17901c439444e4b48e86
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="native-views-in-c"></a>Vistas nativo en C#

_Pueden hacer referencia directamente a vistas nativo de iOS y Android, UWP de páginas de Xamarin.Forms creadas con C#. Este artículo muestra cómo agregar vistas nativo a un diseño de Xamarin.Forms creado con C# y cómo reemplazar el diseño de vistas personalizadas para corregir su uso de la API de medida._

## <a name="overview"></a>Información general

Cualquier control de Xamarin.Forms que permita `Content` para establecerse, o que tenga un `Children` colección, puede agregar vistas específicas de la plataforma. Por ejemplo, un iOS `UILabel` pueden agregarse directamente a la [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propiedad, o a la [ `StackLayout.Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) colección. Sin embargo, tenga en cuenta que esta funcionalidad requiere el uso de `#if` define en las soluciones de proyecto compartido de Xamarin.Forms y no está disponible desde soluciones de la biblioteca de clases portables (PCL) de Xamarin.Forms.

Las capturas de pantalla siguientes muestran específica de la plataforma vistas porque se haya agregado a un Xamarin.Forms [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/):

[![](code-images/screenshots-sml.png "Que contiene vistas específicas de la plataforma de StackLayout")](code-images/screenshots.png#lightbox "StackLayout que contiene vistas específicas de la plataforma")

La capacidad de agregar vistas específicas de la plataforma a un diseño de Xamarin.Forms se habilita mediante dos métodos de extensión en cada plataforma:

- `Add` : agrega una vista específica de la plataforma para la [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) colección de un diseño.
- `ToView` : toma una vista específica de la plataforma y ajusta como un Xamarin.Forms [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) que se puede establecer como la `Content` propiedad de un control.

Uso de estos métodos en un proyecto compartido Xamarin.Forms necesita importar el espacio de nombres de Xamarin.Forms específico de la plataforma adecuado:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **En tiempo de ejecución de Windows** : Xamarin.Forms.Platform.WinRT
- **Plataforma universal de Windows (UWP)** : Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Agregar vistas específicas de la plataforma en cada plataforma

En las secciones siguientes muestran cómo agregar vistas específicas de la plataforma a un diseño de Xamarin.Forms en cada plataforma.

### <a name="ios"></a>iOS

En el ejemplo de código siguiente se muestra cómo agregar un `UILabel` a una [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) y un [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var uiLabel = new UILabel {
  MinimumFontSize = 14f,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = originalText,
};
stackLayout.Children.Add (uiLabel);
contentView.Content = uiLabel.ToView();
```

En el ejemplo se da por supuesto que el `stackLayout` y `contentView` instancias se han creado previamente en XAML o C#.

### <a name="android"></a>Android

En el ejemplo de código siguiente se muestra cómo agregar un `TextView` a una [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) y un [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

En el ejemplo se da por supuesto que el `stackLayout` y `contentView` instancias se han creado previamente en XAML o C#.

### <a name="windows-runtime-and-universal-windows-platform"></a>En tiempo de ejecución de Windows y la plataforma Universal de Windows

En el ejemplo de código siguiente se muestra cómo agregar un `TextBlock` a una [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) y un [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var textBlock = new TextBlock
{
    Text = originalText,
    FontSize = 14,
    FontFamily = new FontFamily("HelveticaNeue"),
    TextWrapping = TextWrapping.Wrap
};
stackLayout.Children.Add(textBlock);
contentView.Content = textBlock.ToView();
```

En el ejemplo se da por supuesto que el `stackLayout` y `contentView` instancias se han creado previamente en XAML o C#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Reemplazar las mediciones de plataforma de vistas personalizadas

Vistas personalizadas en cada plataforma implementan a menudo sólo correctamente medida para el escenario de diseño para el que se diseñaron. Por ejemplo, una vista personalizada que se han diseñado para ocupar solo la mitad del ancho disponible del dispositivo. Sin embargo, después de que se comparten con otros usuarios, la vista personalizada puede ser necesaria para ocupar el ancho total disponible del dispositivo. Por lo tanto, puede ser necesario invalidar la implementación de una medida de vistas personalizadas cuando se reutiliza en un diseño de Xamarin.Forms. Por esta razón, el `Add` y `ToView` métodos de extensión proporcionan invalidaciones que permiten a los delegados de medida que se especifique lo que pueden invalidar el diseño de la vista personalizada cuando se agrega a un diseño de Xamarin.Forms.

En las secciones siguientes se muestran cómo invalidar el diseño de vistas personalizadas, para corregir su uso de la API de medida.

### <a name="ios"></a>iOS

El siguiente ejemplo de código muestra la `CustomControl` (clase), que se hereda de `UILabel`:

```csharp
public class CustomControl : UILabel
{
  public override string Text {
    get { return base.Text; }
    set { base.Text = value.ToUpper (); }
  }

  public override CGSize SizeThatFits (CGSize size)
  {
    return new CGSize (size.Width, 150);
  }
}
```

Una instancia de esta vista se agrega a un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), como se muestra en el ejemplo de código siguiente:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Sin embargo, dado que el `CustomControl.SizeThatFits` invalidación siempre devuelve un valor de alto de 150, la vista se mostrará con un espacio vacío por encima y por debajo de él, como se muestra en la siguiente captura de pantalla:

![](code-images/ios-bad-measurement.png "control personalizado con la implementación de SizeThatFits incorrectos de iOS")

Una solución a este problema consiste en proporcionar un `GetDesiredSizeDelegate` implementación, como se muestra en el ejemplo de código siguiente:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, double width, double height)
{
  var uiView = renderer.Control;

  if (uiView == null) {
    return null;
  }

  var constraint = new CGSize (width, height);

  // Let the CustomControl determine its size (which will be wrong)
  var badRect = uiView.SizeThatFits (constraint);

  // Use the width and substitute the height
  return new SizeRequest (new Size (badRect.Width, 70));
}
```

Este método usa el ancho proporcionado por el `CustomControl.SizeThatFits` método, pero sustituye el alto de 150 para una altura de 70. Cuando el `CustomControl` instancia se agrega a la [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), el `FixSize` método se puede especificar como el `GetDesiredSizeDelegate` corregir la medida incorrecta proporcionada por el `CustomControl` clase:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Esto resulta en la vista personalizada que se muestre correctamente, sin espacio en blanco por encima y por debajo de él, como se muestra en la captura de pantalla siguiente:

![](code-images/ios-good-measurement.png "control personalizado con GetDesiredSize reemplazo de iOS")

### <a name="android"></a>Android

El siguiente ejemplo de código muestra la `CustomControl` (clase), que se hereda de `TextView`:

```csharp
public class CustomControl : TextView
{
  public CustomControl (Context context) : base (context)
  {
  }

  protected override void OnMeasure (int widthMeasureSpec, int heightMeasureSpec)
  {
    int width = MeasureSpec.GetSize (widthMeasureSpec);

    // Force the width to half of what's been requested.
    // This is deliberately wrong to demonstrate providing an override to fix it with.
    int widthSpec = MeasureSpec.MakeMeasureSpec (width / 2, MeasureSpec.GetMode (widthMeasureSpec));

    base.OnMeasure (widthSpec, heightMeasureSpec);
  }
}
```

Una instancia de esta vista se agrega a un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), como se muestra en el ejemplo de código siguiente:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Sin embargo, dado que el `CustomControl.OnMeasure` invalidación siempre devuelve la mitad del ancho solicitado, la vista se mostrará que ocupan solo la mitad el ancho disponible del dispositivo, como se muestra en la captura de pantalla siguiente:

![](code-images/android-bad-measurement.png "Control personalizado de Android con implementación OnMeasure incorrecta")

Una solución a este problema consiste en proporcionar un `GetDesiredSizeDelegate` implementación, como se muestra en el ejemplo de código siguiente:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, int widthConstraint, int heightConstraint)
{
  var nativeView = renderer.Control;

  if ((widthConstraint == 0 && heightConstraint == 0) || nativeView == null) {
    return null;
  }

  int width = Android.Views.View.MeasureSpec.GetSize (widthConstraint);
  int widthSpec = Android.Views.View.MeasureSpec.MakeMeasureSpec (
    width * 2, Android.Views.View.MeasureSpec.GetMode (widthConstraint));
  nativeView.Measure (widthSpec, heightConstraint);
  return new SizeRequest (new Size (nativeView.MeasuredWidth, nativeView.MeasuredHeight));
}
```

Este método usa el ancho proporcionado por el `CustomControl.OnMeasure` (método), pero se multiplica por dos. Cuando el `CustomControl` instancia se agrega a la [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), el `FixSize` método se puede especificar como el `GetDesiredSizeDelegate` corregir la medida incorrecta proporcionada por el `CustomControl` clase:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Esto resulta en la vista personalizada que se va a muestre correctamente, que ocupan el ancho del dispositivo, como se muestra en la siguiente captura de pantalla:

![](code-images/android-good-measurement.png "Control personalizado de Android con el delegado GetDesiredSize personalizado")

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

El siguiente ejemplo de código muestra la `CustomControl` (clase), que se hereda de `Panel`:

```csharp
public class CustomControl : Panel
{
  public static readonly DependencyProperty TextProperty =
    DependencyProperty.Register(
      "Text", typeof(string), typeof(CustomControl), new PropertyMetadata(default(string), OnTextPropertyChanged));

  public string Text
  {
    get { return (string)GetValue(TextProperty); }
    set { SetValue(TextProperty, value.ToUpper()); }
  }

  readonly TextBlock textBlock;

  public CustomControl()
  {
    textBlock = new TextBlock
    {
      MinHeight = 0,
      MaxHeight = double.PositiveInfinity,
      MinWidth = 0,
      MaxWidth = double.PositiveInfinity,
      FontSize = 14,
      TextWrapping = TextWrapping.Wrap,
      VerticalAlignment = VerticalAlignment.Center
    };

    Children.Add(textBlock);
  }

  static void OnTextPropertyChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
  {
    ((CustomControl)dependencyObject).textBlock.Text = (string)args.NewValue;
  }

  protected override Size ArrangeOverride(Size finalSize)
  {
      // This is deliberately wrong to demonstrate providing an override to fix it with.
      textBlock.Arrange(new Rect(0, 0, finalSize.Width/2, finalSize.Height));
      return finalSize;
  }

  protected override Size MeasureOverride(Size availableSize)
  {
      textBlock.Measure(availableSize);
      return new Size(textBlock.DesiredSize.Width, textBlock.DesiredSize.Height);
  }
}
```

Una instancia de esta vista se agrega a un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), como se muestra en el ejemplo de código siguiente:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Sin embargo, dado que el `CustomControl.ArrangeOverride` invalidación siempre devuelve la mitad del ancho solicitado, la vista se ajustarán a la mitad del ancho disponible del dispositivo, como se muestra en la captura de pantalla siguiente:

![](code-images/winrt-bad-measurement.png "Control personalizado UWP con implementación ArrangeOverride incorrecta")

Una solución a este problema consiste en proporcionar una `ArrangeOverrideDelegate` implementación, al agregar la vista para la [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), como se muestra en el ejemplo de código siguiente:

```csharp
stackLayout.Children.Add(fixedControl, arrangeOverrideDelegate: (renderer, finalSize) =>
{
    if (finalSize.Width <= 0 || double.IsInfinity(finalSize.Width))
    {
        return null;
    }
    var frameworkElement = renderer.Control;
    frameworkElement.Arrange(new Rect(0, 0, finalSize.Width * 2, finalSize.Height));
    return finalSize;
});
```

Este método usa el ancho proporcionado por el `CustomControl.ArrangeOverride` (método), pero se multiplica por dos. Esto resulta en la vista personalizada que se va a muestre correctamente, que ocupan el ancho del dispositivo, como se muestra en la siguiente captura de pantalla:

![](code-images/winrt-good-measurement.png "Control personalizado UWP con ArrangeOverride delegado")

## <a name="summary"></a>Resumen

Este artículo explica cómo agregar vistas nativo a un diseño de Xamarin.Forms creado con C# y cómo reemplazar el diseño de vistas personalizadas para corregir su uso de la API de medida.


## <a name="related-links"></a>Vínculos relacionados

- [NativeEmbedding (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)
- [Formularios nativos](~/xamarin-forms/platform/native-forms.md)
