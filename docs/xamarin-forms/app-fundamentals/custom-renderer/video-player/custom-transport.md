---
title: Controles de transporte personalizado de vídeo
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 5463a91dba5840ebe655aa1509d9f98e73643d26
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="custom-video-transport-controls"></a>Controles de transporte personalizado de vídeo

Los controles de transporte de un Reproductor de vídeo incluyen los botones que realizan las funciones **reproducir**, **pausa**, y **detener**. Estos botones se suelen identificar con iconos que ya conoce en lugar de texto y el **reproducir** y **pausa** funciones generalmente se combinan en un botón.

De forma predeterminada, la `VideoPlayer` muestra controles admitidos por cada plataforma de transporte. Al establecer el `AreTransportControlsEnabled` propiedad `false`, estos controles se han suprimido. A continuación, puede controlar la `VideoPlayer` mediante programación o proporcionar sus propios controles de transporte.

## <a name="the-play-pause-and-stop-methods"></a>Los métodos de reproducir, pausar y detener

El `VideoPlayer` clase define tres métodos denominados `Play`, `Pause`, y `Stop` que se implementan mediante la activación de eventos:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        public event EventHandler PlayRequested;

        public void Play()
        {
            PlayRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler PauseRequested;

        public void Pause()
        {
            PauseRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler StopRequested;

        public void Stop()
        {
            StopRequested?.Invoke(this, EventArgs.Empty);
        }
    }
}
```

Controladores de eventos para estos eventos se establecen mediante la `VideoPlayerRenderer` clase en cada plataforma, tal y como se muestra a continuación:

### <a name="ios-transport-implementations"></a>las implementaciones de transporte de iOS

La versión de iOS de `VideoPlayerRenderer` utiliza la `OnElementChanged` método para establecer controladores para estos tres eventos cuando el `NewElement` propiedad no es `null` y separa los controladores de eventos cuando `OldElement` no es `null`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            player.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            player.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            player.Pause();
            player.Seek(new CMTime(0, 1));
        }
    }
}
```

Los controladores de eventos se implementan mediante una llamada a métodos en la `AVPlayer` objeto. No hay ningún `Stop` método para `AVPlayer`, por lo que se simula pausar el vídeo y moviendo la posición al principio.

### <a name="android-transport-implementations"></a>Implementaciones de transporte Android

La implementación de Android es similar a la implementación de iOS. Los controladores de las tres funciones se establecen cuando `NewElement` no `null` y cuando haya desconectado `OldElement` no es `null`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        void OnPlayRequested(object sender, EventArgs args)
        {
            videoView.Start();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            videoView.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            videoView.StopPlayback();
        }
    }
}
```

Las tres funciones llaman a métodos definidos por `VideoView`.

### <a name="uwp-transport-implementations"></a>Implementaciones de transporte UWP

La implementación de UWP de las funciones de tres transporte es muy similar a iOS y Android implementaciones:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            Control.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            Control.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            Control.Stop();
        }
    }
}
```

## <a name="the-video-player-status"></a>El estado del Reproductor de vídeo

Implementar la **reproducir**, **pausa**, y **detener** funciones no es suficiente para admitir los controles de transporte. A menudo la **reproducir** y **pausa** se implementan con el mismo botón que cambia de apariencia para indicar si el vídeo actualmente está reproduciendo o en pausa. Además, el botón no debe habilitarse incluso si todavía no ha cargado el vídeo.

Estos requisitos implican que el Reproductor de vídeo debe disponer de un estado actual que indica si está reproduciendo o en pausa, o si todavía no está listo para reproducir un vídeo. (Las tres plataformas admiten también propiedades que indican si el vídeo puede poner en pausa o se puede mover a una nueva posición, pero estas propiedades son aplicables para vídeo de transmisión por secuencias en lugar de archivos de vídeo, por lo que no se admiten en el `VideoPlayer` descritos aquí.)

El **VideoPlayerDemos** proyecto incluye un `VideoStatus` enumeración con tres miembros:

```csharp
namespace FormsVideoLibrary
{
    public enum VideoStatus
    {
        NotReady,
        Playing,
        Paused
    }
}
```

