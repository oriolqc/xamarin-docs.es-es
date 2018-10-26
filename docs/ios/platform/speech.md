---
title: Reconocimiento de voz en Xamarin.iOS
description: En este artículo presenta la nueva API de voz y se muestra cómo se implementa en una aplicación de Xamarin.iOS para admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivo o grabados) en texto.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: c1f488213f9b3be945fd98e09f630c243d0b0d62
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122769"
---
# <a name="speech-recognition-in-xamarinios"></a>Reconocimiento de voz en Xamarin.iOS

_En este artículo presenta la nueva API de voz y se muestra cómo se implementa en una aplicación de Xamarin.iOS para admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivo o grabados) en texto._

Nuevo en iOS 10, Apple ha liberar la API de reconocimiento de voz que permite que una aplicación de iOS admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivo o grabados) en texto.

Función de Apple, la API de reconocimiento de voz tiene las siguientes características y ventajas:

- Alto grado de precisión
- Estado de la técnica
- Fácil de usar
- Rápido
- Admite varios idiomas
- Respeta la privacidad del usuario

## <a name="how-speech-recognition-works"></a>Cómo funciona el reconocimiento de voz

El reconocimiento de voz se implementa en una aplicación iOS mediante la adquisición de audio en vivo o grabado previamente (en cualquiera de los idiomas que admite la API) y los pasa a un reconocedor de voz que devuelve una transcripción de texto sin formato de las palabras habladas.

