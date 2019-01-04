---
title: Seleccionar una foto de la biblioteca de imágenes
description: En este artículo, se explica cómo usar la clase Xamarin.Forms de DependencyService para seleccionar una foto de la biblioteca de imágenes del teléfono.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 3452c79621013690f967e065c7afaf0768a50c3f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057500"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Seleccionar una foto de la biblioteca de imágenes

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)

En este artículo, se explica cómo crear una aplicación que permita al usuario seleccionar una foto de la biblioteca de imágenes del teléfono. Como en Xamarin.Forms no se incluye esta función, es necesario usar [`DependencyService`](xref:Xamarin.Forms.DependencyService) para acceder a las API nativas en cada plataforma.  En este artículo, se explican los pasos siguientes para usar `DependencyService` para esta tarea:

- **[Crear la interfaz](#Creating_the_Interface)**: obtenga información sobre cómo se crea la interfaz en código compartido.
- **[Implementación de iOS](#iOS_Implementation)**: obtenga información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**: obtenga información sobre cómo implementar la interfaz en código nativo para Android.
- **[Implementación de UWP](#UWP_Implementation)**: obtenga información sobre cómo implementar la interfaz en código nativo para la Plataforma universal de Windows (UWP).
- **[Implementación en código compartido](#Implementing_in_Shared_Code)**: obtenga información sobre cómo usar `DependencyService` para llamar a la implementación nativa desde código compartido.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

Primero, cree una interfaz en el código compartido que exprese la función deseada. En el caso de una aplicación de selección de fotos, solo se necesita un método. Esto se define en la interfaz [`IPicturePicker`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) de la biblioteca de .NET Standard del código de ejemplo:

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

El método `GetImageStreamAsync` se define como asincrónico porque tiene que devolver resultados rápidamente, pero no puede devolver un objeto `Stream` para la foto seleccionada hasta que el usuario haya abierto la biblioteca de imágenes y haya seleccionado una.

Esta interfaz se implementa en todas las plataformas que usan código específico de plataforma.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

La implementación de iOS de la interfaz `IPicturePicker` usa el objeto [`UIImagePickerController`](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/), como se describe en la receta [**Seleccionar una foto de la galería**](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) y en el [código de ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

La implementación de iOS se contiene en la clase [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) del proyecto de iOS del código de ejemplo. Para que el administrador `DependencyService` pueda ver esta clase, tiene que identificarse con un atributo [`assembly`] del tipo `Dependency` y, además, tiene que ser pública e implementar explícitamente la interfaz `IPicturePicker`:

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

El método `GetImageStreamAsync` crea un elemento `UIImagePickerController` y lo inicializa para seleccionar imágenes desde la biblioteca de fotos. Se necesitan dos controladores de eventos: uno para cuando el usuario seleccione una foto y otro para cuando cancele la visualización de la biblioteca de fotos. Después, `PresentModalViewController` muestra la biblioteca de fotos al usuario.

En este momento, el método `GetImageStreamAsync` tiene que devolver un objeto `Task<Stream>` al código que realiza la llamada. Esta tarea solo se completa cuando el usuario termina de interactuar con la biblioteca de fotos y se llama a uno de los controladores de eventos. Para situaciones como esta, la clase [`TaskCompletionSource`](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) es esencial. La clase proporciona un objeto `Task` al tipo genérico adecuado para devolver desde el método `GetImageStreamAsync` y, después, se puede enviar una señal a la clase cuando se complete la tarea.

Se llama al controlador de eventos `FinishedPickingMedia` cuando el usuario ha seleccionado una imagen. Pero el controlador proporciona un objeto `UIImage` y el elemento `Task` tiene que devolver un objeto `Stream` de .NET. Esto se realiza en dos pasos: primero, el objeto `UIImage` se convierte a un archivo JPEG en memoria almacenado en un objeto `NSData` y, después, el objeto `NSData` se convierte a un objeto `Stream` de .NET. Una llamada al método `SetResult` del objeto `TaskCompletionSource` completa la tarea al proporcionar el objeto `Stream`:

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

                UnregisterEventHandlers();

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
            }
            else
            {
                UnregisterEventHandlers();
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            UnregisterEventHandlers();
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }

        void UnregisterEventHandlers()
        {
            imagePicker.FinishedPickingMedia -= OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled -= OnImagePickerCancelled;
        }
    }
}

```

Una aplicación de iOS necesita el permiso del usuario para acceder a la biblioteca de fotos del teléfono. Agregue el código siguiente a la sección `dict` del archivo Info.plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

La implementación de Android usa la técnica descrita en la receta [**Seleccionar una imagen**](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) y en el [código de ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). Pero el método al que se llama cuando el usuario ha seleccionado una imagen desde la biblioteca de imágenes es un reemplazo de `OnActivityResult` en una clase derivada de `Activity`. Por este motivo, la clase [`MainActivity`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) normal del proyecto de Android se ha complementado con un campo, una propiedad y un reemplazo del método `OnActivityResult`:

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

El reemplazo `OnActivityResult` indica el archivo de imagen seleccionado con un objeto `Uri` de Android, pero esto se puede convertir a un objeto `Stream` de .NET si se llama al método `OpenInputStream` del objeto `ContentResolver` obtenido desde la propiedad `ContentResolver` de la actividad.

Al igual que la implementación de iOS, en la implementación de Android se usa un elemento `TaskCompletionSource` para indicar que se ha completado la tarea. Este objeto `TaskCompletionSource` se define como una propiedad pública de la clase `MainActivity`. Esto permite hace referencia a la propiedad en la clase [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) del proyecto de Android. Esta es la clase con el método `GetImageStreamAsync`:

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

Este método accede a la clase `MainActivity` por varios motivos: para la propiedad `Instance`, para el campo `PickImageId`, para la propiedad `TaskCompletionSource` y para llamar a `StartActivityForResult`. Este método se define mediante la clase `FormsAppCompatActivity`, que es la clase base de `MainActivity`.

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>Implementación de UWP

Al contrario que en las implementaciones de iOS y Android, la implementación del selector de fotos para la Plataforma universal de Windows no necesita la clase `TaskCompletionSource`. La clase [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) usa la clase [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) para acceder a la biblioteca de fotos. Como el método `PickSingleFileAsync` de `FileOpenPicker` es en sí asincrónico, el método `GetImageStreamAsync` puede simplemente usar `await` con ese método (y otros métodos asincrónicos) y devolver un objeto `Stream`:


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

## <a name="implementing-in-shared-code"></a>Implementación en código compartido

Después de implementar la interfaz para cada plataforma, la aplicación de la biblioteca de .NET Standard puede usarla.

La clase [`App`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) crea un elemento `Button` para seleccionar una foto:

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

El controlador `Clicked` usa la clase `DependencyService` para llamar a `GetImageStreamAsync`. Como resultado, se produce una llamada en el proyecto de la plataforma. Si el método devuelve un objeto `Stream`, el controlador crea un elemento `Image` para esa imagen con un elemento `TapGestureRecognizer` y reemplaza el elemento `StackLayout` en la página por ese elemento `Image`:

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

Al pulsar `Image` en el elemento, la página vuelve al estado normal.


## <a name="related-links"></a>Vínculos relacionados

- [Seleccionar una foto de la galería (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [Seleccionar una imagen (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [DependencyService (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
