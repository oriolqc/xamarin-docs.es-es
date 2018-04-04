---
title: Posición de vídeo personalizada
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 51256f078411f0ade72867544ced3d9a3a91d7b4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="custom-video-positioning"></a>Posición de vídeo personalizada

Los controles de transporte implementados por cada plataforma incluyen una barra de posición. Esta barra es similar a un control deslizante o una barra de desplazamiento y muestra la ubicación actual del vídeo dentro de su duración total. Además, el usuario puede manipular la barra de posición para mover hacia delante o hacia atrás a una nueva posición en el vídeo.

Este artículo muestra cómo puede implementar su propia barra de posición personalizada.

## <a name="the-duration-property"></a>La propiedad Duration

Un elemento de información que `VideoPlayer` debe admitir un personalizado barra de posición es la duración del vídeo. El `VideoPlayer` define solo lectura `Duration` propiedad de tipo `TimeSpan`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Duration read-only property
        private static readonly BindablePropertyKey DurationPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Duration), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public static readonly BindableProperty DurationProperty = DurationPropertyKey.BindableProperty;

        public TimeSpan Duration
        {
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        TimeSpan IVideoPlayerController.Duration
        {
            set { SetValue(DurationPropertyKey, value); }
            get { return Duration; }
        }
        ···
    }
}
```

Al igual que el `Status` propiedad descrita en el [artículo anterior](custom-transport.md), este `Duration` propiedad es de solo lectura. Se define con una privada `BindablePropertyKey` y sólo se puede establecer mediante una referencia a la `IVideoPlayerController` interfaz, que incluye este `Duration` propiedad:

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

Observe también el controlador de cambio de propiedad que llama a un método denominado `SetTimeToEnd` que se describe más adelante en este artículo.

La duración de un vídeo es *no* disponibles inmediatamente después de la `Source` propiedad de `VideoPlayer` se establece. El archivo de vídeo debe descargarse parcialmente antes de que el Reproductor de vídeo subyacente puede determinar su duración.

Este es el modo en que cada uno de los representadores de plataforma Obtiene la duración del vídeo:

### <a name="video-duration-in-ios"></a>Duración del vídeo en iOS

En iOS, se obtiene la duración de un vídeo de la `Duration` propiedad de `AVPlayerItem`, pero no inmediatamente después del `AVPlayerItem` se crea. Es posible establecer un observador de iOS para la `Duration` propiedad, pero la `VideoPlayerRenderer` Obtiene la duración en el `UpdateStatus` método, que se llama a 10 veces por segundo:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ((IVideoPlayerController)Element).Duration = ConvertTime(playerItem.Duration);
                ···
            }
        }

        TimeSpan ConvertTime(CMTime cmTime)
        {
            return TimeSpan.FromSeconds(Double.IsNaN(cmTime.Seconds) ? 0 : cmTime.Seconds);

        }
        ···
    }
}
```

El `ConvertTime` método convierte un `CMTime` el objeto a un `TimeSpan` valor.

### <a name="video-duration-in-android"></a>Duración del vídeo en Android

El `Duration` propiedad de la Android `VideoView` notifica una duración válida en milisegundos cuando la `Prepared` eventos de `VideoView` se activa. El Android `VideoPlayerRenderer` clase utiliza ese controlador para obtener el `Duration` propiedad:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            ···
            ((IVideoPlayerController)Element).Duration = TimeSpan.FromMilliseconds(videoView.Duration);
        }
        ···
    }
}
```

### <a name="video-duration-in-uwp"></a>Duración del vídeo en UWP

El `NaturalDuration` propiedad de `MediaElement` es un `TimeSpan` valor y empieza a ser válido cuando `MediaElement` se activa el `MediaOpened` eventos:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementMediaOpened(object sender, RoutedEventArgs args)
        {
            ((IVideoPlayerController)Element).Duration = Control.NaturalDuration.TimeSpan;
        }
        ···
    }
}
```

## <a name="the-position-property"></a>La propiedad de posición