El `VideoPlayer` clase define una propiedad enlazable solo real denominada `Status` de tipo `VideoStatus`. Esta propiedad se define como de solo lectura porque solo debe establecerse en el representador de plataforma:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Status read-only property
        private static readonly BindablePropertyKey StatusPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Status), typeof(VideoStatus), typeof(VideoPlayer), VideoStatus.NotReady);

        public static readonly BindableProperty StatusProperty = StatusPropertyKey.BindableProperty;

        public VideoStatus Status
        {
            get { return (VideoStatus)GetValue(StatusProperty); }
        }

        VideoStatus IVideoPlayerController.Status
        {
            set { SetValue(StatusPropertyKey, value); }
            get { return Status; }
        }
        ···
    }
}
```

Normalmente, una propiedad de solo lectura enlazable tendría una privada `set` descriptor de acceso en la `Status` propiedad para permitir que se pueden establecer desde dentro de la clase. Para una `View` derivado compatible con los representadores, sin embargo, la propiedad se debe establecer desde fuera de la clase, pero solo por el representador de plataforma.

Por este motivo, otra propiedad se define con el nombre `IVideoPlayerController.Status`. Esto es una implementación explícita de la interfaz y se realiza mediante la `IVideoPlayerController` de la interfaz que el `VideoPlayer` implementa la clase:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

Esto es similar a cómo el [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) control usa la [ `IWebViewController` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IWebViewController/) interfaz para implementar la `CanGoBack` y `CanGoForward` propiedades. (Vea el código fuente de [ `WebView` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) y sus representadores para obtener más información.)

Esto permite una clase externos a `VideoPlayer` para establecer el `Status` propiedad haciendo referencia a la `IVideoPlayerController` interfaz. (Verá el código en breve.) Se puede establecer la propiedad de otras clases también, pero es poco probable que establecerse de forma inadvertida. Lo más importante, la `Status` no se puede establecer a través de un enlace de datos.

Para ayudar a los representadores de mantener este `Status` propiedad actualizado, el `VideoPlayer` clase define un `UpdateStatus` evento que desencadena cada décima parte de un segundo:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        public event EventHandler UpdateStatus;

        public VideoPlayer()
        {
            Device.StartTimer(TimeSpan.FromMilliseconds(100), () =>
            {
                UpdateStatus?.Invoke(this, EventArgs.Empty);
                return true;
            });
        }
        ···
    }
}
```

### <a name="the-ios-status-setting"></a>La configuración de estado de iOS

IOS `VideoPlayerRenderer` establece un controlador para el `UpdateStatus` evento (y desasocia ese controlador cuando subyacente `VideoPlayer` elemento está presente) y utiliza el controlador para establecer el `Status` propiedad:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (player.Status)
            {
                case AVPlayerStatus.ReadyToPlay:
                    switch (player.TimeControlStatus)
                    {
                        case AVPlayerTimeControlStatus.Playing:
                            videoStatus = VideoStatus.Playing;
                            break;

                        case AVPlayerTimeControlStatus.Paused:
                            videoStatus = VideoStatus.Paused;
                            break;
                    }
                    break;
                }
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
            ···
        }
        ···
    }
}
```

Dos propiedades de `AVPlayer` debe tener acceso a: el [ `Status` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.Status/) propiedad de tipo `AVPlayerStatus` y [ `TimeControlStatus` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.TimeControlStatus/) propiedad de tipo `AVPlayerTimeControlStatus`. Tenga en cuenta que la `Element` propiedad (que es el `VideoPlayer`) deben convertirse a `IVideoPlayerController` para establecer el `Status` propiedad.

### <a name="the-android-status-setting"></a>La configuración de estado de Android

El [ `IsPlaying` ](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/) propiedad de la Android `VideoView` es un valor booleano que sólo indica si el vídeo se reproduce o está en pausa. Para determinar si la `VideoView` puede ni play ni pausar el vídeo todavía, el `Prepared` eventos de `VideoView` deben administrarse. Estos dos controladores se establecen en los `OnElementChanged` método y desasociadas durante la `Dispose` invalidar:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    videoView.Prepared += OnVideoViewPrepared;
                    ···
                }
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }

        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            if (Element != null)
            {
                Element.UpdateStatus -= OnUpdateStatus;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

El `UpdateStatus` controlador utiliza la `isPrepared` campo (establecido el `Prepared` controlador) y la `IsPlaying` propiedad para establecer el `Status` propiedad:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus status = VideoStatus.NotReady;

            if (isPrepared)
            {
                status = videoView.IsPlaying ? VideoStatus.Playing : VideoStatus.Paused;
            }
            ···
        }
        ···
    }
}
```

### <a name="the-uwp-status-setting"></a>La configuración de estado UWP

UWP `VideoPlayerRenderer` hace uso de la `UpdateStatus` eventos, pero no es necesario para la configuración de la `Status` propiedad. El `MediaElement` define un [ `CurrentStateChanged` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentStateChanged) eventos que permite que el representador que se le notifique cuando la [ `CurrentState` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentState) propiedad ha cambiado. La propiedad se separa en el `Dispose` invalidar:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                };
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                ···
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

