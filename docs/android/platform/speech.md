---
title: Voz Android
description: "Este artículo tratan los aspectos básicos del uso del espacio de nombres Android.Speech muy eficaces. Desde su creación, Android ha sido capaz de reconocer voz y salida como texto. Es un proceso relativamente sencillo. Para texto a voz, sin embargo, el proceso es más complicado, no solo el motor de voz tenga que tener en cuenta, pero también los idiomas disponibles e instalados en el sistema de texto a voz (TTS)."
ms.topic: article
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 7c38ebb6b482f4097a4977accecc4a230d3f3ed3
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="android-speech"></a>Voz Android

_Este artículo tratan los aspectos básicos del uso del espacio de nombres Android.Speech muy eficaces. Desde su creación, Android ha sido capaz de reconocer voz y salida como texto. Es un proceso relativamente sencillo. Para texto a voz, sin embargo, el proceso es más complicado, no solo el motor de voz tenga que tener en cuenta, pero también los idiomas disponibles e instalados en el sistema de texto a voz (TTS)._

## <a name="speech-overview"></a>Introducción a la voz

Tener un sistema que "comprende" voz humana y enunciates lo que se escribe, voz a texto y texto a voz, es un área cada vez mayor de desarrollo de aplicaciones móvil a medida que aumente la demanda de comunicación natural con nuestros dispositivos. Existen muchas instancias donde tiene una característica que convierte texto a voz, o viceversa, es una herramienta muy útil para incorporar a la aplicación android.

Por ejemplo, clamp hacia abajo en el uso del teléfono móvil al automóvil, los usuarios desean manos libres de manera operativo sus dispositivos. La gran cantidad de factores de forma diferente de Android, como Android desgaste, y la inclusión de ampliación nunca de ellas puede usar dispositivos Android (por ejemplo, tabletas y blocs de notas), ha creado un enfoque más grande en excelentes aplicaciones TTS.

Google Proporciona al desarrollador de un amplio conjunto de API en el espacio de nombres Android.Speech para cubrir la mayoría de las instancias de hacer que un dispositivo "compatible con voz" (por ejemplo, software diseñado for the blind).  El espacio de nombres incluye la funcionalidad para permitir que el texto que se deben traducir en voz a través de `Android.Speech.Tts`, control sobre el motor utilizado para realizar la traducción, así como un número de `RecognizerIntent`s que permiten voz se convierta en texto.

Mientras que las funciones existen para que voz a entender, existen limitaciones en función del hardware utilizado. No es probable que el dispositivo interpretar correctamente todo lo que se habla en todos los idiomas disponibles.

## <a name="requirements"></a>Requisitos

No hay ningún requisito especial para esta guía, que no sea el dispositivo que tiene un micrófono y altavoces.

El núcleo de un dispositivo Android interpretación de voz es el uso de un `Intent` con su correspondiente `OnActivityResult`.
Sin embargo, es importante reconocer que no se entiende la voz, pero interpretado en texto. La diferencia es importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>La diferencia entre comprender e interpretar

Una definición simple de entender es que es posible determinar el significado real de lo que se está tratando tono y el contexto. Para interpretar simplemente significa tomar las palabras y generarlos en otro formato.

Tenga en cuenta este sencillo ejemplo que se utiliza en todos los días conversación: 

<kbd>¿Hola cómo estás?</kbd>

Sin inflexiones (centraba en partes de palabras o palabras específicas), es una pregunta sencilla. Sin embargo, si se aplica a la lentitud a la línea, la persona que escucha detectará que el autor de la pregunta no es demasiado satisfecho y quizás necesita cheering o que el autor de la pregunta es encontrarse mal. Si se pone el énfasis en "are", la persona que solicite es normalmente más interesada en la respuesta.

Sin procesar de audio bastante eficaz el uso de la inflexión y un grado de inteligencia artificial (AI) para entender el contexto, el software incluso no puede empezar a entender lo que se dijo: el mejor un teléfono simple posible es convertir la voz en texto.

## <a name="setting-up"></a>Configuración

Antes de usar el sistema de voz, siempre es recomendable comprobar para asegurarse de que el dispositivo tiene un micrófono. Sería poco punto al intentar ejecutar la aplicación en un bloc de notas Kindle o Google sin un micrófono instalado.

El ejemplo de código siguiente muestra cómo consultar si hay disponible un micrófono y si no es así, para crear una alerta. Si ningún micrófono está disponible en este momento prefiere bien salir de la actividad o deshabilitar la capacidad de grabar la voz.

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>Crear la intención

La intención de que el sistema de voz usa un tipo particular de intento de llamar el `RecognizerIntent`. Esta intención controla un gran número de parámetros, incluidos cuánto tiempo debe para esperar con latencia hasta que la grabación se considera sobre los idiomas adicionales que reconoce y saldrá y cualquier texto que se incluyen en el `Intent`del cuadro de diálogo modal como medio de instrucción. En este fragmento de código, `VOICE` es un `readonly int` usado para reconocimiento en `OnActivityResult`.

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>Conversión de la oración

