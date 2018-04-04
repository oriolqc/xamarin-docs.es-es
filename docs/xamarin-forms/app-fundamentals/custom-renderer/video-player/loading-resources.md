---
title: La carga de vídeos de recursos de aplicación
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: a9dbf77b85fe7f39c152d4dfb33bdd44c72dca40
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="loading-application-resource-videos"></a>La carga de vídeos de recursos de aplicación

Los representadores personalizados para el `VideoPlayer` vista son capaces de reproducir archivos de vídeo que se han incrustado en los proyectos de plataforma individuales como recursos de la aplicación. Sin embargo, la versión actual de `VideoPlayer` no se puede obtener acceso a los recursos incrustados en una biblioteca de clases portable.

Para cargar estos recursos, cree una instancia de `ResourceVideoSource` estableciendo el `Path` propiedad para el nombre de archivo (o la carpeta y el nombre) del recurso. Como alternativa, puede llamar el método estático `VideoSource.FromResource` método hagan referencia al recurso. A continuación, establezca el `ResourceVideoSource` el objeto a la `Source` propiedad de `VideoPlayer`. 

## <a name="storing-the-video-files"></a>Almacenar los archivos de vídeo

Almacenamiento de un archivo de vídeo en el proyecto de la plataforma es diferente para las tres plataformas:

### <a name="ios-video-resources"></a>recursos de vídeo de iOS

En un proyecto de iOS, puede almacenar un vídeo en el **recursos** , o en una subcarpeta de la **recursos** carpeta. El archivo de vídeo debe tener un `Build Action` de `BundleResource`. Establecer el `Path` propiedad de `ResourceVideoSource` para el nombre de archivo, por ejemplo, **MyFile.mp4** para un archivo en el **recursos** carpeta, o **MyFolder/MyFile.mp4**, donde **MiCarpeta** es una subcarpeta de **recursos**.

En el **VideoPlayerDemos** solución, el **VideoPlayerDemos.iOS** proyecto contiene una subcarpeta de **recursos** denominado **vídeos** que contiene un archivo denominado **iOSApiVideo.mp4**. Se trata de un breve vídeo que muestra cómo utilizar el sitio web de Xamarin para buscar documentación en el programa iOS `AVPlayerViewController` clase.

### <a name="android-video-resources"></a>Recursos de vídeo Android

En un proyecto Android, vídeos deben almacenarse en una subcarpeta de **recursos** denominado **sin formato**. El **sin formato** carpeta no puede contener las subcarpetas. Asigne al archivo de vídeo una `Build Action` de `AndroidResource`. Establecer el `Path` propiedad de `ResourceVideoSource` para el nombre de archivo, por ejemplo, **MyFile.mp4**. 

El **VideoPlayerDemos.Android** proyecto contiene una subcarpeta de **recursos** denominado **sin formato**, que contiene un archivo denominado **AndroidApiVideo.mp4**. 

### <a name="uwp-video-resources"></a>Recursos de vídeo de UWP

En un proyecto de plataforma Universal de Windows, puede almacenar vídeos en cualquier carpeta del proyecto. Asigne al archivo un `Build Action` de `Content`. Establecer el `Path` propiedad de `ResourceVideoSource` a la carpeta y el nombre de archivo, por ejemplo, **MyFolder/MyVideo.mp4**. 

El **VideoPlayerDemos.UWP** proyecto contiene una carpeta denominada **vídeos** con el archivo **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Cargar los archivos de vídeo

Cada una de las clases del representador plataforma contiene código en su `SetSource` método para cargar los archivos de vídeo almacenados como recursos.

### <a name="ios-resource-loading"></a>carga de recursos de iOS

La versión de iOS de `VideoPlayerRenderer` utiliza la `GetUrlForResource` método `NSBundle` para cargar el recurso. La ruta de acceso completa debe dividirse en un nombre de archivo, la extensión y el directorio. El código usa el `Path` clase .NET `System.IO` espacio de nombres para dividir la ruta de acceso en estos componentes:

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

### <a name="android-resource-loading"></a>Carga de recursos Android

El Android `VideoPlayerRenderer` usa el nombre de archivo y nombre de paquete para construir un `Uri` objeto. El nombre del paquete es el nombre de la aplicación, en este caso **VideoPlayerDemos.Android**, que puede obtenerse de estático `Context.PackageName` propiedad. El resultante `Uri` , a continuación, se pasa el objeto a la `SetVideoURI` método `VideoView`:

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

UWP `VideoPlayerRenderer` construye una `Uri` objeto para la ruta de acceso y lo establece en el `Source` propiedad de `MediaElement`:

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

Si el recurso de iOS se almacena en la **recursos** carpeta, y si el recurso UWP se almacena en la carpeta raíz del proyecto, puede usar el mismo nombre de archivo para las tres plataformas. Si es así, puede establecer ese nombre directamente a la `Source` propiedad de `VideoPlayer`. 

Aquí es esa página se ejecuta en las tres plataformas:

[![Reproducir vídeo recursos](loading-resources-images/playvideoresource-small.png "reproducir vídeo recursos")](loading-resources-images/playvideoresource-large.png#lightbox "reproducir vídeo recursos")

Ahora que ha visto cómo [cargar vídeos desde un URI de Web](web-videos.md) y cómo se juega a los recursos incrustados. Además, puede [cargar vídeos desde la biblioteca de vídeos del dispositivo](accessing-library.md).


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de Reproductor de vídeo (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
