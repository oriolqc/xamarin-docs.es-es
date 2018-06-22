---
title: 'Xamarin.Essentials: SMS'
description: La clase de Sms en Xamarin.Essentials permite a una aplicación abrir la aplicación de SMS predeterminado con un mensaje especificado para enviar a un destinatario.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a93a67b83ea8f435a5e3ad5d26e1d6cbbb7092f7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783092"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **Sms** clase permite a una aplicación abrir la aplicación de SMS predeterminado con un mensaje especificado para enviar a un destinatario.

## <a name="using-sms"></a>Uso de Sms

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

La funcionalidad SMS funciona mediante una llamada a la `ComposeAsync` método un `SmsMessage` que contiene el destinatario del mensaje y el cuerpo del mensaje, ambos de los cuales son opcionales.

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, recipient);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Código de origen de SMS](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentación de la API de SMS](xref:Xamarin.Essentials.Sms)
