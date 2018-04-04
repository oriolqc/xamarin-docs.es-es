---
title: Crear los reproductores de vídeo de plataforma
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 316a6b9e9ce65dfb3c19d611f1b4976709546b5f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="creating-the-platform-video-players"></a>Crear los reproductores de vídeo de plataforma

El [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) solución contiene todo el código para implementar un Reproductor de vídeo para Xamarin.Forms. También incluye una serie de páginas que se muestra cómo utilizar el Reproductor de vídeo dentro de una aplicación. Todos los `VideoPlayer` sus representadores de plataforma y código que se encuentran en carpetas de proyecto denominadas `FormsVideoLibrary`y utilizar el espacio de nombres `FormsVideoLibrary`. Debe así le resultará fácil de copiar los archivos en su propia aplicación y hacer referencia a las clases.

## <a name="the-video-player"></a>El Reproductor de vídeo

El [ `VideoPlayer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs) clase forma parte de la **VideoPlayerDemos** biblioteca de clases portables (PCL) que se comparte entre las plataformas. Deriva de `View`:

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

Los miembros de esta clase (y el `IVideoPlayerController` interfaz) se describen en los artículos que van a continuación.

Cada una de las tres plataformas contiene una clase denominada `VideoPlayerRenderer` que contiene el código específico de la plataforma para implementar un Reproductor de vídeo. La tarea principal de este representador es crear un Reproductor de vídeo para esa plataforma.

### <a name="the-ios-player-view-controller"></a>El controlador de vista de Reproductor de iOS

Varias clases implicadas al implementar un Reproductor de vídeo en iOS. La aplicación crea primero un [ `AVPlayerViewController` ](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) y, a continuación, Establece la [ `Player` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.Player/) propiedad a un objeto de tipo [ `AVPlayer` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayer/). Clases adicionales son necesarias cuando el Reproductor se asigna un origen de vídeo.

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

Por lo general se deriva un representador que establece un control de la plataforma de la [ `ViewRenderer<View, NativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) (clase), donde `View` es el Xamarin.Forms `View` derivado (en este caso, `VideoPlayer`) y `NativeView` es un iOS `UIView` derivado de la clase de procesador. Para este representador, dicho argumento genérico simplemente se establece en `UIView`, por motivos que se verá en breve.

Cuando un representador se basa en un `UIViewController` derivado (que este es), a continuación, la clase debe reemplazar el `ViewController` propiedad y valor devuelto el controlador de vista, en este caso `AVPlayerViewController`. Es decir, el propósito de la `_playerViewController` campo:

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

La responsabilidad primaria de la `OnElementChanged` invalidación es comprobar si el `Control` propiedad es `null` y, si es así, crear un control de la plataforma y lo pasa a la `SetNativeControl` método. En este caso, ese objeto solo está disponible desde el `View` propiedad de la `AVPlayerViewController`. Que `UIView` derivado resulta ser una clase privada denominada `AVPlayerView`, pero porque es privado, que no puede especificarse explícitamente como segundo argumento genérico `ViewRenderer`.

Por lo general el `Control` propiedad de la clase de representador posteriormente hace referencia a la `UIView` usado para implementar el representador, pero en este caso el `Control` propiedad no se utiliza en otro lugar.

### <a name="the-android-video-view"></a>La vista del vídeo Android

El representador de Android para `VideoPlayer` se basa en el Android [ `VideoView` ](https://developer.xamarin.com/api/type/Android.Widget.VideoView/) clase. Sin embargo, si `VideoView` se usa por sí solo para reproducir un vídeo en una aplicación de Xamarin.Forms, el vídeo rellena el asignado de área para el `VideoPlayer` sin mantener la relación de aspecto correcta. Para esta razón (como se verá en breve), el `VideoView` se realiza un elemento secundario de un Android `RelativeLayout`. A `using` directiva define `ARelativeLayout` para distinguirlo de la Xamarin.Forms `RelativeLayout`, y que es el segundo argumento genérico el `ViewRenderer`:

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

El `OnElementChanged` reemplazada crea tanto la `VideoView` y `RelativeLayout` y establece los parámetros de diseño para la `VideoView` para centrar dentro el `RelativeLayout`.


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

Un controlador para el `Prepared` evento está conectado en este método y se separa en el `Dispose` método. Este evento se desencadena cuando el `VideoView` tiene suficiente información para empezar a reproducir un archivo de vídeo.

### <a name="the-uwp-media-element"></a>El elemento multimedia UWP

En la plataforma Universal de Windows (UWP), el Reproductor de vídeo más comunes es [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/). Documentación de ese de `MediaElement` indica que la [ `MediaPlayerElement` ](/uwp/api/windows.ui.xaml.controls.mediaplayerelement/) debe utilizarse en su lugar cuando solo es necesario para admitir versiones de compilación 1607 a partir de Windows 10.

El `OnElementChanged` invalidación debe crear un `MediaElement`, establezca un par de controladores de eventos y pasar la `MediaElement` el objeto a `SetNativeControl`:

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

Los dos controladores de eventos se separan en el `Dispose` eventos para el representador.

## <a name="showing-the-transport-controls"></a>Mostrar los controles de transporte

Todos los reproductores de vídeo incluidos en las tres plataformas admiten un conjunto predeterminado de los controles de transporte que incluir botones para reproducir y pausa y una barra para indicar la posición actual en el vídeo y para mover a una nueva posición.

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

Aunque esta propiedad no tiene ambos `set` y `get` descriptores de acceso, el representador tiene que administrar casos solo cuando se establece la propiedad. El `get` descriptor de acceso simplemente devuelve el valor actual de la propiedad.

Propiedades como `AreTransportControlsEnabled` se administran en los representadores de plataforma de dos maneras:

- La primera vez resulta Xamarin.Forms crea un `VideoPlayer` elemento. Esto se indica en la `OnElementChanged` invalidar del procesador cuando la `NewElement` propiedad no es `null`. En este momento, puede establecer el representador es el Reproductor de vídeo de plataforma propia del valor inicial de la propiedad tal como se define en el `VideoPlayer`.

- Si la propiedad en `VideoPlayer` más adelante cambia, la `OnElementPropertyChanged` se llama al método en el representador. Esto permite que el representador actualizar el Reproductor de vídeo de plataforma tomando como base el nuevo valor de propiedad.

Este es el modo `AreTransportControlsEnabled` propiedad se controla en las tres plataformas:

### <a name="ios-playback-controls"></a>controles de reproducción de iOS

La propiedad del iOS `AVPlayerViewController` que controla la presentación de transporte es controles [ `ShowsPlaybackControls` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.ShowsPlaybackControls/). Le mostramos cómo esa propiedad se establece en el archivo iOS `VideoViewRenderer`:

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

### <a name="the-android-media-controller"></a>El controlador de medios Android

En Android, mostrar los controles de transporte requiere la creación de un [ `MediaController` ](https://developer.xamarin.com/api/type/Android.Widget.MediaController/) objeto y asociarla con el `VideoView` objeto. Los mecanismos que se muestran en la `SetAreTransportControlsEnabled` método:

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

UWP `MediaElement` define una propiedad denominada [ `AreTransportControlsEnabled` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_AreTransportControlsEnabled), de modo que la propiedad se establece desde la `VideoPlayer` propiedad el mismo nombre:

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

Una propiedad más es necesaria empezar a reproducir un vídeo: Esto es el fundamental `Source` propiedad que hace referencia a un archivo de vídeo. Implementar la `Source` propiedad se describe en el siguiente artículo, [reproducir un vídeo Web](web-videos.md).


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de Reproductor de vídeo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
