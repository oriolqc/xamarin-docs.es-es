---
title: Creación de los reproductores de vídeo de plataforma
description: En este artículo se explica cómo implementar a un representador personalizado del Reproductor de vídeo en cada plataforma, con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 0090ec798e8d7b1dfb9bd8e25f09d71ec0353b45
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171916"
---
# <a name="creating-the-platform-video-players"></a>Creación de los reproductores de vídeo de plataforma

El [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) solución contiene todo el código para implementar un Reproductor de vídeo para Xamarin.Forms. También incluye una serie de páginas que se muestra cómo usar el Reproductor de vídeo dentro de una aplicación. Todas la `VideoPlayer` sus representadores de plataforma y código residen en carpetas de proyecto denominadas `FormsVideoLibrary`y también usar el espacio de nombres `FormsVideoLibrary`. Esto debería facilitan copie los archivos en su propia aplicación y hacer referencia a las clases.

## <a name="the-video-player"></a>El Reproductor de vídeo

El [ `VideoPlayer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs) clase forma parte de la **VideoPlayerDemos** biblioteca .NET Standard que se comparte entre las plataformas. Se deriva de `View`:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
    }
}
```

Los miembros de esta clase (y el `IVideoPlayerController` interfaz) se describen en los artículos siguientes.

Cada una de las plataformas contiene una clase denominada `VideoPlayerRenderer` que contiene el código específico de plataforma para implementar un Reproductor de vídeo. La tarea principal de este representador es crear un Reproductor de vídeo para esa plataforma.

### <a name="the-ios-player-view-controller"></a>El controlador de vistas del Reproductor de iOS

Varias clases implicadas al implementar un Reproductor de vídeo en iOS. La aplicación crea primero una [ `AVPlayerViewController` ](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) y, a continuación, Establece el [ `Player` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.Player/) propiedad a un objeto de tipo [ `AVPlayer` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayer/). Clases adicionales son necesarias cuando el Reproductor se asigna a un origen de vídeo.

Al igual que todos los representadores, iOS [ `VideoPlayerRenderer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/VideoPlayerRenderer.cs) contiene un `ExportRenderer` atributo que identifica el `VideoPlayer` vista con el representador de:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using AVFoundation;
using AVKit;
using CoreMedia;
using Foundation;
using UIKit;

using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.iOS.VideoPlayerRenderer))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
    }
}
```

Por lo general un representador que establece un plataforma control se deriva de la [ `ViewRenderer<View, NativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) (clase), donde `View` es Xamarin.Forms `View` derivado (en este caso, `VideoPlayer`) y `NativeView` es una de iOS `UIView` derivados de la clase de representador. Para este representador, simplemente se establece ese argumento genérico `UIView`, por motivos que verá en breve.

Cuando un representador se basa en un `UIViewController` derivado (como ésta es,), a continuación, la clase debe reemplazar el `ViewController` propiedad y devuelva el controlador de vista, en este caso `AVPlayerViewController`. Este es el propósito de la `_playerViewController` campo:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Create AVPlayerViewController
                    _playerViewController = new AVPlayerViewController();

                    // Set Player property to AVPlayer
                    player = new AVPlayer();
                    _playerViewController.Player = player;

                    // Use the View from the controller as the native control
                    SetNativeControl(_playerViewController.View);
                }
                ···
            }
        }
        ···
    }
}
```

La responsabilidad principal de la `OnElementChanged` invalidación es comprobar si el `Control` propiedad es `null` y, si es así, cree un control de la plataforma y pasarlo a la `SetNativeControl` método. En este caso, ese objeto solo está disponible desde el `View` propiedad de la `AVPlayerViewController`. Que `UIView` derivado resulta ser una clase privada denominada `AVPlayerView`, pero dado que es privada, no puede especificarse explícitamente como el segundo argumento genérico `ViewRenderer`.

Por lo general el `Control` propiedad de la clase de representador a partir de entonces se refiere a la `UIView` usado para implementar el representador, pero en este caso el `Control` propiedad no se utiliza en otro lugar.

### <a name="the-android-video-view"></a>La vista del vídeo Android

El representador de Android para `VideoPlayer` se basa en el Android [ `VideoView` ](https://developer.xamarin.com/api/type/Android.Widget.VideoView/) clase. Sin embargo, si `VideoView` se usa por sí solo para reproducir un vídeo en una aplicación de Xamarin.Forms, el vídeo llena el asignado de área para el `VideoPlayer` sin mantener la relación de aspecto correcta. Para esta razón (como verá en breve), el `VideoView` se realiza un elemento secundario de un Android `RelativeLayout`. Un `using` define la directiva `ARelativeLayout` para distinguirlo de Xamarin.Forms `RelativeLayout`, y que es el segundo argumento genérico el `ViewRenderer`:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using Android.Content;
using Android.Media;
using Android.Widget;
using ARelativeLayout = Android.Widget.RelativeLayout;

using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.Droid.VideoPlayerRenderer))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        public VideoPlayerRenderer(Context context) : base(context)
        {
        }
        ···
    }
}
```

A partir de Xamarin.Forms 2.5, los representadores de Android deben incluir un constructor con un `Context` argumento.

El `OnElementChanged` reemplazada crea tanto el `VideoView` y `RelativeLayout` y establece los parámetros de diseño para el `VideoView` para centrarlo dentro de la `RelativeLayout`.


```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Save the VideoView for future reference
                    videoView = new VideoView(Context);

                    // Put the VideoView in a RelativeLayout
                    ARelativeLayout relativeLayout = new ARelativeLayout(Context);
                    relativeLayout.AddView(videoView);

                    // Center the VideoView in the RelativeLayout
                    ARelativeLayout.LayoutParams layoutParams =
                        new ARelativeLayout.LayoutParams(LayoutParams.MatchParent, LayoutParams.MatchParent);
                    layoutParams.AddRule(LayoutRules.CenterInParent);
                    videoView.LayoutParameters = layoutParams;

                    // Handle a VideoView event
                    videoView.Prepared += OnVideoViewPrepared;

                    // Use the RelativeLayout as the native control
                    SetNativeControl(relativeLayout);
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            base.Dispose(disposing);
        }

        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
    }
}
```

Un controlador para el `Prepared` eventos se acopla en este método y desconectado en el `Dispose` método. Este evento se desencadena cuando el `VideoView` tiene suficiente información para empezar a reproducir un archivo de vídeo.

### <a name="the-uwp-media-element"></a>El elemento multimedia UWP

En la plataforma Universal de Windows (UWP), el Reproductor de vídeo más comunes es [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/). Dicha documentación de `MediaElement` indica que el [ `MediaPlayerElement` ](/uwp/api/windows.ui.xaml.controls.mediaplayerelement/) debe usarse en su lugar, cuando solo es necesario admitir las versiones de compilación 1607 a partir de Windows 10.

El `OnElementChanged` invalidación debe crear un `MediaElement`, configure un par de controladores de eventos y pasar la `MediaElement` objeto `SetNativeControl`:

```csharp
using System;
using System.ComponentModel;

