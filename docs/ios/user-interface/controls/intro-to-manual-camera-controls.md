---
title: "Controles de la cámara manual"
description: "El marco de AVFoundation hace más fácil que nunca que los usuarios tomar fotos magníficas permitiendo para controles de la cámara Manual. Con este marco de trabajo, una aplicación puede adoptar un control directo sobre el enfoque de cámara, balance de blanco y ajustes de exposición. Una aplicación también puede utilizar entre corchetes exposición capturas captura automáticamente imágenes con distintos ajustes de exposición. En este artículo se echar un vistazo rápido de usar los controles de cámara Manual en una aplicación móvil iOS simple 8."
ms.topic: article
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: e5ff4e3732476415ff32e98b8281f3cb6e03e332
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="manual-camera-controls"></a>Controles de la cámara manual

_El marco de AVFoundation hace más fácil que nunca que los usuarios tomar fotos magníficas permitiendo para controles de la cámara Manual. Con este marco de trabajo, una aplicación puede adoptar un control directo sobre el enfoque de cámara, balance de blanco y ajustes de exposición. Una aplicación también puede utilizar entre corchetes exposición capturas captura automáticamente imágenes con distintos ajustes de exposición. En este artículo se echar un vistazo rápido de usar los controles de cámara Manual en una aplicación móvil iOS simple 8._

Los controles de cámara Manual, proporcionados por el `AVFoundation Framework` en iOS 8, permitir que una aplicación móvil tomar el control completo sobre la cámara de un dispositivo iOS. Este nivel específica del control se puede utilizar para crear aplicaciones de la cámara de nivel profesional y proporcionar las composiciones de intérprete ajustando los parámetros de la cámara al desconectar un vídeo o imagen fija.

Estos controles también pueden ser útiles al desarrollar aplicaciones científicas o industriales, donde los resultados menos específicamente orientados a la corrección o la belleza de la imagen y se orientan más hacia resaltado algunas características o los elementos de la imagen que se interrumpa.

## <a name="avfoundation-capture-objects"></a>Objetos de captura AVFoundation

Si tarda de vídeo o imágenes con la cámara en un dispositivo iOS, el proceso que se utiliza para capturar las imágenes es prácticamente el mismo. Es el caso de las aplicaciones que usan los controles de cámara automatizado de forma predeterminada o que sacar partido de los nuevos controles de cámara Manual:

 [ ![](intro-to-manual-camera-controls-images/image1.png "Información general de objetos de captura AVFoundation")](intro-to-manual-camera-controls-images/image1.png)

Entrada procede de un `AVCaptureDeviceInput` en un `AVCaptureSession` por medio de un `AVCaptureConnection`. El resultado es cualquier salida como una imagen fija o como una secuencia de vídeo. Todo el proceso se controla mediante un `AVCaptureDevice`.

## <a name="manual-controls-provided"></a>Controles manuales que se proporcionan

Mediante las nuevas API proporcionadas por iOS 8, la aplicación puede tomar el control de las siguientes características de cámara:

-  **Enfoque manual** – al permitir que el usuario final tomar el control de la concentración directamente, una aplicación puede proporcionar más control sobre la imagen realizada.
-  **Exposición manual** – proporcionando controlar manualmente la exposición, puede proporcionar más libertad a los usuarios y permitirles lograr una apariencia estilizados con una aplicación.
-  **Balance de blanco manual** – Balance de blanco se utiliza para ajustar el color en una imagen, a menudo para que parezca realista. Diferentes fuentes de luz tienen temperaturas de color distintas y la configuración de la cámara se utiliza para capturar una imagen se ajusta para compensar estas diferencias. Una vez más, si se permite el control de usuario sobre el balance de blanco, los usuarios pueden realizar ajustes que no se puede realizar automáticamente.


iOS 8 proporciona extensiones y mejoras en iOS existente API para proporcionar este control más preciso sobre la imagen de proceso de captura.

## <a name="bracketed-capture"></a>Captura entre corchetes

La captura se encuentran entre corchetes se basa en la configuración de los controles de cámara Manual presentada anteriormente y permite que la aplicación capturar un momento determinado, en una variedad de maneras diferentes.

En pocas palabras, paréntesis de captura es una ráfaga de imágenes fijas tomadas con una variedad de opciones de imagen para la imagen.

## <a name="requirements"></a>Requisitos

Para completar los pasos presentados en este artículo se necesita lo siguiente:

-  **Xcode 7 + iOS 8 o versiones más recientes y** : Xcode 7 y iOS 8 o API más recientes de Apple deben instalarse y configurarse en el equipo del desarrollador.
-  **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el dispositivo de usuario.
-  **iOS 8 dispositivo** : un dispositivo iOS ejecutando la última versión de iOS 8. Características de la cámara no se pueden probar en el simulador de iOS.


