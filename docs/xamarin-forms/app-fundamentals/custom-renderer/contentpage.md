---
title: Personalización de un elemento ContentPage
description: Un elemento ContentPage es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla. En este artículo se muestra cómo crear a un representador personalizado para la página de ContentPage, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 2369b249681b926476cf3938c51c99745eba9098
ms.sourcegitcommit: 8888cb7d75f4469f2a1195b9a426a2e1fbf46bd8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2018
ms.locfileid: "38995747"
---
# <a name="customizing-a-contentpage"></a>Personalización de un elemento ContentPage

_Un elemento ContentPage es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla. En este artículo se muestra cómo crear a un representador personalizado para la página de ContentPage, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma._

Todos los controles Xamarin.Forms tienen un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo. Cuando un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) se representa mediante una aplicación de Xamarin.Forms, en iOS el `PageRenderer` se crea la instancia de clase, que a su vez crea una instancia nativa `UIViewController` control. En la plataforma Android, el `PageRenderer` crea una instancia de la clase un `ViewGroup` control. En la plataforma Universal de Windows (UWP), el `PageRenderer` crea una instancia de la clase un `FrameworkElement` control. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y el representador, consulte [clases Base del representador y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama ilustra la relación entre el [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) y los controles nativos correspondientes que lo implementan:

![](contentpage-images/contentpage-classes.png "Relación entre la clase ContentPage e implementar controles nativos")

El proceso de representación puede aprovecharse para implementar las personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Xamarin.Forms_Page) una página de Xamarin.Forms.
1. [Consumir](#Consuming_the_Xamarin.Forms_Page) la página de Xamarin.Forms.
1. [Crear](#Creating_the_Page_Renderer_on_each_Platform) el representador personalizado para la página en cada plataforma.

Cada elemento ahora se explicará a su vez, para implementar un `CameraPage` que proporciona una fuente de la cámara en vivo y la capacidad para capturar una foto.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Creación de la página de Xamarin.Forms

Un sin modificaciones [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) pueden agregarse al proyecto de Xamarin.Forms compartido, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

De forma similar, el archivo de código subyacente para el [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) también debe permanecer sin modificaciones, como se muestra en el ejemplo de código siguiente:

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

El ejemplo de código siguiente muestra cómo se puede crear la página de C#:

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Una instancia de la `CameraPage` se usará para mostrar la fuente en cada plataforma de la cámara en directo. Personalización del control se llevará a cabo en el representador personalizado, por lo que no se requiere ninguna implementación adicional en el `CameraPage` clase.

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Consumo de la página de Xamarin.Forms

Vacío `CameraPage` deben mostrarse por la aplicación de Xamarin.Forms. Esto se produce cuando un botón en el `MainPage` instancia se pulsa, que a su vez se ejecuta el `OnTakePhotoButtonClicked` método, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Este código simplemente se desplaza a la `CameraPage`, en los representadores personalizados va a personalizar el aspecto de la página en cada plataforma.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Crear al representador de página en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `PageRenderer` clase.
1. Invalidar el `OnElementChanged` método que representa la lógica de página y de escritura nativa para personalizar la página. El `OnElementChanged` método se llama cuando se crea el control correspondiente de Xamarin.Forms.
1. Agregar un `ExportRenderer` a la clase de representador de página para especificar que se utilizará para representar la página de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Es opcional proporcionar a un representador de página en cada proyecto de la plataforma. Si no está registrado un representador de página, se usará el representador predeterminado de la página.

El siguiente diagrama ilustra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con la relación entre ellas:

![](contentpage-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado CameraPage")

El `CameraPage` instancia se representa por específicos de la plataforma `CameraPageRenderer` clases que derivan de la `PageRenderer` clase para esa plataforma. Esto da como resultado de cada `CameraPage` de instancia que se representa con una fuente de la cámara en directo, como se muestra en las capturas de pantalla siguiente:

![](contentpage-images/screenshots.png "CameraPage en cada plataforma")

El `PageRenderer` clase expone la `OnElementChanged` método, que se llama cuando se crea la página de Xamarin.Forms para representar el control nativo correspondiente. Este método toma un `ElementChangedEventArgs` parámetro que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento de Xamarin.Forms que el representador *era* adjunto y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo la `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `CameraPage` instancia.

Una versión reemplazada de la `OnElementChanged` método en el `CameraPageRenderer` clase es el lugar para realizar la personalización de la página nativo. Se puede obtener una referencia a la instancia de la página de Xamarin.Forms que se representa mediante el `Element` propiedad.

Cada clase de presentador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo de la página de Xamarin.Forms que se procesa y el nombre de tipo del representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes describen la implementación de la `CameraPageRenderer` representador personalizado para cada plataforma.

### <a name="creating-the-page-renderer-on-ios"></a>Crear al representador de página en iOS

El ejemplo de código siguiente muestra a la representación de página para la plataforma de iOS:

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
                System.Diagnostics.Debug.WriteLine (@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La llamada a la clase base `OnElementChanged` método crea una instancia de un iOS `UIViewController` control. Solo se procesa la secuencia en directo de cámara siempre que el representador ya no está unido a un elemento existente de Xamarin.Forms, y siempre que exista una instancia de la página que se representa mediante el representador personalizado.

A continuación, personalizar la página mediante una serie de métodos que usan el `AVCapture` API para proporcionar la secuencia en directo desde la cámara y la capacidad para capturar una foto.

### <a name="creating-the-page-renderer-on-android"></a>Crear al representador de página en Android

El ejemplo de código siguiente muestra a la representación de página para la plataforma Android:

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
                System.Diagnostics.Debug.WriteLine(@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La llamada a la clase base `OnElementChanged` método crea una instancia de un Android `ViewGroup` control, que es un grupo de vistas. Solo se procesa la secuencia en directo de cámara siempre que el representador ya no está unido a un elemento existente de Xamarin.Forms, y siempre que exista una instancia de la página que se representa mediante el representador personalizado.

La página, a continuación, se personaliza mediante la invocación de una serie de métodos que usan el `Camera` API para proporcionar la secuencia en directo desde la cámara y la capacidad para capturar una foto, antes el `AddView` se invoca el método para agregar la cámara en vivo transmitir la interfaz de usuario para el `ViewGroup`. Tenga en cuenta que en Android también es necesario reemplazar el `OnLayout` método para realizar operaciones de medida y el diseño en la vista. Para obtener más información, consulte el [ejemplo de representador ContentPage](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/).

### <a name="creating-the-page-renderer-on-uwp"></a>Creando al representador de página en UWP

El ejemplo de código siguiente muestra a la representación de página para UWP:

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
                SetupBasedOnStateAsync();

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

La llamada a la clase base `OnElementChanged` crea una instancia de método un `FrameworkElement` control, en el que se representa la página. Solo se procesa la secuencia en directo de cámara siempre que el representador ya no está unido a un elemento existente de Xamarin.Forms, y siempre que exista una instancia de la página que se representa mediante el representador personalizado. A continuación, personalizar la página mediante la invocación de una serie de métodos que usan el `MediaCapture` API para proporcionar la secuencia en directo desde la cámara y la capacidad para capturar una foto antes de la página personalizada se agrega a la `Children` colección para su presentación.

Al implementar un representador personalizado que derive de `PageRenderer` en UWP, la `ArrangeOverride` método también debe implementarse para organizar los controles de página, ya que el representador de base no sabe qué hacer con ellos. En caso contrario, da como resultado una página en blanco. Por lo tanto, en este ejemplo el `ArrangeOverride` llamadas al método el `Arrange` método en el `Page` instancia.

> [!NOTE]
> Es importante detener y eliminar los objetos que proporcionan acceso a la cámara en una aplicación de UWP. Si no lo hace puede interferir con otras aplicaciones que intentan obtener acceso a la cámara del dispositivo. Para obtener más información, consulte [mostrar la vista previa de cámara](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Resumen

Este artículo demuestra cómo crear un representador personalizado para el [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) página, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma. Un `ContentPage` es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla.


## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererContentPage (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
