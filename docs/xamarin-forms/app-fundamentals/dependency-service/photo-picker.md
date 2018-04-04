---
title: Seleccionar una foto de la biblioteca de imágenes
description: Usar DependencyService para elegir una foto de la biblioteca de imágenes del teléfono
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 5b4e3ab12a74b0f70866dc9f41593bfd5bcec0e8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="picking-a-photo-from-the-picture-library"></a>Seleccionar una foto de la biblioteca de imágenes

Este artículo le guía a través de la creación de una aplicación que permite al usuario elegir una foto de la biblioteca de imágenes del teléfono. Porque Xamarin.Forms no incluye esta funcionalidad, es necesario utilizar [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) para tener acceso a las API nativas en cada plataforma.  En este artículo se tratan los pasos siguientes para utilizar `DependencyService` para esta tarea:

- **[Creación de la interfaz](#Creating_the_Interface)**  &ndash; comprender cómo se crea la interfaz de código compartido.
- **[Implementación de iOS](#iOS_Implementation)**  &ndash; obtener información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**  &ndash; obtener información sobre cómo implementar la interfaz en código nativo para Android.
- **[Implementación de la plataforma de Windows universal](#UWP_Implementation)**  &ndash; obtener información sobre cómo implementar la interfaz en código nativo para la plataforma Universal de Windows (UWP).
- **[Implementación de Windows Phone](#Windows_Phone_Implementation)**  &ndash; obtener información sobre cómo implementar la interfaz en código nativo para Windows Phone 8.1.
- **[Implementar en el código compartido](#Implementing_in_Shared_Code)**  &ndash; obtener información sobre cómo usar `DependencyService` para llamar a la implementación nativa desde código compartido.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

En primer lugar, cree una interfaz en el código compartido que expresa la funcionalidad deseada. En el caso de una aplicación de selección de fotos, es necesario un solo método. Esto se define en el [ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) interfaz en la biblioteca de clases portables del código de ejemplo:

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

El `GetImageStreamAsync` método se define como asincrónicas porque el método debe devolver rápidamente, pero no se puede devolver un `Stream` objeto de la foto seleccionada hasta que el usuario tiene examinar la biblioteca de imágenes y selecciona uno.

Esta interfaz se implementa en todas las plataformas con código específico de la plataforma.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

La implementación de iOS de la `IPicturePicker` utiliza la interfaz la [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) tal y como se describe en el [ **elegir una foto de la galería** ](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/) receta y [código de ejemplo](https://github.com/xamarin/recipes/tree/master/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

La implementación de iOS se encuentra en la [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) clase en el proyecto de iOS del código de ejemplo. Para hacer que esta clase sea visible para el `DependencyService` manager, se debe identificar la clase con un [`assembly`] atributo de tipo `Dependency`, y la clase debe ser públicos e implementar explícitamente la `IPicturePicker` interfaz:

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

En este momento, el `GetImageStreamAsync` método debe devolver un `Task<Stream>` objeto al código que llama. Esta tarea se ha completado solo cuando el usuario ha terminado de interactuar con la biblioteca de fotografías y se llama a uno de los controladores de eventos. En situaciones como esta, el [ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) clase es esencial. La clase proporciona un `Task` objeto del tipo genérico adecuado para devolver desde el `GetImageStreamAsync` método y la clase más adelante se señaliza cuando se completa la tarea.

El `FinishedPickingMedia` controlador de eventos se llama cuando el usuario ha seleccionado una imagen. Sin embargo, el controlador proporciona una `UIImage` objeto y el `Task` debe devolver .NET `Stream` objeto. Esto se realiza en dos pasos: el `UIImage` objeto primero se convierte en un archivo JPEG en memoria almacenada en un `NSData` objeto y, a continuación, el `NSData` objeto se convierte en .NET `Stream` objeto. Una llamada a la `SetResult` método de la `TaskComkpletionSource` objeto completa la tarea, proporcionando la `Stream` objeto:

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

Una aplicación de iOS requiere el permiso del usuario para tener acceso a la biblioteca de fotos del teléfono. Agregue lo siguiente a la `dict` sección del archivo Info.plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

La implementación de Android utiliza la técnica descrita en el [ **seleccionar una imagen de** ](https://developer.xamarin.com/recipes/android/other_ux/pick_image/) receta y [código de ejemplo](https://github.com/xamarin/recipes/tree/master/android/other_ux/pick_image). Sin embargo, el método que se llama cuando el usuario ha seleccionado una imagen de la biblioteca de imágenes es una `OnActivityResult` invalidar en una clase que deriva de `Activity`. Por este motivo, la normal [ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) clase en el proyecto de Android ha complementado con un campo, una propiedad y una invalidación de la `OnActivityResult` método:

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
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

El `OnActivityResult`invalidación indica el archivo de imagen seleccionada con un Android `Uri` objeto, aunque se puede convertir en .NET `Stream` objeto mediante una llamada a la `OpenInputStream` método de la `ContentResolver` objeto que se obtuvo de la la actividad `ContentResolver` propiedad.

Al igual que la implementación de iOS, usa la implementación de Android un `TaskCompletionSource` para indicar cuándo se ha completado la tarea. Esto `TaskCompletionSource` objeto se define como una propiedad pública en la `MainActivity` clase. Esto permite que la propiedad que hace referencia en el [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) clase en el proyecto de Android. Ésta es la clase con la `GetImageStreamAsync` método:

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

Este método tiene acceso a la `MainActivity` clase para varias cosas: para la `Instance` propiedad, para la `PickImageId` field, para la `TaskCompletionSource` propiedad y para llamar a `StartActivityForResult`. Este método se define mediante la `FormsApplicationActivity` clase que es la clase base de `MainActivity`.

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>Implementación de UWP

A diferencia del iOS y Android implementaciones, la implementación del selector de fotografías para la plataforma Universal de Windows no requiere la `TaskCompletionSource` clase. El [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) clase utiliza el [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) clase para obtener acceso a la biblioteca de fotografías. Dado que la `PickSingleFileAsync` método de `FileOpenPicker` es asincrónica, el `GetImageStreamAsync` simplemente puede utilizar el método `await` con ese método (y otros métodos asincrónicos) y devolver un `Stream` objeto:


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

<a name="Windows_Phone_Implementation" />

## <a name="windows-phone-81-implementation"></a>Implementación de Windows Phone 8.1

La implementación de Windows Phone 8.1 es similar a la implementación de UWP excepto una gran diferencia que tendrá un impacto mayor: el `PickSingleFileAsync` método `FileOpenPicker` no está disponible. En su lugar, el `GetImageStreamAsync` debe llamar al método `PickSingleFileAndContinue`. Este método devuelve cuando la biblioteca de fotografías se muestra al usuario, pero la selección del usuario de una foto se señaliza mediante una llamada a la `OnActivated` método de la `App` clase.

Por este motivo, el [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/WinPhone/App.xaml.cs) clase creada mediante la plantilla de proyecto de Xamarin.Forms en el proyecto de Windows Phone 8.1 ha sido completada con una propiedad de tipo `TaskCompletionSource` y una invalidación de la `OnActivated` método:

```csharp
namespace DependencyServiceSample.WinPhone
{
    public sealed partial class App : Application
    {
        ...
        public TaskCompletionSource<Stream> TaskCompletionSource { set; get; }

        protected async override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            IContinuationActivatedEventArgs continuationArgs = args as IContinuationActivatedEventArgs;

            if (continuationArgs != null &&
                continuationArgs.Kind == ActivationKind.PickFileContinuation)
            {
                FileOpenPickerContinuationEventArgs pickerArgs = args as FileOpenPickerContinuationEventArgs;

                if (pickerArgs.Files.Count > 0)
                {
                    // Get the file and a Stream
                    StorageFile storageFile = pickerArgs.Files[0];
                    IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
                    Stream stream = raStream.AsStreamForRead();

                    // Set the completion of the Task
                    TaskCompletionSource.SetResult(stream);
                }
                else
                {
                    TaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

El `OnActivated` método podría denominarse por varios motivos, incluidos el inicio de la aplicación. El código restringe a sí mismo a solo aquellas llamadas cuando el selector de archivo / abrir ha terminado y, a continuación, obtiene un `Stream` objeto desde el `StorageFile`.

El [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/WinPhone/PicturePickerImplementation.cs) clase contiene la `GetImageStreamAsync` método que crea el `FileOpenPicker` y llamadas `PickSingleFileAndContainue`:

```csharp
[assembly: Xamarin.Forms.Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.WinPhone
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
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

            // Display the picker for a single file (resumes in OnActivated in App.xaml.cs)
            openPicker.PickSingleFileAndContinue();

            // Create a TaskCompletionSource stored in App.xaml.cs
            TaskCompletionSource<Stream> taskCompletionSource = new TaskCompletionSource<Stream>();
            (Application.Current as App).TaskCompletionSource = taskCompletionSource;

            // Return the Task object
            return taskCompletionSource.Task;
        }
    }
}

```

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementar en el código compartido

Ahora que se ha implementado la interfaz para cada plataforma, la aplicación en la biblioteca de clases portables común puede sacar partido de ella.

El [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) clase crea un `Button` para seleccionar una foto:

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

El `Clicked` controlador utiliza la `DependencyService` clase para llamar a `GetImageStreamAsync`. Esto produce una llamada en el proyecto de plataforma. Si el método devuelve un `Stream` objeto, a continuación, crea el controlador de un `Image` elemento correspondiente a la imagen con un `TabGestureRecognizer`y reemplaza el `StackLayout` en la página con el que `Image`:

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

Al puntear en el `Image` elemento devuelve la página a su estado normal.


## <a name="related-links"></a>Vínculos relacionados

- [Elija una foto de la Galería (iOS)](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/)
- [Seleccione una imagen (Android)](https://developer.xamarin.com/recipes/android/other_ux/pick_image/)
- [DependencyService (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