## <a name="general-av-capture-setup"></a>Programa de instalación de la captura de AV de general

Grabación de vídeo en un dispositivo iOS, hay un código de configuración general que siempre es necesario. En esta sección se tratará la configuración mínima necesaria para grabar vídeo desde la cámara del dispositivo iOS y mostrar esa vídeo en tiempo real en un `UIImageView`.

### <a name="output-sample-buffer-delegate"></a>Delegado de búfer de ejemplo de salida

Una de las primeras cosas que sea necesario será un delegado para supervisar el búfer de salida de ejemplo y mostrar una imagen obteniendo un elemento desde el búfer a un `UIImageView` en el interfaz de usuario de la aplicación.

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

Con esta rutina en su lugar, el `AppDelegate` se puede modificar para abrir una sesión de captura de AV para grabar un vídeo en vivo.

### <a name="creating-an-av-capture-session"></a>Crear una sesión de captura de AV

La sesión de captura de AV se utiliza para controlar la grabación de vídeo en directo desde la cámara del dispositivo iOS y es necesaria para obtener el vídeo en una aplicación de iOS. Desde el ejemplo `ManualCameraControl` aplicación de ejemplo usa la sesión de captura en varios sitios distintos, se configurará en el `AppDelegate` y están disponibles para toda la aplicación.

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
  
1. Invalide el método finalizado y cámbiela por:
    
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


Con este código en su lugar, se puede implementar fácilmente los controles de cámara Manual para experimentación y las pruebas.

## <a name="manual-focus"></a>Enfoque manual

Al permitir que el usuario final pueda controles del enfoque directamente, una aplicación puede proporcionar artístico más control sobre la imagen realizada.

