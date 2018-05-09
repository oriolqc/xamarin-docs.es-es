---
title: Xamarin.Essentials texto a voz
description: La clase TextToSpeech permite una aplicación utilizar integrado en los motores de texto a voz hablar texto posterior del dispositivo y también a los idiomas disponibles de consulta que puede admitir el motor.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: e5ee5a324c6e753d389f7e80df106dbf4af1a8ca
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials texto a voz

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **TextToSpeech** clase habilita una aplicación utiliza integrado en los motores de texto a voz hablar texto posterior del dispositivo y también a los idiomas disponibles de consulta que puede admitir el motor.

## <a name="using-text-to-speech"></a>Uso de texto a voz

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de texto a voz funciona mediante una llamada a la `SpeakAsync` método con parámetros opcionales y texto y devuelve una vez finalizada la expresión. 

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

Este método toma en un objeto CancellationToken opcional para detener la expresión uno que se inicia. 
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

Texto a voz automáticamente se pondrán en cola las solicitudes de voz desde el mismo subproceso. 

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

Para tener más control sobre cómo se habla el audio realizar copias con `SpeakSettings` que permite establecer el volumen, el tono y la configuración regional.

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

Éstos son los valores admitidos para estos parámetros:

| Parámetro | Mínima | Máximo |
| --- | :---: | :---: |
| Tono | 0 | 2.0 |
| Volumen | 0 | 1.0 |

### <a name="speech-locales"></a>Configuraciones regionales de voz

Cada plataforma ofrece configuraciones regionales para hablar atrás texto en varios idiomas y acentos. Cada plataforma tiene otro códigos y formas de especificar esta, motivo por el que Essentials proporciona una multiplataforma `Locale` clase y una forma para consultarlos con `GetLocalesAsync`.

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

- Cola de la expresión no está garantizada si se llama a través de varios subprocesos.
- Reproducción de audio no se admite oficialmente en segundo plano.

## <a name="api"></a>API

- [Código fuente de TextToSpeech](https://github.com/xamarin/Essentials/tree/master/Essentials/TextToSpeech)
- [Documentación de la API de TextToSpeech](xref:Xamarin.Essentials.TextToSpeech)
