---
title: Agregar un reconocedor de movimiento panorámico
description: Este artículo explica cómo utilizar un movimiento panorámico a horizontalmente y verticalmente arrastrar una imagen, por lo que todo el contenido de imagen pueden verse cuando se muestra en una ventanilla más pequeña que las dimensiones de imagen.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 45c0a1452916f193236e5ba741f8e8e19b6691aa
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996811"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Agregar un reconocedor de movimiento panorámico

_El movimiento panorámico se utiliza para detectar arrastrando y se implementa con la clase PanGestureRecognizer. Un escenario común para el movimiento panorámico es horizontal y verticalmente, arrastre una imagen, por lo que todo el contenido de imagen pueden verse cuando se muestra en una ventanilla más pequeña que las dimensiones de imagen. Esto se logra al mover la imagen dentro de la ventanilla y se muestra en este artículo._

## <a name="overview"></a>Información general

Para que un elemento de interfaz de usuario arrastrable con el movimiento panorámico, cree un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) de la instancia, controlar el [ `PanUpdated` ](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) eventos, y agregue el reconocedor de gestos de nuevo a la [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) colección en el elemento de interfaz de usuario. El siguiente ejemplo de código muestra un `PanGestureRecognizer` adjunta a un [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

También se puede lograr esto en XAML, como se muestra en el ejemplo de código siguiente:

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

El código para el `OnPanUpdated` , a continuación, se agrega el controlador de eventos para el archivo de código subyacente:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> Panorámica correcto en Android requiere la [paquete NuGet de Xamarin.Forms 2.1.0-pre1](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1) como mínimo.

## <a name="creating-a-pan-container"></a>Creación de un contenedor de panorámica

Esta sección contiene una clase auxiliar generalizado que realiza la panorámica de forma libre, que normalmente es adecuada para navegar dentro de imágenes o mapas. Controlar el movimiento panorámico para llevar a cabo una operación de arrastrar, requiere un cálculo matemático para transformar la interfaz de usuario. Se usan este matemáticas arrastrar solo dentro de los límites del elemento de interfaz de usuario ajustado. En el ejemplo de código siguiente se muestra la clase `PanContainer`:

```csharp
public class PanContainer : ContentView
{
  double x, y;

  public PanContainer ()
  {
    // Set PanGestureRecognizer.TouchPoints to control the
    // number of touch points needed to pan
    var panGesture = new PanGestureRecognizer ();
    panGesture.PanUpdated += OnPanUpdated;
    GestureRecognizers.Add (panGesture);
  }

  void OnPanUpdated (object sender, PanUpdatedEventArgs e)
  {
    ...
  }
}
```

Esta clase puede colocarse en torno a un elemento de interfaz de usuario para que el movimiento panorámico arrastrará el elemento de interfaz de usuario ajustado. El ejemplo de código XAML siguiente muestra la `PanContainer` ajuste una [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PanGesture"
             x:Class="PanGesture.HomePage">
    <ContentPage.Content>
        <AbsoluteLayout>
            <local:PanContainer>
                <Image Source="MonoMonkey.jpg" WidthRequest="1024" HeightRequest="768" />
            </local:PanContainer>
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

El siguiente ejemplo de código muestra cómo el `PanContainer` ajusta un [ `Image` ](xref:Xamarin.Forms.Image) elemento en una página de C#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new AbsoluteLayout {
      Padding = new Thickness (20),
      Children = {
        new PanContainer {
          Content = new Image {
            Source = ImageSource.FromFile ("MonoMonkey.jpg"),
            WidthRequest = 1024,
            HeightRequest = 768
          }
        }
      }
    };
  }
}
```

En ambos ejemplos, el [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) y [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propiedades se establecen en los valores de anchura y altura de la imagen que se va a mostrar.

Cuando el [ `Image` ](xref:Xamarin.Forms.Image) elemento recibe un movimiento panorámico, se arrastrarán la imagen mostrada. Realiza la operación de arrastrar el `PanContainer.OnPanUpdated` método, que se muestra en el ejemplo de código siguiente:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  switch (e.StatusType) {
  case GestureStatus.Running:
    // Translate and ensure we don't pan beyond the wrapped user interface element bounds.
    Content.TranslationX =
      Math.Max (Math.Min (0, x + e.TotalX), -Math.Abs (Content.Width - App.ScreenWidth));
    Content.TranslationY =
      Math.Max (Math.Min (0, y + e.TotalY), -Math.Abs (Content.Height - App.ScreenHeight));
    break;

  case GestureStatus.Completed:
    // Store the translation applied during the pan
    x = Content.TranslationX;
    y = Content.TranslationY;
    break;
  }
}
```

Este método actualiza el contenido visible del elemento de interfaz de usuario ajustado, según el gesto de panorámica del usuario. Esto se logra mediante el uso de los valores de la [ `TotalX` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) y [ `TotalY` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) propiedades de la [ `PanUpdatedEventArgs` ](xref:Xamarin.Forms.PanUpdatedEventArgs) instancia para calcular la dirección y distancia de la panorámica. El `App.ScreenWidth` y `App.ScreenHeight` propiedades proporcionan el alto y ancho de la ventanilla y se establecen en el ancho de pantalla y los valores del alto de pantalla del dispositivo de los proyectos específicos de la plataforma correspondiente. A continuación, se arrastra el elemento de usuario ajustado estableciendo su [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) y [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propiedades a los valores calculados.

Cuando el movimiento panorámico contenido en un elemento que no ocupe toda la pantalla, el alto y ancho del área de visualización se pueden obtener desde el elemento [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) y [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) propiedades.

> [!NOTE]
> Mostrar imágenes de alta resolución puede aumentar considerablemente la superficie de memoria de una aplicación. Por lo tanto, se debe sólo crear cuando sea necesario y debe liberarse en cuanto la aplicación ya no los necesita. Para más información, vea [Optimizar los recursos de imagen](~/xamarin-forms/deploy-test/performance.md#optimizeimages).

## <a name="summary"></a>Resumen

El movimiento panorámico se utiliza para detectar arrastrando y se implementa con la [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) clase.



## <a name="related-links"></a>Vínculos relacionados

- [PanGesture (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
