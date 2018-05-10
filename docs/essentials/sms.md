---
title: SMS Xamarin.Essentials
description: La clase de Sms permite a una aplicación abrir la aplicación de SMS predeterminado con un mensaje especificado para enviar a un destinatario.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 5baeee03626ba659ac7e5c06be40039476a67e08
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-sms"></a>SMS Xamarin.Essentials

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

- [Código de origen de SMS](https://github.com/xamarin/Essentials/tree/master/Essentials/Sms)
- [Documentación de la API de SMS](xref:Xamarin.Essentials.Sms)
