---
title: 'Xamarin.Essentials: Transferencia de datos'
description: La clase DataTransfer en Xamarin.Essentials permite que una aplicación compartir datos como texto y web vínculos a otras aplicaciones en el dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c1ed298e1317d0a3f78f4dbd9fc89a2b01c6958c
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855115"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Transferencia de datos

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **DataTransfer** clase permite que una aplicación compartir datos como texto y web vínculos a otras aplicaciones en el dispositivo.

## <a name="using-data-transfer"></a>Uso de transferencia de datos

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de transferencia de datos funciona mediante una llamada a la `RequestAsync` método con una carga de solicitudes de datos que incluye información para compartir con otras aplicaciones. Se pueden combinar el texto y el Uri y va a controlar cada plataforma de filtrado basado en contenido.

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

Interfaz de usuario para compartir a una aplicación externa que aparece cuando se realiza la solicitud:

![Transferencia de datos](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>Diferencias entre las plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `Subject` propiedad se utiliza para el objeto deseada de un mensaje.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` no se utiliza.
* `Title` no se utiliza. 

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `Title` le nombre de la aplicación de forma predeterminada si no se establece.
* `Subject` no se utiliza.

-----

## <a name="api"></a>API

- [Código fuente de transferencia de datos](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentación de API de transferencia de datos](xref:Xamarin.Essentials.DataTransfer)
