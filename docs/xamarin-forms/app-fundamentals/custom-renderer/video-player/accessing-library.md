---
title: Acceso a la biblioteca de vídeos del dispositivo
description: Este artículo explica cómo obtener acceso a la biblioteca de vídeos del dispositivo en una aplicación de Reproductor de vídeo mediante Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 619469e4c4fd3901491c20d6215ec0a25c49f69d
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171188"
---
# <a name="accessing-the-devices-video-library"></a>Acceso a la biblioteca de vídeos del dispositivo

Más modernos dispositivos móviles y equipos de escritorio tienen la capacidad de grabar vídeos uso de la cámara del dispositivo. Los vídeos que se crea un usuario, a continuación, se almacenan como archivos en el dispositivo. Estos archivos se pueden recuperar de la biblioteca de imágenes y desempeñados por la `VideoPlayer` clase igual que cualquier otro vídeo.

## <a name="the-photo-picker-dependency-service"></a>El servicio de dependencia del selector de fotos

Cada una de las plataformas incluye una utilidad que permite al usuario seleccionar una foto o vídeo desde la biblioteca de imágenes del dispositivo. El primer paso para reproducir un vídeo de biblioteca de imágenes del dispositivo está creando un servicio de dependencia que invoca el selector de imagen en cada plataforma. Es muy similar a uno definido en el servicio de dependencia se describe a continuación el [ **seleccionar una foto de la biblioteca de imágenes** ](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md) artículo, salvo que el selector de vídeo que devuelve un nombre de archivo en lugar de un `Stream`objeto.

El proyecto de biblioteca .NET Standard define una interfaz denominada `IVideoPicker` para el servicio de dependencia:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

Cada una de las plataformas contiene una clase denominada `VideoPicker` que implementa esta interfaz.

### <a name="the-ios-video-picker"></a>El selector de vídeo de iOS

IOS `VideoPicker` usa iOS [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) para tener acceso a la biblioteca de imágenes, especifica que debe estar restringido a los vídeos (denominados "movies") en iOS `MediaType` propiedad. Tenga en cuenta que `VideoPicker` implementa explícitamente la `IVideoPicker` interfaz. Tenga en cuenta también la `Dependency` atributo que identifica esta clase como un servicio de dependencia. Estos son los dos requisitos que permiten Xamarin.Forms encontrar el servicio de dependencia en el proyecto de plataforma:

```csharp
using System;
using System.Threading.Tasks;
using UIKit;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.iOS.VideoPicker))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPicker : IVideoPicker
    {
        TaskCompletionSource<string> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<string> GetVideoFileAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.SavedPhotosAlbum,
                MediaTypes = new string[] { "public.movie" }
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<string>();
            return taskCompletionSource.Task;
        }

        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            if (args.MediaType == "public.movie")
            {
                taskCompletionSource.SetResult(args.MediaUrl.AbsoluteString);
            }
            else
            {
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }
    }
}
```

### <a name="the-android-video-picker"></a>El selector de vídeo de Android

La implementación de Android `IVideoPicker` requiere un método de devolución de llamada que forma parte de la actividad de la aplicación. Por ese motivo, la `MainActivity` clase define dos propiedades, un campo y un método de devolución de llamada:

```csharp
namespace VideoPlayerDemos.Droid
{
    ···
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            Current = this;
            ···
        }

        // Field, properties, and method for Video Picker
        public static MainActivity Current { private set; get; }

        public static readonly int PickImageId = 1000;

        public TaskCompletionSource<string> PickImageTaskCompletionSource { set; get; }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);

            if (requestCode == PickImageId)
            {
                if ((resultCode == Result.Ok) && (data != null))
                {
                    // Set the filename as the completion of the Task
                    PickImageTaskCompletionSource.SetResult(data.DataString);
                }
                else
                {
                    PickImageTaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

El `OnCreate` método `MainActivity` almacena su propia instancia en estático `Current` propiedad. Esto permite la implementación de `IVideoPicker` para obtener el `MainActivity` instancia para iniciar el **seleccione vídeo** selector:

```csharp
using System;
using System.Threading.Tasks;
using Android.Content;
using Xamarin.Forms;

// Need application's MainActivity
using VideoPlayerDemos.Droid;

