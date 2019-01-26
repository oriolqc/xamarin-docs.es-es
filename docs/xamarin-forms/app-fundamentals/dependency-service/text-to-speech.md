---
title: Implementación de texto a voz
description: En este artículo se explica cómo usar la clase DependencyService de Xamarin.Forms para llamar a la API nativa de texto a voz de cada plataforma.
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: af40dc99dffd6e04ad0d408aea58b23c5446461e
ms.sourcegitcommit: 2ee36611ef667affee7d417db947fbb614d75315
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479763"
---
# <a name="implementing-text-to-speech"></a>Implementación de texto a voz

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

Este artículo le guía durante el proceso de creación de una aplicación multiplataforma que usa [`DependencyService`](xref:Xamarin.Forms.DependencyService) para acceder a API nativas de texto a voz:

- **[Creación de la interfaz](#Creating_the_Interface)** &ndash; comprenda cómo se crea la interfaz en código compartido.
- **[Implementación de iOS](#iOS_Implementation)**: obtenga información sobre cómo implementar la interfaz en código nativo para iOS.
- **[Implementación de Android](#Android_Implementation)**: aprenda a implementar la interfaz en código nativo para Android.
- **[Implementación de UWP](#WindowsImplementation)**: aprenda a implementar la interfaz en código nativo para Plataforma universal de Windows (UWP).
- **[Implementación en código compartido](#Implementing_in_Shared_Code)**: aprenda a usar `DependencyService` para llamar a la implementación nativa desde código compartido.

La aplicación que usa `DependencyService` tiene la siguiente estructura:

![](text-to-speech-images/tts-diagram.png "Estructura de la aplicación DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creación de la interfaz

En primer lugar, cree una interfaz en el código compartido que exprese la funcionalidad que planea implementar. En este ejemplo, la interfaz contiene un solo método, `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

Al codificar en esta interfaz en el código compartido, se permite que la aplicación de Xamarin.Forms acceda a la API de voz en cada plataforma.

> [!NOTE]
> Las clases que implementan la interfaz deben tener un constructor sin parámetros que funcione con `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementación de iOS

La interfaz debe implementarse en el proyecto de aplicación específico de cada plataforma. Tenga en cuenta que la clase tiene un constructor sin parámetros para que `DependencyService` pueda crear nuevas instancias.

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

El atributo `[assembly]` registra la clase como una implementación de la interfaz `ITextToSpeech`, lo que significa que se puede usar `DependencyService.Get<ITextToSpeech>()` en el código compartido para crear una instancia de ella.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementación de Android

El código de Android es más complejo que la versión de iOS. Exige acceso al contexto actual de Android, que se expone mediante la propiedad `MainActivity.Instance`:

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    internal static MainActivity Instance { get; private set; }

    protected override void OnCreate(Bundle bundle)
    {
        ...
    }
}
```

También requiere que la clase que implementa herede de un elemento `Java.Lang.Object` específico de Android e implemente además la interfaz `IOnInitListener`.

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

El atributo `[assembly]` registra la clase como una implementación de la interfaz `ITextToSpeech`, lo que significa que se puede usar `DependencyService.Get<ITextToSpeech>()` en el código compartido para crear una instancia de ella.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementación de Plataforma universal de Windows

Plataforma universal de Windows tiene una API de voz en el espacio de nombres `Windows.Media.SpeechSynthesis`. La única advertencia es acordarse de marcar la capacidad **Micrófono** en el manifiesto, puesto que de lo contrario se bloquea el acceso a las API de voz.

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

El atributo `[assembly]` registra la clase como una implementación de la interfaz `ITextToSpeech`, lo que significa que se puede usar `DependencyService.Get<ITextToSpeech>()` en el código compartido para crear una instancia de ella.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementación en código compartido

Ahora es posible escribir y probar código compartido que accede a la interfaz de texto a voz. Esta sencilla página incluye un botón que desencadena la funcionalidad de voz. Usa `DependencyService` para obtener una instancia de la interfaz `ITextToSpeech` &ndash; en tiempo de ejecución esta instancia es la implementación específica de la plataforma que tiene acceso completo al SDK nativo.

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

Si se ejecuta esta aplicación en iOS, Android o UWP y se presiona el botón, la aplicación habla mediante el SDK de voz nativo en cada plataforma.

 ![Botón de texto a voz de iOS y Android](text-to-speech-images/running.png "Ejemplo de texto a voz")


## <a name="related-links"></a>Vínculos relacionados

- [Uso de DependencyService (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Ejemplo de DependencyService](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
