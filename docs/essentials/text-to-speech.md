---
title: 'Xamarin.Essentials: Text-to-Speech'
description: La clase TextToSpeech en Xamarin.Essentials permite que una aplicación utilice los motores de texto a voz para responder a texto del dispositivo y también para consultar los idiomas disponibles que el motor puede admitir.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: a7f40fc652c6f02d68a9f01dcdd0f4132893a6ca
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898542"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: Text-to-Speech

La clase **TextToSpeech** permite que una aplicación utilice los motores de texto a voz para responder a texto del dispositivo y también para consultar los idiomas disponibles que el motor puede admitir.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-text-to-speech"></a>Uso de Text-to-Speech

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Text-to-Speech funciona mediante una llamada al método `SpeakAsync` con parámetros opcionales y de texto y se devuelve una vez que finaliza la declaración.

```csharp
public async Task SpeakNowDefaultSettings()
{
    await TextToSpeech.SpeakAsync("Hello World");

    // This method will block until utterance finishes.
}

public void SpeakNowDefaultSettings2()
{
    TextToSpeech.SpeakAsync("Hello World").ContinueWith((t) =>
    {
        // Logic that will run after utterance finishes.

    }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

Este método toma un elemento `CancellationToken` opcional para detener la declaración una vez que se inicia.

```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? false)
        return;

    cts.Cancel();
}
```

Text-to-Speech pondrá automáticamente en la cola las solicitudes de voz del mismo subproceso.

```csharp
bool isBusy = false;
public void SpeakMultiple()
{
    isBusy = true;
    Task.Run(async () =>
    {
        await TextToSpeech.SpeakAsync("Hello World 1");
        await TextToSpeech.SpeakAsync("Hello World 2");
        await TextToSpeech.SpeakAsync("Hello World 3");
        isBusy = false;
    });

    // or you can query multiple without a Task:
    Task.WhenAll(
        TextToSpeech.SpeakAsync("Hello World 1"),
        TextToSpeech.SpeakAsync("Hello World 2"),
        TextToSpeech.SpeakAsync("Hello World 3"))
        .ContinueWith((t) => { isBusy = false; }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

### <a name="speech-settings"></a>Configuración de voz

Para más información sobre cómo se responde el audio, `SpeechOptions` permite ajustar el volumen, el tono y la configuración regional.

```csharp
public async Task SpeakNow()
{
    var settings = new SpeechOptions()
        {
            Volume = .75,
            Pitch = 1.0
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

Los siguientes son los valores compatibles para estos parámetros:

| Parámetro | Mínima | Máximo |
| --- | :---: | :---: |
| Tono | 0 | 2.0 |
| Volumen | 0 | 1.0 |

### <a name="speech-locales"></a>Configuraciones regionales de voz

Cada plataforma admite distintas configuraciones regionales para responder texto en distintos idiomas y acentos. Las plataformas tienen distintos códigos y formas de especificar la configuración regional, que es la razón por la que Xamarin.Essentials proporciona una clase`Locale` multiplataforma y una manera de hacer consultas con `GetLocalesAsync`.

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeechOptions()
        {
            Volume = .75,
            Pitch = 1.0,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>Limitaciones

- No se garantiza la cola de declaraciones si se llama a través de varios subprocesos.
- La reproducción de audio en segundo plano no se admite de manera oficial.

## <a name="api"></a>API

- [Código fuente de TextToSpeech](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Documentación de TextToSpeech API](xref:Xamarin.Essentials.TextToSpeech)
