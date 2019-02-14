---
title: 'Xamarin.Essentials: Compartir'
description: La clase Share de Xamarin.Essentials permite que una aplicación comparta datos como texto y vínculos web con otras aplicaciones del dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: 7e61041fa33557c4e1db3613b75b575e9d456231
ms.sourcegitcommit: c6ff24b524d025d7e87b7b9c25f04c740dd93497
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2019
ms.locfileid: "56240427"
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

![Compartir](share-images/share.png)

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

* La propiedad `Subject` se usa para el asunto deseado de un mensaje.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` no se usa.
* `Title` no se usa.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* El valor predeterminado de `Title` será el nombre de la aplicación si no se establece.
* `Subject` no se usa.

-----

## <a name="api"></a>API

- [Código fuente de Share](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Documentación de API para Share](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