[![](speech-images/speech01.png "Cómo funciona el reconocimiento de voz")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Dictado del teclado

Al pensar en la mayoría de los usuarios de reconocimiento de voz en un dispositivo iOS, piensa en el Asistente de voz Siri integrado, que se publicó junto con el dictado del teclado en iOS 5 con el iPhone 4S.

El dictado del teclado es compatible con ningún elemento de interfaz que admita TextKit (como `UITextField` o `UITextArea`) y es activado por el usuario hace clic en el botón de dictado (directamente a la izquierda de la barra espaciadora) en el teclado virtual iOS.

Apple ha publicado las siguientes estadísticas el dictado del teclado (recopiladas desde 2011):

- Se ha usado ampliamente el dictado del teclado desde el lanzamiento en iOS 5.
- Aproximadamente 65.000 aplicaciones lo usan al día.
- Acerca de un tercio de todos los iOS dictado se realiza en una aplicación de terceros 3rd.

El dictado del teclado es increíblemente fácil de usar, ya que no requiere ningún esfuerzo por parte del desarrollador, que no sea un elemento de la interfaz TextKit en el diseño de interfaz de usuario de la aplicación. El dictado del teclado también tiene la ventaja de que no requiere las solicitudes de privilegio especial de la aplicación antes de poder usarlo.

Las aplicaciones que usan las nuevas API de reconocimiento de voz, requieren permisos especiales para concederse por el usuario, ya que el reconocimiento de voz requiere la transmisión y almacenamiento temporal de datos en servidores de Apple. Consulte nuestra [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) documentación para obtener más información.

Aunque es fácil de implementar el dictado del teclado, viene con varias limitaciones e inconvenientes:

- Requiere el uso de un campo de entrada de texto y la presentación de un teclado.
- Funciona con solo de entrada de audio en vivo y la aplicación no tiene ningún control sobre el proceso de grabación de audio.
- Proporciona ningún control sobre el lenguaje que se usa para interpretar la voz del usuario.
- No hay ninguna manera para la aplicación sepa si el botón de dictado está aún disponible para el usuario.
- La aplicación no puede personalizar el proceso de grabación de audio.
- Proporciona un conjunto de resultados muy superficial que carece de información como el control de tiempo y confianza.

### <a name="speech-recognition-api"></a>API de reconocimiento de voz

Nuevo en iOS 10, Apple ha publicado la API de reconocimiento de voz que proporciona una forma más eficaz para una aplicación de iOS implementar el reconocimiento de voz. Esta API es el mismo que Apple usa para mejorar Siri y el dictado del teclado y es capaz de proporcionar una transcripción rápida con una precisión de vanguardia.

Los resultados proporcionados por la API de reconocimiento de voz se personalizan de forma transparente a los usuarios individuales, sin la necesidad de recopilar o acceder a los datos privados del usuario de aplicación.

La API de reconocimiento de voz proporciona resultados de vuelta a la aplicación que realiza la llamada en casi en tiempo real como el usuario está hablando y proporciona más información sobre los resultados de la traducción que sólo texto. Se incluyen los siguientes:

- Varias interpretaciones de lo que dice que el usuario.
- Niveles de confianza para las traducciones individuales.
- Información de tiempo.

Como se indicó anteriormente, se puede proporcionar audio para la traducción por una fuente directa, o de origen registrado previamente y en cualquiera de los más de 50 idiomas y lenguajes compatibles con iOS 10.

La API de reconocimiento de voz se puede usar en cualquier dispositivo iOS que ejecutan iOS 10 y en la mayoría de los casos, requiere una conexión activa a internet, ya que la mayor parte de las traducciones lleva a cabo en servidores de Apple. Es decir, algunos iOS más recientes que los dispositivos admiten siempre en, en el dispositivo traducción de idiomas concretos.

Apple ha incluido una API de disponibilidad para determinar si un idioma determinado está disponible para la traducción en el momento actual. La aplicación debe usar esta API en lugar de probar directamente para la conectividad de internet.

Como se mencionó anteriormente en la sección de dictado del teclado, reconocimiento de voz requiere la transmisión y almacenamiento temporal de datos en servidores de Apple a través de internet y como tal, la aplicación _debe_ solicitar permiso del usuario para realizar reconocimiento mediante la inclusión de la `NSSpeechRecognitionUsageDescription` clave en su `Info.plist` archivo y llamar a la `SFSpeechRecognizer.RequestAuthorization` método. 

Según el origen de audio que se va a usar para el reconocimiento de voz, otros cambios en la aplicación `Info.plist` archivo puede ser necesario. Consulte nuestra [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) documentación para obtener más información.

## <a name="adopting-speech-recognition-in-an-app"></a>Adopción de reconocimiento de voz en una aplicación

Hay cuatro pasos principales que el desarrollador debe seguir para adoptar el reconocimiento de voz en una aplicación de iOS:

- Proporcione una descripción de uso en la aplicación `Info.plist` archivo empleando el `NSSpeechRecognitionUsageDescription` clave. Por ejemplo, una aplicación de cámara podría incluir la siguiente descripción, _"Esto le permite tomar una foto indicando la palabra 'queso'."_
- Solicitar autorización mediante una llamada a la `SFSpeechRecognizer.RequestAuthorization` método para presentar una explicación (proporcionada en el `NSSpeechRecognitionUsageDescription` clave anterior) de por qué la aplicación desea voz, reconocimiento de tener acceso al usuario en un cuadro de diálogo y que puedan aceptar o rechazar.
- Cree una solicitud de reconocimiento de voz:
    * Para el audio grabado previamente en el disco, use la `SFSpeechURLRecognitionRequest` clase.
    * Audio en vivo (o audio desde la memoria), use el `SFSPeechAudioBufferRecognitionRequest` clase.
- Pasar la solicitud de reconocimiento de voz a un reconocedor de voz (`SFSpeechRecognizer`) para iniciar el reconocimiento. La aplicación puede, opcionalmente, conserve el valor devuelto `SFSpeechRecognitionTask` para supervisar y realizar un seguimiento de los resultados del reconocimiento.

Estos pasos se tratarán en detalle a continuación.

### <a name="providing-a-usage-description"></a>Proporcione una descripción de uso

Para proporcionar la información necesaria `NSSpeechRecognitionUsageDescription` clave en el `Info.plist` de archivos, realice lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Info.plist` archivo para abrirlo y editarlo.
2. Cambie a la **origen** vista: 

    [![](speech-images/speech02.png "La vista del origen")](speech-images/speech02.png#lightbox)
3. Haga clic en **agregar nueva entrada**, escriba `NSSpeechRecognitionUsageDescription` para el **propiedad**, `String` para el **tipo** y un **descripción de uso** como el **valor**. Por ejemplo: 

    [![](speech-images/speech03.png "Agregar NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. Si la aplicación manejará transcripción del audio en directo, también necesitará una descripción de uso del micrófono. Haga clic en **agregar nueva entrada**, escriba `NSMicrophoneUsageDescription` para el **propiedad**, `String` para el **tipo** y un **descripción de uso** como el **valor**. Por ejemplo: 

    [![](speech-images/speech04.png "Agregar NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
4. Guarde los cambios en el archivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el `Info.plist` archivo para abrirlo y editarlo.
3. Haga clic en **agregar nueva entrada**, escriba `NSSpeechRecognitionUsageDescription` para el **propiedad**, `String` para el **tipo** y un **descripción de uso** como el **valor**. Por ejemplo: 

    [![](speech-images/speech03w.png "Agregar NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
4. Si la aplicación manejará transcripción del audio en directo, también necesitará una descripción de uso del micrófono. Haga clic en **agregar nueva entrada**, escriba `NSMicrophoneUsageDescription` para el **propiedad**, `String` para el **tipo** y un **descripción de uso** como el **valor**. Por ejemplo: 

    [![](speech-images/speech04w.png "Agregar NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. Guarde los cambios en el archivo.

-----

> [!IMPORTANT]
> No se puede proporcionar cualquiera de las anteriores `Info.plist` claves (`NSSpeechRecognitionUsageDescription` o `NSMicrophoneUsageDescription`) puede dar lugar a la aplicación que se producía un error sin advertencia al intentar tener acceso el reconocimiento de voz o el micrófono de audio en vivo.




### <a name="requesting-authorization"></a>Solicitud de autorización

Para solicitar la autorización de usuario necesarios que permite que la aplicación tener acceso a reconocimiento de voz, modifique la clase de controlador de vista principal y agregue el código siguiente:

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

El `RequestAuthorization` método de la `SFSpeechRecognizer` clase solicitará permiso del usuario para el reconocimiento de voz de acceso con el motivo de que el desarrollador proporcionado en el `NSSpeechRecognitionUsageDescription` clave de la `Info.plist` archivo.

Un `SFSpeechRecognizerAuthorizationStatus` resultado se devuelve a la `RequestAuthorization` rutina de devolución de llamada del método que se puede usar para tomar medidas según el permiso del usuario. 

> [!IMPORTANT]
> Apple sugiere lo espera hasta que el usuario ha iniciado una acción en la aplicación que requiere el reconocimiento de voz antes de solicitar este permiso.

### <a name="recognizing-pre-recorded-speech"></a>Reconocimiento de voz grabado previamente

Si desea que la aplicación para que reconozca la voz de un archivo WAV o MP3 registrado previamente, puede usar el código siguiente:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

Mirar este código en detalle, en primer lugar, intenta crear un reconocedor de voz (`SFSpeechRecognizer`). Si no se admite el idioma predeterminado para el reconocimiento de voz, `null` se devuelve y las salidas de funciones.

Si el reconocedor de voz está disponible para el idioma predeterminado, la aplicación se comprueba para ver si está actualmente disponible para su reconocimiento mediante la `Available` propiedad. Por ejemplo, los recognition no esté disponible si el dispositivo no tiene una conexión activa a internet.

Un `SFSpeechUrlRecognitionRequest` creada a partir de la `NSUrl` ubicación del archivo grabado previamente en el dispositivo iOS y se entrega al reconocedor de voz para procesar con una rutina de devolución de llamada.

Cuando se llama a la devolución de llamada, si la `NSError` no `null` se ha producido un error que se debe controlar. Dado que el reconocimiento de voz se realiza de forma incremental, la rutina de devolución de llamada se puede llamar más de una vez por lo que la `SFSpeechRecognitionResult.Final` propiedad se prueba para ver si la traducción se completará y se escribe mejor versión de la traducción (`BestTranscription`).

### <a name="recognizing-live-speech"></a>Reconocimiento de voz en vivo

Si desea que la aplicación para que reconozca la voz en vivo, el proceso es muy similar al reconocimiento de voz grabado previamente. Por ejemplo:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

Mirar este código en detalle, se crean varias variables privadas para controlar el proceso de reconocimiento:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

Usa AV Foundation para grabar audio que se pasará a un `SFSpeechAudioBufferRecognitionRequest` para atender la solicitud de reconocimiento:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

La aplicación intenta iniciar la grabación y se controlan los errores si no se puede iniciar la grabación:

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

La tarea de reconocimiento se inicia y se mantiene un identificador a la tarea de reconocimiento (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

La devolución de llamada se usa de forma similar al usado anteriormente en voz grabado previamente.

Si la grabación se ha detenido por el usuario, se informaren el motor de Audio y la solicitud de reconocimiento de voz:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Si el usuario cancela el reconocimiento, se informan el motor de Audio y la tarea de reconocimiento:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

Es importante llamar a `RecognitionTask.Cancel` si el usuario cancela la traducción para liberar memoria y procesador del dispositivo.

> [!IMPORTANT]
> No se puede proporcionar el `NSSpeechRecognitionUsageDescription` o `NSMicrophoneUsageDescription` `Info.plist` claves pueden dar lugar a la aplicación que se producía un error sin advertencia al intentar tener acceso el reconocimiento de voz o el micrófono de audio en vivo (`var node = AudioEngine.InputNode;`). Consulte la **que proporciona una descripción de uso** sección anterior para obtener más información.

## <a name="speech-recognition-limits"></a>Límites de reconocimiento de voz

Apple impone las siguientes limitaciones cuando se trabaja con el reconocimiento de voz en una aplicación de iOS:

- Reconocimiento de voz está disponible para todas las aplicaciones, pero su uso no es ilimitado:
    - Los dispositivos iOS individuales tienen un número limitado de reconocimientos que pueden realizarse por día.
    - Las aplicaciones se limitarán globalmente según una solicitud al día.
- La aplicación debe estar preparada para controlar la conexión de red de reconocimiento de voz y errores de límite de tasa de uso.
- El reconocimiento de voz puede tener un alto costo de la batería se agote rápidamente y tráfico de red elevado en el dispositivo del usuario de iOS, por este motivo, Apple impone un límite de duración del audio estricta de aproximadamente un minuto de voz max.

Si una aplicación habitualmente alcanza su límite de limitación de velocidad, Apple solicita que el desarrollador póngase en contacto con ellos.

## <a name="privacy-and-usability-considerations"></a>Privacidad y las consideraciones de facilidad de uso

Apple tiene la siguiente sugerencia para que se transparente y respetando la privacidad del usuario al incluir el reconocimiento de voz en una aplicación de iOS:

- Cuando se graba la voz del usuario, asegúrese de indicar claramente que la grabación está teniendo lugar en la interfaz de usuario de la aplicación. Por ejemplo, la aplicación puede reproducir el sonido de un "grabación" y mostrar un indicador de grabación.
- No utilice el reconocimiento de voz para obtener información confidencial del usuario como contraseñas, datos de estado o información financiera.
- Mostrar los resultados del reconocimiento _antes_ actúe en ellos. Esto no sólo ofrece informes acerca de la aplicación lo está haciendo, pero permite al usuario controlar los errores de reconocimiento que están realizando.

## <a name="summary"></a>Resumen

En este artículo tiene presenta la nueva API de voz y se ha explicado cómo implementarlo en una aplicación de Xamarin.iOS para admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivo o grabados) en texto. 



## <a name="related-links"></a>Vínculos relacionados

- [SpeakToMe (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