El texto que se interpreta de la voz que se va a entregar en el `Intent`, que se devuelve cuando la actividad se ha completado y se tiene acceso a través de `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Esto devolverá una `IList<string>`, de que el índice se puede usar y mostrar, dependiendo del número de idiomas solicitado en la intención del autor de la llamada (y se especifica en el `RecognizerIntent.ExtraMaxResults`). Como con cualquier lista, merece la pena comprobación para asegurarse de que no hay datos que se mostrará.

Cuando se escucha para el valor devuelto de un `StartActivityForResult`, el `OnActivityResult` método debe especificarse.

En el ejemplo siguiente, `textBox` es un `TextBox` utilizada para generar lo que se ha determinado. Igualmente se podía usar para pasar el texto a algún formato de intérprete y desde allí, la aplicación puede comparar el texto y bifurcación a otra parte de la aplicación.

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
             if (matches.Count != 0)
             {
                  string textInput = textBox.Text + matches[0];
                  textBox.Text = textInput;
                  switch(matches[0].Substring(0,5).ToLower())
                  {
                     case "north":
                      MovePlayer(0);
                     break;
                   case "south":
                     MovePlayer(1);
                     break;
             }
             else
                  textBox.Text = "No speech was recognised";
        }
   }
    base.OnActivityResult(requestCode, resultVal, data);
}
```

## <a name="text-to-speech"></a>Texto a voz

Texto a voz no es bastante el inverso de la oración en texto y se basa en dos componentes clave; se instala un motor de texto a voz en el dispositivo y un idioma que se va a instalar.

En gran medida, dispositivos Android vienen con el valor predeterminado instalado el servicio de Google TTS y al menos un idioma. Esto se establece cuando el dispositivo está configurado en primer lugar y se basará en donde el dispositivo está en el momento (por ejemplo, un teléfono configurado en Alemania instalará el idioma alemán, mientras que uno en América tendrá inglés de Estados Unidos).

### <a name="step-1---instantiating-texttospeech"></a>Paso 1: crear instancias TextToSpeech

`TextToSpeech` puede tardar hasta 3 parámetros, los dos primeros son necesarios con el tercer argumento que es opcional (`AppContext`, `IOnInitListener`, `engine`). El agente de escucha se utiliza para enlazar con el servicio y la prueba de error con el motor que se va a cualquier número de motores de texto a voz Android disponibles, como mínimo, el dispositivo tendrá el motor de Google.

### <a name="step-2---finding-the-languages-available"></a>Paso 2: buscar los idiomas disponibles

El `Java.Util.Locale` espacio de nombres contiene un método útil denominado `GetAvailableLocales()`. A continuación, se puede probar esta lista de idiomas admitidos por el motor de voz en los idiomas instalados.

Es un asunto trivial para generar la lista de idiomas "entiende". Siempre habrá un idioma predeterminado (el idioma del usuario se establece al configurar primero su dispositivo el), por lo que en este ejemplo el `List<string>` tiene "Default" como el primer parámetro, el resto de la lista se rellenará según el resultado de la `textToSpeech.IsLanguageAvailable(locale)`.

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

### <a name="step-3---setting-the-speed-and-pitch"></a>Paso 3: establecer la velocidad y el tono

Android permite al usuario modificar el sonido de la voz modificando la `SpeechRate` y `Pitch` (la tasa de velocidad y el tono de la voz). Esto va de 0 a 1, "normal" voz que se va a 1 para ambos.

### <a name="step-4---testing-and-loading-new-languages"></a>Paso 4: probar y cargar nuevos idiomas

Esto se realiza mediante un `Intent` con el resultado se interpreta en `OnActivityResult`. A diferencia del ejemplo de texto a voz que usa el `RecognizerIntent` como un `PutExtra` parámetro para el `Intent`, la instalación utiliza intención un `Action`.

Es posible instalar un nuevo lenguaje de Google utilizando el código siguiente. El resultado de la `Activity` comprueba si el idioma es necesario y si es así, el idioma se instala después de que se le pida que se producen en la descarga.

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

### <a name="step-5---the-ioninitlistener"></a>Paso 5: el IOnInitListener

Para una actividad para que pueda convertir el texto a voz, el método de interfaz `OnInit` tiene que crear (este es el segundo parámetro especificado para la creación de instancias de la `TextToSpeech` clase). Esto inicializa el agente de escucha y comprueba el resultado.

El agente de escucha debe probar ambas `OperationResult.Success` y `OperationResult.Failure` como mínimo.
En el ejemplo siguiente se muestra exactamente eso:

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>Resumen

En esta guía, hemos hablado de los conceptos básicos de conversión de texto a voz y voz a texto y métodos posibles incluirlos en sus propias aplicaciones. Mientras que no cubren cada caso concreto, ahora debe tener un conocimiento básico de cómo se interpreta la voz, cómo instalar nuevos idiomas y cómo aumentar la inclusivity de las aplicaciones.



## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Texto a voz (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [Voz a texto (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Espacio de nombres Android.Speech](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Espacio de nombres Android.Speech.Tts](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
