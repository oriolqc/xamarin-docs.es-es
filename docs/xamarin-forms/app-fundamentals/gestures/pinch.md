---
title: Agregar un reconocedor de gesto de alejar
description: Este artículo explica cómo usar el gesto de alejar para hacer zoom interactivo de una imagen en la ubicación de esta posibilidad.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 3600a8bf059bf29429cce35a233cc6618daa4d79
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241782"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>Agregar un reconocedor de gesto de alejar

_El gesto de alejar se usa para llevar a cabo zoom interactivo y se implementa con la clase PinchGestureRecognizer. Es un escenario común para el gesto de alejar hacer zoom interactivo de una imagen en la ubicación de esta posibilidad. Esto se logra mediante el ajuste de escala en el contenido de la ventanilla y se muestra en este artículo._

## <a name="overview"></a>Información general

Para hacer que un elemento de la interfaz de usuario puede acercar con el gesto de alejar, crear un [ `PinchGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/) de la instancia, controlar el [ `PinchUpdated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.PinchGestureRecognizer.PinchUpdated/) eventos, y agregue el nuevo reconocedor de movimientos para el [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) colección en el elemento de interfaz de usuario. El siguiente ejemplo de código muestra un `PinchGestureRecognizer` conectado a un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

También se puede lograr esto de XAML, como se muestra en el ejemplo de código siguiente:

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

El código para el `OnPinchUpdated` , a continuación, se agrega el controlador de eventos en el archivo de código subyacente:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>Crear un contenedor de PinchToZoom

Controlar el gesto de alejar para llevar a cabo una operación de zoom requiere algunos cálculos para transformar la interfaz de usuario. Esta sección contiene una clase auxiliar generalizado para realizar las operaciones matemáticas, que se pueden usar para ampliar cualquier elemento de la interfaz de usuario de forma interactiva. En el ejemplo de código siguiente se muestra la clase `PinchToZoomContainer`:

```csharp
public class PinchToZoomContainer : ContentView
{
  ...

  public PinchToZoomContainer ()
  {
    var pinchGesture = new PinchGestureRecognizer ();
    pinchGesture.PinchUpdated += OnPinchUpdated;
    GestureRecognizers.Add (pinchGesture);
  }

  void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
  {
    ...
  }
}
```

Esta clase puede ajustarse alrededor de un elemento de interfaz de usuario para que el gesto de alejar acercará el elemento de la interfaz de usuario ajustado. El ejemplo de código XAML siguiente muestra la `PinchToZoomContainer` ajuste un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PinchGesture;assembly=PinchGesture"
             x:Class="PinchGesture.HomePage">
    <ContentPage.Content>
        <Grid Padding="20">
            <local:PinchToZoomContainer>
                <local:PinchToZoomContainer.Content>
                    <Image Source="waterfront.jpg" />
                </local:PinchToZoomContainer.Content>
            </local:PinchToZoomContainer>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

El siguiente ejemplo de código muestra cómo el `PinchToZoomContainer` ajusta un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento en una página de C#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new Grid {
      Padding = new Thickness (20),
      Children = {
        new PinchToZoomContainer {
          Content = new Image { Source = ImageSource.FromFile ("waterfront.jpg") }
        }
      }
    };
  }
}
```

Cuando el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento recibe un gesto de alejar, la imagen mostrada se se ha aplicado el zoom o no. El acercamiento se realiza mediante el `PinchZoomContainer.OnPinchUpdated` método, que se muestra en el ejemplo de código siguiente:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  if (e.Status == GestureStatus.Started) {
    // Store the current scale factor applied to the wrapped user interface element,
    // and zero the components for the center point of the translate transform.
    startScale = Content.Scale;
    Content.AnchorX = 0;
    Content.AnchorY = 0;
  }
  if (e.Status == GestureStatus.Running) {
    // Calculate the scale factor to be applied.
    currentScale += (e.Scale - 1) * startScale;
    currentScale = Math.Max (1, currentScale);

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the X pixel coordinate.
    double renderedX = Content.X + xOffset;
    double deltaX = renderedX / Width;
    double deltaWidth = Width / (Content.Width * startScale);
    double originX = (e.ScaleOrigin.X - deltaX) * deltaWidth;

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the Y pixel coordinate.
    double renderedY = Content.Y + yOffset;
    double deltaY = renderedY / Height;
    double deltaHeight = Height / (Content.Height * startScale);
    double originY = (e.ScaleOrigin.Y - deltaY) * deltaHeight;

    // Calculate the transformed element pixel coordinates.
    double targetX = xOffset - (originX * Content.Width) * (currentScale - startScale);
    double targetY = yOffset - (originY * Content.Height) * (currentScale - startScale);

    // Apply translation based on the change in origin.
    Content.TranslationX = targetX.Clamp (-Content.Width * (currentScale - 1), 0);
    Content.TranslationY = targetY.Clamp (-Content.Height * (currentScale - 1), 0);

    // Apply scale factor.
    Content.Scale = currentScale;
  }
  if (e.Status == GestureStatus.Completed) {
    // Store the translation delta's of the wrapped user interface element.
    xOffset = Content.TranslationX;
    yOffset = Content.TranslationY;
  }
}
```

Este método actualiza el nivel de zoom del elemento de interfaz de usuario ajustado en función de gesto de alejar del usuario. Esto se logra mediante el uso de los valores de la [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale/), [ `ScaleOrigin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin/) y [ `Status` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.Status/) propiedades de la [ `PinchGestureUpdatedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureUpdatedEventArgs/) instancia para calcular el factor de escala que se aplicará en el origen del gesto de alejar. El elemento de usuario ajustado está ampliado, a continuación, en el origen del gesto de alejar estableciendo su [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/), [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/), y [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedades de los valores calculados.

## <a name="summary"></a>Resumen

El gesto de alejar se usa para llevar a cabo zoom interactivo y se implementa con la [ `PinchGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/) clase.


## <a name="related-links"></a>Vínculos relacionados

- [PinchGesture (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [PinchGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/)
