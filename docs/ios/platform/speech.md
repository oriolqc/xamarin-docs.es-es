---
title: Reconocimiento de voz
description: Este artículo presenta la nueva API de voz y muestra cómo se implementa en una aplicación Xamarin.iOS para admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivos o grabados) en texto.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: fa010f57d163cabe544176608cff2eb6efe872ad
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="speech-recognition"></a>Reconocimiento de voz

_Este artículo presenta la nueva API de voz y muestra cómo se implementa en una aplicación Xamarin.iOS para admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivos o grabados) en texto._

Nuevo en 10 de iOS, Apple ha liberar la API de reconocimiento de voz que permite que una aplicación iOS admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivos o grabados) en texto.

Función de Apple, la API de reconocimiento de voz tiene las siguientes características y ventajas:

- Alto grado de precisión
- Estado de la técnica
- Fácil de usar
- Rápido
- Admite varios idiomas
- Aspectos privacidad del usuario

## <a name="how-speech-recognition-works"></a>Cómo funciona el reconocimiento de voz

El reconocimiento de voz se implementa en una aplicación de iOS al adquirir el audio en vivo o grabado previamente (en cualquiera de los idiomas que admite la API) y pasarla a un reconocedor de voz que devuelve una transcripción de texto sin formato de las palabras.