Por ejemplo, un fotógrafo profesional puede suavizar el foco de una imagen para lograr un [Bokeh efecto](http://en.wikipedia.org/wiki/Bokeh):

[ ![](intro-to-manual-camera-controls-images/image2.png "Un efecto de Bokeh")](intro-to-manual-camera-controls-images/image2.png)

O bien, crear un [foco extracción efecto](http://www.mediacollege.com/video/camera/focus/pull.html), como:

[ ![](intro-to-manual-camera-controls-images/image3.png "El efecto de incorporación de cambios de foco")](intro-to-manual-camera-controls-images/image3.png)

Para científicos o un sistema de escritura de las aplicaciones médicas, la aplicación podría desear mover mediante programación la lente de los experimentos. En ambos casos la nueva API permite que el usuario final o la aplicación para aprovechar el control sobre el foco en el momento de la imagen se toma.

### <a name="how-focus-works"></a>Cómo funciona el foco

Antes de hablar sobre los detalles de control de la selección en una aplicación de IOS 8. ¡Eche un vistazo a cómo funciona el foco en un dispositivo iOS:

[ ![](intro-to-manual-camera-controls-images/image4.png "Cómo funciona el foco en un dispositivo iOS")](intro-to-manual-camera-controls-images/image4.png)

Luz entra en el objetivo de la cámara en el dispositivo iOS y se centra en un sensor de imagen. La distancia de la lente de los controles de sensor donde es el punto Focal (es decir, el área donde la imagen se mostrará el muy nítidas), en relación con el sensor. Cuanto más lejos la lente desde el sensor, objetos de distancia parecen muy nítidos y cuanto más se acerque, cerca de objetos parece muy nítidas.

En un dispositivo iOS, la lente se mueve cuando se acerque a, o elija el sensor imán y springs. Como resultado, la posición exacta de la lente resulta imposible, ya que variará de un dispositivo a otro y puede verse afectada por los parámetros, como la orientación del dispositivo o la antigüedad del dispositivo y primavera.

### <a name="important-focus-terms"></a>Términos de foco importante

Cuando se trabaja con el foco, hay una serie de términos que el desarrollador debe estar familiarizado con:

-  **Profundidad de campo** : la distancia entre los objetos más cercanos y más lejano en el foco. 
-  **Macro** -éste es el cerca del final del espectro de foco y es la distancia más cercana a la que pueda centrarse la lente.
-  **Infinito** : este es el otro extremo del espectro de foco y es la distancia más lejana a la que pueda centrarse la lente.
-  **Distancia de Hyperfocal** : éste es el punto en el espectro de foco donde el objeto situado más abajo en el marco es simplemente en el otro extremo del foco. En otras palabras, se trata de la posición focal que maximiza la profundidad de campo. 
-  **Posición de la lente** – que es la que controla todo lo anterior otros términos. Esto es la distancia de la lente del sensor de y, por tanto, el controlador de foco.


Con estos términos y conocimiento de la cuenta, los nuevos controles de enfoque Manual puede implementarse correctamente en una aplicación de iOS 8.

### <a name="existing-focus-controls"></a>Controles existentes de foco

iOS 7 y versiones anteriores, proporcionan controles de foco existentes a través de `FocusMode`propiedad como:

-   `AVCaptureFocusModeLocked` El foco se bloquea en un punto de enfoque único.
-   `AVCaptureFocusModeAutoFocus` – La cámara barre la lente a través de todos los puntos de focal hasta que encuentra un enfoque nítido y, a continuación, permanece en su ubicación.
-   `AVCaptureFocusModeContinuousAutoFocus` – La cámara refocuses cada vez que detecta una condición de fuera de foco.


Los controles existentes también proporcionan un punto configurable de interés a través de la`FocusPointOfInterest` propiedad, por lo que el usuario puede pulsar para centrarse en un área concreta. La aplicación puede seguir el movimiento de lente mediante la supervisión de la `IsAdjustingFocus` propiedad.

Además, se proporcionó la restricción de rango por el `AutoFocusRangeRestriction` propiedad como:

-   `AVCaptureAutoFocusRangeRestrictionNear` : Restringe el enfoque de automático a profundidades cercanos. Resulta útil en situaciones como la exploración de un código QR o código de barras.
-   `AVCaptureAutoFocusRangeRestrictionFar` : Restringe el enfoque de automático a profundidades lejanos. Resulta útil en situaciones en que los objetos que se sabe que son irrelevantes en el campo de vista (por ejemplo, un marco de ventana).


Finalmente, hay el `SmoothAutoFocus` propiedad que ralentiza el algoritmo de enfoque automático y pasos en incrementos más pequeños para evitar el movimiento de artefactos en la grabación de vídeo.

### <a name="new-focus-controls-in-ios-8"></a>Nuevos controles de foco en iOS 8

Además de las características que ya le proporcionadas por iOS 7 y versiones posteriores, ahora están disponibles para controlar el foco en iOS 8 las siguientes características:

-  Completo control manual de la posición de la lente al bloquear el foco.
-  Observación de clave y valor de la posición de la lente en cualquier modo de enfoque.


Para implementar las características anteriores, el `AVCaptureDevice` clase se ha modificado para incluir solo lectura `LensPosition` propiedad utilizada para obtener la posición actual del objetivo de la cámara.

Para tomar el control manual de la posición de la lente, el dispositivo de captura debe estar en el modo de enfoque bloqueado. Ejemplo:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

El `SetFocusModeLocked` método del dispositivo de captura se utiliza para ajustar la posición del objetivo de la cámara. Una rutina de devolución de llamada opcional se puede proporcionar para obtener notificaciones cuando el cambio surta efecto. Ejemplo:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Tal como se muestra en el código anterior, se debe bloquear el dispositivo de captura para la configuración para poder realizar un cambio en la posición de la lente. Los valores de posición de la lente válidos son entre 0,0 y 1,0.

### <a name="manual-focus-example"></a>Ejemplo de enfoque manual

Con el código de configuración de la captura de AV General en su lugar, un `UIViewController` se pueden agregar a guión gráfico de la aplicación y configurar del modo siguiente:

[ ![](intro-to-manual-camera-controls-images/image5.png "Se puede agregar a las aplicaciones de guión gráfico y configurado como se muestra aquí una UIViewController")](intro-to-manual-camera-controls-images/image5.png)

La vista contiene los siguientes elementos principales:

-  Un `UIImageView` que mostrará la fuente de vídeo.
-  Un `UISegmentedControl` que cambiará el modo de enfoque de automático a bloqueado.
-  Un `UISlider` que se muestran y se actualiza la posición actual de la lente.


Realice lo siguiente para el controlador de vista de cable de seguridad para Control de enfoque Manual:


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
  
1. Invalidar el `ViewDidAppear` método y agregue el siguiente procedimiento para iniciar la grabación cuando se carga la vista:

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
  
1. Con la cámara en el modo Auto, el control deslizante se moverá automáticamente cuando la cámara ajusta foco:

    [![](intro-to-manual-camera-controls-images/image6.png "El control deslizante se moverá automáticamente cuando la cámara ajusta el foco en esta aplicación de ejemplo")](intro-to-manual-camera-controls-images/image6.png)
1. Puntee en el segmento bloqueado y arrastre el control deslizante de posición para ajustar la posición de la lente manualmente:

    [![](intro-to-manual-camera-controls-images/image7.png "Ajustar manualmente la posición de la lente")](intro-to-manual-camera-controls-images/image7.png)
1. Detenga la aplicación.


El código anterior muestra cómo supervisar la posición lens cuando la cámara está en el modo automático o usar un control deslizante para controlar la posición de la lente cuando se encuentra en el modo bloqueado.

## <a name="manual-exposure"></a>Exposición manual

Exposición hace referencia al brillo de una imagen en relación con el brillo de origen y se determina por la cantidad de luz alcanza el sensor, para saber cómo larga y por el nivel de ganancia del sensor (asignación de ISO). Proporcionando controlar manualmente la exposición, una aplicación puede proporcionar más libertad al usuario final y que puedan lograr un vistazo estilizados con.

Con los controles de exposición Manual, el usuario puede tomar una imagen desde excesivamente claro a oscuro y moody:

[ ![](intro-to-manual-camera-controls-images/image8.png "Un ejemplo de una imagen que muestra la exposición en caso de excesivamente claro a oscuro y moody")](intro-to-manual-camera-controls-images/image8.png)

Una vez más, esto puede hacerse automáticamente con el control mediante programación en aplicaciones científicas o a través de controles manuales proporcionados por la interfaz de usuario de las aplicaciones. En cualquier caso, el nuevo iOS 8 API de exposición proporcionan control más preciso sobre los ajustes de exposición de la cámara.

### <a name="how-exposure-works"></a>Cómo funciona la exposición

Antes de hablar sobre los detalles de controlar la exposición en una aplicación de IOS 8. ¡Eche un vistazo a cómo funciona la exposición:

[ ![](intro-to-manual-camera-controls-images/image9.png "Cómo funciona la exposición")](intro-to-manual-camera-controls-images/image9.png)

Los tres elementos básicos que unificación para controlar el riesgo de son:

-  **Velocidad de obturación** : ésta es la longitud de tiempo que la obturación está abierto para permitir que la luz en el sensor de cámara. La cadena más corta se permite el tiempo que la obturación está abierto, la luz menos y es la imagen más nítida el (menos desenfoque de movimiento). Mayor será la obturación está abierto, la luz más se permite en y movimiento más desenfoque que tiene lugar.
-  **Asignación de ISO** : Esto es un término extraído de fotografía de película y hace referencia a la confidencialidad de los productos químicos en la película a la luz. Valores de ISO de baja en las películas tienen menor nivel de detalle y la reproducción del color más preciso; los valores bajos de ISO en sensores digitales tienen menos ruido de sensor pero menos brillo. Cuanto mayor sea el valor ISO, más brillante será la imagen, pero con más ruido de sensor. "ISO" en un sensor digital es una medida de [ganancia electrónica](http://en.wikipedia.org/wiki/Gain), no es una característica física. 
-  **Apertura de la lente** : este es el tamaño de la apertura de la lente. En todos los dispositivos iOS se fija la apertura de la lente, por lo que los dos únicos valores que pueden usarse para ajustar exposición son la velocidad de obturación e ISO.


### <a name="how-continuous-auto-exposure-works"></a>Continua cómo funciona la exposición automática

Antes de aprendizaje cómo funciona la exposición manual, es una buena idea comprender cómo continua exposición automática funciona en un dispositivo iOS.

[ ![](intro-to-manual-camera-controls-images/image10.png "Cómo funciona la exposición automática continua en un dispositivo iOS")](intro-to-manual-camera-controls-images/image10.png)

En primer lugar es el bloque de exposición automática, tiene el trabajo de calcular la exposición ideal y continuamente se suministran estadísticas de disponibilidad. Utiliza esta información para calcular la combinación óptima de ISO y velocidad de obturación para obtener la escena y encendida. Este ciclo se conoce como el bucle AE.

### <a name="how-locked-exposure-works"></a>Bloqueado cómo funciona la exposición

A continuación, vamos a examinar bloqueado cómo funciona la exposición en dispositivos iOS.

[ ![](intro-to-manual-camera-controls-images/image11.png "Cómo bloqueada exposición funciona en dispositivos iOS")](intro-to-manual-camera-controls-images/image11.png)

Una vez más, tiene el bloque de exposición automática que está intentando calcular los valores de duración y iOS óptimo. Sin embargo, en este modo el bloque AE se desconecta el motor de estadísticas de disponibilidad.

### <a name="existing-exposure-controls"></a>Controles existentes de exposición

iOS 7 y versiones posteriores, proporcione los siguientes controles de exposición existentes a través de la `ExposureMode` propiedad:

-   `AVCaptureExposureModeLocked` – Muestrea la escena una vez y utiliza los valores a lo largo de la escena.
-   `AVCaptureExposureModeContinuousAutoExposure` : Ejemplos de la escena continuamente para asegurarse de que también esté encendida.


El `ExposurePointOfInterest` puede usarse para puntear para exponer la escena seleccionando un objeto de destino se expongan en y la aplicación puede supervisar el `AdjustingExposure` propiedad para ver cuándo se va a Ajustar exposición.

### <a name="new-exposure-controls-in-ios-8"></a>Nuevos controles de exposición en iOS 8

Además de las características que ya le proporcionadas por iOS 7 y versiones posteriores, ahora están disponibles para controlar la exposición en iOS 8 las siguientes características:

-  Exposición personalizada completamente manual.
-  Get, Set y clave-valor observar IOS y la velocidad de obturación (duración).


Para implementar las características anteriores, un nuevo `AVCaptureExposureModeCustom` modo se ha agregado. Una vez la cámara en el modo personalizado, el código siguiente se puede utilizar para ajustar la duración de la exposición y ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

En los modos Auto y bloqueado, la aplicación puede ajustar la diferencia de la rutina de exposición automática con el código siguiente:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Los intervalos de configuración mínima y máxima dependen de los dispositivos que se ejecuta la aplicación, por lo que no deben nunca se codifican de forma rígida. En su lugar, utilice las siguientes propiedades para obtener los intervalos de valores mínimo y máximo:

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


Tal como se muestra en el código anterior, se debe bloquear el dispositivo de captura para la configuración para poder realizar un cambio en la exposición.

### <a name="manual-exposure-example"></a>Ejemplo de exposición manual

Con el código de configuración de la captura de AV General en su lugar, un `UIViewController` se pueden agregar a guión gráfico de la aplicación y configurar del modo siguiente:

[ ![](intro-to-manual-camera-controls-images/image12.png "Se puede agregar a las aplicaciones de guión gráfico y configurado como se muestra aquí una UIViewController")](intro-to-manual-camera-controls-images/image12.png)

La vista contiene los siguientes elementos principales:

-  Un `UIImageView` que mostrará la fuente de vídeo.
-  Un `UISegmentedControl` que cambiará el modo de enfoque de automático a bloqueado.
-  Cuatro `UISlider` controles que se muestran y actualizan el desplazamiento, la duración, ISO y la desviación.


Realice lo siguiente para el controlador de vista de cable de seguridad para Control de exposición Manual:


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
  
1. Invalidar el `ViewDidAppear` método y agregue el siguiente procedimiento para iniciar la grabación cuando se carga la vista:

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
  
1. Con la cámara en el modo Auto, los controles deslizantes se moverán automáticamente cuando la cámara ajusta la exposición:

    [![](intro-to-manual-camera-controls-images/image13.png "Los controles deslizantes se moverán automáticamente cuando la cámara ajusta la exposición")](intro-to-manual-camera-controls-images/image13.png)
1. Puntee en el segmento bloqueado y arrastre el control deslizante de compensación para ajustar manualmente la diferencia de la exposición automática:

    [![](intro-to-manual-camera-controls-images/image14.png "Ajuste manualmente la diferencia de la exposición automática")](intro-to-manual-camera-controls-images/image14.png)
1. Puntee en el segmento personalizado y arrastre los controles deslizantes ISO y la duración para controlar manualmente la exposición:

    [![](intro-to-manual-camera-controls-images/image15.png "Arrastre los controles deslizantes duración e ISO al control de forma manual la exposición")](intro-to-manual-camera-controls-images/image15.png)
1. Detenga la aplicación.


El código anterior le ha mostrado cómo supervisar la configuración de exposición cuando la cámara está en el modo automático y cómo usar controles deslizantes para controlar la exposición cuando se encuentra en los modos bloqueada o personalizado.

## <a name="manual-white-balance"></a>Balance de blanco manual

Los controles de Balance de blanco permiten a los usuarios ajustar el balance de colosr en una imagen para que parezcan más realistas. Fuentes de luz diferentes tienen temperaturas de color distintas y se deban ajustar la configuración de la cámara que se utiliza para capturar una imagen para compensar estas diferencias. De nuevo, por lo que permite el control de usuario sobre el balance de blanco puede realizar ajustes profesionales que son incapaces de las rutinas automática lograr efectos artísticos.

[ ![](intro-to-manual-camera-controls-images/image16.png "Una imagen de ejemplo que muestra los ajustes de Balance de blanco Manual")](intro-to-manual-camera-controls-images/image16.png)

Por ejemplo, el horario de verano tiene una conversión blueish, mientras que las luces de tungsteno tienen un matiz para preparar y amarillo naranja. (Confusa, colores "en frío" tienen temperaturas de color superiores a colores "activa". Temperatura de color es una medida física, no uno perceptual).

La mente humana es muy adecuada para compensar las diferencias de temperatura de color, pero esto es algo que no se puede hacer una cámara. La cámara funciona por lo que incrementa el color en el espectro opuesto para ajustarse a las diferencias de color.

El nuevo iOS 8 exposición API permite que la aplicación tomar el control del proceso y proporciona un control específico sobre la configuración de balance de blanco de la cámara.

### <a name="how-white-balance-works"></a>Cómo blanco funciona de saldo

Antes de hablar sobre los detalles de controlar el balance de blanco en una aplicación de IOS 8. ¡Eche un vistazo a cómo blanco funciona de saldo:

En el estudio de percepción de color, el [CIE 1931 RGB color espacio y el espacio de color de CIE 1931 XYZ](http://en.wikipedia.org/wiki/CIE_1931_color_space) son la primera matemáticamente definidos por los espacios de color. 1931 que se crearon por la Comisión internacional de iluminación (CIE).

[ ![](intro-to-manual-camera-controls-images/image17.png "El espacio de color RGB CIE 1931 y CIE 1931 XYZ un espacio de colores")](intro-to-manual-camera-controls-images/image17.png)

El gráfico anterior nos muestra todos los colores visible para el ojo humano en niveles profundos azul a verde a rojo brillante. Cualquier punto en el diagrama se puede trazar con un valor de X e Y, como se muestra en el gráfico anterior.

Como visible en el gráfico, hay valores X e Y que se pueden trazar en el gráfico que se fuera del intervalo de la visión humana y, como resultado, estos colores no se puede reproducir con una cámara.

Se llama a la curva más pequeña en el gráfico anterior la [Planckian Locus](http://en.wikipedia.org/wiki/Planckian_locus), que expresa la temperatura de color (en grados kelvin), con los números más altos en el lado azul (aun más) y números inferior en la parte roja (frío). Estos son útiles para situaciones de iluminación típica.

En condiciones de iluminación mixto, los ajustes de balance de blanco tendrá que difiera de la Planckian Locus para realizar los cambios necesarios. En estos casos que será necesario el ajuste se desplazan a verde o rojo/fucsia lado de la CIE escalar.

Dispositivos iOS compensarán las conversiones de color por lo que incrementa la ganancia de color opuesto. Por ejemplo, si una escena tiene demasiados azul, se aumenta la ganancia de rojo para compensar. Ganancia de estos valores estén calibrados para dispositivos específicos para que sean depende del dispositivo.

### <a name="existing-white-balance-controls"></a>Controles existentes de Balance de blanco

iOS 7 y versiones posteriores proporcionan los siguientes controles de Balance de blanco existentes a través de `WhiteBalanceMode` propiedad:

-   `AVCapture WhiteBalance ModeLocked` : Ejemplos de la escena una vez y utilizar esos valores a lo largo de la escena.
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` : Ejemplos de la escena continuamente para asegurarse de que también está equilibrada.


Y la aplicación puede supervisar el `AdjustingWhiteBalance` propiedad para ver cuándo se va a Ajustar exposición.

### <a name="new-white-balance-controls-in-ios-8"></a>Nuevos controles de Balance de blanco en iOS 8

Además de las características que ya le proporcionadas por iOS 7 y versiones posteriores, las siguientes características ahora están disponibles para el control de Balance de blanco en iOS 8:

-  Obtiene el control completamente manual del dispositivo RGB.
-  Get, Set y Observe el valor de clave se obtiene el dispositivo RGB.
-  Compatibilidad con el Balance de blanco con una tarjeta de gris.
-  Rutinas de conversión a y desde los espacios de color independientes del dispositivo.


Para implementar las características anteriores, el `AVCaptureWhiteBalanceGain` estructura se ha agregado con los miembros siguientes:

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


La ganancia de balance de blanco máximo es actualmente cuatro (4) y puede estar listo en el `MaxWhiteBalanceGain` propiedad. Por lo que el rango permitido es de uno (1) `MaxWhiteBalanceGain` (4) actualmente.

El `DeviceWhiteBalanceGains` propiedad puede utilizarse para observar los valores actuales. Use `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` ajustar el balance logra cuando la cámara está en el modo de balance de blanco bloqueado.

#### <a name="conversion-routines"></a>Rutinas de conversión

Rutinas de conversión se han agregado a iOS 8 para ayudar a con la conversión a y desde, espacios de color independientes del dispositivo. Para implementar las rutinas de conversión, el `AVCaptureWhiteBalanceChromaticityValues` estructura se ha agregado con los miembros siguientes:

-   `X` -es un valor entre 0 y 1.
-   `Y` -es un valor entre 0 y 1.


Un `AVCaptureWhiteBalanceTemperatureAndTintValues` estructura también se ha agregado con los miembros siguientes:

-   `Temperature` -flotante es un valor de coma en grados Kelvin.
-   `Tint` -es un desplazamiento de color verde o magenta de 0 a 150 con valores positivos hacia la dirección verde y negativo hacia en el fucsia.


Use la `CaptureDevice.GetTemperatureAndTintValues`y `CaptureDevice.GetDeviceWhiteBalanceGains`métodos para convertir entre temperatura y tono, cromaticidad y RGB obtienen espacios de color.

> [!NOTE]
> **Tenga en cuenta**: las rutinas de conversión son más precisas, cuanto más se acerque el valor que se va a convertir es el Planckian Locus.




#### <a name="gray-card-support"></a>Compatibilidad con tarjetas gris

Apple utiliza el término gris World para hacer referencia a la compatibilidad de tarjeta de gris incorporada iOS 8. Permite al usuario centrarse en una tarjeta física de gris que cubre al menos el 50% del centro del marco y la utiliza para ajustar el Balance de blanco. El propósito de la tarjeta de gris es lograr blanco que aparece neutro.

Esto se puede implementar en una aplicación que hace es pedirle al usuario que coloque una tarjeta física gris delante de la cámara, supervisión del `GrayWorldDeviceWhiteBalanceGains` propiedad y esperar a que los valores relájese.

La aplicación podría bloquear, a continuación, las mejoras de Balance de blanco para la `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` método con los valores de la `GrayWorldDeviceWhiteBalanceGains` propiedad que se va a aplicar los cambios.

El dispositivo de captura se debe bloquear para la configuración para poder realizar un cambio en el Balance de blanco.

### <a name="manual-white-balance-example"></a>Ejemplo de Balance de blanco manual

Con el código de configuración de la captura de AV General en su lugar, un `UIViewController` se pueden agregar a guión gráfico de la aplicación y configurar del modo siguiente:

[ ![](intro-to-manual-camera-controls-images/image18.png "Se puede agregar a las aplicaciones de guión gráfico y configurado como se muestra aquí una UIViewController")](intro-to-manual-camera-controls-images/image18.png)

La vista contiene los siguientes elementos principales:

-  Un `UIImageView` que mostrará la fuente de vídeo.
-  Un `UISegmentedControl` que cambiará el modo de enfoque de automático a bloqueado.
-  Dos `UISlider` controles que se muestran y actualizan la temperatura y el tinte.
-  Un `UIButton` utilizado para el muestreo de un espacio gris tarjeta (gris World) y definir el Balance de blanco con esos valores.


Realice lo siguiente para el controlador de vista de seguridad de conexión para el Control de Balance de blanco Manual:


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
  
1. Agregue el siguiente método privado para establecer la nueva blanco equilibrar la temperatura y el tinte:

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
  
1. Invalidar el `ViewDidAppear` método y agregue el siguiente procedimiento para iniciar la grabación cuando se carga la vista:

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
  
1. Guardar los cambios del código y ejecutar la aplicación.
1. Con la cámara en el modo Auto, los controles deslizantes se moverán automáticamente cuando la cámara ajusta el balance de blanco:

    [![](intro-to-manual-camera-controls-images/image19.png "Los controles deslizantes se moverán automáticamente cuando la cámara ajusta el balance de blanco")](intro-to-manual-camera-controls-images/image19.png)
1. Puntee en el segmento bloqueado y arrastre los controles deslizantes de Temp y tinte para ajustar el balance de blanco manualmente:

    [![](intro-to-manual-camera-controls-images/image20.png "Arrastre los controles deslizantes de Temp y tinte para ajustar el balance de blanco manualmente")](intro-to-manual-camera-controls-images/image20.png)
1. Con el segmento bloqueado aún seleccionado, coloque una tarjeta física gris en la parte frontal de la cámara y puntee el botón de tarjeta de color gris para ajustar el balance de blanco para el mundo gris:

    [![](intro-to-manual-camera-controls-images/image21.png "Puntee en el botón de la tarjeta de color gris para ajustar el balance de blanco para el mundo gris")](intro-to-manual-camera-controls-images/image21.png)
1. Detenga la aplicación.

El código anterior muestra cómo supervisar la configuración de balance de blanco cuando la cámara está en el modo automático o utilizar los controles deslizantes para controlar el balance de blanco cuando se encuentra en el modo bloqueado.

## <a name="bracketed-capture"></a>Captura entre corchetes

La captura se encuentran entre corchetes se basa en la configuración de los controles de cámara Manual presentada anteriormente y permite que la aplicación capturar un momento determinado, en una variedad de maneras diferentes.

En pocas palabras, paréntesis de captura es una ráfaga de imágenes fijas tomadas con una variedad de opciones de imagen para la imagen.

[ ![](intro-to-manual-camera-controls-images/image22.png "Cómo funciona el paréntesis de captura")](intro-to-manual-camera-controls-images/image22.png)

Mediante la captura se encuentran entre corchetes en iOS 8, una aplicación puede preestablecer una serie de controles de la cámara Manual, emite un comando único y la escena actual volver una serie de imágenes para cada uno de los valores preestablecidos del manuales.

### <a name="bracketed-capture-basics"></a>Conceptos básicos de captura entre corchetes

De nuevo, el paréntesis de captura constituye una ráfaga de imágenes fijas tomadas con diversos valores de imagen a imagen. Los tipos de paréntesis de captura disponibles son:

-  **Auto corchete de cierre de exposición** : donde todas las imágenes tienen una cantidad de sesgo variada.
-  **Corchete de cierre de exposición manual** : donde todas las imágenes tienen una velocidad de obturación (duración) e ISO variadas cantidad.
-  **Corchete irrumpir sencillas** : una serie de imágenes fijas realizada en una sucesión rápida.


### <a name="new-bracketed-capture-controls-in-ios-8"></a>Paréntesis de captura controles nuevos en iOS 8

Todos los comandos de paréntesis de captura se implementan en la `AVCaptureStillImageOutput` clase. Use la `CaptureStillImageBracket`método para obtener una serie de imágenes con la matriz especificada de valores.

Se han implementado para administrar la configuración de dos clases nuevas:

-   `AVCaptureAutoExposureBracketedStillImageSettings` : Se tiene una propiedad, `ExposureTargetBias`, que se usa para establecer la diferencia de un corchete de cierre de exposición automática. 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` : Se tiene dos propiedades, `ExposureDuration` y `ISO`, que se usa para establecer la velocidad de obturación e ISO para un corchete de cierre de exposición manual. 


### <a name="bracketed-capture-controls-dos-and-donts"></a>Captura entre corchetes controla los pros y los contras

#### <a name="dos"></a>Evitarse

La siguiente es una lista de los aspectos que debe realizarse si controla el uso de paréntesis de captura en iOS 8:

-  Preparar la aplicación para la situación del peor caso captura mediante una llamada a la `PrepareToCaptureStillImageBracket` método.
-  Se supone que los búferes de ejemplo estarán proceden del mismo grupo compartido.
-  Para liberar la memoria asignada por una llamada anterior de preparación, llame a `PrepareToCaptureStillImageBracket` nuevo y enviar una matriz de un objeto.


#### <a name="donts"></a>Acciones

La siguiente es una lista de tareas que no debe realizarse si controla el uso de paréntesis de captura en iOS 8:

-  No mezcle los tipos de configuración de paréntesis de captura en una única captura.
-  No solicite más de `MaxBracketedCaptureStillImageCount` imágenes en una única captura.


### <a name="bracketed-capture-details"></a>Detalles de captura entre corchetes

Los siguientes detalles deben tenerse en cuenta al trabajar con paréntesis de captura en iOS 8:

-  Invalidación las opciones entre corchetes el `AVCaptureDevice` configuración.
-  Se omite Flash y configuración de estabilización la imagen.
-  Todas las imágenes deben usar el mismo formato de salida (jpeg, png, etcetera.)
-  Vista previa de vídeo puede elimina fotogramas.
-  Captura entre corchetes se admite en todos los dispositivos compatibles con iOS 8.


Con esta información en mente, echemos un vistazo a un ejemplo del uso de paréntesis de captura en iOS 8.

### <a name="bracket-capture-example"></a>Ejemplo de captura de corchete de cierre

Con el código de configuración de la captura de AV General en su lugar, un `UIViewController` se pueden agregar a guión gráfico de la aplicación y configurar del modo siguiente:

[ ![](intro-to-manual-camera-controls-images/image23.png "Se puede agregar a las aplicaciones de guión gráfico y configurado como se muestra aquí una UIViewController")](intro-to-manual-camera-controls-images/image23.png)

La vista contiene los siguientes elementos principales:

-  Un `UIImageView` que mostrará la fuente de vídeo.
-  Tres `UIImageViews` que mostrará los resultados de la captura.
-  Un `UIScrollView` para albergar la fuente de vídeo y vistas de los resultados.
-  Un `UIButton` utiliza para dejar un paréntesis de captura con algunos valores preestablecidos.


Realice lo siguiente para el controlador de vista de cable de seguridad para capturar se encuentran entre corchetes:


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
    
1. Guardar los cambios del código y ejecutar la aplicación.
1. Marco de una escena y puntee en el botón de paréntesis de captura:

    [![](intro-to-manual-camera-controls-images/image24.png "Marco de una escena y puntee en el botón de paréntesis de captura")](intro-to-manual-camera-controls-images/image24.png)
1. Deslice el dedo de derecha a izquierda para ver las tres imágenes realizadas por la captura se encuentran entre corchetes:

    [![](intro-to-manual-camera-controls-images/image25.png "Deslice el dedo de derecha a izquierda para ver las tres imágenes realizadas por la captura se encuentran entre corchetes")](intro-to-manual-camera-controls-images/image25.png)
1. Detenga la aplicación.


El código anterior muestra cómo configurar y realizar una captura de paréntesis de exposición de Auto en iOS 8.

## <a name="summary"></a>Resumen

En este artículo hemos cubierto una introducción a los nuevos controles de cámara Manual proporciona iOS 8 y tratan los conceptos básicos de qué hacen y cómo funcionan. Le hemos dado ejemplos de enfoque Manual, exposición Manual y Balance de blanco Manual. Por último, asignamos un ejemplo realizando un paréntesis de captura mediante los controles de cámara Manual se expuso con anterioridad

## <a name="related-links"></a>Vínculos relacionados

- [ManualCameraControls (ejemplo)](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