El `CurrentState` propiedad es de tipo [ `MediaElementState` ](/uwp/api/windows.ui.xaml.media.mediaelementstate)y asigna fácilmente a `VideoStatus`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementCurrentStateChanged(object sender, RoutedEventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (Control.CurrentState)
            {
                case MediaElementState.Playing:
                    videoStatus = VideoStatus.Playing;
                    break;

                case MediaElementState.Paused:
                case MediaElementState.Stopped:
                    videoStatus = VideoStatus.Paused;
                    break;
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
        }
        ···
    }
}
```

## <a name="play-pause-and-stop-buttons"></a>Reproducir, pausa y detener botones

Uso de caracteres Unicode para simbólico **reproducir**, **pausa**, y **detener** imágenes es problemático. El [varios técnica](https://unicode-table.com/en/blocks/miscellaneous-technical/) sección del estándar Unicode define tres caracteres de símbolos aparentemente apropiados para este propósito. Estos son:

- 0x23F5 (triángulo negro de Media hacia la derecha) o &#x23F5; para **reproducir**
- 0x23F8 (doble barra vertical) o &#x23F8; para **pausar**
- 0x23F9 (cuadrado negro) o &#x23F9; para **detener**

A pesar de todo cómo estos símbolos aparecen en el explorador (y otros exploradores controlarán de maneras diferentes), no se muestran constantemente en las plataformas compatibles con Xamarin.Forms. En dispositivos iOS y UWP, el **pausa** y **detener** caracteres tienen un aspecto de gráfico, con un fondo azul 3D y un primer plano blanco. Esto no es el caso en Android, donde el símbolo es simplemente azul. Sin embargo, el punto de código 0x23F5 para **reproducir** no tiene que la misma apariencia en UWP y ni siquiera es compatible con iOS y Android.

Por esta razón, no se puede usar el punto de código 0x23F5 para **reproducir**. Un buen sustituto es:

- 0x25B6 (triángulo negro hacia la derecha) o &#x25B6; para **reproducir**

Esto es compatible con las tres plataformas salvo que es un triángulo negro sin formato que no se parece a la apariencia 3D de **pausa** y **detener**. Una posibilidad es seguir el punto de código de 0x25B6 con un código de variante:

- 0x25B6 seguido 0xFE0F (variante de 16) o &#x25B6; &#xFE0F; para **reproducir**

Esto es lo que se utiliza en el marcado que se muestra a continuación. En iOS, proporciona el **reproducir** de símbolos de la misma apariencia 3D como el **pausa** y **detener** botones, pero la variante no funciona en UWP y Android.

El **transporte Custom** página conjuntos el **AreTransportControlsEnabled** propiedad **false** e incluye una `ActivityIndicator` muestra cuando se carga el vídeo y dos botones. `DataTrigger` objetos se utilizan para habilitar y deshabilitar la `ActivityIndicator` y los botones y para cambiar el primer botón entre **reproducir** y **pausa**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomTransportPage"
             Title="Custom Transport">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AutoPlay="False"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource BigBuckBunny}" />

        <ActivityIndicator Grid.Row="0"
                           Color="Gray"
                           IsVisible="False">
            <ActivityIndicator.Triggers>
                <DataTrigger TargetType="ActivityIndicator"
                             Binding="{Binding Source={x:Reference videoPlayer},
                                               Path=Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsVisible" Value="True" />
                    <Setter Property="IsRunning" Value="True" />
                </DataTrigger>
            </ActivityIndicator.Triggers>
        </ActivityIndicator>

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="0, 10"
                     BindingContext="{x:Reference videoPlayer}">

            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.Playing}">
                        <Setter Property="Text" Value="&#x23F8; Pause" />
                    </DataTrigger>

                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>

            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

Desencadenadores de datos se describen en detalle en el artículo [datos desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md#data).

El archivo de código subyacente tiene los controladores del botón `Clicked` eventos:

```csharp
namespace VideoPlayerDemos
{
    public partial class CustomTransportPage : ContentPage
    {
        public CustomTransportPage()
        {
            InitializeComponent();
        }

        void OnPlayPauseButtonClicked(object sender, EventArgs args)
        {
            if (videoPlayer.Status == VideoStatus.Playing)
            {
                videoPlayer.Pause();
            }
            else if (videoPlayer.Status == VideoStatus.Paused)
            {
                videoPlayer.Play();
            }
        }

        void OnStopButtonClicked(object sender, EventArgs args)
        {
            videoPlayer.Stop();
        }
    }
}
```

Dado que `AutoPlay` está establecido en `false` en el **CustomTransport.xaml** archivo, deberá presionar el **reproducir** botón cuando se habilita para iniciar el vídeo. Los botones se definen para que los caracteres Unicode mencionados están acompañados por sus equivalentes de texto. Los botones tienen una apariencia coherente en cada plataforma cuando se está reproduciendo el vídeo:

[![Reproducción de transporte personalizado](custom-transport-images/customtransportplaying-small.png "reproducción de transporte personalizado")](custom-transport-images/customtransportplaying-large.png#lightbox "reproducción de transporte personalizado")

Pero en Android y UWP, el **reproducir** botón tiene un aspecto muy diferente cuando se pausa el vídeo:

[![Transporte personalizado en pausa](custom-transport-images/customtransportpaused-small.png "transporte personalizado en pausa")](custom-transport-images/customtransportpaused-large.png#lightbox "transporte personalizado en pausa")

En una aplicación de producción, probablemente deseará usar sus propias imágenes de mapa de bits de los botones para lograr uniformidad visual.


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de Reproductor de vídeo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
