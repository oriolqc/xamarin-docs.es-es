---
title: Seleccionar una foto de la biblioteca de imágenes
description: En este artículo se explica cómo usar la clase Xamarin.Forms DependencyService para elegir una foto de la biblioteca de imágenes del teléfono.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: b593815df9ce942a98496806116bacfa63e2a2d9
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38999085"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Seleccionar una foto de la biblioteca de imágenes

En este artículo le guía a través de la creación de una aplicación que permite al usuario elegir una foto de la biblioteca de imágenes del teléfono. Dado que Xamarin.Forms no incluye esta funcionalidad, es necesario utilizar [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) para tener acceso a las API nativas en cada plataforma.  En este artículo se describen los pasos siguientes para usar `DependencyService` para esta tarea:

- **[Creación de la interfaz](#Creating_the_Interface)**  &ndash; comprender cómo se crea la interfaz en código compartido.
- **[Implementación de iOS](#iOS_Implementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para Android.
- **[Implementación de la plataforma Windows universal](#UWP_Implementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para la plataforma Universal de Windows (UWP).
- **[Implementar en código compartido](#Implementing_in_Shared_Code)**  &ndash; Aprenda a usar `DependencyService` para llamar a la implementación nativa desde código compartido.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

En primer lugar, cree una interfaz en código compartido que expresa la funcionalidad deseada. En el caso de una aplicación de selección de fotos, es necesario un solo método. Esto se define en el [ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) interfaz en la biblioteca estándar de .NET del código de ejemplo:

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

El `GetImageStreamAsync` método se define como asincrónica, porque el método debe devolver rápidamente, pero no se puede devolver un `Stream` objeto de la foto seleccionada hasta que el usuario tiene la biblioteca de imágenes puede examinar y selecciona uno.

Esta interfaz se implementa en todas las plataformas con código específico de plataforma.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

La implementación de iOS de la `IPicturePicker` interfaz usa el [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) como se describe en el [ **elija una foto desde la galería** ](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/) receta y [código de ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

La implementación de iOS se encuentra en la [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) clase en el proyecto de iOS del código de ejemplo. Para que esta clase sea visible para el `DependencyService` manager, la clase debe identificarse con un [`assembly`] atributo de tipo `Dependency`, y la clase debe ser pública y se implementa explícitamente la `IPicturePicker` interfaz:

```csharp
[assembly: Dependency (typeof (PicturePickerImplementation))]

namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<Stream> GetImageStreamAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.PhotoLibrary,
                MediaTypes = UIImagePickerController.AvailableMediaTypes(UIImagePickerControllerSourceType.PhotoLibrary)
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<Stream>();
            return taskCompletionSource.Task;
        }
        ...
    }
}

```

El `GetImageStreamAsync` método crea un `UIImagePickerController` y lo inicializa para seleccionar las imágenes de la biblioteca de fotografías. Se requieren dos controladores de eventos: uno para cuando el usuario selecciona una foto y otro para cuando el usuario cancela la presentación de la biblioteca de fotografías. El `PresentModalViewController` , a continuación, muestra la biblioteca de fotografías al usuario.

En este momento, el `GetImageStreamAsync` método debe devolver un `Task<Stream>` objeto al código que llama. Esta tarea se completa solo cuando el usuario ha terminado de interactuar con la biblioteca de fotografías y se llama a uno de los controladores de eventos. Para situaciones como esta, el [ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) clase es esencial. La clase proporciona un `Task` objeto del tipo genérico correcto para devolver desde el `GetImageStreamAsync` método y la clase más adelante se señaliza cuando se completa la tarea.

El `FinishedPickingMedia` se llama al controlador de eventos cuando el usuario ha seleccionado una imagen. Sin embargo, el controlador proporciona una `UIImage` objeto y el `Task` debe devolver un .NET `Stream` objeto. Esto se realiza en dos pasos: el `UIImage` objeto primero se convierte en un archivo JPEG en la memoria almacenada en un `NSData` objeto y, a continuación, el `NSData` objeto se convierte en un .NET `Stream` objeto. Una llamada a la `SetResult` método de la `TaskCompletionSource` objeto completa la tarea proporcionando la `Stream` objeto:

```csharp
namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;
        ...
        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            UIImage image = args.EditedImage ?? args.OriginalImage;

            if (image != null)
            {
                // Convert UIImage to .NET Stream object
                NSData data = image.AsJPEG(1);
                Stream stream = data.AsStream();

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
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

Una aplicación de iOS requiere el permiso del usuario para tener acceso a la biblioteca de fotografías. Agregue lo siguiente a la `dict` sección del archivo Info.plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

La implementación de Android usa la técnica descrita en el [ **seleccionar una imagen** ](https://developer.xamarin.com/recipes/android/other_ux/pick_image/) receta y [código de ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). Sin embargo, el método que se llama cuando el usuario ha seleccionado una imagen de la biblioteca de imágenes es una `OnActivityResult` invalidar en una clase que derive de `Activity`. Por este motivo, el valor normal a [ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) ha complementado la clase en el proyecto de Android con un campo, una propiedad y un reemplazo del `OnActivityResult` método:

```csharp
public class MainActivity : FormsAppCompatActivity
{
    ...
    // Field, property, and method for Picture Picker
    public static readonly int PickImageId = 1000;

    public TaskCompletionSource<Stream> PickImageTaskCompletionSource { set; get; }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent intent)
    {
        base.OnActivityResult(requestCode, resultCode, intent);

        if (requestCode == PickImageId)
        {
            if ((resultCode == Result.Ok) && (intent != null))
            {
                Android.Net.Uri uri = intent.Data;
                Stream stream = ContentResolver.OpenInputStream(uri);

                // Set the Stream as the completion of the Task
                PickImageTaskCompletionSource.SetResult(stream);
            }
            else
            {
                PickImageTaskCompletionSource.SetResult(null);
            }
        }
    }
}

```

El `OnActivityResult`invalidación indica el archivo de imagen seleccionada con un Android `Uri` objeto, pero esto puede convertirse en un .NET `Stream` objeto mediante una llamada a la `OpenInputStream` método de la `ContentResolver` objeto que se obtuvo de la la actividad `ContentResolver` propiedad.

Al igual que la implementación de iOS, la implementación de Android usa un `TaskCompletionSource` para señalar cuándo se ha completado la tarea. Esto `TaskCompletionSource` objeto se define como una propiedad pública en el `MainActivity` clase. Esto permite que la propiedad que se haga referencia en el [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) clase en el proyecto de Android. Esta es la clase con el `GetImageStreamAsync` método:

```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.Droid
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("image/*");
            intent.SetAction(Intent.ActionGetContent);

            // Start the picture-picker activity (resumes in MainActivity.cs)
            MainActivity.Instance.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Picture"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            MainActivity.Instance.PickImageTaskCompletionSource = new TaskCompletionSource<Stream>();

            // Return Task object
            return MainActivity.Instance.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Este método tiene acceso a la `MainActivity` clase para varios propósitos: para el `Instance` propiedad, para el `PickImageId` field, para el `TaskCompletionSource` propiedad y llamar a `StartActivityForResult`. Este método se define mediante el `FormsAppCompatActivity` (clase), que es la clase base de `MainActivity`.

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>Implementación de UWP

A diferencia de las implementaciones de Android y iOS, la implementación del selector de fotografías de la plataforma Universal de Windows no requiere la `TaskCompletionSource` clase. El [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) clase utiliza la [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) clase para obtener acceso a la biblioteca de fotos. Dado que el `PickSingleFileAsync` método de `FileOpenPicker` es asincrónico, el `GetImageStreamAsync` simplemente puede usar el método `await` con ese método (y otros métodos asincrónicos) y devolver un `Stream` objeto:


```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.UWP
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public async Task<Stream> GetImageStreamAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary,
            };

            openPicker.FileTypeFilter.Add(".jpg");
            openPicker.FileTypeFilter.Add(".jpeg");
            openPicker.FileTypeFilter.Add(".png");

            // Get a file and return a Stream
            StorageFile storageFile = await openPicker.PickSingleFileAsync();

            if (storageFile == null)
            {
                return null;
            }

            IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
            return raStream.AsStreamForRead();
        }
    }
}
```

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementar en código compartido

Ahora que ha implementado la interfaz para cada plataforma, la aplicación en la biblioteca estándar de .NET puede aprovecharlo.

El [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) clase crea un `Button` para elegir una foto:

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

El `Clicked` controlador utiliza la `DependencyService` clase para llamar a `GetImageStreamAsync`. Esto da como resultado una llamada en el proyecto de la plataforma. Si el método devuelve un `Stream` objeto, a continuación, el controlador crea una `Image` (elemento) para esa imagen con un `TabGestureRecognizer`y reemplaza el `StackLayout` en la página con la que `Image`:

```csharp
pickPictureButton.Clicked += async (sender, e) =>
{
    pickPictureButton.IsEnabled = false;
    Stream stream = await DependencyService.Get<IPicturePicker>().GetImageStreamAsync();

    if (stream != null)
    {
        Image image = new Image
        {
            Source = ImageSource.FromStream(() => stream),
            BackgroundColor = Color.Gray
        };

        TapGestureRecognizer recognizer = new TapGestureRecognizer();
        recognizer.Tapped += (sender2, args) =>
        {
            (MainPage as ContentPage).Content = stack;
            pickPictureButton.IsEnabled = true;
        };
        image.GestureRecognizers.Add(recognizer);

        (MainPage as ContentPage).Content = image;
    }
    else
    {
        pickPictureButton.IsEnabled = true;
    }
};
```

Pulsar el `Image` elemento devuelve la página a la normalidad.


## <a name="related-links"></a>Vínculos relacionados

- [Elija una foto desde la Galería (iOS)](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/)
- [Seleccione una imagen (Android)](https://developer.xamarin.com/recipes/android/other_ux/pick_image/)
- [DependencyService (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
