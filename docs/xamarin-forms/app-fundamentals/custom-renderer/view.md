---
title: Implementar una vista
description: En este artículo se explica cómo crear a un representador personalizado para un control personalizado de Xamarin.Forms que se usa para mostrar una secuencia de vídeo de vista previa de la cámara del dispositivo.
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: eb0bc199bfd31ac631ca9d131796b606960d76aa
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240491"
---
# <a name="implementing-a-view"></a>Implementar una vista

_Xamarin.Forms controles de la interfaz de usuario personalizado deben derivar de la clase de vista, que se utiliza para colocar controles en la pantalla y diseños. Este artículo demuestra cómo crear a un representador personalizado para un control personalizado de Xamarin.Forms que se usa para mostrar una secuencia de vídeo de vista previa de la cámara del dispositivo._

Cada vista Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Cuando un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) se representa por una aplicación de Xamarin.Forms en iOS, el `ViewRenderer` se crea la instancia de clase, que a su vez crea una instancia nativo `UIView` control. En la plataforma Android, el `ViewRenderer` clase crea instancias nativo `View` control. En la plataforma Universal de Windows (UWP), el `ViewRenderer` clase crea instancias nativo `FrameworkElement` control. Para obtener más información acerca de las clases de control nativo que se asignan a los controles de Xamarin.Forms y representador, consulte [clases del representador Base y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

En el diagrama siguiente ilustra la relación entre el [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) y los controles nativos correspondientes que lo implementan:

![](view-images/view-classes.png "Relación entre la clase de vista y sus clases nativas de implementación")

El proceso de representación se puede utilizar para implementar las personalizaciones específicas de la plataforma mediante la creación de un representador personalizado para un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) en cada plataforma. El proceso para hacer esto es como sigue:

