---
title: Agregar un reconocedor de movimiento panorámico
description: Este artículo explica cómo utilizar un movimiento panorámico horizontalmente y verticalmente arrastrar una imagen, por lo que todo el contenido de la imagen puede verse cuando se está mostrando en una ventanilla menor que las dimensiones de la imagen.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: d3e4dfc57678ff75fb8f9761360748d94aeefcc2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35239989"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Agregar un reconocedor de movimiento panorámico

_El movimiento panorámico se utiliza para detectar arrastrando y se implementa con la clase PanGestureRecognizer. Un escenario común para el movimiento panorámico es horizontal y verticalmente, arrastre una imagen, por lo que todo el contenido de la imagen puede verse cuando se está mostrando en una ventanilla menor que las dimensiones de la imagen. Esto se logra moviendo la imagen dentro de la ventanilla y se muestra en este artículo._

## <a name="overview"></a>Información general

Para hacer que un elemento de la interfaz de usuario arrastrable con el movimiento panorámico, crear un [ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/) de la instancia, controlar el [ `PanUpdated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.PanGestureRecognizer.PanUpdated/) eventos, y agregue el nuevo reconocedor de movimientos para el [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) colección en el elemento de interfaz de usuario. El siguiente ejemplo de código muestra un `PanGestureRecognizer` conectado a un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

También se puede lograr esto de XAML, como se muestra en el ejemplo de código siguiente:

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

El código para el `OnPanUpdated` , a continuación, se agrega el controlador de eventos en el archivo de código subyacente:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> Panorámica correcto en Android requiere la [paquete NuGet de Xamarin.Forms 2.1.0-pre1](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1) como mínimo.

## <a name="creating-a-pan-container"></a>Crear un contenedor de panorámica

Esta sección contiene una clase auxiliar generalizado que realiza la panorámica de forma libre, que normalmente es adecuada para navegar dentro de las imágenes o los mapas. El movimiento panorámico para llevar a cabo una operación de arrastrar el control requiere algunos cálculos para transformar la interfaz de usuario. Este matemáticas se usan para arrastrar solo dentro de los límites del elemento de interfaz de usuario ajustado. En el ejemplo de código siguiente se muestra la clase `PanContainer`:

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

Esta clase puede ajustarse alrededor de un elemento de interfaz de usuario para que el movimiento panorámico arrastre el elemento de la interfaz de usuario ajustado. El ejemplo de código XAML siguiente muestra la `PanContainer` ajuste un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

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

El siguiente ejemplo de código muestra cómo el `PanContainer` ajusta un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento en una página de C#:

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

En ambos ejemplos, el [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) y [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) propiedades se establecen en los valores de ancho y alto de la imagen que se va a mostrar.

Cuando el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento recibe un movimiento panorámico, se arrastrarán la imagen mostrada. Realiza la operación de arrastrar el `PanContainer.OnPanUpdated` método, que se muestra en el ejemplo de código siguiente:

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

Este método actualiza el contenido visible del elemento de interfaz de usuario ajustado, en función de movimiento de panorámica del usuario. Esto se logra mediante el uso de los valores de la [ `TotalX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalX/) y [ `TotalY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalY/) propiedades de la [ `PanUpdatedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanUpdatedEventArgs/) instancia para calcular la dirección y distancia de la pan. El `App.ScreenWidth` y `App.ScreenHeight` propiedades proporcionan el alto y ancho de la ventanilla y se establecen en el ancho de pantalla y los valores de altura de la pantalla del dispositivo por los proyectos específicos de la plataforma correspondientes. A continuación, se arrastra el elemento de usuario ajustado estableciendo su [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) y [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propiedades a los valores calculados.

Al aplicar una panorámica en el contenido de un elemento que no ocupa la pantalla completa, el alto y ancho del área de visualización se pueden obtener desde el elemento [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) y [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) propiedades.

> [!NOTE]
> Mostrar imágenes de alta resolución puede aumentar considerablemente el consumo de memoria de una aplicación. Por lo tanto, debería solo se creaban cuando sea necesario y deben liberarse tan pronto como la aplicación ya no los necesita. Para más información, vea [Optimizar los recursos de imagen](~/xamarin-forms/deploy-test/performance.md#optimizeimages).

## <a name="summary"></a>Resumen

El movimiento panorámico se utiliza para detectar arrastrando y se implementa con la [ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/) clase.



## <a name="related-links"></a>Vínculos relacionados

- [PanGesture (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [PanGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/)