[assembly: Dependency(typeof(FormsVideoLibrary.Droid.VideoPicker))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPicker : IVideoPicker
    {
        public Task<string> GetVideoFileAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("video/*");
            intent.SetAction(Intent.ActionGetContent);

            // Get the MainActivity instance
            MainActivity activity = MainActivity.Current;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Video"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<string>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Las adiciones a la `MainActivity` objeto son el único código de la [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) solución donde el código de aplicación normal debe modificarse para admitir el `FormsVideoLibrary` clases.

### <a name="the-uwp-video-picker"></a>El selector de vídeo de UWP

La implementación de UWP de la `IVideoPicker` interfaz utiliza UWP [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/). Comienza la búsqueda de archivos con la biblioteca de imágenes y restringe los tipos de archivo MP4 y WMV (vídeo de Windows Media):

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Pickers;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.UWP.VideoPicker))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPicker : IVideoPicker
    {
        public async Task<string> GetVideoFileAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary
            };

            openPicker.FileTypeFilter.Add(".wmv");
            openPicker.FileTypeFilter.Add(".mp4");

            // Get a file and return the path
            StorageFile storageFile = await openPicker.PickSingleFileAsync();
            return storageFile?.Path;
        }
    }
}
```

## <a name="invoking-the-dependency-service"></a>Invocar el servicio de dependencia

El **reproducir vídeo de la biblioteca** página de la [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) programa muestra cómo usar el servicio de dependencia de selector de vídeo. El archivo XAML contiene una `VideoPlayer` instancia y un `Button` con la etiqueta **mostrar vídeo biblioteca**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayLibraryVideoPage"
             Title="Play Library Video">
    <StackLayout>
        <video:VideoPlayer x:Name="videoPlayer"
                           VerticalOptions="FillAndExpand" />

        <Button Text="Show Video Library"
                Margin="10"
                HorizontalOptions="Center"
                Clicked="OnShowVideoLibraryClicked" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente contiene el `Clicked` controlador para el `Button`. Invocar el servicio de dependencia es necesario llamar a `DependencyService.Get` para obtener la implementación de un `IVideoPicker` interfaz en el proyecto de la plataforma. El `GetVideoFileAsync` , a continuación, se llama al método en esa instancia:

```csharp
namespace VideoPlayerDemos
{
    public partial class PlayLibraryVideoPage : ContentPage
    {
        public PlayLibraryVideoPage()
        {
            InitializeComponent();
        }

       async void OnShowVideoLibraryClicked(object sender, EventArgs args)
        {
            Button btn = (Button)sender;
            btn.IsEnabled = false;

            string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();

            if (!String.IsNullOrWhiteSpace(filename))
            {
                videoPlayer.Source = new FileVideoSource
                {
                    File = filename
                };
            }

            btn.IsEnabled = true;
        }
    }
}
```

El `Clicked` controlador, a continuación, utiliza ese nombre de archivo para crear un `FileVideoSource` objeto y establecerlo en el `Source` propiedad de la `VideoPlayer`.

Cada uno de los `VideoPlayerRenderer` clases contiene código en su `SetSource` método para objetos de tipo `FileVideoSource`. Se muestran a continuación:

### <a name="handling-ios-files"></a>Control de archivos de iOS

La versión de iOS de `VideoPlayerRenderer` procesos `FileVideoSource` objetos mediante el uso de estático `Asset.FromUrl` método con el nombre de archivo. Esto crea un `AVAsset` objeto que representa el archivo de biblioteca de imágenes del dispositivo:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is FileVideoSource)
            {
                string uri = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-android-files"></a>Control de archivos de Android

Al procesar los objetos de tipo `FileVideoSource`, la implementación de Android `VideoPlayerRenderer` usa el `SetVideoPath` método `VideoView` para especificar el archivo de biblioteca de imágenes del dispositivo:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    videoView.SetVideoPath(filename);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-uwp-files"></a>Control de archivos UWP

Cuando se administran los objetos de tipo `FileVideoSource`, la implementación de UWP de la `SetSource` método necesita para crear un `StorageFile` objeto, abra ese archivo para leerlo y pase el objeto de secuencia al `SetSource` método de la `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                // Code requires Pictures Library in Package.appxmanifest Capabilities to be enabled
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    StorageFile storageFile = await StorageFile.GetFileFromPathAsync(filename);
                    IRandomAccessStreamWithContentType stream = await storageFile.OpenReadAsync();
                    Control.SetSource(stream, storageFile.ContentType);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

Para cada plataforma, el vídeo empieza a reproducirse casi inmediatamente después de que el vídeo se establece el origen porque el archivo se encuentra en el dispositivo y no debe descargarse.



## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones en el Reproductor de vídeo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
- [Seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