`VideoPlayer` También es necesario un `Position` propiedad que aumenta de cero a `Duration` cuando se está reproduciendo el vídeo. `VideoPlayer` implementa esta propiedad como el `Position` propiedad UWP `MediaElement`, que es una propiedad enlazable normal por public `set` y `get` descriptores de acceso:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Position property
        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }
        ···
    }
}
```

El `get` descriptor de acceso devuelve la posición actual del vídeo se reproduce, pero la `set` descriptor de acceso está diseñado para responder a la manipulación del usuario de la barra de posición al mover la posición de vídeo avanzar o retroceder.

En iOS y Android, la propiedad que obtiene la posición actual tiene solo un `get` descriptor de acceso y un `Seek` método está disponible para realizar esta tarea de segundo. Si piensa sobre él, otro `Seek` método parece ser un enfoque más significativo de una sola `Position` propiedad. Una sola `Position` propiedad tiene un problema inherente: mientras se reproduce el vídeo, el `Position` propiedad se debe actualizar continuamente para reflejar la nueva posición. Pero no desea realizar cambios en el `Position` propiedad para hacer que el Reproductor de vídeo mover a una nueva posición en el vídeo. Si esto ocurre, el Reproductor de vídeo respondería buscando hasta el último valor de la `Position` propiedad y el vídeo no avanzar.

A pesar de las dificultades de la implementación de un `Position` propiedad con `set` y `get` descriptores de acceso, se ha elegido este enfoque porque es coherente con el UWP `MediaElement`, y tiene una gran ventaja con enlace de datos: el `Position` propiedad de la `VideoPlayer` pueden estar limitados por el control deslizante que se utiliza para mostrar la posición y de búsqueda a una nueva posición. Sin embargo, son necesarias varias precauciones al implementar esto `Position` propiedad para evitar los bucles de comentarios.

### <a name="setting-and-getting-ios-position"></a>Establecer u obtener la posición de iOS

En iOS, el `CurrentTime` propiedad de la `AVPlayerItem` objeto indica la posición actual de la reproducción de vídeo. El archivo iOS `VideoPlayerRenderer` establece la `Position` propiedad en el `UpdateStatus` controlador a la vez que establece la `Duration` propiedad:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ···
                ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, ConvertTime(playerItem.CurrentTime));
            }
        }
        ···
    }
}
```

El representador detecta cuándo el `Position` conjunto de propiedades de `VideoPlayer` ha cambiado en el `OnElementPropertyChanged` invalidar y usa ese nuevo valor para llamar a un `Seek` método en la `AVPlayer` objeto:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                TimeSpan controlPosition = ConvertTime(player.CurrentTime);

                if (Math.Abs((controlPosition - Element.Position).TotalSeconds) > 1)
                {
                    player.Seek(CMTime.FromSeconds(Element.Position.TotalSeconds, 1));
                }
            }
        }
        ···
    }
}
```

Tenga en cuenta que cada vez el `Position` propiedad en `VideoPlayer` se establece desde la `OnUpdateStatus` controlador, el `Position` se activa la propiedad un `PropertyChanged` eventos, que se ha detectado en el `OnElementPropertyChanged` invalidar. Para la mayoría de estos cambios, el `OnElementPropertyChanged` método debería hacer nada. De lo contrario, con cada cambio de posición del vídeo, se podría mover a la misma posición que llegado!

Para evitar este bucle de comentarios, la `OnElementPropertyChanged` método se llama solo `Seek` cuando la diferencia entre el `Position` propiedad y la posición actual de la `AVPlayer` es mayor que un segundo.

### <a name="setting-and-getting-android-position"></a>Establecer u obtener la posición de Android

Al igual que en el representador de iOS, el Android `VideoPlayerRenderer` establece un nuevo valor para el `Position` propiedad en el `OnUpdateStatus` controlador. El `CurrentPosition` propiedad de `VideoView` contiene la nueva posición en unidades de milisegundos:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            TimeSpan timeSpan = TimeSpan.FromMilliseconds(videoView.CurrentPosition);
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, timeSpan);
        }
        ···
    }
}
```

Además, al igual que en el representador de iOS, el representador Android llama el `SeekTo` método de `VideoView` cuando el `Position` propiedad ha cambiado, pero solo cuando el cambio es diferente de más de un segundo el `CurrentPosition` valo `VideoView`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs(videoView.CurrentPosition - Element.Position.TotalMilliseconds) > 1000)
                {
                    videoView.SeekTo((int)Element.Position.TotalMilliseconds);
                }
            }
        }
        ···
    }
}
```

### <a name="setting-and-getting-uwp-position"></a>Establecer u obtener la posición de UWP

UWP `VideoPlayerRenderer` controla la `Position` de la misma manera como iOS y Android en los representadores, sin embargo, dado el `Position` propiedad de UWP `MediaElement` también es un `TimeSpan` valor, no es necesario realizar ninguna conversión:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs((Control.Position - Element.Position).TotalSeconds) > 1)
                {
                    Control.Position = Element.Position;
                }
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, Control.Position);
        }
        ···
    }
}
```

## <a name="calculating-a-timetoend-property"></a>Calcular una propiedad TimeToEnd

Reproductores de vídeo a veces muestran el tiempo restante en el vídeo. Este valor empieza por la duración del vídeo cuando el vídeo comienza y se reduce a cero cuando finaliza el vídeo.

