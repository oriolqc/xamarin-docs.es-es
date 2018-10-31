---
title: 'Xamarin.Essentials: Transferencia de datos'
description: La clase DataTransfer en Xamarin.Essentials permite que una aplicación compartir datos como texto y web vínculos a otras aplicaciones en el dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 31e27556a6681b144084d2177cf3fde8fe8e5459
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353524"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Transferencia de datos

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

La clase **DataTransfer** permite a una aplicación compartir datos como texto y vínculos web a otras aplicaciones del dispositivo.

## <a name="using-data-transfer"></a>Uso de transferencia de datos

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

Interfaz de usuario para que aparece cuando se realiza la solicitud para compartir datos a una aplicación externa:

![Transferencia de datos](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

* La propiedad `Subject` se utiliza como el asunto deseada de un mensaje.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` No se utiliza.
* `Title` No se utiliza.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `Title` sera el nombre de la aplicación de forma predeterminada si no se establece.
* `Subject` No se utiliza.

-----

## <a name="api"></a>API

- [Código fuente de la clase DataTransfer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentación de la API DataTransfer](xref:Xamarin.Essentials.DataTransfer)
