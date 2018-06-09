---
title: Agregar un reconocedor de movimiento de derivación de gestos
description: Este artículo explica cómo usar el gesto de pulsar para la detección de derivación de una aplicación de Xamarin.Forms. Detección de derivación se implementa con la clase TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: bbe4ca7a1080459b8aeb33640be5158b15e97715
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240670"
---
# <a name="adding-a-tap-gesture-gesture-recognizer"></a>Agregar un reconocedor de movimiento de derivación de gestos

_El gesto de pulsar se utiliza para la derivación de detección y se implementa con la clase TapGestureRecognizer._

## <a name="overview"></a>Información general

Para hacer que un elemento de la interfaz de usuario puede hacer clic con el gesto de pulsar, crear un [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) de la instancia, controlar el [ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/) eventos y agregar el nuevo reconocedor de movimientos para el [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) colección en el elemento de interfaz de usuario. El siguiente ejemplo de código muestra un `TapGestureRecognizer` conectado a un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

De forma predeterminada, la imagen responderá a derivaciones único. Establecer el [ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/) propiedad que se va a esperar un doble punteo (o más derivaciones si es necesario).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Cuando [ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/) se establece por encima de uno solo se ejecutarán previamente el controlador de eventos si se producen las derivaciones dentro de un período de tiempo (período no es configurable) establecido. Si las derivaciones de segunda (o posteriores) no se producen dentro de ese período se omite y se reinicia el recuento de derivación de' '.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Uso de Xaml

Un reconocedor de movimiento puede agregarse a un control en Xaml mediante las propiedades adjuntas. La sintaxis para agregar una [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) a una imagen se muestra a continuación (en este caso se define un *doble punteo* evento):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

El código para el controlador de eventos (en el ejemplo) incrementa un contador y cambia la imagen de color a negro &amp; blanco.

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>Uso de ICommand

Aplicaciones que usan el patrón Mvvm suelen usar `ICommand` en lugar de la conexión de controladores de eventos directamente. El [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) puede admitir fácilmente `ICommand` estableciendo el enlace en el código:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

o bien, mediante Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

El código completo de este modelo de vista se puede encontrar en el ejemplo. La correspondiente `Command` detalles de implementación se muestran a continuación:

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```

## <a name="summary"></a>Resumen

El gesto de pulsar se utiliza para la derivación de detección y se implementa con la [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) clase. El número de bifurcaciones puede especificarse para que reconozca el doble punteo (triple punteo o más puntea) comportamiento.


## <a name="related-links"></a>Vínculos relacionados

- [TapGesture (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [TapGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)