`VideoPlayer` sólo lectura incluye `TimeToEnd` propiedad que se controla por completo dentro de la clase según los cambios a la `Duration` y `Position` propiedades:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        private static readonly BindablePropertyKey TimeToEndPropertyKey =
            BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan());

        public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

        public TimeSpan TimeToEnd
        {
            private set { SetValue(TimeToEndPropertyKey, value); }
            get { return (TimeSpan)GetValue(TimeToEndProperty); }
        }

        void SetTimeToEnd()
        {
            TimeToEnd = Duration - Position;
        }
        ···
    }
}
```

El `SetTimeToEnd` método se llama desde los controladores de cambio de propiedad de ambos `Duration` y `Position`.

## <a name="a-custom-slider-for-video"></a>Un control deslizante personalizado para vídeo

Es posible escribir un control personalizado de una barra de posición, o utilizar el Xamarin.Forms `Slider` o una clase que deriva de `Slider`, como el siguiente `PositionSlider` clase. La clase define dos nuevas propiedades denominados `Duration` y `Position` de tipo `TimeSpan` que están diseñados para estar enlazado a las dos propiedades del mismo nombre en `VideoPlayer`. Tenga en cuenta que el valor predeterminado del modo de enlace el `Position` propiedad es bidireccional:

```csharp
namespace FormsVideoLibrary
{
    public class PositionSlider : Slider
    {
        public static readonly BindableProperty DurationProperty =
            BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                    });

        public TimeSpan Duration
        {
            set { SetValue(DurationProperty, value); }
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                    defaultBindingMode: BindingMode.TwoWay,
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Value = seconds;
                                    });

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }

        public PositionSlider()
        {
            PropertyChanged += (sender, args) =>
            {
                if (args.PropertyName == "Value")
                {
                    TimeSpan newPosition = TimeSpan.FromSeconds(Value);

                    if (Math.Abs(newPosition.TotalSeconds - Position.TotalSeconds) / Duration.TotalSeconds > 0.01)
                    {
                        Position = newPosition;
                    }
                }
            };
        }
    }
}
```

El controlador de cambio de propiedad para el `Duration` conjuntos de propiedades el `Maximum` propiedad de subyacente `Slider` a la `TotalSeconds` propiedad de la `TimeSpan` valor. De forma similar, el controlador de cambio de propiedad para `Position` establece la `Value` propiedad de la `Slider`. De esta manera, subyacente `Slider` realiza un seguimiento de la posición de la `PositionSlider`.

El `PositionSlider` se actualizan desde subyacente `Slider` en una única instancia: cuando el usuario manipula el `Slider` para indicar que el vídeo debe ser avanzado o revertido a una nueva posición. Esto se detecta en el `PropertyChanged` controlador en el constructor de la `PositionSlider`. El controlador comprueba si hay cambios en el `Value` propiedad, y si es diferente de la `Position` propiedad, el `Position` propiedad se establece desde la `Value` propiedad.

En teoría, interna `if` instrucción podría escribirse similar al siguiente:

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

Sin embargo, la implementación de Android `Slider` sólo 1.000 pasos discretos con independencia de la `Minimum` y `Maximum` configuración. La longitud de un vídeo es mayor que 1.000 segundos y, a continuación, dos `Position` valores corresponde a la misma `Value` configuración de la `Slider`y esto `if` instrucción desencadenaría un falso positivo para una manipulación de usuario de el `Slider`. Es más seguro en su lugar, compruebe que la nueva posición y la posición existente son mayores que centésima parte de la duración total.

## <a name="using-the-positionslider"></a>Usar el PositionSlider

Documentación de UWP [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/) advierte sobre el enlace a la `Position` propiedad porque la propiedad se actualiza con frecuencia. La documentación recomienda que un temporizador se usa para consultar el `Position` propiedad.

Que es una buena recomendación, pero los tres `VideoPlayerRenderer` clases indirectamente ya están utilizando un temporizador para actualizar la `Position` propiedad. El `Position` se cambia una propiedad en un controlador para el `UpdateStatus` evento, que se activa sólo 10 veces por segundo.

Por lo tanto, la `Position` propiedad de la `VideoPlayer` pueden estar limitados por la `Position` propiedad de la `PositionSlider` sin problemas de rendimiento, como se muestra en el **Custom posición Bar** página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomPositionBarPage"
             Title="Custom Position Bar">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource ElephantsDream}" />

        ···

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="10, 0"
                     BindingContext="{x:Reference videoPlayer}">

            <Label Text="{Binding Path=Position,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center"/>

            ···

            <Label Text="{Binding Path=TimeToEnd,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center" />
        </StackLayout>

        <video:PositionSlider Grid.Row="2"
                              Margin="10, 0, 10, 10"
                              BindingContext="{x:Reference videoPlayer}"
                              Duration="{Binding Duration}"           
                              Position="{Binding Position}">
            <video:PositionSlider.Triggers>
                <DataTrigger TargetType="video:PositionSlider"
                             Binding="{Binding Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </video:PositionSlider.Triggers>
        </video:PositionSlider>
    </Grid>
</ContentPage>
```

Los puntos suspensivos primer (···) ocultan la `ActivityIndicator`; es el mismo que en el anterior **transporte Custom** página. Tenga en cuenta los dos `Label` elementos mostrando la `Position` y `TimeToEnd` propiedades. Los puntos suspensivos entre esos dos `Label` elementos oculta los dos `Button` elementos que aparecen en la **transporte Custom** página para reproducir, pausar y detener. La lógica de código subyacente también es el mismo que el **transporte Custom** página.

[![Posición personalizada](custom-positioning-images/custompositioning-small.png "posición personalizada")](custom-positioning-images/custompositioning-large.png#lightbox "posición personalizada")

Con esto concluye la explicación de la `VideoPlayer`.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de Reproductor de vídeo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
