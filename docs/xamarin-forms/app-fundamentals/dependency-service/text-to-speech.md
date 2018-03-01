---
title: "Implementación de texto a voz"
description: Usar DependencyService para llamar a API de texto a voz nativo de la plataforma
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: c5bd753aaa3a9e807a03a9fb05b233cfa39d0dc3
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="implementing-text-to-speech"></a>Implementación de texto a voz

En este artículo le ayudará a medida que cree una aplicación multiplataforma que use [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) para tener acceso a las API nativas de texto a voz:

- **[Creación de la interfaz](#Creating_the_Interface)**  &ndash; comprender cómo se crea la interfaz de código compartido.
- **[Implementación de iOS](#iOS_Implementation)**  &ndash; obtener información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**  &ndash; obtener información sobre cómo implementar la interfaz en código nativo para Android.
- **[Implementación de Windows](#WindowsImplementation)**  &ndash; obtener información sobre cómo implementar la interfaz en código nativo para Windows Phone y la plataforma Universal de Windows (UWP).
- **[Implementar en el código compartido](#Implementing_in_Shared_Code)**  &ndash; obtener información sobre cómo usar `DependencyService` para llamar a la implementación nativa desde código compartido.

La aplicación que utiliza `DependencyService` tendrá la estructura siguiente:

![](text-to-speech-images/tts-diagram.png "Estructura de aplicación DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

En primer lugar, cree una interfaz en el código compartido que expresa la funcionalidad que desee implementar. En este ejemplo, la interfaz contiene un método único, `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

Codificar en esta interfaz en el código compartido le permitirá que la aplicación de Xamarin.Forms tener acceso a las API de la oración en cada plataforma.

> [!NOTE]
> **Tenga en cuenta**: las clases que implementan la interfaz deben tener un constructor sin parámetros para que funcione con el `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

La interfaz se debe implementar en cada proyecto de aplicación específico de la plataforma. Tenga en cuenta que la clase tiene un constructor sin parámetros para que la `DependencyService` puede crear nuevas instancias.

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

El `[assembly]` atributo registra la clase como una implementación de la `ITextToSpeech` interfaz, lo que significa que `DependencyService.Get<ITextToSpeech>()` puede utilizarse en el código compartido para crear una instancia del mismo.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

El código de Android es más complejo que la versión de iOS: requiere la clase de implementación para heredar de específicos de Android `Java.Lang.Object` e implementar el `IOnInitListener` interfaz así.

Xamarin.Forms ofrece también la `Forms.Context` objeto, que es una instancia del contexto de Android actual. Muchos métodos de SDK de Android requieren esta opción, un buen ejemplo que se va a la capacidad de llamar a `StartActivity()`.

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
                speaker = new TextToSpeech(Forms.Context, this);
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

El `[assembly]` atributo registra la clase como una implementación de la `ITextToSpeech` interfaz, lo que significa que `DependencyService.Get<ITextToSpeech>()` puede utilizarse en el código compartido para crear una instancia del mismo.

<a name="WindowsImplementation" />

## <a name="windows-phone-and-universal-windows-platform-implementation"></a>Windows Phone y la implementación de la plataforma Universal de Windows

Windows Phone y la plataforma Universal de Windows tienen una API de voz en el `Windows.Media.SpeechSynthesis` espacio de nombres. La única advertencia es recordar marca la **micrófono** capacidad en el manifiesto, en caso contrario, el acceso a la voz se bloquean las API.

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

El `[assembly]` atributo registra la clase como una implementación de la `ITextToSpeech` interfaz, lo que significa que `DependencyService.Get<ITextToSpeech>()` puede utilizarse en el código compartido para crear una instancia del mismo.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementar en el código compartido

Ahora podemos escribir y probar el código compartido que tiene acceso a la interfaz de texto a voz. Esta página simple incluye un botón que activa la funcionalidad de voz. Usa el `DependencyService` para obtener una instancia de la `ITextToSpeech` interfaz &ndash; en tiempo de ejecución esta instancia será la implementación específica de la plataforma que tiene acceso completo a lo SDK nativo.

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

Ejecutar esta aplicación en iOS, Android, o las plataformas de Windows y presione el botón dará como resultado de la aplicación general mediante el SDK de voz nativa en cada plataforma.

 ![iOS y Android botón texto a voz](text-to-speech-images/running.png "ejemplo de texto a voz")


## <a name="related-links"></a>Vínculos relacionados

- [Uso de DependencyService (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
- [Libro de texto a voz](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/text-to-speech/text-to-speech.workbook)
