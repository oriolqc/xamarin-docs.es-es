---
title: SMS Xamarin.Essentials
description: La clase de Sms permite a una aplicación abrir la aplicación de SMS predeterminado con un mensaje especificado para enviar a un destinatario.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1f466e01d9b1e48849e60a364cf1d49d9cbdcb37
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
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

- [Código de origen de SMS](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentación de la API de SMS](xref:Xamarin.Essentials.Sms)
