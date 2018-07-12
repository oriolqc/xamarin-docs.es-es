---
title: 'Xamarin.Essentials: texto a voz'
description: La clase TextToSpeech en habilita Xamarin.Essentials una aplicación utilizar integrado en los motores de texto a voz para hablar texto atrás desde el dispositivo y también a los idiomas disponibles de consulta que puede admitir el motor.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 9383411074bc43af1034138aadbb6ac5494c2c01
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815666"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: texto a voz

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **TextToSpeech** clase permite que una aplicación usar la compilación en los motores de texto a voz para hablar texto atrás desde el dispositivo y también a los idiomas disponibles de consulta que puede admitir el motor.

## <a name="using-text-to-speech"></a>Uso de texto a voz

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de texto a voz funciona mediante una llamada a la `SpeakAsync` método con el texto y los parámetros opcionales y devuelve una vez finalizada la declaración. 

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

Este método toma un CancellationToken opcional para detener la declaración de una vez que se inicia. 
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

Texto a voz se pondrán en cola automáticamente las solicitudes de voz desde el mismo subproceso. 

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

Para obtener más control sobre cómo se habla el audio, realizar una copia con `SpeakSettings` que permite establecer el volumen, el cabeceo y la configuración regional.

```csharp
public async Task SpeakNow()
{
    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

Los siguientes son los valores admitidos para estos parámetros:

| Parámetro | Mínima | Máximo |
| --- | :---: | :---: |
| Tono | 0 | 2.0 |
| Volumen | 0 | 1.0 |

### <a name="speech-locales"></a>Configuraciones regionales de voz

Cada plataforma ofrece configuraciones regionales para hablar de back-texto en varios idiomas y acentos. Cada plataforma tiene distintos códigos y formas de especificar esto, que es por eso Essentials proporciona una multiplataforma `Locale` clase y una forma de consultas en ellos con `GetLocalesAsync`.

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>Limitaciones

- Cola utterance (dictado) no se garantiza que si llama a través de varios subprocesos.
- Reproducción de audio en segundo plano no se admite oficialmente.

## <a name="api"></a>API

- [Código fuente de TextToSpeech](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Documentación de la API de TextToSpeech](xref:Xamarin.Essentials.TextToSpeech)