using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.UWP.VideoPlayerRenderer))]

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
                    MediaElement mediaElement = new MediaElement();
                    SetNativeControl(mediaElement);

                    mediaElement.MediaOpened += OnMediaElementMediaOpened;
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                Control.MediaOpened -= OnMediaElementMediaOpened;
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }        
        ···
    }
}
```

Los dos controladores de eventos se desasocian de la `Dispose` eventos para el representador.

## <a name="showing-the-transport-controls"></a>Que muestra los controles de transporte

Todos los reproductores de vídeo incluidos en las plataformas admiten un conjunto predeterminado de los controles de transporte que incluir botones para reproducir y pausa y una barra para indicar la posición actual dentro del vídeo y para mover a una nueva posición.

El `VideoPlayer` clase define una propiedad denominada `AreTransportControlsEnabled` y establece el valor predeterminado en `true`:


```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // AreTransportControlsEnabled property
        public static readonly BindableProperty AreTransportControlsEnabledProperty =
            BindableProperty.Create(nameof(AreTransportControlsEnabled), typeof(bool), typeof(VideoPlayer), true);

        public bool AreTransportControlsEnabled
        {
            set { SetValue(AreTransportControlsEnabledProperty, value); }
            get { return (bool)GetValue(AreTransportControlsEnabledProperty); }
        }
        ···
    }
}
```

Aunque esta propiedad tiene ambos `set` y `get` descriptores de acceso, el representador debe controlar los casos solo cuando se establece la propiedad. El `get` descriptor de acceso devuelve simplemente el valor actual de la propiedad.

Propiedades, como `AreTransportControlsEnabled` se controlan en los representadores de plataforma de dos maneras:

- La primera vez es cuando crea Xamarin.Forms un `VideoPlayer` elemento. Esto se indica en la `OnElementChanged` invalidar del procesador cuando la `NewElement` propiedad no es `null`. En este momento, puede establecer el representador es el Reproductor de vídeo de plataforma propia del valor inicial de la propiedad tal como se define en el `VideoPlayer`.

- Si la propiedad en `VideoPlayer` cambia más adelante, el `OnElementPropertyChanged` se llama al método en el representador. Esto permite que el representador actualizar el Reproductor de vídeo de plataforma en función del valor de propiedad nuevo.

Las secciones siguientes se describe cómo el `AreTransportControlsEnabled` propiedad se controla en cada plataforma.

### <a name="ios-playback-controls"></a>controles de reproducción de iOS

La propiedad de iOS `AVPlayerViewController` que controla la presentación de transporte es controles [ `ShowsPlaybackControls` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.ShowsPlaybackControls/). Le mostramos cómo esa propiedad está establecida en iOS `VideoViewRenderer`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            ((AVPlayerViewController)ViewController).ShowsPlaybackControls = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

El `Element` propiedad del representador hace referencia a la `VideoPlayer` clase.

### <a name="the-android-media-controller"></a>El controlador de multimedia de Android

En Android, mostrando los controles de transporte requiere la creación de un [ `MediaController` ](https://developer.xamarin.com/api/type/Android.Widget.MediaController/) objeto y lo asocia a la `VideoView` objeto. La mecánica se muestra en el `SetAreTransportControlsEnabled` método:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            if (Element.AreTransportControlsEnabled)
            {
                mediaController = new MediaController(Context);
                mediaController.SetMediaPlayer(videoView);
                videoView.SetMediaController(mediaController);
            }
            else
            {
                videoView.SetMediaController(null);

                if (mediaController != null)
                {
                    mediaController.SetMediaPlayer(null);
                    mediaController = null;
                }
            }
        }
        ···
    }
}
```

### <a name="the-uwp-transport-controls-property"></a>La propiedad de los controles de transporte de UWP

UWP `MediaElement` define una propiedad denominada [ `AreTransportControlsEnabled` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_AreTransportControlsEnabled), de modo que la propiedad se establece desde el `VideoPlayer` propiedad el mismo nombre:

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
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            Control.AreTransportControlsEnabled = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

Una propiedad más es necesaria para empezar a reproducir un vídeo: trata el fundamental `Source` propiedad que hace referencia a un archivo de vídeo. Implementar el `Source` propiedad se describe en el siguiente artículo, [reproducir un vídeo Web](web-videos.md).


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones en el Reproductor de vídeo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
