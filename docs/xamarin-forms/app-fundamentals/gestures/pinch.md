---
title: Adición de un módulo de reconocimiento del gesto Pinch
description: En este artículo se explica cómo usar el gesto de alejar para hacer zoom interactiva de una imagen en la ubicación pinch.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 37befdcd4ccbcd49e3cebda92d55ae6f70da2ad6
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998708"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>Adición de un módulo de reconocimiento del gesto Pinch

_El gesto de alejar se usa para llevar a cabo zoom interactivo y se implementa con la clase PinchGestureRecognizer. Un escenario común para el gesto pinch es hacer zoom interactiva de una imagen en la ubicación pinch. Esto se consigue cambiando el contenido de la ventanilla y se muestra en este artículo._

## <a name="overview"></a>Información general

Para que un elemento de interfaz de usuario se puede acercar con el gesto pinch, cree un [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) de la instancia, controlar el [ `PinchUpdated` ](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) eventos, y agregue el reconocedor de gestos de nuevo a la [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) colección en el elemento de interfaz de usuario. El siguiente ejemplo de código muestra un `PinchGestureRecognizer` adjunta a un [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

También se puede lograr esto en XAML, como se muestra en el ejemplo de código siguiente:

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

El código para el `OnPinchUpdated` , a continuación, se agrega el controlador de eventos para el archivo de código subyacente:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>Creación de un contenedor PinchToZoom

Controlar el gesto de alejar para llevar a cabo una operación de zoom, requiere un cálculo matemático para transformar la interfaz de usuario. Esta sección contiene una clase auxiliar generalizado para realizar los cálculos, que pueden usarse para cualquier elemento de la interfaz de usuario de zoom de forma interactiva. En el ejemplo de código siguiente se muestra la clase `PinchToZoomContainer`:

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

Esta clase puede colocarse en torno a un elemento de interfaz de usuario para que el gesto de alejar irá el elemento de interfaz de usuario ajustado. El ejemplo de código XAML siguiente muestra la `PinchToZoomContainer` ajuste una [ `Image` ](xref:Xamarin.Forms.Image) elemento:

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

El siguiente ejemplo de código muestra cómo el `PinchToZoomContainer` ajusta un [ `Image` ](xref:Xamarin.Forms.Image) elemento en una página de C#:

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

Cuando el [ `Image` ](xref:Xamarin.Forms.Image) elemento recibe un gesto pinch, la imagen mostrada se se ha aplicado el zoom o no. El zoom se realiza mediante el `PinchZoomContainer.OnPinchUpdated` método, que se muestra en el ejemplo de código siguiente:

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

Este método actualiza el nivel de zoom del elemento de interfaz de usuario ajustado según el gesto de reducir del usuario. Esto se logra mediante el uso de los valores de la [ `Scale` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale), [ `ScaleOrigin` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) y [ `Status` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) propiedades de la [ `PinchGestureUpdatedEventArgs` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) instancia para calcular el factor de escala que se aplicará en el origen del gesto pinch. El elemento de usuario ajustado está ampliado, a continuación, en el origen del gesto pinch estableciendo su [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX), [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY), y [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedades de los valores calculados.

## <a name="summary"></a>Resumen

El gesto pinch se usa para llevar a cabo zoom interactivo y se implementa con la [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) clase.


## <a name="related-links"></a>Vínculos relacionados

- [PinchGesture (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