[![](speech-images/speech01.png "Cómo funciona el reconocimiento de voz")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Dictado de teclado

Cuando cree la mayoría de los usuarios de reconocimiento de voz en un dispositivo iOS, piensan del Asistente de voz Siri integrado, que se publicó junto con teclado dictado en iOS 5 con la 4S iPhone.

Dictado de teclado es compatible con cualquier elemento de la interfaz que admite TextKit (como `UITextField` o `UITextArea`) y se activa por el usuario hace clic en el botón de dictado (directamente a la izquierda de la barra espaciadora) en el teclado virtual iOS.

Apple ha publicado las siguientes estadísticas de dictado de teclado (recopiladas desde 2011):

- Dictado de teclado ha ampliamente usado desde que se publicó en iOS 5.
- Aproximadamente 65.000 aplicaciones usan al día.
- Acerca de un tercio de las operaciones de E/s dictado se realiza en una aplicación 3.

Dictado de teclado es muy fácil de usar, ya que no requiere ningún esfuerzo por parte del desarrollador, que no sea un elemento de la interfaz TextKit en diseño de interfaz de usuario de la aplicación. Dictado de teclado también tiene la ventaja de que no requiere ninguna solicitud de privilegio especial de la aplicación antes de poder utilizarlo.

Aplicaciones que usan las nuevas API de reconocimiento de voz requieren permisos especiales para que se conceda por el usuario, puesto que el reconocimiento de voz requiere la transmisión y el almacenamiento temporal de datos en servidores de Apple. Visite nuestro [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) documentación para obtener más información.

Si bien es fácil de implementar dictado de teclado, proceder con varias desventajas y limitaciones:

- Requiere el uso de un campo de entrada de texto y la presentación de un teclado.
- Funciona con solo de entrada de audio en vivo y la aplicación no tiene ningún control sobre el proceso de grabación de audio.
- No proporciona ningún control sobre el idioma que se usa para interpretar la voz del usuario.
- No hay ninguna manera de que la aplicación saber si el botón de dictado es incluso disponible para el usuario.
- La aplicación no puede personalizar el proceso de grabación de audio.
- Proporciona un conjunto de resultados muy superficial que carece de información como el tiempo y la confianza.

### <a name="speech-recognition-api"></a>API de reconocimiento de voz

Nuevo en 10 de iOS, Apple ha lanzado la API de reconocimiento de voz que proporciona una forma más eficaz para una aplicación de iOS implementar el reconocimiento de voz. Esta API es la misma que usa Apple encendido Siri y teclado dictado y es capaz de proporcionar la transcripción de rápida con una precisión de estado de la técnica.

Los resultados proporcionados por la API de reconocimiento de voz se personalizan forma transparente a los usuarios individuales, sin la necesidad de recopilar o acceder a los datos privados del usuario de aplicación.

La API de reconocimiento de voz proporciona resultados a la aplicación que realiza la llamada en casi en tiempo real, tal y como se habla el usuario y proporciona más información sobre los resultados de la traducción que sólo texto. Se incluyen los siguientes:

- Varias interpretaciones de lo que dice que el usuario.
- Niveles de confianza para las traducciones individuales.
- Información de tiempo.

Como se mencionó anteriormente, audio de traducción puede proporcionarse mediante una fuente directa, o de origen previamente grabado y en cualquiera de los más de 50 idiomas y lenguajes compatibles con iOS 10.

La API de reconocimiento de voz se puede usar en cualquier dispositivo de iOS con iOS 10 y en la mayoría de los casos, requiere una conexión activa a internet, ya que la mayor parte de las traducciones tiene lugar en los servidores de Apple. Es decir, algunas iOS más reciente compatible con dispositivos siempre en, en el dispositivo traducción de idiomas concretos.

Apple ha incluido una API de disponibilidad para determinar si un idioma determinado está disponible para su traducción en el momento actual. La aplicación debe usar esta API en lugar de probar directamente para la conectividad de internet.

Como se mencionó anteriormente en la sección de teclado dictado, el reconocimiento de voz requiere la transmisión y el almacenamiento temporal de datos en servidores de Apple a través de internet y, como tal, la aplicación _debe_ solicitar el permiso del usuario para realizar reconocimiento mediante la inclusión de la `NSSpeechRecognitionUsageDescription` clave en su `Info.plist` archivo y llamar a la `SFSpeechRecognizer.RequestAuthorization` método. 

Según el origen del audio que se va a usar para el reconocimiento de voz, otros cambios en la aplicación `Info.plist` archivo puede ser necesario. Visite nuestro [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) documentación para obtener más información.

## <a name="adopting-speech-recognition-in-an-app"></a>Al adoptar el reconocimiento de voz en una aplicación

Hay cuatro pasos principales que el programador debe asumir que deben adoptarse en el reconocimiento de voz en una aplicación de iOS:

- Proporcione una descripción de uso en la aplicación `Info.plist` archivo utilizando el `NSSpeechRecognitionUsageDescription` clave. Por ejemplo, una aplicación de la cámara puede incluir la siguiente descripción _"Esto le permite tomar una foto haciendo que indica que la palabra 'cheese'."_
- Solicitar autorización mediante una llamada a la `SFSpeechRecognizer.RequestAuthorization` método para presentar una explicación (proporcionada en el `NSSpeechRecognitionUsageDescription` clave anterior) de por qué la aplicación desea voz reconocimiento de tener acceso al usuario en un cuadro de diálogo y que puedan aceptar o rechazar.
- Cree una solicitud de reconocimiento de voz:
    * Para que el audio grabado previamente en el disco, use la `SFSpeechURLRecognitionRequest` clase.
    * Para audio en vivo (o audio de la memoria), utilice la `SFSPeechAudioBufferRecognitionRequest` clase.
- Pasar la solicitud de reconocimiento de voz a un reconocedor de voz (`SFSpeechRecognizer`) para iniciar el reconocimiento. La aplicación puede contener opcionalmente en el valor devuelto `SFSpeechRecognitionTask` para supervisar y realizar un seguimiento de los resultados del reconocimiento.

Estos pasos se explicará en detalle a continuación.

### <a name="providing-a-usage-description"></a>Proporciona una descripción de uso

Para proporcionar los necesarios `NSSpeechRecognitionUsageDescription` clave en el `Info.plist` de archivos, realice lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en el `Info.plist` archivo para abrirlo y editarlo.
2. Cambie a la **origen** vista: 

    [![](speech-images/speech02.png "La vista del origen")](speech-images/speech02.png#lightbox)
3. Haga clic en **agregar nueva entrada**, escriba `NSSpeechRecognitionUsageDescription` para el **propiedad**, `String` para el **tipo** y un **descripción de uso** como el **valor**. Por ejemplo: 

    [![](speech-images/speech03.png "Agregar NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. Si la aplicación vaya a procesar la transcripción de audio en directo, también necesitará una descripción de uso de micrófono. Haga clic en **agregar nueva entrada**, escriba `NSMicrophoneUsageDescription` para el **propiedad**, `String` para el **tipo** y un **descripción de uso** como el **valor**. Por ejemplo: 

    [![](speech-images/speech04.png "Agregar NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
4. Guarde los cambios en el archivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en el `Info.plist` archivo para abrirlo y editarlo.
3. Haga clic en **agregar nueva entrada**, escriba `NSSpeechRecognitionUsageDescription` para el **propiedad**, `String` para el **tipo** y un **descripción de uso** como el **valor**. Por ejemplo: 

    [![](speech-images/speech03w.png "Agregar NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
4. Si la aplicación vaya a procesar la transcripción de audio en directo, también necesitará una descripción de uso de micrófono. Haga clic en **agregar nueva entrada**, escriba `NSMicrophoneUsageDescription` para el **propiedad**, `String` para el **tipo** y un **descripción de uso** como el **valor**. Por ejemplo: 

    [![](speech-images/speech04w.png "Agregar NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. Guarde los cambios en el archivo.

-----

> [!IMPORTANT]
> No poder proporcionar uno de los pasos anteriores `Info.plist` claves (`NSSpeechRecognitionUsageDescription` o `NSMicrophoneUsageDescription`) puede dar lugar a la aplicación que se produzca un error sin previo aviso cuando intenta acceder al reconocimiento de voz o el micrófono de audio en vivo.




### <a name="requesting-authorization"></a>Solicite la autorización

Para solicitar la autorización de usuarios requerido que permite que la aplicación tener acceso a reconocimiento de voz, modifique la clase de controlador de la vista principal y agregue el código siguiente:

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

El `RequestAuthorization` método de la `SFSpeechRecognizer` clase solicitará permiso del usuario para el reconocimiento de voz de acceso con el motivo por el que el desarrollador proporcionado en el `NSSpeechRecognitionUsageDescription` clave de la `Info.plist` archivo.

A `SFSpeechRecognizerAuthorizationStatus` resultado se devuelve a la `RequestAuthorization` rutina de devolución de llamada del método que puede utilizarse para tomar medidas tomando como base el permiso del usuario. 

> [!IMPORTANT]
> Apple sugiere espera hasta que el usuario ha iniciado una acción en la aplicación que requiere el reconocimiento de voz antes de solicitar este permiso.

### <a name="recognizing-pre-recorded-speech"></a>Reconocimiento de voz grabado previamente

Si desea que la aplicación para que reconozca la voz de un archivo WAV o MP3 previamente grabado, puede usar el código siguiente:

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

Se trata de este código en detalle, en primer lugar, intenta crear un reconocedor de voz (`SFSpeechRecognizer`). Si no se admite el idioma predeterminado para el reconocimiento de voz `null` se devuelve y las salidas de funciones.

Si el reconocedor de voz está disponible para el idioma predeterminado, la aplicación se comprueba para ver si está disponible actualmente para usar el reconocimiento del `Available` propiedad. Por ejemplo, el reconocimiento no estén disponible si el dispositivo no tiene una conexión activa a internet.

A `SFSpeechUrlRecognitionRequest` se crea a partir del `NSUrl` ubicación del archivo grabado previamente en el dispositivo iOS y se va a pasar al reconocedor de voz para procesar con una rutina de devolución de llamada.

Cuando se llama a la devolución de llamada, si la `NSError` no `null` se ha producido un error que debe ser administrado. Dado que el reconocimiento de voz se realiza de forma incremental, la rutina de devolución de llamada se puede llamar más de una vez por lo que la `SFSpeechRecognitionResult.Final` propiedad se prueba para ver si la traducción se completará y se escribe mejor versión de la traducción (`BestTranscription`).

### <a name="recognizing-live-speech"></a>Reconocimiento de voz en vivo

Si desea que la aplicación para que reconozca la voz en vivo, el proceso es muy similar a reconocimiento de voz grabado previamente. Por ejemplo:

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

Se trata de este código en detalle, se crean varias variables privadas para controlar el proceso de reconocimiento:

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

Se inició la tarea de reconocimiento y se mantiene un identificador a la tarea de reconocimiento (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

La devolución de llamada se usa de forma similar al utilizado anteriormente en voz grabado previamente.

Si la grabación se ha detenido por el usuario, el motor de Audio y la solicitud de reconocimiento de voz son informados:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Si el usuario cancela el reconocimiento, se informaren el motor de Audio y la tarea de reconocimiento:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

Es importante llamar a `RecognitionTask.Cancel` si el usuario cancela la traducción para liberar memoria y procesador del dispositivo.

> [!IMPORTANT]
> No puede proporcionar el `NSSpeechRecognitionUsageDescription` o `NSMicrophoneUsageDescription` `Info.plist` claves pueden dar lugar a la aplicación que se produzca un error sin previo aviso cuando intenta acceder al reconocimiento de voz o el micrófono de audio en vivo (`var node = AudioEngine.InputNode;`). Vea la **proporciona una descripción de uso** sección anterior para obtener más información.

## <a name="speech-recognition-limits"></a>Límites de reconocimiento de voz

Apple impone las siguientes limitaciones cuando se trabaja con reconocimiento de voz en una aplicación de iOS:

- Reconocimiento de voz está disponible para todas las aplicaciones, pero su uso no es ilimitado:
    - Dispositivos iOS individuales tienen un número limitado de reconocimientos que pueden realizarse por día.
    - Las aplicaciones se limitarán globalmente según una solicitud al día.
- La aplicación debe estar preparada para controlar la conexión de red de reconocimiento de voz y errores de límite de frecuencia de uso.
- El reconocimiento de voz puede tener un coste más elevado de purga de la batería y tráfico de red elevado en el dispositivo del usuario iOS, por este motivo, Apple impone un límite de duración de audio estricta de aproximadamente un minuto de voz max.

Si una aplicación habitualmente alcanza su límite de limitación de velocidad, Apple pide que el desarrollador póngase en contacto con ellos.

## <a name="privacy-and-usability-considerations"></a>Privacidad y las consideraciones de facilidad de uso

Apple tiene la siguiente sugerencia para que se transparente y respeta la privacidad del usuario cuando el reconocimiento de voz se incluyen en una aplicación de iOS:

- Al grabar la voz del usuario, asegúrese de indicar claramente que la grabación está teniendo lugar en la interfaz de usuario de la aplicación. Por ejemplo, la aplicación puede reproducir el sonido de un "grabar" y muestra un indicador de grabación.
- No use el reconocimiento de voz para información confidencial del usuario, como contraseñas, datos de estado o información financiera.
- Mostrar los resultados del reconocimiento _antes de_ actúe en ellos. Esto no solo proporciona comentarios en cuanto a la aplicación lo está haciendo, pero permite al usuario controlar los errores de reconocimiento que están realizando.

## <a name="summary"></a>Resumen

En este artículo se presenta la nueva API de voz y se ha explicado cómo se implementa en una aplicación Xamarin.iOS para admitir el reconocimiento de voz continua y transcribir voz (de secuencias de audio en vivos o grabados) en texto. 



## <a name="related-links"></a>Vínculos relacionados

- [SpeakToMe (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
