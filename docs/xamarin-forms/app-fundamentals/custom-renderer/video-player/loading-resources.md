---
title: La carga de vídeos de recursos de aplicación
description: Este artículo explica cómo cargar vídeos almacenados como recursos de la aplicación en una aplicación de Reproductor de vídeo mediante Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 17e9e7061e4329431a0f34abdbbb616a1aff1b43
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171331"
---
# <a name="loading-application-resource-videos"></a>La carga de vídeos de recursos de aplicación

Los representadores personalizados para el `VideoPlayer` vista son capaces de reproducir archivos de vídeo que se han incrustado en los proyectos de plataforma individuales como recursos de la aplicación. Sin embargo, la versión actual de `VideoPlayer` no puede tener acceso a los recursos incrustados en una biblioteca .NET Standard.

Para cargar estos recursos, cree una instancia de `ResourceVideoSource` estableciendo el `Path` propiedad para el nombre de archivo (o la carpeta y nombre de archivo) del recurso. Como alternativa, puede llamar a estático `VideoSource.FromResource` método para hacer referencia al recurso. A continuación, establezca el `ResourceVideoSource` de objeto para el `Source` propiedad de `VideoPlayer`.

## <a name="storing-the-video-files"></a>Almacenar los archivos de vídeo

Almacenamiento de un archivo de vídeo en el proyecto de la plataforma es diferente para cada plataforma.

### <a name="ios-video-resources"></a>recursos de vídeo de iOS

En un proyecto de iOS, puede almacenar un vídeo en el **recursos** , o en una subcarpeta de la **recursos** carpeta. El archivo de vídeo debe tener un `Build Action` de `BundleResource`. Establecer el `Path` propiedad de `ResourceVideoSource` al nombre de archivo, por ejemplo, **MyFile.mp4** para un archivo en el **recursos** carpeta, o **MyFolder/MyFile.mp4**, donde **MiCarpeta** es una subcarpeta de **recursos**.

En el **VideoPlayerDemos** solución, el **VideoPlayerDemos.iOS** proyecto contiene una subcarpeta de **recursos** denominado **vídeos** que contiene un archivo denominado **iOSApiVideo.mp4**. Se trata de un breve vídeo que muestra cómo usar el sitio web de Xamarin para encontrar documentación para iOS `AVPlayerViewController` clase.

### <a name="android-video-resources"></a>Recursos de vídeo de Android

En un proyecto de Android, vídeos deben almacenarse en una subcarpeta de **recursos** denominado **raw**. El **raw** carpeta no puede contener las subcarpetas. Asigne al archivo de vídeo una `Build Action` de `AndroidResource`. Establecer el `Path` propiedad de `ResourceVideoSource` al nombre de archivo, por ejemplo, **MyFile.mp4**.

El **VideoPlayerDemos.Android** proyecto contiene una subcarpeta de **recursos** denominado **raw**, que contiene un archivo denominado **AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>Recursos de vídeo de UWP

En un proyecto de plataforma Universal de Windows, puede almacenar vídeos en cualquier carpeta del proyecto. Asigne al archivo un `Build Action` de `Content`. Establecer el `Path` propiedad de `ResourceVideoSource` a la carpeta y el nombre de archivo, por ejemplo, **MyFolder/MyVideo.mp4**.

El **VideoPlayerDemos.UWP** proyecto contiene una carpeta denominada **vídeos** con el archivo **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Al cargar los archivos de vídeo

Cada una de las clases del representador de plataforma contiene código en su `SetSource` método para cargar los archivos de vídeo almacenados como recursos.

### <a name="ios-resource-loading"></a>carga de recursos de iOS

La versión de iOS de `VideoPlayerRenderer` usa el `GetUrlForResource` método `NSBundle` para cargar el recurso. La ruta de acceso completa debe dividirse en un nombre de archivo, extensión y directorio. El código usa el `Path` clase en .NET `System.IO` espacio de nombres para dividir la ruta de acceso en estos componentes:

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
            else if (Element.Source is ResourceVideoSource)
            {
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhitespace(path))
                {
                    string directory = Path.GetDirectoryName(path);
                    string filename = Path.GetFileNameWithoutExtension(path);
                    string extension = Path.GetExtension(path).Substring(1);
                    NSUrl url = NSBundle.MainBundle.GetUrlForResource(filename, extension, directory);
                    asset = AVAsset.FromUrl(url);
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="android-resource-loading"></a>Carga de recursos de Android

Android `VideoPlayerRenderer` usa el nombre de paquete y el nombre de archivo para construir un `Uri` objeto. El nombre del paquete es el nombre de la aplicación, en este caso **VideoPlayerDemos.Android**, que puede obtenerse de estático `Context.PackageName` propiedad. El resultante `Uri` objeto, a continuación, se pasa a la `SetVideoURI` método `VideoView`:

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
            else if (Element.Source is ResourceVideoSource)
            {
                string package = Context.PackageName;
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string filename = Path.GetFileNameWithoutExtension(path).ToLowerInvariant();
                    string uri = "android.resource://" + package + "/raw/" + filename;
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="uwp-resource-loading"></a>Carga de recursos UWP

UWP `VideoPlayerRenderer` construye un `Uri` objeto para la ruta de acceso y lo establece en el `Source` propiedad de `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = "ms-appx:///" + (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    Control.Source = new Uri(path);
                    hasSetSource = true;
                }
            }
        }
        ···
    }
}
```

## <a name="playing-the-resource-file"></a>Reproducir el archivo de recursos

El **reproducir vídeo recursos** página en el **VideoPlayerDemos** solución usa el `OnPlatform` clase para especificar el archivo de vídeo para cada plataforma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayVideoResourcePage"
             Title="Play Video Resource">
    <video:VideoPlayer>
        <video:VideoPlayer.Source>
            <video:ResourceVideoSource>
                <video:ResourceVideoSource.Path>
                    <OnPlatform x:TypeArguments="x:String">
                        <On Platform="iOS" Value="Videos/iOSApiVideo.mp4" />
                        <On Platform="Android" Value="AndroidApiVideo.mp4" />
                        <On Platform="UWP" Value="Videos/UWPApiVideo.mp4" />
                    </OnPlatform>
                </video:ResourceVideoSource.Path>
            </video:ResourceVideoSource>
        </video:VideoPlayer.Source>
    </video:VideoPlayer>
</ContentPage>
```

Si el recurso de iOS se almacena en el **recursos** carpeta, y si el recurso UWP se almacena en la carpeta raíz del proyecto, puede usar el mismo nombre de archivo para cada plataforma. Si es así, puede establecer ese nombre directamente a la `Source` propiedad de `VideoPlayer`.

Aquí es esa página se ejecuta:

[![Reproducir vídeo recursos](loading-resources-images/playvideoresource-small.png "reproducir vídeo recursos")](loading-resources-images/playvideoresource-large.png#lightbox "reproducir los recursos de vídeo")

Ahora ha visto cómo [cargar vídeos desde un URI de Web](web-videos.md) y cómo se juega a los recursos incrustados. Además, puede [cargar vídeos desde la biblioteca de vídeos del dispositivo](accessing-library.md).


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones en el Reproductor de vídeo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
