---
title: Orígenes de vídeo de enlace para el Reproductor
description: Este artículo explica cómo enlazar orígenes de vídeo en el Reproductor de vídeo, uso de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 504E0C7E-051A-4AF2-B654-BAB4D0957928
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: b0efdc1a20f52231f15b7a08eb86962e2079c678
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240035"
---
# <a name="binding-video-sources-to-the-player"></a>Orígenes de vídeo de enlace para el Reproductor

Cuando el `Source` propiedad de la `VideoPlayer` ver se estableció como un nuevo archivo de vídeo, detiene la reproducción de vídeo existente y el nuevo vídeo comienza. Esto se demuestra la **seleccione Web vídeo** página de la [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) ejemplo. La página incluye una `ListView` con los títulos de los tres vídeos hace referencia desde el **App.xaml** archivo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.SelectWebVideoPage"
             Title="Select Web Video">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0" />

        <ListView Grid.Row="1"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Elephant's Dream</x:String>
                    <x:String>Big Buck Bunny</x:String>
                    <x:String>Sintel</x:String>
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

Cuando se selecciona un vídeo, el `ItemSelected` se ejecuta el controlador de eventos en el archivo de código subyacente. El controlador quita los espacios en blanco y apóstrofos el título y que se utiliza como clave para obtener uno de los recursos definidos en el **App.xaml** archivo. Que `UriVideoSource` , a continuación, se establece el objeto para el `Source` propiedad de la `VideoPlayer`.

```csharp
namespace VideoPlayerDemos
{
    public partial class SelectWebVideoPage : ContentPage
    {
        public SelectWebVideoPage()
        {
            InitializeComponent();
        }

        void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
        {
            if (args.SelectedItem != null)
            {
                string key = ((string)args.SelectedItem).Replace(" ", "").Replace("'", "");
                videoPlayer.Source = (UriVideoSource)Application.Current.Resources[key];
            }
        }
    }
}
```

Cuando la página se carga por primera vez, se selecciona ningún elemento en el `ListView`, por lo que debe seleccionar una para que empiece a reproducir el vídeo:

[![Seleccione vídeo Web](source-bindings-images/selectwebvideo-small.png "seleccione vídeo Web")](source-bindings-images/selectwebvideo-large.png#lightbox "seleccione vídeo Web")

El `Source` propiedad de `VideoPlayer` está respaldado por una propiedad enlazable, lo que significa que puede ser el destino de un enlace de datos. Esto se demuestra la **enlazar a VideoPlayer** página. El marcado en el **BindToVideoPlayer.xaml** archivo es compatible con la siguiente clase que encapsula un título de un vídeo y su correspondiente `VideoSource` objeto:

```csharp
namespace VideoPlayerDemos
{
    public class VideoInfo
    {
        public string DisplayName { set; get; }

        public VideoSource VideoSource { set; get; }

        public override string ToString()
        {
            return DisplayName;
        }
    }
}
```

El `ListView` en el **BindToVideoPlayer.xaml** archivo contiene una matriz de estos `VideoInfo` objetos, cada uno de ellos inicializado con un título de vídeo y la `UriVideoSource` objeto desde el diccionario de recursos en  **App.XAML**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoPlayerDemos"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.BindToVideoPlayerPage"
             Title="Bind to VideoPlayer">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           Source="{Binding Source={x:Reference listView},
                                            Path=SelectedItem.VideoSource}" />

        <ListView x:Name="listView"
                  Grid.Row="1">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:VideoInfo}">
                    <local:VideoInfo DisplayName="Elephant's Dream"
                                     VideoSource="{StaticResource ElephantsDream}" />

                    <local:VideoInfo DisplayName="Big Buck Bunny"
                                     VideoSource="{StaticResource BigBuckBunny}" />

                    <local:VideoInfo DisplayName="Sintel"
                                     VideoSource="{StaticResource Sintel}" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

El `Source` propiedad de la `VideoPlayer` está enlazado a la `ListView`. El `Path` del enlace se especifica como `SelectedItem.VideoSource`, que es una ruta de acceso compuesta que consta de dos propiedades: `SelectedItem` es una propiedad de `ListView`. El elemento seleccionado es de tipo `VideoInfo`, que tiene un `VideoSource` propiedad.

Al igual que con la primera **seleccione Web vídeo** página, inicialmente se seleccionó ningún elemento de la `ListView`, por lo que debe seleccionar uno de los vídeos antes de que empiece a reproducirse.


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de Reproductor de vídeo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
