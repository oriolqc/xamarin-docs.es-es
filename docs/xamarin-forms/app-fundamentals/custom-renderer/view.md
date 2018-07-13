---
title: Implementación de una vista
description: En este artículo se explica cómo crear a un representador personalizado para un control personalizado de Xamarin.Forms que se usa para mostrar una secuencia de vídeo de vista previa de la cámara del dispositivo.
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 8ee9926eb3b726673711141e7c75a68b607d02d3
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994707"
---
# <a name="implementing-a-view"></a>Implementación de una vista

_Controles de interfaz de usuario personalizado de Xamarin.Forms deben derivar de la clase de vista, que se utiliza para colocar los diseños y controles en la pantalla. En este artículo se muestra cómo crear a un representador personalizado para un control personalizado de Xamarin.Forms que se usa para mostrar una secuencia de vídeo de vista previa de la cámara del dispositivo._

Cada vista Xamarin.Forms tiene un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo. Cuando un [ `View` ](xref:Xamarin.Forms.View) se representa mediante una aplicación de Xamarin.Forms en iOS, el `ViewRenderer` se crea la instancia de clase, que a su vez crea una instancia nativa `UIView` control. En la plataforma Android, el `ViewRenderer` nativo crea una instancia de clase `View` control. En la plataforma Universal de Windows (UWP), el `ViewRenderer` nativo crea una instancia de clase `FrameworkElement` control. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y el representador, consulte [clases Base del representador y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama ilustra la relación entre el [ `View` ](xref:Xamarin.Forms.View) y los controles nativos correspondientes que lo implementan:

![](view-images/view-classes.png "Relación entre la clase de vista y sus clases nativas de implementación")

El proceso de representación puede utilizarse para implementar las personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para un [ `View` ](xref:Xamarin.Forms.View) en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Control) un control personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Control) el control personalizado de Xamarin.Forms.
1. [Crear](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para el control en cada plataforma.

Cada elemento ahora se explicará a su vez, para implementar un `CameraPreview` representador que muestra una secuencia de vídeo de vista previa de la cámara del dispositivo. Pulsar en la secuencia de vídeo se detenerlo e iniciarlo.

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>Crear el Control personalizado

Se puede crear un control personalizado mediante la creación de subclases del [ `View` ](xref:Xamarin.Forms.View) clase, como se muestra en el ejemplo de código siguiente:

```csharp
public class CameraPreview : View
{
  public static readonly BindableProperty CameraProperty = BindableProperty.Create (
    propertyName: "Camera",
    returnType: typeof(CameraOptions),
    declaringType: typeof(CameraPreview),
    defaultValue: CameraOptions.Rear);

  public CameraOptions Camera {
    get { return (CameraOptions)GetValue (CameraProperty); }
    set { SetValue (CameraProperty, value); }
  }
}
```

El `CameraPreview` control personalizado se crea en el proyecto de clases portables (PCL) de la biblioteca y define la API para el control. El control personalizado expone un `Camera` propiedad que se usa para controlar si se debe mostrar la secuencia de vídeo desde la parte delantera o trasera cámara del dispositivo. Si no se especifica un valor para el `Camera` propiedad cuando se crea el control, el valor predeterminado es especificar la cámara trasera.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumir el Control personalizado

El `CameraPreview` control personalizado puede hacer referencia en XAML en el proyecto PCL declarar un espacio de nombres para su ubicación y utilizando el prefijo de espacio de nombres en el elemento de control personalizado. El siguiente ejemplo de código muestra cómo el `CameraPreview` control personalizado puede utilizarse en una página XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             ...>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Camera Preview:" />
            <local:CameraPreview Camera="Rear"
              HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El `local` prefijo de espacio de nombres puede tener cualquier nombre. Sin embargo, el `clr-namespace` y `assembly` valores deben coincidir con los detalles del control personalizado. Una vez que se declara el espacio de nombres, el prefijo se utiliza para hacer referencia al control personalizado.

El siguiente ejemplo de código muestra cómo el `CameraPreview` control personalizado puede utilizarse en una página de C#:

```csharp
public class MainPageCS : ContentPage
{
  public MainPageCS ()
  {
    ...
    Content = new StackLayout {
      Children = {
        new Label { Text = "Camera Preview:" },
        new CameraPreview {
          Camera = CameraOptions.Rear,
          HorizontalOptions = LayoutOptions.FillAndExpand,
          VerticalOptions = LayoutOptions.FillAndExpand
        }
      }
    };
  }
}
```

Una instancia de la `CameraPreview` control personalizado que se usará para mostrar la secuencia de vídeo de vista previa de la cámara del dispositivo. Aparte de especificar opcionalmente un valor para el `Camera` propiedad, la personalización del control se llevará a cabo en el representador personalizado.

Ahora se puede agregar a un representador personalizado a cada proyecto de aplicación para crear controles de vista previa de cámara específicos de la plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Crear al representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `ViewRenderer<T1,T2>` clase que representa el control personalizado. El primer argumento de tipo debe ser el control personalizado, el representador de es, en este caso `CameraPreview`. El segundo argumento de tipo debe ser el control nativo que implementará el control personalizado.
1. Invalidar el `OnElementChanged` método que representa la lógica personalizada de control y escribir para personalizarla. Este método se llama cuando se crea el control correspondiente de Xamarin.Forms.
1. Agregar un `ExportRenderer` a la clase de representador personalizado para especificar que se utilizará para representar el control personalizado de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Para la mayoría de los elementos de Xamarin.Forms, es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no está registrado un representador personalizado, se usará el representador predeterminado de la clase base del control. Sin embargo, los representadores personalizados son necesarios en cada proyecto de la plataforma al representar un [vista](xref:Xamarin.Forms.View) elemento.

El siguiente diagrama ilustra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](view-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado CameraPreview")

El `CameraPreview` control personalizado se representa mediante las clases de representador específica de la plataforma, que se derivan de la `ViewRenderer` clase para cada plataforma. Esto da como resultado de cada `CameraPreview` control personalizado que se representa con controles específicos de la plataforma, como se muestra en las capturas de pantalla siguiente:

![](view-images/screenshots.png "CameraPreview en cada plataforma")

El `ViewRenderer` clase expone la `OnElementChanged` método, que se llama cuando se crea el control personalizado de Xamarin.Forms para representar el control nativo correspondiente. Este método toma un `ElementChangedEventArgs` parámetro que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento de Xamarin.Forms que el representador *era* adjunto y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo, el `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `CameraPreview` instancia.

Una versión reemplazada de la `OnElementChanged` método en cada clase de presentador específica de la plataforma, es el lugar para realizar la personalización y creación de instancias de control nativo. El `SetNativeControl` método se debe utilizar para crear una instancia del control nativo y este método también asignará la referencia de control para el `Control` propiedad. Además, se puede obtener una referencia al control de Xamarin.Forms que se representa mediante el `Element` propiedad.

En algunas circunstancias, el `OnElementChanged` método puede llamarse varias veces. Por lo tanto, para evitar pérdidas de memoria, debe tener cuidado al crear una instancia de un nuevo control nativo. El enfoque que usar al crear instancias de un nuevo control nativo en un presentador personalizado se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Solo se debe crear una instancia de un nuevo control nativo una vez, cuando la propiedad `Control` es `null`. Solo se debe configurar el control y suscribir los controladores de eventos cuando se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. De forma similar, los controladores de eventos que se han suscrito a sólo debe cancelar la suscripción cuando se cambia el elemento que está conectado el representador. Adoptar este enfoque le ayudará a crear a un representador personalizado de alto rendimiento que no sufra pérdidas de memoria.

Cada clase de presentador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control personalizado de Xamarin.Forms que se procesa y el nombre de tipo del representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes describen la implementación de cada clase de representador personalizado específico de la plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado en iOS

El ejemplo de código siguiente muestra al representador personalizado para la plataforma de iOS:

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, UICameraPreview>
    {
        UICameraPreview uiCameraPreview;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                SetNativeControl (uiCameraPreview);
            }
            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                // Subscribe
                uiCameraPreview.Tapped += OnCameraPreviewTapped;
            }
        }

        void OnCameraPreviewTapped (object sender, EventArgs e)
        {
            if (uiCameraPreview.IsPreviewing) {
                uiCameraPreview.CaptureSession.StopRunning ();
                uiCameraPreview.IsPreviewing = false;
            } else {
                uiCameraPreview.CaptureSession.StartRunning ();
                uiCameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

Siempre que el `Control` propiedad es `null`, el `SetNativeControl` método se llama para crear una nueva instancia `UICameraPreview` control y asignar una referencia a ella para el `Control` propiedad. El `UICameraPreview` control es un control personalizado específico de la plataforma que utiliza el `AVCapture` API para proporcionar la secuencia de vista previa de la cámara. Expone un `Tapped` eventos que controlan la `OnCameraPreviewTapped` método para detener e iniciar la vista previa de vídeo al que se pulsa. El `Tapped` suscripción a un evento cuando el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms y cancelado la suscripción a solo cuando el representador de elemento se adjunta a los cambios.

### <a name="creating-the-custom-renderer-on-android"></a>Crear al representador personalizado en Android

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : ViewRenderer<CustomRenderer.CameraPreview, CustomRenderer.Droid.CameraPreview>
    {
        CameraPreview cameraPreview;

        public CameraPreviewRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<CustomRenderer.CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                cameraPreview = new CameraPreview(Context);
                SetNativeControl(cameraPreview);
            }

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
                Control.Preview = Camera.Open((int)e.NewElement.Camera);

                // Subscribe
                cameraPreview.Click += OnCameraPreviewClicked;
            }
        }

        void OnCameraPreviewClicked(object sender, EventArgs e)
        {
            if (cameraPreview.IsPreviewing)
            {
                cameraPreview.Preview.StopPreview();
                cameraPreview.IsPreviewing = false;
            }
            else
            {
                cameraPreview.Preview.StartPreview();
                cameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

Siempre que el `Control` propiedad es `null`, `SetNativeControl` método se llama para crear una nueva instancia `CameraPreview` controlar y asignar una referencia a ella para el `Control` propiedad. El `CameraPreview` control es un control personalizado específico de la plataforma que utiliza el `Camera` API para proporcionar la secuencia de vista previa de la cámara. El `CameraPreview` control, a continuación, se configura, siempre que se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. Esta configuración implica la creación de un nuevo nativo `Camera` de objetos para tener acceso a una cámara de hardware concreto y registrar un controlador de eventos para procesar el `Click` eventos. A su vez detendrá e iniciará la vista previa de vídeo al que se pulsa este controlador. El `Click` evento está cancelado la suscripción a si el elemento de Xamarin.Forms el representador se adjunta a los cambios.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creando al representador personalizado en UWP

En el ejemplo de código siguiente se muestra al representador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        ...
        CaptureElement _captureElement;
        bool _isPreviewing;

        protected override void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                ...
                _captureElement = new CaptureElement();
                _captureElement.Stretch = Stretch.UniformToFill;

                SetupCamera();
                SetNativeControl(_captureElement);
            }
            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped(object sender, TappedRoutedEventArgs e)
        {
            if (_isPreviewing)
            {
                await StopPreviewAsync();
            }
            else
            {
                await StartPreviewAsync();
            }
        }
        ...
    }
}
```

Siempre que el `Control` propiedad es `null`, un nuevo `CaptureElement` se crea una instancia y el `SetupCamera` llama el método, que usa el `MediaCapture` API para proporcionar la secuencia de vista previa de la cámara. El `SetNativeControl` , a continuación, se llama el método para asignar una referencia a la `CaptureElement` de instancia para el `Control` propiedad. El `CaptureElement` control expone un `Tapped` eventos que controlan la `OnCameraPreviewTapped` método para detener e iniciar la vista previa de vídeo al que se pulsa. El `Tapped` suscripción a un evento cuando el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms y cancelado la suscripción a solo cuando el representador de elemento se adjunta a los cambios.

> [!NOTE]
> Es importante detener y eliminar los objetos que proporcionan acceso a la cámara en una aplicación de UWP. Si no lo hace puede interferir con otras aplicaciones que intentan obtener acceso a la cámara del dispositivo. Para obtener más información, consulte [mostrar la vista previa de cámara](/windows/uwp/audio-video-camera/simple-camera-preview-access/).

## <a name="summary"></a>Resumen

Este artículo haya demostrado cómo crear a un representador personalizado para un control personalizado de Xamarin.Forms que se usa para mostrar una secuencia de vídeo de vista previa de la cámara del dispositivo. Controles de interfaz de usuario personalizado de Xamarin.Forms deben derivar de la [ `View` ](xref:Xamarin.Forms.View) (clase), que se utiliza para colocar los diseños y controles en la pantalla.


## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)
