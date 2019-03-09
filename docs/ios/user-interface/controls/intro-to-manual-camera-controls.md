---
title: Controles manuales de la cámara en Xamarin.iOS
description: Este documento describe cómo se puede usar el marco de trabajo de AVFoundation de iOS con Xamarin.iOS para habilitar los controles manuales de la cámara. Controles manuales de la cámara, permitir que a un usuario foco de control, el balance de blanco y ajustes de exposición.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c61b3fee9009afb86ccd3fd0e16d7812a8e90feb
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672799"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Controles manuales de la cámara en Xamarin.iOS

Los controles de cámara Manual, proporcionados por el `AVFoundation Framework` en iOS 8, permitir que una aplicación móvil tomar el control total sobre la cámara de un dispositivo iOS. Este nivel específico de control puede utilizarse para crear aplicaciones de la cámara de nivel profesional y proporcionar las composiciones de intérprete al ajustar los parámetros de la cámara al mismo tiempo que un vídeo o imagen fija.

Estos controles también pueden ser útiles al desarrollar aplicaciones científicas o industriales, donde los resultados de menos orientados a la exactitud o la belleza de la imagen y más orientados a resaltar algunas características o los elementos de la imagen que se va a realizar.

## <a name="avfoundation-capture-objects"></a>Objetos de captura AVFoundation

