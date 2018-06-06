---
title: 'Xamarin.Essentials: Transferencia de datos'
description: La clase DataTransfer en Xamarin.Essentials permite a una aplicación compartir datos como texto y web vínculos a otras aplicaciones en el dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 69d429b1cdbbbd6dbb53e3cefa89695666494ba7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782390"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Transferencia de datos

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **DataTransfer** clase permite a una aplicación compartir datos como texto y web vínculos a otras aplicaciones en el dispositivo.

## <a name="using-data-transfer"></a>Uso de la transferencia de datos

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad de transferencia de datos funciona mediante una llamada a la `RequestAsync` método con una carga de solicitud de datos que incluye información para compartir con otras aplicaciones. Se pueden combinar el texto y el Uri y cada plataforma encargará de filtrado basado en contenido.

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

Interfaz de usuario para compartir contenido con aplicación externa que aparece cuando se realiza la solicitud:

![Transferencia de datos](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>Diferencias entre las plataformas

| Plataforma | Diferencia |
| --- | --- |
| Android | Se utiliza la propiedad Subject para deseado asunto de un mensaje. |
| iOS | Asunto no utilizado. |
| iOS | Título no usado. |
| UWP | Título le nombre de la aplicación de forma predeterminada si no se establece. |
| UWP | Asunto no utilizado. |

## <a name="api"></a>API

- [Código fuente de transferencia de datos](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentación de la API de transferencia de datos](xref:Xamarin.Essentials.DataTransfer)
