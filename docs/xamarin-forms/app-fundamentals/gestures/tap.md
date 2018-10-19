---
title: Agregar un reconocedor de movimiento de tap
description: En este artículo se explica cómo usar el gesto de pulsar para la detección de derivación de una aplicación de Xamarin.Forms. Detección de TAP se implementa con la clase TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: a28afb30770f15861aef06643e7f51070199ea9b
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "38994859"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Agregar un reconocedor de movimiento de tap

_El gesto de pulsar se usa para la detección de tap y se implementa con la clase TapGestureRecognizer._

Para que un elemento de interfaz de usuario puede hacer clic con el gesto de pulsar, cree un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) de la instancia, controlar el [ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) eventos y agregue el reconocedor de gestos de nuevo a la [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) colección en el elemento de interfaz de usuario. El siguiente ejemplo de código muestra un `TapGestureRecognizer` adjunta a un [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

De forma predeterminada, la imagen responderá a derivaciones único. Establecer el [ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) propiedad que se va a esperar un doble punteo (o más derivaciones si es necesario).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Cuando [ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) se establece superiores a uno, el controlador de eventos solo se ejecuta si se producen las derivaciones dentro de un período de tiempo (período no es configurable). Si las derivaciones de segunda (o posteriores) no se producen dentro de ese período se omite y se reinicia el recuento de derivación de' '.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Uso de Xaml

Un reconocedor de movimiento se puede agregar a un control en Xaml mediante las propiedades adjuntas. La sintaxis para agregar un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) a una imagen se muestra a continuación (en este caso se define un *doble punteo* eventos):

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

## <a name="using-icommand"></a>Mediante ICommand

Las aplicaciones que usan el patrón Model-View-ViewModel (MVVM) suelen usar `ICommand` en lugar de inserción de controladores de eventos directamente. El [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) puede admitir fácilmente `ICommand` estableciendo el enlace en el código:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

o con Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

El código completo para este modelo de vista se puede encontrar en el ejemplo. La correspondiente `Command` a continuación se muestran los detalles de implementación:

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


## <a name="related-links"></a>Vínculos relacionados

- [TapGesture (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