Si tarda de vídeo o imágenes con la cámara en un dispositivo iOS, el proceso que se utiliza para capturar esas imágenes es prácticamente el mismo. Es el caso de las aplicaciones que usan los controles de cámara automatizado de forma predeterminada, o aquellas que aprovechan las ventajas de los nuevos controles de cámara Manual:

 [![](intro-to-manual-camera-controls-images/image1.png "Información general de los objetos de captura AVFoundation")](intro-to-manual-camera-controls-images/image1.png#lightbox)

Entrada procede de un `AVCaptureDeviceInput` en un `AVCaptureSession` por medio de un `AVCaptureConnection`. El resultado es una salida como una imagen fija o como una secuencia de vídeo. Todo el proceso se controla mediante un `AVCaptureDevice`.

## <a name="manual-controls-provided"></a>Controles manuales proporcionados

Mediante las nuevas API que proporciona iOS 8, la aplicación puede tomar el control de las siguientes características de cámara:

-  **Enfoque manual** – permitiendo que el usuario final puede tomar el control del enfoque directamente, una aplicación puede proporcionar más control sobre la imagen tomada.
-  **Exposición manual** – proporcionando controlar manualmente la exposición, una aplicación puede proporcionar más libertad a los usuarios y permitirles lograr una apariencia ESTILIZADA.
-  **Balance de blanco manual** : Balance de blanco se usa para ajustar el color de una imagen, a menudo para que parezca realista. Distintas fuentes de luz tienen las temperaturas de color diferente y la configuración de cámara utilizada para capturar una imagen se ajusta para compensar estas diferencias. De nuevo, por lo que permite el control de usuario sobre el balance de blanco, los usuarios pueden realizar los ajustes que no se puede realizar automáticamente.


iOS 8 proporciona extensiones y mejoras de iOS existente de API para proporcionar esta un mayor control sobre la imagen de proceso de captura.

## <a name="bracketed-capture"></a>Captura entre corchetes

La captura entre corchetes se basa en la configuración de los controles de cámara Manual presentada anteriormente y permite que la aplicación capturar un momento dado, en una variedad de formas diferentes.

En pocas palabras, paréntesis de captura es una ráfaga de imágenes fijas tomadas con una variedad de opciones de imagen en imagen.

## <a name="requirements"></a>Requisitos

Los siguientes son necesarios para completar los pasos que aparecen en este artículo:

-  **Xcode 7 + y iOS 8 o posterior** : Xcode 7 y iOS 8 o las API más recientes de Apple deben estar instalado y configurado en el equipo del desarrollador.
-  **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el dispositivo del usuario.
-  **iOS 8 dispositivo** : un dispositivo iOS ejecutando la última versión de iOS 8. No se pueden probar las características de la cámara en el simulador de iOS.


## <a name="general-av-capture-setup"></a>Programa de instalación de la captura de AV general

Grabación de vídeo en un dispositivo iOS, hay código de configuración general que siempre es necesario. Esta sección explica la configuración mínima necesaria para grabar vídeo desde la cámara del dispositivo iOS y mostrar ese vídeo en tiempo real en un `UIImageView`.

### <a name="output-sample-buffer-delegate"></a>Delegado de búfer de ejemplo de salida

Una de las primeras cosas que es necesario será un delegado para supervisar el búfer de salida de ejemplo y mostrar una imagen de búfer para un `UIImageView` en el interfaz de usuario de la aplicación.

La siguiente rutina supervisará el búfer de ejemplo y actualizar la interfaz de usuario:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

Con esta rutina en su lugar, el `AppDelegate` puede modificarse para abrir una sesión de captura de AV para grabar un vídeo en vivo.

### <a name="creating-an-av-capture-session"></a>Crear una sesión de captura de AV

La sesión de captura de AV se utiliza para controlar la grabación de vídeo en directo desde la cámara del dispositivo iOS y es necesario para obtener el vídeo en una aplicación de iOS. Desde el ejemplo `ManualCameraControl` aplicación de ejemplo usa la sesión de captura en distintos lugares, se configurará en la `AppDelegate` y estarán disponibles para toda la aplicación.

Siga este procedimiento para modificar la aplicación `AppDelegate` y agregue el código necesario:


1. Haga doble clic en el `AppDelegate.cs` archivo en el Explorador de soluciones para abrirlo y editarlo.
1. Agregue las siguientes instrucciones using a la parte superior del archivo:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. Agregue las siguientes variables privadas y las propiedades calculadas a la `AppDelegate` clase:
    
    ```
    #region Private Variables
    private NSError Error;
    #endregion
    
    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```
  
1. Invalidar el método finalizado y cámbielo por:
    
    ```
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;
    
        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }
    
        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }
    
        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);
    
        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();
    
        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }
    
        // Attach input to session
        Session.AddInput (Input);
    
        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;
    
        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);
    
        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```  
  
1. Guarde los cambios en el archivo.


Con este código en su lugar, se puede implementar fácilmente los controles de cámara Manual para las pruebas y experimentación.

## <a name="manual-focus"></a>Enfoque manual

Por lo que permite aprovechar los controles del enfoque directamente al usuario final, una aplicación puede proporcionar artístico más control sobre la imagen tomada.

Por ejemplo, un fotógrafo profesional puede suavizar el foco de una imagen para lograr un [Bokeh efecto](https://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Un efecto Bokeh")](intro-to-manual-camera-controls-images/image2.png#lightbox)

O bien, crear un [efecto de incorporación de cambios de foco](http://www.mediacollege.com/video/camera/focus/pull.html), tales como:

[![](intro-to-manual-camera-controls-images/image3.png "El efecto de incorporación de cambios de foco")](intro-to-manual-camera-controls-images/image3.png#lightbox)

Para científicos o un sistema de escritura de aplicaciones médicas, la aplicación es posible que desee mover mediante programación la lente de los experimentos. En cualquier caso, la nueva API permite que el usuario final o la aplicación para aprovechar control sobre el foco en el momento de la imagen se toma.

### <a name="how-focus-works"></a>Cómo funciona el foco

Antes de tratar los detalles de control de la selección en una aplicación de IOS 8. Echemos un vistazo a cómo funciona el foco en un dispositivo iOS:

[![](intro-to-manual-camera-controls-images/image4.png "Cómo funciona el foco en un dispositivo iOS")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Luz entra en el objetivo de la cámara en el dispositivo iOS y se centra en un sensor de imagen. La distancia de la lente del sensor controla donde es el punto Focal (el área de la imagen donde aparecerá la más apuntada), en relación con el sensor. Cuanto más lejos de la lente es desde el sensor, los objetos de distancia parecen más apuntados y cuanto más se acerque, cerca de los objetos parecen más apuntada.

En un dispositivo iOS, la lente se mueve más cercanos a, o más lejos, el sensor imanes y springs. Como resultado, la posición exacta de la lente es imposible, ya que variará de un dispositivo a otro y puede verse afectado por los parámetros como la orientación del dispositivo o la antigüedad del dispositivo y spring.

### <a name="important-focus-terms"></a>Términos de foco importante

Cuando se trabaja con el foco, hay algunos términos que el desarrollador debe estar familiarizado con:

-  **Profundidad de campo** : la distancia entre los objetos más cercanos y más en el foco. 
-  **Macro** -se trata la cerca del final del espectro de foco y es la distancia más cercana a la que se puede Centrar la lente.
-  **Infinito** : éste es el otro extremo del espectro de foco y es la distancia más alejada en el que se puede Centrar la lente.
-  **Distancia Hyperfocal** : este es el punto en el espectro de foco donde el objeto en el marco más alejado es simplemente en el otro extremo de foco. En otras palabras, esta es la posición focal que maximiza la profundidad de campo. 
-  **Posición de la lente** : eso es lo que controla todo lo anterior otros términos. Esta es la distancia de la lente del sensor y, por tanto, el controlador de foco.


Con estos términos y los conocimientos en mente, los nuevos controles del enfoque Manual puede implementarse correctamente en una aplicación de iOS 8.

### <a name="existing-focus-controls"></a>Controles existentes de foco

iOS 7 y versiones anteriores, proporcionan controles de foco existentes a través de `FocusMode`propiedad como:

-   `AVCaptureFocusModeLocked` : El foco se bloquea en un punto de enfoque único.
-   `AVCaptureFocusModeAutoFocus` : La cámara barre la lente a través de todos los puntos de focal hasta que encuentra un enfoque nítido y, a continuación, permanece allí.
-   `AVCaptureFocusModeContinuousAutoFocus` : La cámara refocuses siempre que detecta una condición de desenfocada.


Los controles existentes también proporcionan un punto configurable de interés a través de la`FocusPointOfInterest` propiedad, por lo que el usuario puede pulsar para centrarse en un área determinada. La aplicación puede hacer un seguimiento del movimiento de la lente mediante la supervisión de la `IsAdjustingFocus` propiedad.

Además, se proporcionó la restricción de rango mediante el `AutoFocusRangeRestriction` propiedad como:

-   `AVCaptureAutoFocusRangeRestrictionNear` : Restringe el autofocus a profundidades cercanos. Resulta útil en situaciones como la exploración de un código QR o código de barras.
-   `AVCaptureAutoFocusRangeRestrictionFar` : Restringe el autofocus a profundidades distantes. Resulta útil en situaciones en que los objetos que se sabe que son irrelevantes en el campo de visión (por ejemplo, un marco de ventana).


Por último el `SmoothAutoFocus` propiedad que se ralentiza el algoritmo de enfoque automático y los pasos en incrementos más pequeños para evitar mover los artefactos de grabación de vídeo.

### <a name="new-focus-controls-in-ios-8"></a>Nuevos controles de foco en iOS 8

Además de las características ya proporcionadas por iOS 7 y versiones posteriores, ahora están disponibles para controlar el foco en iOS 8 las siguientes características:

-  Control manual completa de la posición de la lente al bloquear el foco.
-  Observación de pares clave-valor de la posición de la lente en cualquier modo de enfoque.


Para implementar las características anteriores, el `AVCaptureDevice` clase se ha modificado para incluir solo lectura `LensPosition` propiedad que se utiliza para obtener la posición actual del objetivo de la cámara.

Para tomar el control manual de la posición de la lente, el dispositivo de captura debe estar en el modo de enfoque bloqueado. Ejemplo:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

El `SetFocusModeLocked` método del dispositivo de captura se utiliza para ajustar la posición de la lente de la cámara. Para recibir notificación cuando el cambio surta efecto, se puede proporcionar una rutina de devolución de llamada opcional. Ejemplo:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Tal como se muestra en el código anterior, el dispositivo de captura debe estar bloqueado para la configuración antes de hacer un cambio en la posición de la lente. Los valores de posición de la lente válidos están entre 0.0 y 1.0.

### <a name="manual-focus-example"></a>Ejemplo de enfoque manual

Con el código de configuración General de captura de AV en su lugar, un `UIViewController` se pueden agregar a guión gráfico de la aplicación y configurar del modo siguiente:

[![](intro-to-manual-camera-controls-images/image5.png "Se puede agregar a las aplicaciones de guión gráfico y configurado tal como se muestra a continuación un UIViewController")](intro-to-manual-camera-controls-images/image5.png#lightbox)

La vista contiene los siguientes elementos principales:

-  Un `UIImageView` que mostrará la fuente de vídeo.
-  Un `UISegmentedControl` que cambiará el modo de enfoque de automático a bloqueado.
-  Un `UISlider` que mostrará y actualizar la posición actual de la lente.


Siga este procedimiento para la conexión de seguridad el controlador de vista para el Control de enfoque Manual:


1. Agregue las siguientes instrucciones using:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Agregue las siguientes variables privadas:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```  
  
1. Agregue las siguientes propiedades calculadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Invalidar el `ViewDidLoad` método y agregue el código siguiente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Invalidar el `ViewDidAppear` método y agregue lo siguiente para iniciar la grabación cuando se cargue la vista:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Con la cámara en el modo Auto, el control deslizante se moverá automáticamente como la cámara ajusta el foco:

    [![](intro-to-manual-camera-controls-images/image6.png "El control deslizante se moverá automáticamente como la cámara ajusta el foco en esta aplicación de ejemplo")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. Puntee en el segmento bloqueado y arrastre el control de posición para ajustar la posición de la lente manualmente:

    [![](intro-to-manual-camera-controls-images/image7.png "Ajuste manual de la posición de la lente.")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Detener la aplicación.


El código anterior muestra cómo supervisar la posición de la lente cuando la cámara está en el modo automático o usar un control deslizante para controlar la posición de la lente cuando se encuentra en el modo de bloqueo.

## <a name="manual-exposure"></a>Exposición manual

Exposición hace referencia a la luminosidad de una imagen en relación con el brillo de origen y viene determinada por la cantidad de luz alcanza el sensor, acerca de cómo long y por el nivel de ganancia del sensor (asignación de ISO). Al proporcionar control manual a través de la exposición, una aplicación puede proporcionar más libertad al usuario final y permitirles lograr una apariencia ESTILIZADA.

Con los controles de exposición Manual, el usuario puede tomar una imagen de brillante ser realistas oscuro y moody:

[![](intro-to-manual-camera-controls-images/image8.png "Un ejemplo de una imagen que muestra la exposición de ser realistas brillante a oscuro y moody")](intro-to-manual-camera-controls-images/image8.png#lightbox)

Nuevamente, esto puede hacerse automáticamente con el control mediante programación para las aplicaciones científicas o a través de controles manuales proporcionados por la interfaz de usuario de aplicaciones. En cualquier caso, la nueva iOS 8 API exposición proporcionan un mayor control sobre los ajustes de exposición de la cámara.

### <a name="how-exposure-works"></a>Cómo funciona la exposición

Antes de tratar los detalles de control de la exposición de una aplicación de IOS 8. Echemos un vistazo a cómo funciona la exposición:

[![](intro-to-manual-camera-controls-images/image9.png "Cómo funciona la exposición")](intro-to-manual-camera-controls-images/image9.png#lightbox)

Los tres elementos básicos que se combinan para controlar la exposición son:

-  **Velocidad de obturación** : ésta es la longitud de tiempo que el obturador esté abierto para permitir que la luz en el sensor de cámara. La cadena más corta se permite el momento en que el obturador está abierto, la luz menos y es la imagen más nítida el (menos desenfoque de movimiento). Cuanto más tiempo del obturador esté abierto, la luz más se permite en y movimiento más desenfoque que se produce.
-  **Asignación de ISO** : este es un término que se toma prestado de película fotográfica y hace referencia a la confidencialidad de los productos químicos en la película a la luz. Valores ISO de baja en una película tienen menos grano y reproducción de color más precisa; los valores bajos de ISO en sensores digitales tienen menos ruido del sensor, pero menos brillo. Cuanto mayor sea el valor ISO, más brillante será la imagen, pero con más ruido de sensor. "ISO" de un sensor digital es una medida de [ganancia electrónica](https://en.wikipedia.org/wiki/Gain), no es una característica física. 
-  **Apertura de la lente** : este es el tamaño de la apertura de la lente. En todos los dispositivos iOS se fija la apertura de la lente, por lo que los solo dos valores que se pueden usar para ajustar la exposición son la velocidad de obturación e ISO.


### <a name="how-continuous-auto-exposure-works"></a>Continua cómo funciona la exposición automática

Antes de aprendizaje cómo funciona la exposición manual, es una buena idea entender cómo continua exposición automática funciona en un dispositivo iOS.

[![](intro-to-manual-camera-controls-images/image10.png "Cómo funciona la exposición automática continua en un dispositivo iOS")](intro-to-manual-camera-controls-images/image10.png#lightbox)

En primer lugar es el bloque de exposición automática, que tiene el trabajo de cálculo de exposición ideal y continuamente se introduce las estadísticas de disponibilidad. Usa esta información para calcular la combinación óptima de ISO y velocidad de obturación para obtener la escena también iluminada. Este ciclo se conoce como el bucle de AE.

### <a name="how-locked-exposure-works"></a>Funciona de forma bloqueada exposición

A continuación, vamos a examinar bloqueado cómo funciona la exposición en dispositivos iOS.

[![](intro-to-manual-camera-controls-images/image11.png "Forma bloqueada exposición funciona en dispositivos iOS")](intro-to-manual-camera-controls-images/image11.png#lightbox)

De nuevo, tendrá el bloque de exposición automática que intenta calcular los valores de duración y iOS óptimo. Sin embargo, en este modo el bloque de AE se desconecta el motor de estadísticas de disponibilidad.

### <a name="existing-exposure-controls"></a>Controles existentes de exposición

iOS 7 y versiones posteriores, proporcione los siguientes controles existentes de exposición a través de la `ExposureMode` propiedad:

-   `AVCaptureExposureModeLocked` – Muestrea la escena una vez y usa esos valores a lo largo de la escena.
-   `AVCaptureExposureModeContinuousAutoExposure` : Ejemplos de la escena continuamente para asegurarse de que también esté encendida.


El `ExposurePointOfInterest` puede usarse para puntear para exponer la escena seleccionando un objeto de destino va a exponer en, y la aplicación puede supervisar el `AdjustingExposure` propiedad para ver cuándo se va a Ajustar exposición.

### <a name="new-exposure-controls-in-ios-8"></a>Nuevos controles de exposición en iOS 8

Además de las características ya proporcionadas por iOS 7 y versiones posteriores, las siguientes características ahora están disponibles para controlar la exposición en iOS 8:

-  Exposición personalizado completamente manual.
-  Get, Set y pares clave-valor observar IOS y la velocidad de obturación (duración).


Para implementar las características anteriores, un nuevo `AVCaptureExposureModeCustom` se ha agregado el modo. Una vez la cámara en el modo personalizado, el código siguiente puede usarse para ajustar la duración de la exposición y ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

En los modos Auto y bloqueado, la aplicación puede ajustar el sesgo de la rutina de exposición automática con el código siguiente:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Los intervalos mínimo y máximo de configuración dependen del dispositivo que se ejecuta la aplicación, por lo que no deben nunca se de forma rígidas. En su lugar, utilice las siguientes propiedades para obtener los intervalos de valores mínimos y máximos:

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


Tal como se muestra en el código anterior, el dispositivo de captura debe estar bloqueado para la configuración antes de que se puede realizar un cambio en la exposición.

### <a name="manual-exposure-example"></a>Ejemplo de exposición manual

Con el código de configuración General de captura de AV en su lugar, un `UIViewController` se pueden agregar a guión gráfico de la aplicación y configurar del modo siguiente:

[![](intro-to-manual-camera-controls-images/image12.png "Se puede agregar a las aplicaciones de guión gráfico y configurado tal como se muestra a continuación un UIViewController")](intro-to-manual-camera-controls-images/image12.png#lightbox)

La vista contiene los siguientes elementos principales:

-  Un `UIImageView` que mostrará la fuente de vídeo.
-  Un `UISegmentedControl` que cambiará el modo de enfoque de automático a bloqueado.
-  Cuatro `UISlider` controles que se muestran y actualizan el desplazamiento, duración, ISO y sesgo.


Siga este procedimiento para la conexión de seguridad el controlador de vista de Control de exposición Manual:


1. Agregue las siguientes instrucciones using:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Agregue las siguientes variables privadas:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```  
  
1. Agregue las siguientes propiedades calculadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Invalidar el `ViewDidLoad` método y agregue el código siguiente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;
    
        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;
    
        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });
    
            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });
    
            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });
    
            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        ISO.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        Bias.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Invalidar el `ViewDidAppear` método y agregue lo siguiente para iniciar la grabación cuando se cargue la vista:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Con la cámara en el modo Auto, los controles deslizantes se moverán automáticamente como el ajusta la exposición de la cámara:

    [![](intro-to-manual-camera-controls-images/image13.png "Los controles deslizantes se moverán automáticamente como el ajusta la exposición de la cámara")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. Puntee en el segmento bloqueado y arrastre el control deslizante de sesgo para ajustar manualmente el sesgo de la exposición automática:

    [![](intro-to-manual-camera-controls-images/image14.png "Ajuste manualmente la inclinación de la exposición automática")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. Puntee en el segmento personalizado y arrastre los controles deslizantes de duración e ISO para controlar la exposición manualmente:

    [![](intro-to-manual-camera-controls-images/image15.png "Arrastre los controles deslizantes de duración e ISO para el control de forma manual la exposición")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Detener la aplicación.


El código anterior muestra cómo supervisar la configuración de la exposición cuando se encuentra la cámara en el modo automático y cómo usar controles deslizantes para controlar la exposición cuando se encuentra en los modos de bloqueo o personalizado.

## <a name="manual-white-balance"></a>Balance de blanco manual

Controles de Balance de blanco permiten a los usuarios ajustar el balance de colosr en una imagen para que parezcan más realistas. Distintas fuentes de luz tienen las temperaturas de color diferente y se debe ajustar la configuración de cámara utilizada para capturar una imagen para compensar estas diferencias. De nuevo, por lo que permite el control de usuario sobre el balance de blanco puede realizar ajustes profesionales que son incapaces de las rutinas automática lograr efectos artísticos.

[![](intro-to-manual-camera-controls-images/image16.png "Una imagen de ejemplo que muestra los ajustes de Balance de blanco Manual")](intro-to-manual-camera-controls-images/image16.png#lightbox)

Por ejemplo, el horario de verano tiene una conversión blueish, mientras que las luces de tungsten incandescente tienen un tono más cálido, amarillo naranja. (Forma confusa, colores "en frío" tienen las temperaturas de color superiores a los colores "activos". Las temperaturas de color son una medida física, no uno perceptual).

La mente humana es muy buena para compensar las diferencias de temperatura de color, pero esto es algo que no se puede hacer una cámara. La cámara funciona mediante el aumento de color en el espectro de opuesto a ajustarse a las diferencias de color.

API de exposición nueva iOS 8 permite que la aplicación tomar el control del proceso y proporcionar un mayor control sobre la configuración de balance de blanco de la cámara.

### <a name="how-white-balance-works"></a>Blanco cómo funciona el equilibrio

Antes de tratar los detalles de controlar el balance de blanco en una aplicación de IOS 8. Echemos un vistazo rápido a blanco cómo funciona el saldo:

En el estudio de percepción de color, el [CIE 1931 RGB de color espacio y el espacio de color CIE 1931 XYZ](https://en.wikipedia.org/wiki/CIE_1931_color_space) la primera matemáticamente definen espacios de colores. 1931 que se crearon por la Comisión internacional de iluminación (CIE).

[![](intro-to-manual-camera-controls-images/image17.png "El espacio de colores RGB CIE 1931 y CIE 1931 XYZ espacio de colores")](intro-to-manual-camera-controls-images/image17.png#lightbox)

El gráfico anterior nos muestra todos los colores visible para el ojo humano, en niveles profundos de azul a verde brillante a color rojo brillante. Cualquier punto del diagrama se puede trazar con un valor de X e Y, como se muestra en el gráfico anterior.

Como puede verse en el gráfico, hay valores X e Y que se pueden trazar en el gráfico que sería fuera del intervalo de la visión humana y, como resultado no se puede reproducir estos colores con una cámara.

La curva más pequeña en el gráfico anterior se denomina el [Planckian raíz](https://en.wikipedia.org/wiki/Planckian_locus), que expresa la temperatura de color (en grados kelvin), con un número mayor en la parte azul (más cálido) y números inferior en la parte roja (frío). Estos son útiles para situaciones de iluminación típico.

En condiciones de iluminación mixto, los ajustes de balance de blanco deberá difiera de la raíz de Planckian para realizar los cambios necesarios. En estas situaciones que será necesario el ajuste se desplazan a verde o rojo/magenta lado de la CIE escalar.

Dispositivos iOS compensarán las conversiones de color incrementando la ganancia de color opuesto. Por ejemplo, si una escena tiene demasiado azul, la ganancia de rojo se potenciarán para compensar. Ganancia de estos valores estén calibrados para dispositivos específicos, por lo que son dependientes del dispositivo.

### <a name="existing-white-balance-controls"></a>Controles existentes de Balance de blanco

iOS 7 y versiones posteriores proporcionan los siguientes controles de Balance de blanco existentes a través de `WhiteBalanceMode` propiedad:

-   `AVCapture WhiteBalance ModeLocked` : Ejemplos de la escena una vez y el uso de esos valores a lo largo de la escena.
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` : Ejemplos de la escena continuamente para asegurarse de que también está equilibrado.


Y la aplicación puede supervisar el `AdjustingWhiteBalance` propiedad para ver cuándo se va a Ajustar exposición.

### <a name="new-white-balance-controls-in-ios-8"></a>Nuevos controles de Balance de blanco en iOS 8

Además de las características ya proporcionadas por iOS 7 y versiones posteriores, ahora están disponibles al control de Balance de blanco en iOS 8 las siguientes características:

-  Obtiene el control completamente manual del dispositivo RGB.
-  Get, Set y observar el valor de clave se adquiere el dispositivo RGB.
-  Compatibilidad con el Balance de blanco con una tarjeta de gris.
-  Rutinas de conversión a y desde los espacios de colores independientes de dispositivo.


Para implementar las características anteriores, el `AVCaptureWhiteBalanceGain` estructura se ha agregado con los miembros siguientes:

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


La ganancia de balance de blanco máximo es actualmente cuatro (4) y puede estar lista desde el `MaxWhiteBalanceGain` propiedad. Por lo que el rango permitido es de uno (1) `MaxWhiteBalanceGain` (4) actualmente.

El `DeviceWhiteBalanceGains` propiedad puede usarse para observar los valores actuales. Use `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` ajustar el balance logra cuando la cámara está en el modo de balance de blanco bloqueado.

#### <a name="conversion-routines"></a>Rutinas de conversión

Rutinas de conversión se han agregado a iOS 8 para ayudar a con la conversión a y desde los espacios de colores independientes de dispositivo. Para implementar las rutinas de conversión, el `AVCaptureWhiteBalanceChromaticityValues` estructura se ha agregado con los miembros siguientes:

-   `X` -es un valor comprendido entre 0 y 1.
-   `Y` -es un valor comprendido entre 0 y 1.


Un `AVCaptureWhiteBalanceTemperatureAndTintValues` estructura también se ha agregado con los miembros siguientes:

-   `Temperature` -flotante es un valor de punto en grados Kelvin.
-   `Tint` -es un desfase de verde o magenta de 0 a 150 con valores positivos hacia la dirección verde y negativo hacia en el magenta.


Use la `CaptureDevice.GetTemperatureAndTintValues`y `CaptureDevice.GetDeviceWhiteBalanceGains`métodos para convertir entre la temperatura y un tono, cromacidad y RGB obtienen espacios de colores.

> [!NOTE]
> Las rutinas de conversión son más precisas, cuanto más se acerque el valor que se va a convertir es la raíz de Planckian.




#### <a name="gray-card-support"></a>Compatibilidad con tarjetas gris

Apple usa el término mundo gris para hacer referencia a la compatibilidad de la tarjeta de color gris integrada en iOS 8. Permite al usuario centrarse en una tarjeta física de gris que cubre al menos el 50% del centro del marco y usa esa información para ajustar el Balance de blanco. El propósito de la tarjeta de gris es lograr en blanco que aparece neutro.

Esto se puede implementar en una aplicación solicitando al usuario para colocar una tarjeta física gris delante de la cámara, supervisión del `GrayWorldDeviceWhiteBalanceGains` propiedad y esperar a que los valores relájese.

La aplicación, a continuación, se bloquearía las mejoras de Balance de blanco para el `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` método con los valores de la `GrayWorldDeviceWhiteBalanceGains` propiedad para aplicar los cambios.

El dispositivo de captura debe estar bloqueado para la configuración antes de hacer un cambio en el Balance de blanco.

### <a name="manual-white-balance-example"></a>Ejemplo de Balance de blanco manual

Con el código de configuración General de captura de AV en su lugar, un `UIViewController` se pueden agregar a guión gráfico de la aplicación y configurar del modo siguiente:

[![](intro-to-manual-camera-controls-images/image18.png "Se puede agregar a las aplicaciones de guión gráfico y configurado tal como se muestra a continuación un UIViewController")](intro-to-manual-camera-controls-images/image18.png#lightbox)

La vista contiene los siguientes elementos principales:

-  Un `UIImageView` que mostrará la fuente de vídeo.
-  Un `UISegmentedControl` que cambiará el modo de enfoque de automático a bloqueado.
-  Dos `UISlider` controles que se muestran y actualizan la temperatura y la tinta.
-  Un `UIButton` usada para un espacio de tarjeta de color gris (gris World) de ejemplo y establecer el Balance de blanco con esos valores.


Siga este procedimiento para el controlador de vista de copia de hilo para Control del saldo Manual en blanco:


1. Agregue las siguientes instrucciones using:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Agregue las siguientes variables privadas:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    #endregion
    ```
  
1. Agregue las siguientes propiedades calculadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Agregue el siguiente método privado para establecer el color blanco nuevo equilibrar la temperatura y un tono:

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }
    
    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```   
  
1. Invalidar el `ViewDidLoad` método y agregue el código siguiente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ``` 
  
1. Invalidar el `ViewDidAppear` método y agregue lo siguiente para iniciar la grabación cuando se cargue la vista:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Guarde los cambios del código y ejecutar la aplicación.
1. Con la cámara en el modo Auto, los controles deslizantes se moverán automáticamente como la cámara ajusta el balance de blanco:

    [![](intro-to-manual-camera-controls-images/image19.png "Los controles deslizantes se moverán automáticamente como la cámara ajusta el balance de blanco")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. Puntee en el segmento bloqueado y arrastre los controles deslizantes de Temp y tono para ajustar el balance de blanco manualmente:

    [![](intro-to-manual-camera-controls-images/image20.png "Arrastre los controles deslizantes de Temp y tono para ajustar el balance de blanco manualmente")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. Con el segmento bloqueado aún seleccionado, colocar una tarjeta física gris en la parte delantera de la cámara y pulse el botón de tarjeta de color gris para ajustar el balance de blanco al gris mundo:

    [![](intro-to-manual-camera-controls-images/image21.png "Pulse el botón de tarjeta de color gris para ajustar el balance de blanco al mundo gris")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Detener la aplicación.

El código anterior muestra cómo supervisar la configuración de balance de blanco cuando se encuentra la cámara en el modo automático o usar controles deslizantes para controlar el balance de blanco cuando se encuentra en el modo de bloqueo.

## <a name="bracketed-capture"></a>Captura entre corchetes

La captura entre corchetes se basa en la configuración de los controles de cámara Manual presentada anteriormente y permite que la aplicación capturar un momento dado, en una variedad de formas diferentes.

En pocas palabras, paréntesis de captura es una ráfaga de imágenes fijas tomadas con una variedad de opciones de imagen en imagen.

[![](intro-to-manual-camera-controls-images/image22.png "Cómo funciona el paréntesis de captura")](intro-to-manual-camera-controls-images/image22.png#lightbox)

Mediante la captura enmarcadas en iOS 8, una aplicación puede preestablecer una serie de controles de cámara Manual, emitir un comando único y la escena actual volver una serie de imágenes para cada uno de los valores preestablecidos manuales.

### <a name="bracketed-capture-basics"></a>Conceptos básicos de captura entre corchetes

Nuevamente, paréntesis de captura es una ráfaga de imágenes fijas realizada con configuración variadas de imagen a la imagen. Los tipos de enmarcadas captura disponibles son:

-  **Auto exposición corchete** : donde todas las imágenes tienen una cantidad de sesgo variada.
-  **Manual de exposición de corchete** : donde todas las imágenes tienen una velocidad de obturación (duración) e ISO variadas cantidad.
-  **Corchete ráfaga simple** – una serie de imágenes fijas tomadas en una sucesión rápida.


### <a name="new-bracketed-capture-controls-in-ios-8"></a>Entre corchetes capturar controles nuevos en iOS 8

Todos los comandos de captura entre corchetes se implementan en el `AVCaptureStillImageOutput` clase. Use el `CaptureStillImageBracket`método para obtener una serie de imágenes con la matriz especificada de valores.

Se han implementado dos nuevas clases para controlar la configuración:

-   `AVCaptureAutoExposureBracketedStillImageSettings` : No tiene una propiedad, `ExposureTargetBias`, que se usa para establecer el sesgo de un corchete de exposición automática. 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` – Tiene dos propiedades, `ExposureDuration` y `ISO`, que se usa para establecer la velocidad de obturación e ISO para un corchete de cierre de exposición manual. 


### <a name="bracketed-capture-controls-dos-and-donts"></a>Captura entre corchetes controla qué hacer y qué no

#### <a name="dos"></a>Qué hacer

La siguiente es una lista de las cosas que debe realizarse cuando se usa la captura de enmarcadas controles en iOS 8:

-  Preparar la aplicación para la situación del peor caso captura mediante una llamada a la `PrepareToCaptureStillImageBracket` método.
-  Se supone que los búferes de ejemplo se van a proceder del mismo grupo compartido.
-  Para liberar la memoria que se asignó mediante una llamada de preparación anterior, llame a `PrepareToCaptureStillImageBracket` nuevo y enviarle una matriz de un objeto.


#### <a name="donts"></a>Contras

La siguiente es una lista de las cosas que no se deben realizar cuando se usa la captura de enmarcadas controles en iOS 8:

-  No mezcle enmarcadas capturar los tipos de configuración en una única captura.
-  No solicite más de `MaxBracketedCaptureStillImageCount` imágenes en una única captura.


### <a name="bracketed-capture-details"></a>Detalles de captura entre corchetes

Cuando se trabaja con paréntesis de captura en iOS 8 deben tenerse en cuenta los siguientes detalles:

-  Configuración entre corchetes invalida temporalmente el `AVCaptureDevice` configuración.
-  Se omite Flash y sigue la configuración de estabilización de imagen.
-  Todas las imágenes deben usar el mismo formato de salida (jpeg, png, etcetera.)
-  Vista previa de vídeo puede elimina fotogramas.
-  Captura entre corchetes se admite en todos los dispositivos compatibles con iOS 8.


Con esta información en mente, echemos un vistazo a un ejemplo del uso de paréntesis de captura en iOS 8.

### <a name="bracket-capture-example"></a>Ejemplo de captura de corchete

Con el código de configuración General de captura de AV en su lugar, un `UIViewController` se pueden agregar a guión gráfico de la aplicación y configurar del modo siguiente:

[![](intro-to-manual-camera-controls-images/image23.png "Se puede agregar a las aplicaciones de guión gráfico y configurado tal como se muestra a continuación un UIViewController")](intro-to-manual-camera-controls-images/image23.png#lightbox)

La vista contiene los siguientes elementos principales:

-  Un `UIImageView` que mostrará la fuente de vídeo.
-  Tres `UIImageViews` que mostrará los resultados de la captura.
-  Un `UIScrollView` para alojar la fuente de vídeo y vistas de los resultados.
-  Un `UIButton` solía tardar una captura entre corchetes con algunos valores preestablecidos.


Siga este procedimiento para la conexión de seguridad el controlador de vista para la captura de entre corchetes:


1. Agregue las siguientes instrucciones using:

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```  
  
1. Agregue las siguientes variables privadas:

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```    
  
1. Agregue las siguientes propiedades calculadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```  
  
1. Agregue el método privado siguiente para crear las vistas de la imagen de salida requerida:

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {
    
        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));
    
        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");
    
        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);
    
        // Add to scrolling view
        ScrollView.AddSubview (imageView);
    
        // Return new image view
        return imageView;
    }
    #endregion
    ```  
  
1. Invalidar el `ViewDidLoad` método y agregue el código siguiente:
    
    ```
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);
    
        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));
    
        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };
    
        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;
    
            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });
    
            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);
    
                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);
    
                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```
    
  
1. Invalidar el `ViewDidAppear` método y agregue el código siguiente:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
        }
    }
    
    ```  
    
1. Guarde los cambios del código y ejecutar la aplicación.
1. Una escena de fotogramas y pulse el botón de paréntesis de captura:

    [![](intro-to-manual-camera-controls-images/image24.png "Una escena de fotogramas y pulse el botón de paréntesis de captura")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Deslice el dedo de derecha a izquierda para ver las tres imágenes realizadas por la captura entre corchetes:

    [![](intro-to-manual-camera-controls-images/image25.png "Deslice el dedo de derecha a izquierda para ver las tres imágenes realizadas por la captura entre corchetes")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Detener la aplicación.


El código anterior muestra cómo configurar y tomar una captura de entre corchetes de exposición de Auto iOS 8.

## <a name="summary"></a>Resumen

En este artículo hemos cubierto una introducción a los nuevos controles de cámara Manual proporcionado por iOS 8 y tratan los aspectos básicos de qué hacen y cómo funcionan. Le hemos dado algunos ejemplos de enfoque Manual, exposición Manual y Balance de blanco Manual. Por último, asignamos un ejemplo teniendo un enmarcadas capturar mediante los controles de cámara se explicó anteriormente Manual

## <a name="related-links"></a>Vínculos relacionados

- [ManualCameraControls (ejemplo)](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
