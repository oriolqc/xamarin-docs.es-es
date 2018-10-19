---
title: Implementación de texto a voz
description: En este artículo se explica cómo usar la clase Xamarin.Forms DependencyService para llamar a la API nativa de texto a voz de cada plataforma.
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: 6d1948214b97a1b536b07b6420c32e4d27124518
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "38997548"
---
# <a name="implementing-text-to-speech"></a>Implementación de texto a voz

En este artículo le ayudará a medida que cree una aplicación multiplataforma que usa [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) para tener acceso a las API nativas de texto a voz:

- **[Creación de la interfaz](#Creating_the_Interface)**  &ndash; comprender cómo se crea la interfaz en código compartido.
- **[Implementación de iOS](#iOS_Implementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para Android.
- **[Implementación de UWP](#WindowsImplementation)**  &ndash; Obtenga información sobre cómo implementar la interfaz en código nativo para la plataforma Universal de Windows (UWP).
- **[Implementar en código compartido](#Implementing_in_Shared_Code)**  &ndash; Aprenda a usar `DependencyService` para llamar a la implementación nativa desde código compartido.

La aplicación mediante `DependencyService` tendrá la siguiente estructura:

![](text-to-speech-images/tts-diagram.png "Estructura de la aplicación de DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

En primer lugar, cree una interfaz en el código compartido que expresa la funcionalidad que se va a implementar. En este ejemplo, la interfaz contiene un único método, `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

Codificar esta interfaz en el código compartido permitirá que la aplicación de Xamarin.Forms tener acceso a la API de voz en cada plataforma.

> [!NOTE]
> Las clases que implementan la interfaz deben tener un constructor sin parámetros para que funcione con el `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

La interfaz debe implementarse en cada proyecto de aplicación específico de la plataforma. Tenga en cuenta que la clase tiene un constructor sin parámetros para que el `DependencyService` puede crear nuevas instancias.

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.iOS
{

    public class TextToSpeechImplementation : ITextToSpeech
    {
        public TextToSpeechImplementation() { }

        public void Speak(string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer();
            var speechUtterance = new AVSpeechUtterance(text)
            {
                Rate = AVSpeechUtterance.MaximumSpeechRate / 4,
                Voice = AVSpeechSynthesisVoice.FromLanguage("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance(speechUtterance);
        }
    }
}
```

El `[assembly]` atributo registra la clase como una implementación de la `ITextToSpeech` interfaz, lo que significa que `DependencyService.Get<ITextToSpeech>()` puede usarse para crear una instancia de ella en el código compartido.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

El código de Android es más complejo que la versión de iOS: requiere que la clase de implementación para heredar de Android específicos `Java.Lang.Object` e implementar el `IOnInitListener` interfaz también. También requiere acceso al contexto actual de Android, que se expone mediante el `MainActivity.Instance` propiedad.

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.Droid
{
    public class TextToSpeechImplementation : Java.Lang.Object, ITextToSpeech, TextToSpeech.IOnInitListener
    {
        TextToSpeech speaker;
        string toSpeak;

        public void Speak(string text)
        {
            toSpeak = text;
            if (speaker == null)
            {
                speaker = new TextToSpeech(MainActivity.Instance, this);
            }
            else
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }

        public void OnInit(OperationResult status)
        {
            if (status.Equals(OperationResult.Success))
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }
    }
}
```

El `[assembly]` atributo registra la clase como una implementación de la `ITextToSpeech` interfaz, lo que significa que `DependencyService.Get<ITextToSpeech>()` puede usarse para crear una instancia de ella en el código compartido.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementación de la plataforma universal de Windows

La plataforma Universal de Windows tiene una API de voz en el `Windows.Media.SpeechSynthesis` espacio de nombres. La única salvedad es que no se olvide de graduación el **micrófono** capacidad en el manifiesto, en caso contrario, el acceso a la speech API se bloquean.

```csharp
[assembly:Dependency(typeof(TextToSpeechImplementation))]
public class TextToSpeechImplementation : ITextToSpeech
{
    public async void Speak(string text)
    {
        var mediaElement = new MediaElement();
        var synth = new Windows.Media.SpeechSynthesis.SpeechSynthesizer();
        var stream = await synth.SynthesizeTextToStreamAsync(text);

        mediaElement.SetSource(stream, stream.ContentType);
        mediaElement.Play();
    }
}
```

El `[assembly]` atributo registra la clase como una implementación de la `ITextToSpeech` interfaz, lo que significa que `DependencyService.Get<ITextToSpeech>()` puede usarse para crear una instancia de ella en el código compartido.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementar en código compartido

Ahora podemos escribir y probar el código compartido que tiene acceso a la interfaz de texto a voz. Esta página simple incluye un botón que desencadena la funcionalidad de voz. Usa el `DependencyService` para obtener una instancia de la `ITextToSpeech` interfaz &ndash; en tiempo de ejecución esta instancia será la implementación específica de la plataforma que tiene acceso completo a lo SDK nativo.

```csharp
public MainPage ()
{
    var speak = new Button {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    speak.Clicked += (sender, e) => {
        DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
    };
    Content = speak;
}
```

Ejecutar la aplicación en iOS, Android o UWP y presione el botón dará como resultado de la aplicación hablar con usted mediante de speech SDK nativa en cada plataforma.

 ![iOS y Android botón texto a voz](text-to-speech-images/running.png "ejemplo de texto a voz")


## <a name="related-links"></a>Vínculos relacionados

- [Uso de DependencyService (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)