1. [Crear](#Creating_the_Custom_Control) un control personalizado de Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Control) el control personalizado de Xamarin.Forms.
1. [Crear](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para el control en cada plataforma.

Cada elemento ahora se tratarán a su vez, para implementar un `CameraPreview` representador que muestra una secuencia de vídeo de vista previa de la cámara del dispositivo. Puntee en la secuencia de vídeo se detenerlo e iniciarlo.

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>Crear el Control personalizado

Un control personalizado se puede crear subclases el [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) de la clase, como se muestra en el ejemplo de código siguiente:

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

El `CameraPreview` control personalizado se crea en el proyecto de biblioteca (PCL) de clases portables y define la API para el control. El control personalizado expone un `Camera` propiedad que se usa para controlar si se debe mostrar la secuencia de vídeo desde la parte delantera o trasera cámara del dispositivo. Si no se especifica un valor para el `Camera` propiedad cuando se crea el control, el valor predeterminado es especificar la cámara trasera.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumir el Control personalizado

El `CameraPreview` control personalizado puede hacer referencia en XAML en el proyecto PCL declarar un espacio de nombres para su ubicación y el uso del prefijo de espacio de nombres en el elemento de control personalizado. El siguiente ejemplo de código muestra cómo el `CameraPreview` control personalizado se pueden usar en una página XAML:

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

El siguiente ejemplo de código muestra cómo el `CameraPreview` control personalizado se pueden usar en una página de C#:

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

Una instancia de la `CameraPreview` control personalizado se usará para mostrar la secuencia de vídeo de vista previa de la cámara del dispositivo. Además, opcionalmente, especifica un valor para el `Camera` propiedad, personalización del control se llevará a cabo en el representador personalizado.

Ahora puede agregarse un representador personalizado a cada proyecto de aplicación para crear controles de vista previa de cámara específica de la plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Crear al representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizado es como sigue:

1. Crear una subclase de la `ViewRenderer<T1,T2>` clase que representa el control personalizado. El primer argumento de tipo debe ser el control personalizado que el representador de es, en este caso `CameraPreview`. El segundo argumento de tipo debe ser el control nativo que va a implementar el control personalizado.
1. Invalidar el `OnElementChanged` método que representa la lógica personalizada de control y de escritura para personalizarlo. Se llama a este método cuando se crea el control de Xamarin.Forms correspondiente.
1. Agregar un `ExportRenderer` atributo a la clase de representador personalizado para especificar que se utilizará para representar el control personalizado de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Para la mayoría de los elementos de Xamarin.Forms, es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no se ha registrado un representador personalizado, se usará el representador predeterminado para la clase base del control. Sin embargo, los representadores personalizados necesarios en cada proyecto de la plataforma al representar un [vista](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) elemento.

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](view-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado CameraPreview")

El `CameraPreview` control personalizado se representa por clases del representador de específico de la plataforma, que se derivan de la `ViewRenderer` clase para cada plataforma. Esto da como resultado de cada `CameraPreview` control personalizado que se va a representar con controles específicos de la plataforma, como se muestra en las capturas de pantalla siguiente:

![](view-images/screenshots.png "CameraPreview en cada plataforma")

El `ViewRenderer` clase expone el `OnElementChanged` método, que se llama cuando se crea el control personalizado de Xamarin.Forms para representar el control nativo correspondiente. Este método toma una `ElementChangedEventArgs` parámetro que contiene `OldElement` y `NewElement` propiedades. Estas propiedades representan el elemento Xamarin.Forms que el representador *era* adjunta y el elemento de Xamarin.Forms que el representador *es* adjunta, respectivamente. En la aplicación de ejemplo, el `OldElement` propiedad será `null` y `NewElement` propiedad contendrá una referencia a la `CameraPreview` instancia.

Una versión reemplazada de la `OnElementChanged` método en cada clase de representador específico de la plataforma, es el lugar para realizar la personalización y creación de instancias de control nativo. El `SetNativeControl` método se debe utilizar para crear una instancia del control nativo, y este método también asignará la referencia de control a la `Control` propiedad. Además, una referencia al control Xamarin.Forms que se esté representando puede obtenerse a través de la `Element` propiedad.

En algunas circunstancias, el `OnElementChanged` método puede llamarse varias veces. Por lo tanto, para evitar pérdidas de memoria, se debe tener cuidado al crear instancias de un nuevo control nativo. El enfoque que usar al crear instancias de un nuevo control nativo en un presentador personalizado se muestra en el ejemplo de código siguiente:

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

Solo se debe crear una instancia de un nuevo control nativo una vez, cuando la propiedad `Control` es `null`. Solo se debe configurar el control y suscribir los controladores de eventos cuando se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. Del mismo modo, solo deben cancelar su suscripción cuando se cambia el elemento que está asociado el representador para los controladores de eventos que se han suscrito. Este enfoque le ayudará a crear a un representador personalizado de rendimiento que no sufren pérdidas de memoria.

Cada clase de representador personalizado se decora con un `ExportRenderer` atributo que se registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control personalizado Xamarin.Forms que se procesa y el nombre del tipo de representador personalizado. El `assembly` prefijo para el atributo especifica que el atributo se aplica a todo el ensamblado.

Las secciones siguientes tratan la implementación de cada clase de representador personalizado específico de la plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Crear al representador personalizado de iOS

En el ejemplo de código siguiente se muestra al representador personalizado para la plataforma de iOS:

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

Siempre que el `Control` propiedad es `null`, el `SetNativeControl` método se llama para crear instancias de un nuevo `UICameraPreview` control y asignar una referencia a él en el `Control` propiedad. El `UICameraPreview` control es un control personalizado específico de la plataforma que usa el `AVCapture` API para proporcionar la secuencia de vista previa de la cámara. Expone un `Tapped` eventos que controlan el `OnCameraPreviewTapped` método para detener e iniciar la vista previa de vídeo al que se derivan. El `Tapped` evento se ha suscrito al representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms y cancelado la suscripción a solo cuando el elemento el representador se adjunta a los cambios.

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

Siempre que el `Control` propiedad es `null`, el `SetNativeControl` método se llama para crear instancias de un nuevo `CameraPreview` controlar y asignar una referencia a él en el `Control` propiedad. El `CameraPreview` control es un control personalizado específico de la plataforma que usa el `Camera` API para proporcionar el flujo de vista previa de la cámara. El `CameraPreview` control, a continuación, se configura, siempre que el representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms. Esta configuración implica la creación de un nuevo nativo `Camera` de objetos para tener acceso a una cámara de determinado hardware y registrar un controlador de eventos para procesar el `Click` eventos. A su vez este controlador se detendrá e iniciará la vista previa de vídeo al que se derivan. El `Click` evento está cancelado la suscripción a si se asocia el elemento de Xamarin.Forms el representador de cambios.

### <a name="creating-the-custom-renderer-on-uwp"></a>Crear al representador personalizado en UWP

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

Siempre que el `Control` propiedad es `null`, un nuevo `CaptureElement` se crea una instancia y el `SetupCamera` llama el método, que usa el `MediaCapture` API para proporcionar el flujo de vista previa de la cámara. El `SetNativeControl` , a continuación, se llama el método para asignar una referencia a la `CaptureElement` de instancia para el `Control` propiedad. El `CaptureElement` control expone un `Tapped` eventos que controlan el `OnCameraPreviewTapped` método para detener e iniciar la vista previa de vídeo al que se derivan. El `Tapped` evento se ha suscrito al representador personalizado se adjunta a un nuevo elemento de Xamarin.Forms y cancelado la suscripción a solo cuando el elemento el representador se adjunta a los cambios.

> [!NOTE]
> Es importante detener y eliminar los objetos que proporcionan acceso a la cámara en una aplicación de UWP. Si no lo hace puede interferir con otras aplicaciones que intentan tener acceso a la cámara del dispositivo. Para obtener más información, consulte [mostrar la vista previa de la cámara](/windows/uwp/audio-video-camera/simple-camera-preview-access/).

## <a name="summary"></a>Resumen

Este artículo demuestra cómo crear a un representador personalizado para un control personalizado de Xamarin.Forms que se usa para mostrar una secuencia de vídeo de vista previa de la cámara del dispositivo. Xamarin.Forms controles de la interfaz de usuario personalizado deben derivarse de la [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) (clase), que se utiliza para colocar controles en la pantalla y diseños.


## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)
