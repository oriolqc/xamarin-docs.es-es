---
title: 'Xamarin.Essentials: Transferencia de datos'
description: La clase DataTransfer de Xamarin.Essentials permite que una aplicación comparta datos como texto y vínculos web con otras aplicaciones del dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 179d4327aa768e7aa2c81dbbffd694d078327400
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674839"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Transferencia de datos

![Versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **DataTransfer** permite que una aplicación comparta datos como texto y vínculos web con otras aplicaciones del dispositivo.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-data-transfer"></a>Uso de la transferencia de datos

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcion transferencia de datos funciona mediante una llamada al método `RequestAsync` con algunos parametros que incluyen información para compartir con otras aplicaciones. Se pueden combinar texto y Uri, cada plataforma controlara el filtrado basado en el contenido.

```csharp

public class DataTransferTest
{
    public async Task ShareText(string text)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

Interfaz de usuario para compartir con una aplicación externa que aparece cuando se realiza la solicitud:

![Transferencia de datos](data-transfer-images/data-transfer.png)

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

- [Código fuente de la clase DataTransfer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentación de API para DataTransfer](xref:Xamarin.Essentials.DataTransfer)
