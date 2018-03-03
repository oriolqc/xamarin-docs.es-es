---
title: Personalizar un contenidoPage
description: "Un contenidoPage es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla. Este artículo demuestra cómo crear a un representador personalizado para la página contenidoPage, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propios personalización específica de la plataforma."
ms.topic: article
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: f1f420641691e700894687fef8ea3bd44fd60ff2
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="customizing-a-contentpage"></a>Personalizar un contenidoPage

_Un contenidoPage es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla. Este artículo demuestra cómo crear a un representador personalizado para la página contenidoPage, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propios personalización específica de la plataforma._

Cada control Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Cuando un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) se representa mediante una aplicación de Xamarin.Forms, en iOS la `PageRenderer` se crea la instancia de clase, que a su vez crea una instancia nativo `UIViewController` control. En la plataforma Android, el `PageRenderer` crea una instancia de la clase un `ViewGroup` control. En Windows Phone y la plataforma Universal de Windows (UWP), el `PageRenderer` crea una instancia de la clase un `FrameworkElement` control. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y representador, consulte [clases del representador Base y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

En el diagrama siguiente ilustra la relación entre el [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) y los controles nativos correspondientes que lo implementan:

![](contentpage-images/contentpage-classes.png "Relación entre la clase contenidoPage e implementar controles nativos")

El proceso de representación puede tomar ventaja de implementar personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Xamarin.Forms_Page) una página de Xamarin.Forms.
1. [Consumir](#Consuming_the_Xamarin.Forms_Page) la página de Xamarin.Forms.
1. [Crear](#Creating_the_Page_Renderer_on_each_Platform) el representador personalizado para la página en cada plataforma.

Cada elemento ahora se tratarán a su vez, para implementar un `CameraPage` que proporciona una cámara en directo de fuentes de distribución y la capacidad para capturar una foto.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Creación de la página de Xamarin.Forms

Un inalterados [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) pueden agregarse al proyecto Xamarin.Forms compartido, tal como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

De forma similar, el archivo de código subyacente para la [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) también debe permanecer sin modificaciones, tal como se muestra en el ejemplo de código siguiente:

```csharp
public partial class CameraPage : ContentPage
{
    public CameraPage ()
    {
        // A custom renderer is used to display the camera UI
        InitializeComponent ();
    }
}
```

En el ejemplo de código siguiente se muestra cómo se puede crear la página en C#:

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Una instancia de la `CameraPage` se usará para mostrar la cámara en vivo fuentes de distribución en cada plataforma. Personalización del control se llevará a cabo en el representador personalizado, por lo que no se requiere ninguna implementación adicional en la `CameraPage` clase.

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Consumir la página de Xamarin.Forms

Vacío `CameraPage` deben mostrarse por la aplicación de Xamarin.Forms. Esto se produce cuando un botón en el `MainPage` instancia que se derivan, que a su vez se ejecuta la `OnTakePhotoButtonClicked` método, tal como se muestra en el ejemplo de código siguiente:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Este código simplemente se desplaza a la `CameraPage`, en los representadores personalizados a personalizar el aspecto de la página en cada plataforma.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Crear al representador de página en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `PageRenderer` clase.
1. Invalidar el `OnElementChanged` método que representa la lógica de página y de escritura nativa para personalizar la página. El `OnElementChanged` método se llama cuando se crea el control de Xamarin.Forms correspondiente.
1. Agregar un `ExportRenderer` atributo a la clase de representador de página para especificar que se utilizará para representar la página de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> **Tenga en cuenta**: es opcional proporcionar un representador en página en cada proyecto de la plataforma. Si no se ha registrado un representador en página, se usará el representador predeterminado de la página.

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con la relación entre ellos:

![](contentpage-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado CameraPage")

El `CameraPage` representa instancia específica de la plataforma `CameraPageRenderer` clases que derivan de la `PageRenderer` clase para esa plataforma. Esto da como resultado de cada `CameraPage` de instancia que se procesa con una fuente de la cámara en directo, como se muestra en las siguientes capturas de pantalla:

![](contentpage-images/screenshots.png "CameraPage en cada plataforma")

El `PageRenderer` clase expone el `OnElementChanged` método, que se llama cuando se crea la página de Xamarin.Forms para representar el control nativo correspondiente. Este método toma una `ElementChangedEventArgs` parámetro que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento Xamarin.Forms que el representador *era* adjunta y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo la `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `CameraPage` instancia.

Una versión reemplazada de la `OnElementChanged` método en la `CameraPageRenderer` clase es el lugar donde realice la personalización de la página nativo. Una referencia a la instancia de la página de Xamarin.Forms que se está representando puede obtenerse a través de la `Element` propiedad.

Cada clase de representador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo de la página de Xamarin.Forms que se procesa y el nombre del tipo de representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes describe la implementación de la `CameraPageRenderer` un representador personalizado para cada plataforma.

### <a name="creating-the-page-renderer-on-ios"></a>Crear al representador de página en iOS

En el ejemplo de código siguiente se muestra al representador de página para la plataforma de iOS:

```csharp
[assembly:ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                AuthorizeCameraUse ();
            } catch (Exception ex) {
                System.Diagnostics.Debug.WriteLine (@"          ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La llamada a la clase base `OnElementChanged` método crea una instancia de un iOS `UIViewController` control. La secuencia de la cámara en directo solo se representa siempre que el representador ya no está conectado a un elemento existente de Xamarin.Forms y siempre que exista una instancia de la página que se está representando por el representador personalizado.

A continuación, personalizar la página mediante una serie de métodos que utilizan el `AVCapture` API para proporcionar la secuencia en directo de la cámara y la capacidad de capturar una foto.

### <a name="creating-the-page-renderer-on-android"></a>Crear al representador de página en Android

En el ejemplo de código siguiente se muestra al representador de página para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
    {
        ...
        public CameraPageRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetupUserInterface();
                SetupEventHandlers();
                AddView(view);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(@"           ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La llamada a la clase base `OnElementChanged` método crea una instancia de un Android `ViewGroup` control, que es un grupo de vistas. La secuencia de la cámara en directo solo se representa siempre que el representador ya no está conectado a un elemento existente de Xamarin.Forms y siempre que exista una instancia de la página que se está representando por el representador personalizado.

A continuación, personalizar la página mediante la invocación de una serie de métodos que utilizan el `Camera` API para proporcionar la secuencia en directo de la cámara y la capacidad de capturar una foto, antes el `AddView` se invoca el método para agregar la cámara en vivo transmitir la interfaz de usuario para el `ViewGroup`.

### <a name="creating-the-page-renderer-on-windows-phone"></a>Crear al representador de páginas en Windows Phone

En el ejemplo de código siguiente se muestra al representador de página para la plataforma Windows Phone:

```csharp
[assembly: ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.WinPhone81
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                ...
                var container = ContainerElement as Canvas;

                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                container.Children.Add (page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}
```

La llamada a la clase base `OnElementChanged` método crea una instancia de un Windows Phone `Canvas` control, en el que se representa la página. La secuencia de la cámara en directo solo se representa siempre que el representador ya no está conectado a un elemento existente de Xamarin.Forms y siempre que exista una instancia de la página que se está representando por el representador personalizado.

En la plataforma Windows Phone, una referencia con tipo a la página nativo que se va a usar en la plataforma puede tener acceso mediante el `ContainerElement` propiedad, con el `Canvas` controlan que la referencia con tipo a la `FrameworkElement`. A continuación, personalizar la página mediante la invocación de una serie de métodos que utilizan el `MediaCapture` API para proporcionar la secuencia en directo de la cámara y la capacidad de capturar una fotografía antes de la página personalizada se agrega a la `Canvas` para su presentación.

Al implementar un representador personalizado que deriva de `PageRenderer` en el tiempo de ejecución de Windows, la `ArrangeOverride` método también debe implementarse para organizar los controles de la página, porque el representador de base no sabe qué hacer con ellos. En caso contrario, da como resultado una página en blanco. Por lo tanto, en este ejemplo el `ArrangeOverride` llamadas al método el `Arrange` método en el `Page` instancia.

> [!NOTE]
> **Tenga en cuenta**: es importante detener y eliminar los objetos que proporcionan acceso a la cámara en una aplicación de Windows Phone 8.1 WinRT. Si no lo hace puede interferir con otras aplicaciones que intentan tener acceso a la cámara del dispositivo. Para obtener más información, consulte el `CleanUpCaptureResourcesAsync` método en el proyecto de Windows Phone en la solución de ejemplo y [inicio rápido: capturar vídeo mediante la API de MediaCapture](https://msdn.microsoft.com/library/windows/apps/xaml/dn642092.aspx).

### <a name="creating-the-page-renderer-on-uwp"></a>Crear al representador de página en UWP

En el ejemplo de código siguiente se muestra al representador de página para UWP:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPageRenderer : PageRenderer
    {
        ...
        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                ...
                SetupUserInterface();
                SetupCamera();

                this.Children.Add(page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}

```

La llamada a la clase base `OnElementChanged` crea una instancia de método un `FrameworkElement` control, en el que se representa la página. La secuencia de la cámara en directo solo se representa siempre que el representador ya no está conectado a un elemento existente de Xamarin.Forms y siempre que exista una instancia de la página que se está representando por el representador personalizado. A continuación, personalizar la página mediante la invocación de una serie de métodos que utilizan el `MediaCapture` API para proporcionar la secuencia en directo de la cámara y la capacidad de capturar una fotografía antes de la página personalizada se agrega a la `Children` colección para su presentación.

Al implementar un representador personalizado que deriva de `PageRenderer` en UWP, la `ArrangeOverride` método también debe implementarse para organizar los controles de la página, porque el representador de base no sabe qué hacer con ellos. En caso contrario, da como resultado una página en blanco. Por lo tanto, en este ejemplo el `ArrangeOverride` llamadas al método el `Arrange` método en el `Page` instancia.

> [!NOTE]
> **Tenga en cuenta**: es importante detener y eliminar los objetos que proporcionan acceso a la cámara en una aplicación de UWP. Si no lo hace puede interferir con otras aplicaciones que intentan tener acceso a la cámara del dispositivo. Para obtener más información, consulte [mostrar la vista previa de la cámara](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Resumen

Este artículo demuestra cómo crear un representador personalizado para el [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) página, permitiendo a los programadores reemplazar el procesamiento nativo de forma predeterminada con su propios personalización específica de la plataforma. A `ContentPage` es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla.


## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererContentPage (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
