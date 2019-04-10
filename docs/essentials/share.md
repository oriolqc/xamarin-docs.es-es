---
title: 'Xamarin.Essentials: Compartir'
description: La clase Share de Xamarin.Essentials permite que una aplicación comparta datos como texto y vínculos web con otras aplicaciones del dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: 1a9a7b008773255d9d7743a4fcb21f02feb3e116
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58869382"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials: Compartir

La clase **Share** permite que una aplicación comparta datos como texto y vínculos web con otras aplicaciones del dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>Uso de Share

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Share funciona mediante una llamada al método `RequestAsync` con una carga de solicitud de datos que incluye información para compartir con otras aplicaciones. Se pueden combinar texto y Uri, cada plataforma controlara el filtrado basado en el contenido.

```csharp

public class ShareTest
{
    public async Task ShareText(string text)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

Interfaz de usuario para compartir con una aplicación externa que aparece cuando se realiza la solicitud:

![Compartir](images/share.png)

## <a name="platform-differences"></a>Diferencias entre plataformas

# [<a name="android"></a>Android](#tab/android)

* `Subject` La propiedad se usa para el asunto deseado de un mensaje.

# [<a name="ios"></a>iOS](#tab/ios)

* `Subject` No se usa.
* `Title` No se usa.

# [<a name="uwp"></a>UWP](#tab/uwp)

* `Title` El valor predeterminado será el nombre de la aplicación si no se establece.
* `Subject` No se usa.

-----

## <a name="files"></a>Archivos

![Características en versión preliminar](~/media/shared/preview.png)

El uso compartido de archivos está disponible como versión preliminar experimental en Xamarin.Essentials versión 1.1.0. Esta característica permite que una aplicación comparta archivos con otras aplicaciones del dispositivo. Para habilitar esta característica, establezca la siguiente propiedad en el código de inicio de la aplicación:

```csharp
ExperimentalFeatures.Enable(ExperimentalFeatures.ShareFileRequest);
```

Una vez que se haya habilitado la característica, se puede compartir cualquier archivo. Xamarin.Essentials detectará automáticamente el tipo de archivo (MIME) y solicitará el uso compartido. Cada plataforma podría admitir únicamente determinadas extensiones de archivo.

A continuación se muestra un ejemplo en el que se escribe texto en el disco y se comparte con otras aplicaciones:

```csharp
var fn =  "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file)
});
```

## <a name="api"></a>API

- [Código fuente de Share](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Documentación de API para Share](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
